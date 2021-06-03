---
description: The destinations configuration allows you to indicate basic information like your destination name, category, description, logo, and more. The settings in this configuration also determine how your destination appears in the Experience Platform user interface and the identities that can be exported to your destination.
title: Destination configuration options for the Destination SDK
exl-id: 2ecd0bdf-55c3-4946-a304-0147bc16ff39
---
# Destinations API reference {#destination-configuration}

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

**API endpoint**: `platform.adobe.io/data/core/activation/authoring/v1/destinations` 

This page lists and describes all the API operations that you can perform using the `/authoring/v1/destinations` API endpoint. For a description of the functionality supported by this endpoint, read [destination configuration](/help/destination-configuration.md). 

## Getting started with destination API operations

Before continuing, please review the [getting started guide](./getting-started.md) for important information that you need to know in order to successfully make calls to the API, including how to obtain required headers and how to get allow listed.

## Create configuration for a destination {#create}

You can create a new destination configuration by making a POST request to the `/authoring/v1/destinations` endpoint.

**API format**


```http
POST /authoring/v1/destinations
```

**Request**

The following request creates a new destination configuration, configured by the parameters provided in the payload. The payload below includes all parameters accepted by the `/authoring/v1/destinations` endpoint. Note that you do not have to add all parameters on the call and that the template is customizable, according to your API requirements.

```shell
curl -X POST https://platform.adobe.io/data/core/activation/authoring/v1/destinations \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'Content-Type: application/json' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
  "name": "Moviestar destination",
  "destinationServerType": "URL_BASED",
  "urlBasedDestination": {
    "url": {
      "templatingStrategy": "PEBBLE_V1",
      "value": "https://api.moviestar.com/data/{{endpoint.region}}/items"
    },
    "maxUsersPerRequest": 100,
    "splitUserById": true
  }
},
  "httpTemplate": {
    "httpMethod": "POST",
    "requestBody": {
            "templatingStrategy": "PEBBLE_V1",
            "value": "{ \"attributes\": [ {% for ns in [\"external_id\", \"yourdestination_id\"] %} {% if input.profile.identityMap[ns] is not empty and first_namespace_encountered %} , {% endif %} {% set first_namespace_encountered = true %} {% for identity in input.profile.identityMap[ns]%} { \"{{ ns }}\": \"{{ identity.id }}\" {% if input.profile.segmentMembership.ups is not empty %} , \"AEPSegments\": { \"add\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"realized\" or segment.value.status == \"existing\" %} {% if added_segment_found %} , {% endif %} {% set added_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ], \"remove\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"exited\" %} {% if removed_segment_found %} , {% endif %} {% set removed_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ] } {% set removed_segment_found = false %} {% set added_segment_found = false %} {% endif %} {% if input.profile.attributes is not empty %} , {% endif %} {% for attribute in input.profile.attributes %} \"{{ attribute.key }}\": {% if attribute.value is empty %} null {% else %} \"{{ attribute.value.value }}\" {% endif %} {% if not loop.last%} , {% endif %} {% endfor %} } {% if not loop.last %} , {% endif %} {% endfor %} {% endfor %} ] }"
        },
        "contentType": "application/json"
    }
```

| Parameter | Type | Description |
| -------- | ----------- | ----------- |
|`name` | String | Represents a friendly name of your server, visible only to Adobe. This name is not visible to partners or customers. Example `Moviestar destination`.  |
|`destinationServerType` | String | `URL_BASED` is the only available option in the beta release phase. |
|`urlBasedDestination.url.templatingStrategy` | String | <ul><li>Use `PEBBLE_V1` if Adobe needs to transform the URL in the `value` field below. Use this option if you have an endpoint like: `https://api.moviestar.com/data/{{endpoint.region}}/items` </li><li> Use `NONE` if no transformation is needed on the Adobe side, for example if you have an endpoint like: `https://api.moviestar.com/data/items` </li></ul>  |
|`urlBasedDestination.url.value` | String | Fill in the address of the API endpoint that Experience Platform should connect to. |
|`urlBasedDestination.maxUsersPerRequest` | Integer | Adobe can aggregate multiple exported profiles in a single HTTP call. Specify the maximum number of profiles that your endpoint should receive in a single HTTP call. Note that this is a best effort aggregation. For example, if you specify the value 100, Adobe might send any number of profiles smaller than 100 on a call. <br> If your server does not accept multiple users per request, set this value to 1. |
|`urlBasedDestination.splitUserById` | Boolean | Use this flag if the call to the destination should be split by identity. Set this flag to `true` if your server only accepts one identity per call, for a given namespace. |
|`httpTemplate.httpMethod` | String | The method that Adobe will use in calls to your server. Options are `GET`, `PUT`, `POST`, `DELETE`, `PATCH`. |
|`httpTemplate.requestBody.templatingStrategy` | String | Use `PEBBLE_V1`. |
|`httpTemplate.requestBody.value` | String | This string is the character-escaped version that transforms Platform customers' data to the format your service expects. <br> For information how to write the template, read the [Using templating section](/help/message-format.md#using-templating). <br> For more information about character escaping, refer to the [RFC JSON standard, section seven](https://tools.ietf.org/html/rfc8259#section-7). <br> For an example of a simple transformation, refer to the [Profile Attributes](/help/message-format.md#attributes) transformation. |
|`httpTemplate.contentType` | String | The content type that your server accepts. This value is most likely `application/json`. |

{style="table-layout:auto"}

**Response**

A successful response returns HTTP status 200 with details of your newly created destination configuration.

## List destination configurations {#retrieve-list}

You can retrieve a list of all destination configurations for your IMS Organization by making a GET request to the `/authoring/v1/destinations` endpoint.

**API format**


```http
GET /authoring/v1/destinations
```

**Request**

The following request will retrieve the list of destination configurations that you have access to, based on IMS Organization and sandbox configuration.

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destinations \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'  
```

**Response**

The following response returns HTTP status 200 with a list of destination configurations that you have access to, based on the IMS Organization ID, sandbox name, and sandbox ID that you used. One `instanceId` corresponds to the template for one destination. The response is truncated for brevity.

```json

[
       {
        "instanceId": "2307ec2b-4798-45a4-9239-5d0a2fb0ed67",
        "createdDate": "2020-11-17T06:49:24.331012Z",
        "lastModifiedDate": "2020-11-17T06:49:24.331012Z",
        "name": "Moviestar destination",
        "destinationServerType": "URL_BASED",
        "urlBasedDestination": {
            "url": {
                "templatingStrategy": "PEBBLE_V1",
                "value": "https://go.{% if destination.config.domain == \"US\" %}moviestar.com{% else %}moviestar.eu{% endif%}/api/named_users/tags"
            },
            "splitUserById": false
        },
        "httpTemplate": {
            "requestBody": {
                "templatingStrategy": "PEBBLE_V1",
                "value": "{ \"audience\": { \"named_user_id\": [ {% for named_user in input.profile.identityMap.named_user_id %} \"{{ named_user.id }}\"{% if not loop.last %},{% endif %} {% endfor %} ] }, {% if addedSegments(input.profile.segmentMembership.ups) is not empty %} \"add\": { \"adobe-segments\": [ {% for added_segment in addedSegments(input.profile.segmentMembership.ups) %} \"{{ destination.segmentNames[added_segment.key] }}\"{% if not loop.last %},{% endif %} {% endfor %} ] } {% endif %} {% if addedSegments(input.profile.segmentMembership.ups) is not empty and removedSegments(input.profile.segmentMembership.ups) is not empty %} , {% endif %} {% if removedSegments(input.profile.segmentMembership.ups) is not empty %} \"remove\": { \"adobe-segments\": [ {% for removed_segment in removedSegments(input.profile.segmentMembership.ups) %} \"{{ destination.segmentNames[removed_segment.key] }}\"{% if not loop.last %},{% endif %} {% endfor %} ] } {% endif %} }"
            },
            "httpMethod": "POST",
            "contentType": "application/json",
            "headers": [
                {
                    "header": "Accept",
                    "value": {
                        "templatingStrategy": "NONE",
                        "value": "application/vnd.moviestar+json; version=3;"
                    }
                }
            ]
        },
        "qos": {
            "name": "freeform"
        }
    },
    {
        "instanceId": "d88de647-a352-4824-8b46-354afc7acbff",
        "createdDate": "2020-11-17T16:50:59.635228Z",
        "lastModifiedDate": "2020-11-17T16:50:59.635228Z",
        "name": "Test11 destination",
        "destinationServerType": "URL_BASED",
        "urlBasedDestination": {
            "url": {
                "templatingStrategy": "PEBBLE_V1",
                "value": "https://go.{% if destination.config.domain == \"US\" %}moviestar.com{% else %}airship.eu{% endif%}/api/named_users/tags"
            },
            "splitUserById": false
        },
        "httpTemplate": {
            "requestBody": {
                "templatingStrategy": "PEBBLE_V1",
                "value": "{ \"audience\": { \"named_user_id\": [ {% for named_user in input.profile.identityMap.named_user_id %} \"{{ named_user.id }}\"{% if not loop.last %},{% endif %} {% endfor %} ] }, {% if addedSegments(input.profile.segmentMembership.ups) is not empty %} \"add\": { \"adobe-segments\": [ {% for added_segment in addedSegments(input.profile.segmentMembership.ups) %} \"{{ destination.segmentNames[added_segment.key] }}\"{% if not loop.last %},{% endif %} {% endfor %} ] } {% endif %} {% if addedSegments(input.profile.segmentMembership.ups) is not empty and removedSegments(input.profile.segmentMembership.ups) is not empty %} , {% endif %} {% if removedSegments(input.profile.segmentMembership.ups) is not empty %} \"remove\": { \"adobe-segments\": [ {% for removed_segment in removedSegments(input.profile.segmentMembership.ups) %} \"{{ destination.segmentNames[removed_segment.key] }}\"{% if not loop.last %},{% endif %} {% endfor %} ] } {% endif %} }"
            },
            "httpMethod": "POST",
            "contentType": "application/json",
            "headers": [
                {
                    "header": "Accept",
                    "value": {
                        "templatingStrategy": "NONE",
                        "value": "application/vnd.moviestar+json; version=3;"
                    }
                }
            ]
        },
        "qos": {
            "name": "freeform"
        }
    },
]
    
```

## Update an existing destination configuration {#update}

You can update an existing destination configuration by making a PUT request to the `/authoring/v1/destinations` endpoint and providing the instance ID of the destination configuration you want to update. In the body of the call, provide the updated destination configuration.

**API format**


```http
PUT /authoring/v1/destinations/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the destination configuration that you want to update. |

**Request**

The following request updates an existing destination configuration, configured by the parameters provided in the payload.

```shell

curl -X PUT https://platform.adobe.io/data/core/activation/authoring/v1/destinations/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
  "name": "Moviestar destination",
  "destinationServerType": "URL_BASED",
  "urlBasedDestination": {
    "url": {
      "templatingStrategy": "PEBBLE_V1",
      "value": "https://api.moviestar.com/data/{{endpoint.region}}/items"
    },
    "maxUsersPerRequest": 100,
    "splitUserById": true
  }
},
  "httpTemplate": {
    "httpMethod": "POST",
    "requestBody": {
            "templatingStrategy": "PEBBLE_V1",
            "value": "{ \"attributes\": [ {% for ns in [\"external_id\", \"yourdestination_id\"] %} {% if input.profile.identityMap[ns] is not empty and first_namespace_encountered %} , {% endif %} {% set first_namespace_encountered = true %} {% for identity in input.profile.identityMap[ns]%} { \"{{ ns }}\": \"{{ identity.id }}\" {% if input.profile.segmentMembership.ups is not empty %} , \"AEPSegments\": { \"add\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"realized\" or segment.value.status == \"existing\" %} {% if added_segment_found %} , {% endif %} {% set added_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ], \"remove\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"exited\" %} {% if removed_segment_found %} , {% endif %} {% set removed_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ] } {% set removed_segment_found = false %} {% set added_segment_found = false %} {% endif %} {% if input.profile.attributes is not empty %} , {% endif %} {% for attribute in input.profile.attributes %} \"{{ attribute.key }}\": {% if attribute.value is empty %} null {% else %} \"{{ attribute.value.value }}\" {% endif %} {% if not loop.last%} , {% endif %} {% endfor %} } {% if not loop.last %} , {% endif %} {% endfor %} {% endfor %} ] }"
        },
        "contentType": "application/json"
    }

```





## Retrieve a specific destination configuration {#get}

You can retrieve detailed information about a specific destination configuration by making a GET request to the `/authoring/v1/destinations` endpoint and providing the instance ID of the destination configuration you want to update.

**API format**


```http
GET /authoring/v1/destinations/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the destination configuration you want to retrieve. |

**Request**

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destinations/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'
```

**Response**

A successful response returns HTTP status 200 with detailed information about the specified destination configuration.

```json
{
  "name": "Moviestar destination",
  "destinationServerType": "URL_BASED",
  "urlBasedDestination": {
    "url": {
      "templatingStrategy": "PEBBLE_V1",
      "value": "https://api.moviestar.com/data/{{endpoint.region}}/items"
    },
    "maxUsersPerRequest": 100,
    "splitUserById": true
  }
},
  "httpTemplate": {
    "httpMethod": "POST",
    "requestBody": {
            "templatingStrategy": "PEBBLE_V1",
            "value": "{ \"attributes\": [ {% for ns in [\"external_id\", \"yourdestination_id\"] %} {% if input.profile.identityMap[ns] is not empty and first_namespace_encountered %} , {% endif %} {% set first_namespace_encountered = true %} {% for identity in input.profile.identityMap[ns]%} { \"{{ ns }}\": \"{{ identity.id }}\" {% if input.profile.segmentMembership.ups is not empty %} , \"AEPSegments\": { \"add\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"realized\" or segment.value.status == \"existing\" %} {% if added_segment_found %} , {% endif %} {% set added_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ], \"remove\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"exited\" %} {% if removed_segment_found %} , {% endif %} {% set removed_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ] } {% set removed_segment_found = false %} {% set added_segment_found = false %} {% endif %} {% if input.profile.attributes is not empty %} , {% endif %} {% for attribute in input.profile.attributes %} \"{{ attribute.key }}\": {% if attribute.value is empty %} null {% else %} \"{{ attribute.value.value }}\" {% endif %} {% if not loop.last%} , {% endif %} {% endfor %} } {% if not loop.last %} , {% endif %} {% endfor %} {% endfor %} ] }"
        },
        "contentType": "application/json"
    }
```


## Delete a specific destination configuration {#delete}

You can delete the specified destination configuration by making a DELETE request to the `/authoring/v1/destinations` endpoint and providing the ID of the destination configuration you wish to delete in the request path.

**API format**

```http
DELETE /authoring/v1/destinations/{INSTANCE_ID}
```

| Parameter | Description |
| --------- | ----------- |
| `{INSTANCE_ID}` | The `id` of the destination configuration you want to delete. |

**Request**

```shell
curl -X DELETE https://platform.adobe.io/data/core/activation/authoring/v1/destinations/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}'
```

**Response**

A successful response returns HTTP status 200 along with an empty HTTP response.

## API error handling

Destination SDK API endpoints follow the general Experience Platform API error message principles. Refer to [API status codes](https://experienceleague.adobe.com/docs/experience-platform/landing/troubleshooting.html?lang=en#api-status-codes) and [request header errors](https://experienceleague.adobe.com/docs/experience-platform/landing/troubleshooting.html?lang=en#request-header-errors) in the Platform troubleshooting guide.

## Example configuration {#example-configuration}

Below is an example configuration for a fictional destination, Moviestar, which has endpoints in four locations on the globe. The destination belongs to the mobile destinations category.

```json

{
  "name": "Moviestar",
  "description": "Moviestar is a fictional destination, used for this example.",
  "releaseNotes": "Test release",
  "status": "TEST",
  "customerAuthenticationConfigurations": [
    {
      "authType": "BEARER"
    }
  ],
  "customerDataFields": [
      {
        "name": "endpointsInstance",
        "type": "string",
        "title": "Select Endpoint",
        "description": "Moviestar manages several instances across the globe for REST endpoints that our customers are provisioned for. Select your endpoint in the dropdown list.",
        "isRequired": true,
        "enum": [
          "US",
          "EU",
          "APAC",
          "NZ"
        ]
      },
      {
      "name": "customerID",
      "type": "string",
      "title": "Moviestar Customer ID",
      "description": "Your customer ID in the Moviestar destination (e.g. abcdef).",
      "isRequired": true,
      "pattern": "^[A-Za-z]+$"
      }
    ],
  "uiAttributes": {
    "documentationLink": "http://www.adobe.com/go/destinations-moviestar-en",
    "category": "mobile",
    "iconUrl": "https://address-to-your-logo.png",
    "connectionType": "Server-to-server",
    "frequency": "Streaming"
  },
  "identityNamespaces": {
    "external_id": {
      "acceptsAttributes": true,
      "acceptsCustomNamespaces": true
    },
    "another_id": {
      "acceptsAttributes": true,
      "acceptsCustomNamespaces": true
    }
  },
  "destinationDelivery": [
    {
      "authenticationRule": "CUSTOMER_AUTHENTICATION",
      "destinationServerId": "9c77000a-4559-40ae-9119-a04324a3ecd4"
    }
  ],
  "inputSchemaId": "cc8621770a9243b98aba4df79898b1ed"
}

```

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Indicates the title of your destination in the Experience Platform catalog |
|`description` | String | Provide a description that Adobe will use in the Experience Platform destinations catalog for your destination card. Aim for no more than 4-5 sentences. |
|`releaseNotes` | String | This field is not necessary in the beta phase of Destination SDK. |
|`status` | String | Indicates the lifecycle status of the destination card. Accepted values are `TEST`, `PUBLISHED`, and `DELETED`. Use `TEST` when you first configure your destination. |
|`customerAuthenticationConfigurations` | String | Indicates the configuration used to authenticate Experience Platform customers to your server. See `authType` below for accepted values. |
|`authType` | String | Accepted values are `S3, SFTP_WITH_SSH_KEY, SFTP_WITH_PASSWORD, OAUTH1, OAUTH2, BASIC, BEARER`. |

## Customer Data Fields {#customer-data-fields}

This section allows partners to introduce custom fields. In the example configuration above, `customerDataFields` requires users to select an endpoint in the authentication flow and indicate their customer ID with the destination. The configuration is reflected in the authentication flow as shown below:

![Custom field authentication flow](/help/assets/custom-field-authentication-flow.png)

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Provide a name for the custom field you are introducing. |
|`type` | String | Indicates what type of custom field you are introducing. Accepted values are `string`, `object`, `integer` |
|`title` | String | Indicates the name of the field, as it is seen by customers in the Experience Platform user interface |
|`description` | String | Provide a description for the custom field. |
|`isRequired` | Boolean | Indicates if this field is required in the destination setup workflow. |
|`enum` | String | Renders the custom field as a dropdown menu and lists the options available to the user. |
|`pattern` | String | Enforces a pattern for the custom field, if needed. Use regular expressions to enforce a pattern. For example, if your customer IDs don't include numbers or underscores, enter `^[A-Za-z]+$` in this field. |

## UI attributes {#ui-attributes}

This section refers to the UI elements in the configuration template above that Adobe should use for your destination in the Adobe Experience Platform user interface. See below:

|Parameter | Type | Description|
|---------|----------|------|
|`documentationLink` | String | Refers to the documentation page in the [Destinations Catalog](https://experienceleague.adobe.com/docs/experience-platform/destinations/catalog/overview.html?lang=en#catalog) for your destination. Use `http://www.adobe.com/go/destinations-YOURDESTINATION-en`, where `YOURDESTINATION` is the name of your destination. For a destination called Moviestar, you would use `http://www.adobe.com/go/destinations-moviestar-en` |
|`category` | String | Refers to the category assigned to your destination in Adobe Experience Platform. For more information, read [Destination Categories](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/destination-types.html?lang=en#destination-categories). Use one of the following values: `adobeSolutions, advertising, analytics, cdp, cloudStorage, crm, customerSuccess, database, dmp, ecommerce, email, emailMarketing, enrichment, livechat, marketingAutomation, mobile, personalization, protocols, social, streaming, subscriptions, surveys, tagManagers, voc, warehouses, payments` |
|`iconUrl` | String | Provide the logo that Adobe will display in the Adobe Experience Platform destinations catalog for your destination card. |
|`connectionType` | String | In the beta release phase of Destination SDK, `Server-to-server` is the only available option. |
|`frequency` | String | In the beta release phase of Destination SDK, `Streaming` is the only available option. |

## Identities {#identities}

Adobe needs to know which [!DNL Platform] identities customers will be able to export to your destination. Some examples are [!DNL Experience Cloud ID], hashed email, device ID ([!DNL IDFA], [!DNL GAID]). These values are [!DNL Platform] identity namespaces that customers can map to identity namespaces from your destination.

Identity namespaces do not require a 1-to-1 correspondence between [!DNL Platform] and your destination.
For instance, customers could map a [!DNL Platform] [!DNL IDFA] namespace to an [!DNL IDFA] namespace from your destination, or they can map the same [!DNL Platform] [!DNL IDFA] namespace to a [!DNL Customer ID] namespace in your destination.

 Read more in the [Identity Namespace overview](https://docs.adobe.com/content/help/en/experience-platform/identity/namespaces.html).

|Parameter | Type | Description|
|---------|----------|------|
|`acceptsAttributes` | Boolean | Indicates if your destination accepts standard profile attributes. Usually, these attributes are highlighted in our partners' documentation. |
|`acceptsCustomNamespaces` | Boolean | Indicates if customers can set up custom namespaces in your destination. |
|`allowedAttributesTransformation` | String | _Not shown in example configuration_. Used, for example, when the [!DNL Platform] customer has plain email addresses as an attribute and your platform only accepts hashed emails. This is where you would provide the transformation that needs to be applied (for example, transform the email to lowercase, then hash).   |
|`acceptedGlobalNamespaces` | - | _Not shown in example configuration_. Used for cases when your platform accepts [standard identity namespaces](https://experienceleague.adobe.com/docs/experience-platform/identity/namespaces.html?lang=en#standard-namespaces) (for example, IDFA), so you can restrict Platform users to only selecting these identity namespaces. |

## Destination delivery {#destination-delivery}

|Parameter | Type | Description|
|---------|----------|------|
|`authenticationRule` | String | Indicates how [!DNL Platform] customers connect to your destination. Accepted values are `CUSTOMER_AUTHENTICATION`, `PLATFORM_AUTHENTICATION`, `NONE`. <br> <ul><li>Use `CUSTOMER_AUTHENTICATION` if Platform customers log into your system via a username and password, a bearer token, or another method of authentication. For example, you would select this option if you also selected `authType: OAUTH2` or `authType:BEARER` in `customerAuthenticationConfigurations`. </li><li> Use `PLATFORM_AUTHENTICATION` if there is a global authentication system between Adobe and your destination and the [!DNL Platform] customer does not need to provide any authentication credentials to connect to your destination. In this case, you must create a credentials object using the [Credentials](/help/credentials-configuration.md) configuration. </li><li>Use `NONE` if no authentication is required to send data to your destination platform. </li></ul> |
|`destinationServerId` | String | This field is not required in the beta phase of Destination SDK. |
|`inputSchemaId` | String | This field is not required in the beta phase of Destination SDK. |

<!--

commenting out the `backfillHistoricalProfileData` parameter, which will only be used after an April release

|`backfillHistoricalProfileData` | Boolean | Controls whether historical profile data is exported when segments are activated to the destination. <br> <ul><li> `true`: [!DNL Platform] sends the historical user profiles that qualified for the segment before the segment is activated. </li><li> `false`: [!DNL Platform] only includes user profiles that qualify for the segment after the segment is activated. </li></ul> |

-->
