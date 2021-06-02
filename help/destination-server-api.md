---
description: The server and template specs in Adobe Destination SDK can be configured via the common endpoint `/authoring/v1/destination-servers`.
title: Configuration options for server and template specs in Destination SDK
exl-id: 9bda8aa7-d7cf-4729-8890-6945da104719
---
# Destination server API reference

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.


**API endpoint**: `platform.adobe.io/data/core/activation/authoring/v1/destination-servers`

The server and template specs in Adobe Destination SDK can be configured via the common endpoint `/authoring/v1/destination-servers`. For a description of the functionality provided by this endpoint, read [server and template specs](/help/server-and-template-configuration.md). 

## Getting started with destination server API operations

Before continuing, please review the [getting started guide](./getting-started.md) for important information that you need to know in order to successfully make calls to the API, including how to obtain required headers and how to get allow listed.

## Create configuration for a destination server {#create}

You can create a new destination server configuration by making a POST request to the `/authoring/v1/destination-servers` endpoint.

**API format**


```http
POST /authoring/v1/destination-servers
```

**Request**

The following request creates a new destination server configuration, configured by the parameters provided in the payload. The payload below includes all parameters accepted by the `/authoring/v1/destination-servers` endpoint. Note that you do not have to add all parameters on the call and that the template is customizable, according to your API requirements.

```shell
curl -X POST https://platform.adobe.io/data/core/activation/authoring/v1/destination-servers \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'Content-Type: application/json' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
  "name": "Moviestar destination server",
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
|`name` | String | Represents a friendly name of your server, visible only to Adobe. This name is not visible to partners or customers. Example `Moviestar destination server`.  |
|`destinationServerType` | String | `URL_BASED` is the only available option in the beta release phase. |
|`templatingStrategy` | String | <ul><li>Use `PEBBLE_V1` if Adobe needs to transform the URL in the `value` field below. Use this option if you have an endpoint like: `https://api.moviestar.com/data/{{endpoint.region}}/items` </li><li> Use `NONE` if no transformation is needed on the Adobe side, for example if you have an endpoint like: `https://api.moviestar.com/data/items` </li></ul>  |
|`value` | String | Fill in the address of the API endpoint that Experience Platform should connect to. |
|`maxUsersPerRequest` | Integer | Adobe can aggregate multiple exported profiles in a single HTTP call. Specify the maximum number of profiles that your endpoint should receive in a single HTTP call. Note that this is a best effort aggregation. For example, if you specify the value 100, Adobe might send any number of profiles smaller than 100 on a call. <br> If your server does not accept multiple users per request, set this value to 1. |
|`splitUserById` | Boolean | Use this flag if the call to the destination should be split by identity. Set this flag to `true` if your server only accepts one identity per call, for a given namespace. |
|`httpMethod` | String | The method that Adobe will use in calls to your server. Options are `GET`, `PUT`, `POST`, `DELETE`, `PATCH`. |
|`templatingStrategy` | String | Use `PEBBLE_V1`. |
|`value` | String | This string is the character-escaped version that transforms Platform customers' data to the format your service expects. <br> For information how to write the template, read the [Using templating section](/help/message-format.md#using-templating). <br> For more information about character escaping, refer to the [RFC JSON standard, section seven](https://tools.ietf.org/html/rfc8259#section-7). <br> For an example of a simple transformation, refer to the [Profile Attributes](/help/message-format.md#attributes) transformation. |
|`contentType` | String | The content type that your server accepts. This value is most likely `application/json`. |

{style="table-layout:auto"}

**Response**

A successful response returns HTTP status 200 with details of your newly created destination server configuration.

## List destination server configurations {#retrieve-list}

You can retrieve a list of all destination server configurations for your IMS Organization by making a GET request to the `/authoring/v1/destination-servers` endpoint.

**API format**


```http
GET /authoring/v1/destination-servers
```

**Request**

The following request will retrieve the list of destination server configurations that you have access to, based on IMS Organization and sandbox configuration.

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destination-servers \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'  
```

**Response**

The following response returns HTTP status 200 with a list of destination server configurations that you have access to, based on the IMS Organization ID, sandbox name, and sandbox ID that you used. One `instanceId` corresponds to the template for one destination server. The response is truncated for brevity.

```json

[
       {
        "instanceId": "2307ec2b-4798-45a4-9239-5d0a2fb0ed67",
        "createdDate": "2020-11-17T06:49:24.331012Z",
        "lastModifiedDate": "2020-11-17T06:49:24.331012Z",
        "name": "Moviestar Destination Server",
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
        "name": "Test11 Destination Server",
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

## Update an existing destination server configuration {#update}

You can update an existing destination server configuration by making a PUT request to the `/authoring/v1/destination-servers` endpoint and providing the instance ID of the destination server configuration you want to update. In the body of the call, provide the updated destination server configuration.

**API format**


```http
PUT /authoring/v1/destination-servers/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the destination server configuration that you want to update. |

**Request**

The following request updates an existing destination server configuration, configured by the parameters provided in the payload.

```shell

curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destination-servers/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
  "name": "Moviestar destination server",
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





## Retrieve a specific destination server configuration {#get}

You can retrieve detailed information about a specific destination server configuration by making a GET request to the `/authoring/v1/destination-servers` endpoint and providing the instance ID of the destination server configuration you want to update.

**API format**


```http
GET /authoring/v1/destination-servers/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the destination server configuration you want to retrieve. |

**Request**

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/destination-servers/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'
```

**Response**

A successful response returns HTTP status 200 with detailed information about the specified destination server configuration.

```json
{
    "instanceId": "bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9",
    "createdDate": "2021-04-15T20:40:39.500796Z",
    "lastModifiedDate": "2021-04-15T20:40:39.500796Z",
    "audience": {
        "context": "Generic",
        "name": "{{segment.name}}",
        "account": "{{targetConnection.params.accountId}}",
        "externalAudienceId": "{{segment.alias}}"
    },
    "credential": {
        "clientId": "{{authentication.oauth1Authentication.apiKey}}",
        "clientSecret": "{{authentication.oauth1Authentication.apiSecret}}",
        "token": "{{baseConnection.auth.params.accessToken}}",
        "tokenSecret": "{{baseConnection.auth.params.tokenSecret}}",
        "authType": "OAUTH1"
    },
    "metadataTemplate": {
        "create": {
            "uri": "/accounts/{{audience.account}}/tailored_audiences?name={{audience.name}}",
            "method": "POST",
            "headers": {
                "Content-Type": "application/json"
            },
            "schemaMap": {
                "externalAudienceId": "{{response.data.id}}"
            },
            "errorSchemaMap": {
                "message": "errors[0].message"
            }
        },
        "update": {
            "uri": "/accounts/{{audience.account}}/tailored_audiences/{{audience.externalAudienceId}}?name={{audience.name}}",
            "method": "PUT",
            "headers": {
                "Content-Type": "application/json"
            },
            "errorSchemaMap": {
                "message": "errors[0].message"
            }
        },
        "delete": {
            "uri": "/accounts/{{audience.account}}/tailored_audiences/{{audience.externalAudienceId}}",
            "method": "DELETE",
            "headers": {
                "Content-Type": "application/json"
            },
            "errorSchemaMap": {
                "message": "errors[0].message"
            }
        },
        "host": "https://ads-api.moviestar.com/8"
    }
}
```


## Delete a specific destination server configuration {#delete}

You can delete the specified destination server configuration by making a DELETE request to the `/authoring/v1/destination-servers` endpoint and providing the ID of the destination server configuration you wish to delete in the request path.

**API format**

```http
DELETE /authoring/v1/destination-servers/{INSTANCE_ID}
```

| Parameter | Description |
| --------- | ----------- |
| `{INSTANCE_ID}` | The `id` of the destination server configuration you want to delete. |

**Request**

```shell
curl -X DELETE https://platform.adobe.io/data/core/activation/authoring/v1/destination-servers/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
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