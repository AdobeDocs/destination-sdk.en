---
description: This page lists and describes all the API operations that you can perform using the `/authoring/v1/destinations` API endpoint.
title: Destinations API endpoint operations
exl-id: 1a3bd1af-c66c-4787-96ce-57e17393b4e5
---
# Destinations endpoint API operations {#destination-configuration}

**API endpoint**: `platform.adobe.io/data/core/activation/authoring/v1/destinations` 

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

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
  ]
  "backfillHistoricalProfileData": true
}
```

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Indicates the title of your destination in the Experience Platform catalog |
|`description` | String | Provide a description that Adobe will use in the Experience Platform destinations catalog for your destination card. Aim for no more than 4-5 sentences. |
|`releaseNotes` | String | This field is not necessary in the beta phase of Destination SDK. |
|`status` | String | Indicates the lifecycle status of the destination card. Accepted values are `TEST`, `PUBLISHED`, and `DELETED`. Use `TEST` when you first configure your destination. |
|`customerAuthenticationConfigurations` | String | Indicates the configuration used to authenticate Experience Platform customers to your server. See `authType` below for accepted values. |
|`customerAuthenticationConfigurations.authType` | String | Accepted values are `S3, SFTP_WITH_SSH_KEY, SFTP_WITH_PASSWORD, OAUTH1, OAUTH2, BASIC, BEARER`.  |
|`customerDataFields.name` | String | Provide a name for the custom field you are introducing. |
|`customerDataFields.type` | String | Indicates what type of custom field you are introducing. Accepted values are `string`, `object`, `integer` |
|`customerDataFields.title` | String | Indicates the name of the field, as it is seen by customers in the Experience Platform user interface |
|`customerDataFields.description` | String | Provide a description for the custom field. |
|`customerDataFields.isRequired` | Boolean | Indicates if this field is required in the destination setup workflow. |
|`customerDataFields.enum` | String | Renders the custom field as a dropdown menu and lists the options available to the user. |
|`customerDataFields.pattern` | String | Enforces a pattern for the custom field, if needed. Use regular expressions to enforce a pattern. For example, if your customer IDs don't include numbers or underscores, enter `^[A-Za-z]+$` in this field. |
|`uiAttributes.documentationLink` | String | Refers to the documentation page in the [Destinations Catalog](https://experienceleague.adobe.com/docs/experience-platform/destinations/catalog/overview.html?lang=en#catalog) for your destination. Use `http://www.adobe.com/go/destinations-YOURDESTINATION-en`, where `YOURDESTINATION` is the name of your destination. For a destination called Moviestar, you would use `http://www.adobe.com/go/destinations-moviestar-en` |
|`uiAttributes.category` | String | Refers to the category assigned to your destination in Adobe Experience Platform. For more information, read [Destination Categories](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/destination-types.html?lang=en#destination-categories). Use one of the following values: `adobeSolutions, advertising, analytics, cdp, cloudStorage, crm, customerSuccess, database, dmp, ecommerce, email, emailMarketing, enrichment, livechat, marketingAutomation, mobile, personalization, protocols, social, streaming, subscriptions, surveys, tagManagers, voc, warehouses, payments` |
|`uiAttributes.iconUrl` | String | Provide the logo that Adobe will display in the Adobe Experience Platform destinations catalog for your destination card. |
|`uiAttributes.connectionType` | String | In the beta release phase of Destination SDK, `Server-to-server` is the only available option. |
|`uiAttributes.frequency` | String | In the beta release phase of Destination SDK, `Streaming` is the only available option. |
|`identityNamespaces.externalId.acceptsAttributes` | Boolean | Indicates if your destination accepts standard profile attributes. Usually, these attributes are highlighted in our partners' documentation. |
|`identityNamespaces.externalId.acceptsCustomNamespaces` | Boolean | Indicates if customers can set up custom namespaces in your destination. |
|`identityNamespaces.externalId.allowedAttributesTransformation` | String | _Not shown in example configuration_. Used, for example, when the [!DNL Platform] customer has plain email addresses as an attribute and your platform only accepts hashed emails. This is where you would provide the transformation that needs to be applied (for example, transform the email to lowercase, then hash).   |
|`identityNamespaces.externalId.acceptedGlobalNamespaces` | - | _Not shown in example configuration_. Used for cases when your platform accepts [standard identity namespaces](https://experienceleague.adobe.com/docs/experience-platform/identity/namespaces.html?lang=en#standard-namespaces) (for example, IDFA), so you can restrict Platform users to only selecting these identity namespaces. |
|`destinationDelivery.authenticationRule` | String | Indicates how [!DNL Platform] customers connect to your destination. Accepted values are `CUSTOMER_AUTHENTICATION`, `PLATFORM_AUTHENTICATION`, `NONE`. <br> <ul><li>Use `CUSTOMER_AUTHENTICATION` if Platform customers log into your system via a username and password, a bearer token, or another method of authentication. For example, you would select this option if you also selected `authType: OAUTH2` or `authType:BEARER` in `customerAuthenticationConfigurations`. </li><li> Use `PLATFORM_AUTHENTICATION` if there is a global authentication system between Adobe and your destination and the [!DNL Platform] customer does not need to provide any authentication credentials to connect to your destination. In this case, you must create a credentials object using the [Credentials](/help/credentials-configuration.md) configuration. </li><li>Use `NONE` if no authentication is required to send data to your destination platform. </li></ul> |
|`destinationDelivery.destinationServerId` | String | The `instanceId` of the [destination server template](/help/destination-server-api.md) used for this destination. |
|`backfillHistoricalProfileData` | Boolean | Controls whether historical profile data is exported when segments are activated to the destination. <br> <ul><li> `true`: [!DNL Platform] sends the historical user profiles that qualified for the segment before the segment is activated. </li><li> `false`: [!DNL Platform] only includes user profiles that qualify for the segment after the segment is activated. </li></ul> |
|`segmentMappingConfig.mapUserInput` | Boolean | Controls whether the segment mapping id in the destination activation workflow is input by user. |
|`segmentMappingConfig.mapExperiencePlatformSegmentId` | Boolean | Controls whether the segment mapping id in the destination activation workflow is the Experience Platform segment ID. |
|`segmentMappingConfig.mapExperiencePlatformSegmentName` | Boolean | Controls whether the segment mapping id in the destination activation workflow is the Experience Platform segment name. |
|`segmentMappingConfig.audienceTemplateId` | Boolean | The `instanceId` of the [audience metadata template](/help/audience-metadata-api.md) used for this destination. |

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
  "instanceId": "b0780cb5-2bb7-4409-bf2c-c625ca818588",
  "createdDate": "2020-10-28T06:14:09.784471Z",
  "lastModifiedDate": "2021-06-28T06:14:09.784471Z",
  "imsOrg": "AC3428435BF324E90A49402A@AdobeOrg",
  "sandboxName": "prod",
  "sandboxId": "r5g6660-c5da-11e9-93d4-6d5fc3a66a8e",
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
  "inputSchemaId": "cc8621770a9243b98aba4df79898b1ed",
  "destConfigId": "410631b8-f6b3-4b7c-82da-7998aa3f327c",
  "backfillHistoricalProfileData": true
}
]
    
```

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Indicates the title of your destination in the Experience Platform catalog |
|`description` | String | Provide a description that Adobe will use in the Experience Platform destinations catalog for your destination card. Aim for no more than 4-5 sentences. |
|`releaseNotes` | String | This field is not necessary in the beta phase of Destination SDK. |
|`status` | String | Indicates the lifecycle status of the destination card. Accepted values are `TEST`, `PUBLISHED`, and `DELETED`. Use `TEST` when you first configure your destination. |
|`customerAuthenticationConfigurations` | String | Indicates the configuration used to authenticate Experience Platform customers to your server. See `authType` below for accepted values. |
|`customerAuthenticationConfigurations.authType` | String | Accepted values are `S3, SFTP_WITH_SSH_KEY, SFTP_WITH_PASSWORD, OAUTH1, OAUTH2, BASIC, BEARER`.  |
|`customerDataFields.name` | String | Provide a name for the custom field you are introducing. |
|`customerDataFields.type` | String | Indicates what type of custom field you are introducing. Accepted values are `string`, `object`, `integer` |
|`customerDataFields.title` | String | Indicates the name of the field, as it is seen by customers in the Experience Platform user interface |
|`customerDataFields.description` | String | Provide a description for the custom field. |
|`customerDataFields.isRequired` | Boolean | Indicates if this field is required in the destination setup workflow. |
|`customerDataFields.enum` | String | Renders the custom field as a dropdown menu and lists the options available to the user. |
|`customerDataFields.pattern` | String | Enforces a pattern for the custom field, if needed. Use regular expressions to enforce a pattern. For example, if your customer IDs don't include numbers or underscores, enter `^[A-Za-z]+$` in this field. |
|`uiAttributes.documentationLink` | String | Refers to the documentation page in the [Destinations Catalog](https://experienceleague.adobe.com/docs/experience-platform/destinations/catalog/overview.html?lang=en#catalog) for your destination. Use `http://www.adobe.com/go/destinations-YOURDESTINATION-en`, where `YOURDESTINATION` is the name of your destination. For a destination called Moviestar, you would use `http://www.adobe.com/go/destinations-moviestar-en` |
|`uiAttributes.category` | String | Refers to the category assigned to your destination in Adobe Experience Platform. For more information, read [Destination Categories](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/destination-types.html?lang=en#destination-categories). Use one of the following values: `adobeSolutions, advertising, analytics, cdp, cloudStorage, crm, customerSuccess, database, dmp, ecommerce, email, emailMarketing, enrichment, livechat, marketingAutomation, mobile, personalization, protocols, social, streaming, subscriptions, surveys, tagManagers, voc, warehouses, payments` |
|`uiAttributes.iconUrl` | String | Provide the logo that Adobe will display in the Adobe Experience Platform destinations catalog for your destination card. |
|`uiAttributes.connectionType` | String | In the beta release phase of Destination SDK, `Server-to-server` is the only available option. |
|`uiAttributes.frequency` | String | In the beta release phase of Destination SDK, `Streaming` is the only available option. |
|`identityNamespaces.externalId.acceptsAttributes` | Boolean | Indicates if your destination accepts standard profile attributes. Usually, these attributes are highlighted in our partners' documentation. |
|`identityNamespaces.externalId.acceptsCustomNamespaces` | Boolean | Indicates if customers can set up custom namespaces in your destination. |
|`identityNamespaces.externalId.allowedAttributesTransformation` | String | _Not shown in example configuration_. Used, for example, when the [!DNL Platform] customer has plain email addresses as an attribute and your platform only accepts hashed emails. This is where you would provide the transformation that needs to be applied (for example, transform the email to lowercase, then hash).   |
|`identityNamespaces.externalId.acceptedGlobalNamespaces` | - | _Not shown in example configuration_. Used for cases when your platform accepts [standard identity namespaces](https://experienceleague.adobe.com/docs/experience-platform/identity/namespaces.html?lang=en#standard-namespaces) (for example, IDFA), so you can restrict Platform users to only selecting these identity namespaces. |
|`destinationDelivery.authenticationRule` | String | Indicates how [!DNL Platform] customers connect to your destination. Accepted values are `CUSTOMER_AUTHENTICATION`, `PLATFORM_AUTHENTICATION`, `NONE`. <br> <ul><li>Use `CUSTOMER_AUTHENTICATION` if Platform customers log into your system via a username and password, a bearer token, or another method of authentication. For example, you would select this option if you also selected `authType: OAUTH2` or `authType:BEARER` in `customerAuthenticationConfigurations`. </li><li> Use `PLATFORM_AUTHENTICATION` if there is a global authentication system between Adobe and your destination and the [!DNL Platform] customer does not need to provide any authentication credentials to connect to your destination. In this case, you must create a credentials object using the [Credentials](/help/credentials-configuration.md) configuration. </li><li>Use `NONE` if no authentication is required to send data to your destination platform. </li></ul> |
|`destinationDelivery.destinationServerId` | String | The `instanceId` of the [destination server template](/help/destination-server-api.md) used for this destination. |
|`inputSchemaId` | String | This field is automatically generated and does not require your input. |
|`destConfigId` | String | This field is automatically generated and does not require your input. |
|`backfillHistoricalProfileData` | Boolean | Controls whether historical profile data is exported when segments are activated to the destination. <br> <ul><li> `true`: [!DNL Platform] sends the historical user profiles that qualified for the segment before the segment is activated. </li><li> `false`: [!DNL Platform] only includes user profiles that qualify for the segment after the segment is activated. </li></ul> |
|`segmentMappingConfig.mapUserInput` | Boolean | Controls whether the segment mapping id in the destination activation workflow is input by user. |
|`segmentMappingConfig.mapExperiencePlatformSegmentId` | Boolean | Controls whether the segment mapping id in the destination activation workflow is the Experience Platform segment ID. |
|`segmentMappingConfig.mapExperiencePlatformSegmentName` | Boolean | Controls whether the segment mapping id in the destination activation workflow is the Experience Platform segment name. |
|`segmentMappingConfig.audienceTemplateId` | Boolean | The `instanceId` of the [audience metadata template](/help/audience-metadata-api.md) used for this destination. |


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

The following request updates an existing destination configuration, configured by the parameters provided in the payload. In the example call below, we are updating the configuration [created earlier](/help/destination-configuration-api.md#create) to now accept GAID, IDFA, and hashed email identifiers as identity namespaces.  

```shell

curl -X PUT https://platform.adobe.io/data/core/activation/authoring/v1/destinations/b0780cb5-2bb7-4409-bf2c-c625ca818588 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
  {
  "instanceId": "b0780cb5-2bb7-4409-bf2c-c625ca818588",
  "createdDate": "2020-10-28T06:14:09.784471Z",
  "lastModifiedDate": "2021-04-28T06:14:09.784471Z",
  "imsOrg": "AC3428435BF324E90A49402A@AdobeOrg",
  "sandboxName": "prod",
  "sandboxId": "r5g6660-c5da-11e9-93d4-6d5fc3a66a8e",
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
    },
            "gaid": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "acceptedGlobalNamespaces": {
                    "GAID": {}
                }
            },
            "idfa": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "acceptedGlobalNamespaces": {
                    "IDFA": {}
                }
            },
            "email_lc_sha256": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "transformation": "sha256(lower($))",
                "acceptedGlobalNamespaces": {
                    "Email": {
                        "requiredTransformation": "sha256(lower($))"
                    },
                    "Email_LC_SHA256": {}
                }
            }
  },
  "destinationDelivery": [
    {
      "authenticationRule": "CUSTOMER_AUTHENTICATION",
      "destinationServerId": "9c77000a-4559-40ae-9119-a04324a3ecd4"
    }
  ],
  "inputSchemaId": "cc8621770a9243b98aba4df79898b1ed",
  "backfillHistoricalProfileData": true
}

```

## Retrieve a specific destination configuration {#get}

You can retrieve detailed information about a specific destination configuration by making a GET request to the `/authoring/v1/destinations` endpoint and providing the instance ID of the destination configuration you want to retrieve.

**API format**


```http
GET /authoring/v1/destinations/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the destination configuration you want to retrieve. |

**Request**

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destinations/b0780cb5-2bb7-4409-bf2c-c625ca818588 \
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
  "instanceId": "b0780cb5-2bb7-4409-bf2c-c625ca818588",
  "createdDate": "2020-10-28T06:14:09.784471Z",
  "lastModifiedDate": "2021-06-04T06:14:09.784471Z",
  "imsOrg": "AC3428435BF324E90A49402A@AdobeOrg",
  "sandboxName": "prod",
  "sandboxId": "r5g6660-c5da-11e9-93d4-6d5fc3a66a8e",
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
    },
            "gaid": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "acceptedGlobalNamespaces": {
                    "GAID": {}
                }
            },
            "idfa": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "acceptedGlobalNamespaces": {
                    "IDFA": {}
                }
            },
            "email_lc_sha256": {
                "acceptsAttributes": true,
                "acceptsCustomNamespaces": true,
                "transformation": "sha256(lower($))",
                "acceptedGlobalNamespaces": {
                    "Email": {
                        "requiredTransformation": "sha256(lower($))"
                    },
                    "Email_LC_SHA256": {}
                }
            }
  },
  "destinationDelivery": [
    {
      "authenticationRule": "CUSTOMER_AUTHENTICATION",
      "destinationServerId": "9c77000a-4559-40ae-9119-a04324a3ecd4"
    }
  ],
  "inputSchemaId": "cc8621770a9243b98aba4df79898b1ed",
  "backfillHistoricalProfileData": true
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
curl -X DELETE https://platform.adobe.io/data/core/activation/authoring/v1/destinations/b0780cb5-2bb7-4409-bf2c-c625ca818588 \
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

## Next steps

After reading this document, you now know how to configure your destination using the `/authoring/v1/destinations` API endpoint. Read [how to use Destination SDK to configure your destination](/help/configure-destination-instructions.md) to understand where this step fits into the process of configuring your destination.
