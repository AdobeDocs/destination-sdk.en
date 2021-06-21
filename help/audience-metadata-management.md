---
description: Use audience metadata templates to programmatically create, update, or delete audiences in your destination. Adobe provides an extensible audience metadata template, which you can configure based on the specifications of your marketing API. After you define, test, and submit the template, it will be used by Adobe to structure the API calls to your destination.
title: Audience metadata management
exl-id: 63ed9a03-eb4d-46c6-85e9-6c1d84acdbad
---
# Audience metadata management {#audience-metadata-management}

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

Use audience metadata templates to programmatically create, update, or delete audiences in your destination. Adobe provides an extensible audience metadata template, which you can configure based on the specifications of your marketing API. After you define, test, and submit the template, it will be used by Adobe to structure the API calls to your destination.

The functionality described in this document can be configured using the `/authoring/v1/audience-templates` API endpoint. Read [Audience metadata endpoint API operations](/help/audience-metadata-api.md) for a complete list of operations you can perform on the endpoint.

## When to use the audience metadata management endpoint {#when-to-use}

Depending on your API configuration, you may or may not need to use the audience metadata management endpoint as you configure your destination in Experience Platform. Use the decision tree diagram below to understand when to use the audience metadata endpoint and how to configure an audience metadata template for your destination.

![Whiteboard diagram](/help/assets/audience-metadata-decision-tree.png)

## Use cases supported by audience metadata management {#use-cases}

With audience metadata support in Destination SDK, when you configure your Experience Platform destination, you can give Platform users one of several options when they map and activate segments to your destination:

### Use case 1 - You have a 3rd party API and users don't need to input mapping IDs

If you have an API endpoint to create/update/delete segments or audiences, you can use audience metadata templates to configure Destination SDK to match the specs of your segment create/update/delete endpoint. Experience Platform can programmatically create/update/delete segments and synchronize metadata back to Experience Platform.

When activating segments to your destination in the Experience Platform user interface (UI), users don't need to manually fill in the segment mapping ID field in the activation workflow.

### Use case 2 - Users need to create a segment in your destination first and are required to manually input mapping ID

If segments and other metadata need to be created by partners or users manually in your destination, then users must manually fill in the segment mapping ID field in the activation workflow to sync the segment metadata between your destination and Experience Platform.

![Input mapping ID](/help/assets/input-mapping-id.png)

### Use case 3 - Your destination accepts the Experience Platform segment ID, users don't need to manually input mapping ID  

If your destination system accepts the Experience Platform segment ID, you can configure this in your audience metadata template. Users do not have to populate a segment mapping ID when activating a segment.

## Generic and extensible audience template {#generic-and-extensible}

To support the use cases listed above, Adobe provides you with a generic template that can be customized to adjust to your API specifications.

Select the `Generic` option when you [create a new audience template](/help/audience-metadata-api.md#create) if your API supports:

* The HTTP methods: POST, GET, PUT, DELETE, PATCH
* The authentication types: OAuth 1, OAuth 2 with refresh token, OAuth 2 with bearer token
* The functions: create an audience, update an audience, get an audience, delete an audience, validate credentials 


## Template example

This section includes an example of a generic audience template, for your reference, along with descriptions of the main sections of the template.

```json

        "instanceId": "a54b7a97-1a3b-40cf-8ae5-4dd635cd9a20",
        "createdDate": "2021-04-13T19:31:27.201228Z",
        "lastModifiedDate": "2021-04-21T15:30:16.131235Z",
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
                "uri": "/{{audience.account}}/customaudiences?fields=name,description,account_id&subtype=CUSTOM",
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
                "uri": "/validate/permissions?access_token={{credential.token}}",
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
            "host": "https://api.moviestar.com/v8.0",
            "name": "Moviestar fictional destination template"
        },
        "validations": [
            {
                "field": "{{audience.account}}",
                "regex": "^moviestar_[0-9]+$"
            }
        ]
    },

```

|Template section | Description |
|--- |--- |
|`audience` | Includes all metadata from Experience Platform needed to create an audience in the destination platform. |
|`credential` | Specifies the authorization type for your destination. |
|`metadataTemplate` | Includes basic information for HTTP calls to your destination, to create, update, validate, and delete audiences. |
|`validations` | Runs validations for any fields in the template configuration before making a call to the partner API. For example, you could validate that the user's account ID is input correctly. |

Find descriptions of all parameters in the template in the reference documentation [Audience metadata endpoint API operations](/help/audience-metadata-api.md).
