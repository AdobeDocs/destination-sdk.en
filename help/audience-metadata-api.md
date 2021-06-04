---
description: This page describes all the API operations that you can perform using the `/authoring/v1/audience-templates` API endpoint.
title: Audience metadata endpoint API operations
---
# Audience metadata endpoint API operations

**API endpoint**: `platform.adobe.io/data/core/activation/authoring/v1/audience-templates`

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change. 

This page lists and describes all the API operations that you can perform using the `/authoring/v1/audience-templates` API endpoint. For a description of when to use this endpoint, read [audience metadata management](/help/audience-metadata-management.md). 

## Getting started with audience templates API operations

Before continuing, please review the [getting started guide](./getting-started.md) for important information that you need to know in order to successfully make calls to the API, including how to obtain required headers and how to get allow listed.

## Create a new audience template {#create}

You can create a new audience template by making a POST request to the `/authoring/v1/audience-templates` endpoint.

**API format**


```http
POST /authoring/v1/audience-templates
```

**Request**

The following request creates a new audience metadata template, configured by the parameters provided in the payload. The payload below includes all parameters accepted by the `/authoring/v1/audience-templates` endpoint. Note that you do not have to add all parameters on the call and that the template is customizable, according to your API requirements.

```shell
curl -X POST https://platform.adobe.io/data/core/activation/authoring/v1/audience-templates \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'Content-Type: application/json' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
  "audience": {
    "context": "string",
    "name": "string",
    "description": "string",
    "sid": "string",
    "account": "string",
    "accountType": "string",
    "externalAudienceId": "string",
    "imsOrgId": "string",
    "metadata": {
      "partnerDefinedField": "string"
    }
  },
  "credential": {
    "clientId": "string",
    "clientSecret": "string",
    "token": "string",
    "tokenSecret": "string",
    "refreshToken": "string",
    "developerToken": "string",
    "clientCustomerId": "string",
    "authType": "OAUTH2",
    "metadata": {
      "partnerDefinedField": "string"
    }
  },
  "metadataTemplate": {
    "name": "string",
    "host": "string",
    "create": {
      "uri": "string",
      "method": "string",
      "headers": {
        "partnerDefinedField": "string"
      },
      "params": {
        "partnerDefinedField": "string"
      },
      "body": {},
      "schemaMap": {
        "partnerDefinedField": "string"
      },
      "errorSchemaMap": {
        "partnerDefinedField": "string"
      }
    },
    "update": {
      "uri": "string",
      "method": "string",
      "headers": {
        "partnerDefinedField": "string"
      },
      "params": {
        "partnerDefinedField": "string"
      },
      "body": {},
      "schemaMap": {
        "partnerDefinedField": "string"
      },
      "errorSchemaMap": {
        "partnerDefinedField": "string"
      }
    },
    "delete": {
      "uri": "string",
      "method": "string",
      "headers": {
        "partnerDefinedField": "string",
      },
      "params": {
        "partnerDefinedField": "string",
      },
      "body": {},
      "schemaMap": {
        "partnerDefinedField": "string",
      },
      "errorSchemaMap": {
        "partnerDefinedField": "string",
      }
    },
    "validate": {
      "uri": "string",
      "method": "string",
      "headers": {
        "partnerDefinedField": "string",
      },
      "params": {
        "partnerDefinedField": "string",
      },
      "body": {},
      "schemaMap": {
        "partnerDefinedField": "string",
      },
      "errorSchemaMap": {
        "partnerDefinedField": "string",
      }
    }
  },
  "validations": [
    {
      "field": "string",
      "regex": "string"
    }
  ]
}'
```

| Property | Type | Description |
| -------- | ----------- | ----------- |
| `audience.context` | String | Use "context": "Generic" if your API satisfies the requirements in the [generic and extensible](/help/audience-metadata-management.md#generic-and-extensible) section. |
| `audience.description` | String | Use `"{{segment.description}}"` to pass the description of the Experience Platform segment to your API endpoint. |
| `audience.name` | String | Use `"{{segment.name}}"` to pass the name of the Experience Platform segment to your API endpoint. |
| `audience.sid` | String | Use `"{{segment.sid}}"` to pass the ID of the Experience Platform segment to your API endpoint. |
| `audience.account` | String | Use `"{{audience.account}}"` to pass the user's account ID to your API endpoint.|
| `audience.accountType` | String | If your destination supports several types of customer accounts, you can specify that here. |
| `audience.externalAudienceId` | String | The segment or audience alias in your destination. Use `{{segment.alias}}`. |
| `audience.imsOrgId` | String | The customer's Organization ID. Use `{{userContext.imsOrgId}}`.  |
| `audience.metadata.partnerDefinedField` | String | You can define any number of additional fields, specific to your destination. <br> For example, based on their [Marketing API documentation](https://developers.facebook.com/docs/marketing-api/audiences/guides/custom-audiences/), Facebook would add the field `"customer_file_source": "{{segment.metadata.customer_file_source}}"` to their destination configuration. |
| `credential.clientId` | String |  |
| `credential.clientSecret` | String | |
| `credential.token` | String |  |
| `credential.tokenSecret` | String | |
| `credential.refreshToken` | String | |
| `credential.developerToken` | String | |
| `credential.clientCustomerId` | String | |
| `credential.authType` | String | Use `OAUTH1`, `OAUTH2`, or `OAUTH2_REFRESH`, depending on your destination's authentication method. |
| `credential.metadata.partnerDefinedField` | String | |
| `metadataTemplate.name` | String | The name of the audience metadata template for your destination. This name will appear in any partner-specific error message in the Experience Platform user interface, followed by the error message parsed from `metadataTemplate.create.errorSchemaMap`. |
| `metadataTemplate.host` | String | The URL of the API on your side. Two industry examples are: `https://ads-api.twitter.com/` and `https://graph.facebook.com/v10.0`. | 
| `metadataTemplate.create.uri` | String | The endpoint of your API, which is used for creating audiences/segments in your platform. For example: `/v2/audiences/{{audience.id}}` | 
| `metadataTemplate.create.method` | String | The method used on your endpoint to programmatically create the segment/audience in your destination. For example: `POST`, `PUT` | 
| `metadataTemplate.create.headers.partnerDefinedField` | String | Specifies any HTTP headers that should be added to the call to your API. For example, `"Content-Type": "application/x-www-form-urlencoded"` |
| `metadataTemplate.create.params.partnerDefinedField` | String | Specifies any query parameters that should be appended to the call to your API. |
| `metadataTemplate.create.body` | String | Specifies the content of the message body that should be sent to your API. |
| `metadataTemplate.create.schemaMap.partnerDefinedField` | String | Returns the audience ID from your destination.  |
| `metadataTemplate.create.errorSchemaMap.partnerDefinedField` | String | Parses any error messages returned on API call responses from your destination. These error messages will be surfaced to users in the Experience Platform user interface. |
| `metadataTemplate.update.uri` | String | The endpoint of your API, which is used for updating audiences/segments in your platform. For example: `/v2/audiences/{{audience.id}}` | 
| `metadataTemplate.update.method` | String | The method used on your endpoint to programmatically update the segment/audience in your destination. For example: `POST`, `PUT` | 
| `metadataTemplate.update.headers.partnerDefinedField` | String | Specifies any HTTP headers that should be added to the call to your API. For example, `"Content-Type": "application/x-www-form-urlencoded"`.  |
| `metadataTemplate.update.params.partnerDefinedField` | String | Specifies any query parameters that should be appended to the call to your API. | 
| `metadataTemplate.update.body` | String | Specifies the content of the message body that should be sent to your API. | 
| `metadataTemplate.update.schemaMap.partnerDefinedField` | String | *Optional for update operations.* Returns the audience ID from your destination. | 
| `metadataTemplate.update.errorSchemaMap.partnerDefinedField` | String | Parses any error messages returned on API call responses from your destination. These error messages will be surfaced to users in the Experience Platform user interface. | 
| `metadataTemplate.delete.uri` | String | The endpoint of your API, which is used for removing audiences/segments in your platform. For example: `/v2/audiences/{{audience.id}}` | 
| `metadataTemplate.delete.method` | String | The method used on your endpoint to programmatically delete the segment/audience in your destination. For example: `DELETE`. | 
| `metadataTemplate.delete.headers.partnerDefinedField` | String | Specifies any HTTP headers that should be added to the call to your API. For example, `"Content-Type": "application/x-www-form-urlencoded"` or `"Authorization": "Bearer {{credential.token}}"`. |
| `metadataTemplate.delete.params.partnerDefinedField` | String | Specifies any query parameters that should be appended to the call to your API. |
| `metadataTemplate.delete.body` | String | Specifies the content of the message body that should be sent to your API. | 
| `metadataTemplate.delete.schemaMap.partnerDefinedField` | String | *Optional for delete operations.* Returns the audience ID that was removed from your destination. |
| `metadataTemplate.delete.errorSchemaMap.partnerDefinedField` | String | Parses any error messages returned on API call responses from your destination. These error messages will be surfaced to users in the Experience Platform user interface. |
| `metadataTemplate.validate.uri` | String | The endpoint of your API, which is used to validate audiences/segments in your platform. For example: `/v2/audiences/{{audience.id}}` | 
| `metadataTemplate.validate.method` | String | The method used on your endpoint to programmatically validate the segment/audience in your destination. For example: `GET` | 
| `metadataTemplate.validate.headers.partnerDefinedField` | String | Specifies any HTTP headers that should be added to the call to your API. For example, `"Content-Type": "application/x-www-form-urlencoded"` or `"Authorization": "Bearer {{credential.token}}"` |
| `metadataTemplate.validate.params.partnerDefinedField` | String | Specifies any query parameters that should be appended to the call to your API. |
| `metadataTemplate.validate.body` | String | Specifies the content of the message body that should be sent to your API. | 
| `metadataTemplate.validate.schemaMap.partnerDefinedField` | String | *Optional for validation operations.* Returns an ID for your validation operation. |
| `metadataTemplate.validate.errorSchemaMap.partnerDefinedField` | String | Parses any error messages returned on API call responses from your destination. |
| `validations.field` | String | Indicates if validations should be run for any fields before API calls are made to your destination. For example, you can use `{{validations.accountId}}` to validate the user's account ID. |
| `validations.regex` | String | Indicates how the field should be structured in order for the validation to pass.  |

{style="table-layout:auto"}

**Response**

A successful response returns HTTP status 200 with details of your newly created audience template.

## Update audience template {#update}

You can update an existing audience template by making a PUT request to the `/authoring/v1/audience-templates` endpoint and providing the instance ID of the audience template you want to update. In the body of the call, provide the updated template.

**API format**


```http
PUT /authoring/v1/audience-templates/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the audience metadata template that you want to update. |

**Request**

The following request updates an existing audience metadata template, configured by the parameters provided in the payload.

```shell

curl -X PUT https://platform.adobe.io/data/core/activation/authoring/v1/audience-templates/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}' \
 -H 'x-sandbox-id: {SANDBOX_ID}' \
 -d '
{
    "audience": {
        "context": "Generic",
        "name": "{{segment.name}}",
        "description": "{{segment.description}}",
        "account": "{{targetConnection.params.accountId}}",
        "externalAudienceId": "{{segment.alias}}",
        "metadata": {
            "customer_file_source": "{{segment.metadata.customer_file_source}}"
        },
        "imsOrgId": "{{userContext.imsOrgId}}"
    },
    "credential": {
        "token": "{{baseConnection.auth.params.accessToken}}",
        "authType": "OAUTH2"
    },
    "metadataTemplate": {
        "create": {
            "uri": "/{{audience.account}}/segments?fields=name,description,account_id&subtype=CUSTOM",
            "method": "POST",
            "headers": {
                "Content-Type": "application/x-www-form-urlencoded"
            },
            "params": {
                "name": "{{audience.name}}",
                "description": "{{audience.description}}",
                "access_token": "{{credential.token}}",
                "customer_file_source": "{{audience.metadata.customer_file_source}}"
            },
            "schemaMap": {
                "externalAudienceId": "{{response.id}}"
            },
            "errorSchemaMap": {
                "message": "{{error.message}}"
            }
        },
        "update": {
            "uri": "/{{audience.externalAudienceId}}",
            "method": "POST",
            "headers": {
                "Content-Type": "application/x-www-form-urlencoded"
            },
            "params": {
                "name": "{{audience.name}}",
                "fields": "name,description,account_id",
                "description": "{{audience.description}}",
                "access_token": "{{credential.token}}",
                "customAudienceId": "{{audience.externalAudienceId}}",
                "customer_file_source": "{{audience.metadata.customer_file_source}}"
            },
            "schemaMap": {
                "externalAudienceId": "{{response.id}}"
            },
            "errorSchemaMap": {
                "message": "{{error.message}}"
            }
        },
        "delete": {
            "uri": "/{{audience.externalAudienceId}}",
            "method": "DELETE",
            "headers": {
                "Content-Type": "application/x-www-form-urlencoded"
            },
            "params": {
                "fields": "name,description,account_id",
                "access_token": "{{credential.token}}",
                "customAudienceId": "{{audience.externalAudienceId}}"
            },
            "schemaMap": {
                "externalAudienceId": "{{response.id}}"
            },
            "errorSchemaMap": {
                "message": "{{error.message}}"
            }
        },
        "validate": {
            "uri": "/me/permissions?access_token={{credential.token}}",
            "method": "GET",
            "headers": {
                "Content-Type": "application/x-www-form-urlencoded"
            },
            "schemaMap": {
                "Id": "{{response.id}}"
            },
            "errorSchemaMap": {
                "message": "{{error.message}}"
            }
        },
        "host": "https://api.moviestar.com/v1.0",
        "name": "Moviestar"
    },
    "validations": [
        {
            "field": "{{audience.account}}",
            "regex": "^moviestar_id_[0-9]+$"
        }
    ]
}

```

## Retrieve a list of audience templates {#retrieve-list}

You can retrieve a list of all audience templates for your IMS Organization by making a GET request to the `/authoring/v1/audience-templates` endpoint.

**API format**


```http
GET /authoring/v1/audience-templates
```

**Request**

The following request will retrieve the list of audience templates that you have access to, based on IMS Organization and sandbox configuration.

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/audience-templates \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'  
```

**Response**

The following response returns HTTP status 200 with a list of audience metadata templates that you have access to, based on the IMS Organization ID, sandbox name, and sandbox ID that you used. One `instanceId` corresponds to the template for one destination. The response is truncated for brevity. 

```json

[
    {
        "instanceId": "bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9",
        "createdDate": "2021-04-13T19:33:09.733226Z",
        "lastModifiedDate": "2021-04-13T19:33:09.733226Z",
        "audience": {
            "context": "Generic",
            "name": "{{segment.name}}",
            "description": "{{segment.description}}",
            "account": "urn:li:sponsoredAccount:{{targetConnection.params.accountId}}",
            "externalAudienceId": "{{segment.alias}}",
            "imsOrgId": "{{userContext.imsOrgId}}"
        },
        "credential": {
            "token": "{{baseConnection.auth.params.accessToken}}",
            "authType": "OAUTH2"
        },
        "metadataTemplate": {
            "create": {
                "uri": "/v1/segments",
                "method": "POST",
                "headers": {
                    "Content-Type": "application/json",
                    "Authorization": "Bearer {{credential.token}}"
                },
                "body": {
                    "name": "{{audience.name}}",
                    "type": "USER",
                    "account": "{{audience.account}}",
                    "accessPolicy": "PRIVATE",
                    "destinations": [
                        {
                            "destination": "Moviestar"
                        }
                    ],
                    "sourcePlatform": "ADOBE"
                },
                "schemaMap": {
                    "externalAudienceId": "{{headers.x-moviestar-id}}"
                },
                "errorSchemaMap": {
                    "message": "message"
                }
            },
            "update": {
                "uri": "/v1/segments/{{audience.id}}",
                "method": "POST",
                "headers": {
                    "Content-Type": "application/json",
                    "Authorization": "Bearer {{credential.token}}"
                },
                "body": {
                    "patch": {
                        "$set": {
                            "name": "{{audience.name}}"
                        }
                    }
                },
                "errorSchemaMap": {
                    "message": "message"
                }
            },
            "delete": {
                "uri": "/v1/segments/{{audience.externalAudienceId}}",
                "method": "DELETE",
                "headers": {
                    "Content-Type": "application/json",
                    "Authorization": "Bearer {{credential.token}}"
                },
                "body": {
                    "patch": {
                        "$set": {
                            "name": "{{audience.name}}"
                        }
                    }
                },
                "errorSchemaMap": {
                    "message": "message"
                }
            },
            "host": "https://api.moveistar.com",
            "name": "Moviestar audience template"
        }
    }
]
    
```



## Retrieve a specific audience template {#get}

You can retrieve detailed information about a specific audience template by making a GET request to the `/authoring/v1/audience-templates` endpoint and providing the instance ID of the audience template you want to retrieve.

**API format**


```http
GET /authoring/v1/audience-templates/{INSTANCE_ID}
```

| Parameter | Description |
| -------- | ----------- |
| `{INSTANCE_ID}` | The ID of the audience metadata template you want to retrieve. |

**Request**

```shell
curl -X GET https://platform.adobe.io/data/core/activation/authoring/v1/audience-templates/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
 -H 'Authorization: Bearer {ACCESS_TOKEN}' \
 -H 'x-gw-ims-org-id: {IMS_ORG}' \
 -H 'x-api-key: {API_KEY}' \
 -H 'x-sandbox-name: {SANDBOX_NAME}'
 -H 'x-sandbox-id: {SANDBOX_ID}'
```

**Response**

A successful response returns HTTP status 200 with detailed information about the specified audience template.

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


## Delete a specific audience template {#delete}

You can delete the specified audience template by making a DELETE request to the `/authoring/v1/audience-templates` endpoint and providing the ID of the audience template you wish to delete in the request path.

**API format**

```http
DELETE /authoring/v1/audience-templates/{INSTANCE_ID}
```

| Parameter | Description |
| --------- | ----------- |
| `{INSTANCE_ID}` | The `id` of the audience template you want to delete. |

**Request**

```shell
curl -X DELETE https://platform.adobe.io/data/core/activation/authoring/v1/audience-templates/bd4ec8f0-e98f-4b6a-8064-dd7adbfffec9 \
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

After reading this guide, you now know when to use audience metadata templates and how to configure an audience metadata template using the `/authoring/v1/audience-templates` API endpoint. Read [how to use Destination SDK to configure your destination](/help/configure-destination-instructions.md) to understand where this step fits into the process of configuring your destination.