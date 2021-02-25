---
description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-title: Configuration options for the Destination SDK
title: Configuration options for the Destination SDK
---

# Configuration options for the Destination SDK


>[!IMPORTANT]
>
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

<br>&nbsp;

## Overview 

The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem. The templates used in Adobe Experience Platform are:

* **Server and template specs**: ties together information about the server specs and the templating used by Adobe to deliver payloads to your destination
  * **Server specs**: a template that stores your endpoint details.
  * **Template specs**: in this template, you can define how to transform profile attribute fields between XDM schema and the format that your platform supports. For in-depth information about supported templating languages, message formats, and the information required by Adobe to set up the integration with your platform, see [Message format](/help/message-format.md). 
* **Credentials**: this template defines how Adobe Experience Platform users connect to your destination.
* **Destination configuration**: contains further information about your destination. This includes the identity types that your destination can support, and various UI attributes for your destination card in the Adobe Experience Platform user interface.

![Self-service configuration](/help/assets/self-service-configuration.png)

The sections below provide more detail about the configuration options available for the templates.

>[!IMPORTANT]
>
>The configurations described in the sections below are subject to change in the alpha release phase of Destination SDK.

<br>&nbsp;

## Server and template specs

**API endpoint**: `/authoring/v1/destination-servers`

>[!NOTE]
>
>The API endpoint is not available publicly in the alpha release phase of Destination SDK.

The server and template specs in Adobe Destination SDK can be configured via the common endpoint `/authoring/v1/destination-servers`.

### Server specs

Customers will be able to activate data from Adobe Experience Platform to your destination via HTTP exports. The server configuration contains information about the server receiving the messages (the server on your side).

**Example configuration**

```json

{
  "name": "Moviestar destination server",
  "destinationServerType": "URL_BASED",
  "urlBasedDestination": {
    "url": {
      "templatingStrategy": "PEBBLE_V1",
      "value": "YOUR_TEMPLATE_OUTPUT"
    },
    "maxUsersPerRequest": 2,
    "splitUserById": true
  }
}
```

This process delivers user data as a series of JSON formatted messages to your destination platform. The parameters below form the HTTP server specs template.


|Parameter | Type | Description|
|---|---|---|
|`name` | String | This is a friendly name of your server, visible only to Adobe. This name is not visible to partners or customers. Example `Moviestar destination server`.  |
|`destinationServerType` | String | `URL_BASED` is the only available option in the alpha release phase. |
|`maxUsersPerRequest` | Integer | Specifies the maximum number of users per request allowed for your server. If your server does not accept multiple users per request, set this value to `1`. |
|`splitUserById` | Boolean | Use this flag if the call to the destination should to be split by identity. Set this flag to `true` if your server only accepts one identity per call, for a given namespace.  |
|`httpMethod` | String | The method that Adobe will use in calls to your server. Options are `GET`, `PUT`, `POST`, `DELETE`, `PATCH`, `OPTIONS`, `HEAD`. |

<!--

|Parameter | Type | Description|
|---------|----------|------|
|`hostname` | String | This is the hostname of your server. Example `https://data-in.acmecompany.net`.  |
|`port` | integer | The server port of your destination, for example `443`, `80`. |
|`maxUsersPerRequest` | integer | Specifies the maximum number of users per request allowed for your server. |
|`path` | String | This represents the url path and parameters of your server. Example:  `/path/to/import` |
|`httpMethod` | String | The method that Adobe will use in calls to your server. Options are `GET`, `PUT`, `POST`, `DELETE`, `PATCH`, `OPTIONS`, `HEAD`. |
|`contentType` | String | Defines how to structure the content sent to your servers. Supported options are JSON and XML. |

-->

### Template specs 

The template spec allows you to configure how to format the exported message to your destination. Adobe uses a [Jinja-based](https://jinja.palletsprojects.com/en/2.11.x/) templating language to transform the fields from the XDM schema into a format supported by your destination. For more information about the transformation, visit the links below:

* [Message format](/help/message-format.md)
* [Using a templating language for the identity, attributes, and segment membership transformations ](/help/message-format.md#using-templating)

**Example configuration**

```json

    "httpTemplate": {
        "httpMethod": "POST",
        "requestBody": {
            "templatingStrategy": "PEBBLE_V1",
            "value": "{ \"attributes\": [ {% for ns in [\"external_id\", \"yourdestination_id\"] %} {% if input.profile.identityMap[ns] is not empty and first_namespace_encountered %} , {% endif %} {% set first_namespace_encountered = true %} {% for identity in input.profile.identityMap[ns]%} { \"{{ ns }}\": \"{{ identity.id }}\" {% if input.profile.segmentMembership.ups is not empty %} , \"AEPSegments\": { \"add\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"realized\" or segment.value.status == \"existing\" %} {% if added_segment_found %} , {% endif %} {% set added_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ], \"remove\": [ {% for segment in input.profile.segmentMembership.ups %} {% if segment.value.status == \"exited\" %} {% if removed_segment_found %} , {% endif %} {% set removed_segment_found = true %} \"{{ destination.segmentAliases[segment.key] }}\" {% endif %} {% endfor %} ] } {% set removed_segment_found = false %} {% set added_segment_found = false %} {% endif %} {% if input.profile.attributes is not empty %} , {% endif %} {% for attribute in input.profile.attributes %} \"{{ attribute.key }}\": {% if attribute.value is empty %} null {% else %} \"{{ attribute.value.value }}\" {% endif %} {% if not loop.last%} , {% endif %} {% endfor %} } {% if not loop.last %} , {% endif %} {% endfor %} {% endfor %} ] }"
        },
        "contentType": "application/json"
    }

```

>[!NOTE]
>
>The `value` attribute in the example configuration above is a [character-escaped](https://www.w3schools.com/python/gloss_python_escape_characters.asp) version that ties together the examples in [Attributes](/help/message-format.md#attributes), [Segment membership](/help/message-format.md#segment-membership), and [Identities](/help/message-format.md#identities).

<!--

|`requestBody` | String | The request body contains the data exported from Real-time CDP, activated to your destination. <br> We need to know which data format macros your destination should support. See [Outbound Template Macros](https://docs.adobe.com/content/help/en/audience-manager/user-guide/implementation-integration-guides/receiving-audience-data/batch-outbound-data-transfers/outbound-template-macros.html) and [Outbound Macro Examples](https://docs.adobe.com/content/help/en/audience-manager/user-guide/implementation-integration-guides/receiving-audience-data/batch-outbound-data-transfers/outbound-macro-examples.html) for examples from Adobe's DMP, Audience Manager. <br> See also, [Message format](#message-format) for further information.  |
|`queryParameters` | String | Request parameters defined as macros. See above.|



<br>&nbsp;

#### Example

A valid HTTP specs template could look like below:

```

{
  "name": "ACME company HTTP template",
  "type": "HTTP", 
  "httpTemplate": {
    "httpMethod": "POST",
    "requestBody": "{"AdvertiserId":"12345", "DataCenterId": 2, "SegmentID":"dfd215e4-8d6b-4fdb-90b9-fab4456f2c9d","Data":[{"Name":"4321"}]}",
    "queryParameters": "{"AdvertiserId":"12345", "DataCenterId": 2, "SegmentID":"dfd215e4-8d6b-4fdb-90b9-fab4456f2c9d","Data":[{"Name":"4321"}]}",
    "contentType": "JSON"
  }
}

```

// commenting out this part as these types of destination specs are not supported in phase one

### File specifications

File-based destinations deliver file exports containing segment qualifications and profile attributes to your preferred storage location. If you want to set up a batch file-based destination, the template we'll use will be as below:

## Server specs

The server configuration contains information about the server receiving the messages (the server on your side). 
Adobe Real-time CDP currently supports three types of server configurations:
* URL
* File-based SFTP
* File-based S3

For URL destinations, you would provide us your server's information, for File-based SFTP and File-based S3 you would provide information as to the storage locations where files should be delivered.
Provide us the necessary information about your server or storage locations, as shown in the sections below.


**urlBasedDestination**

|Parameter | Type | Description|
|---------|----------|------|
|`hostname` | String | This is the hostname of your server. Example `https://data-in.acmecompany.net`.  |
|`port` | integer | The server port of your destination, for example `443`, `80`. |
|`maxUsersPerRequest` | integer | Specifies the maximum number of users per request allowed for your server. |
|`path` | String | This represents the url path and parameters of your server. Example:  `/path/to/import` |


// commenting out this part as these types of destination specs are not supported in phase one

**SFTP Destinations**

For FTP destinations, we need the protocol details below:

Parameter | Type | 
---------|----------|
 hostname | String | 
 port | integer | 
 rootDirectory | String | 
 moveToWhenCompleted | integer | 
 tmpFileRename | integer | 
 encryptionMode | String |
 filenameSuffix | String | 

**Amazon S3 Destinations**

For Amazon S3 destinations, we need the protocol details below:

Parameter | Description | 
---------|----------|
 bucket | Your Amazon S3 bucket name | 
 path | Your Amazon S3 bucket path | 

-->

<br>&nbsp;

## Credentials

**API endpoint**: `/authoring/v1/credentials` 

>[!NOTE]
>
>The API endpoint is not available publicly in the alpha release phase of Destination SDK.

This configuration determines how Adobe Experience Platform users authenticate to your destination to activate data. Adobe Experience Platform supports several authentication types:

* Basic authentication
* Oauth1
* OAuth2 user credentials
* OAuth2 client credentials
* OAuth2 access token
* OAuth2 refresh token

**Example configuration for a Basic authentication credential configuration with username and password**

```json
{
  "type": "BASIC",
  "name": "YOUR_DESTINATION_NAME",
  "basicAuthentication": {
    "username": "YOUR_DESTINATION_SERVER_USERNAME",
    "password": "YOUR_DESTINATION_SERVER_PASSWORD"
  }
}

```

**Example configuration for an Oauth2 credential configuration**

```json

{
  "oauth2AccessTokenAuthentication": {
    "accessToken": "YOUR_DESTINATION_SERVER_ACCESS_TOKEN",
    "expiration": "YOUR_TOKEN_TIME_TO_LIVE",
    "username": "YOUR_DESTINATION_SERVER_USERNAME",
    "userId": "YOUR_DESTINATION_USER_ID",
    "url": "AUTHORIZARION_PROVIDER_URL",
    "header": "YOUR_AUTHORIZATION_HEADER"
  }
}

```

The sections below list out the necessary parameters for each authentication type. Let us know which authentication type your server uses and provide us with the relevant information for your server type.

### Basic authentication

|Parameter | Type | Description|
|---------|----------|------|
|`username` | String | Destination Server login username |
|`password` | String | Destination Server login password |

<!--

// commenting out this part as these types of authentication methods are not supported in phase one

### S3 authentication

|Parameter | Type | Description|
|---------|----------|------|
|accessId | String | Destination Server S3 credential Access key ID |
|secretKey | String | Destination Server S3 credential Secret key |

### SSH 

|Parameter | Type | Description|
|---------|----------|------|
|username | String | Destination Server SSH username |
|SSHKey | String | Destination Server SSH key |

-->

### OAuth1

|Parameter | Type | Description|
|---------|----------|------|
|`apiKey` | String | A value used by the Destinations Service to identify itself to the Service Provider. |
|`apiSecret` | String | Secret used by the Destinations Service to establish ownership of the API key to the Service Provider. |
|`acccessToken` | String | A value used by the Destinations Service to gain access to the Protected Resources on behalf of the User |
|`tokenSecret` | String | A secret used by the Destinations Service to establish ownership of an access token. |

### OAuth2 user credentials

|Parameter | Type | Description|
|---------|----------|------|
|`clientId` | String | Client ID of Client/Application credential |
|`clientSecret` | String | Client secret of Client/Application credential |
|`username` | String | The user's username to log on to your platform. |
|`password` | String | The user's password to log on to your platform. |
|`url` | String | URL of authorization provider |
|`header` | String | Any header required for authorization |

### OAuth2 client credentials

|Parameter | Type | Description|
|---------|----------|------|
|`clientId` | String | Client ID of Client/Application credential |
|`clientSecret` | String | Client secret of Client/Application credential |
|`username`| String | URL of authorization provider |
|`password` | String | Any header required for authorization |

### OAuth2 access token

|Parameter | Type | Description|
|---------|----------|------|
|`accessToken` | String | Access token provided by the authorization provider |
|`expiration` | String | The time-to-live for the access token |
|`username` | String | The user's username to log on to your platform. |
|`userId` | String | The user's ID with your platform. |
|`url` | String | URL of authorization provider |
|`header` | String | Any header required for authorization |

### OAuth2 refresh token

|Parameter | Type | Description|
|---------|----------|------|
|`clientId` | String | Client ID of Client/Application credential |
|`clientSecret` | String | Client secret of Client/Application credential |
|`refreshToken` | String | Refresh token provided by the authorization provider |
|`url` | String | URL of authorization provider |
|`expiration` | String | The time-to-live for the refresh token |
|`header` | integer | Any header required for authorization |

<br>&nbsp;

## Destination configuration

**API endpoint**: `/authoring/v1/destinations` 

>[!NOTE]
>
>The API endpoint is not available publicly in the alpha release phase of Destination SDK.

This configuration allows you to indicate basic information like your destination name, category, description, and logo. The settings in this configuration determine how your destination appears in the Experience Platform user interface and the identities that can be exported to your destination.


**Example configuration**

This is an example configuration for a fictional destination, Moviestar, which has endpoints in four locations on the globe. The destination belongs to the mobile destinations category.

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
  "customerTargetType": {
    "additionalFields": [
      {
        "name": "endpointsInstance",
        "type": "string",
        "title": "Endpoints Instance",
        "description": "Moviestar manages several instances across the globe for REST endpoints that our customers are provisioned for. Select the endpoint that you are provisioned to.",
        "isRequired": true,
        "enum": [
          "US",
          "EU",
          "APAC",
          "NZ"
        ]
      }
    ]
  },
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

### UI attributes

This section refers to the UI elements in the configuration template above that Adobe will add for your destination in the Adobe Experience Platform user interface. See below:

|Parameter | Type | Description|
|---------|----------|------|
|`documentationLink` | String | Refers to the documentation page in the [Destinations Catalog](https://experienceleague.adobe.com/docs/experience-platform/destinations/catalog/overview.html?lang=en#catalog) for your destination. Use `http://www.adobe.com/go/destinations-YOURDESTINATION-en`, where `YOURDESTINATION` is the name of your destination. For a destination called Moviestar, you would use `http://www.adobe.com/go/destinations-moviestar-en` |
|`category` | String | Refers to the category assigned to your destination in Adobe Experience Platform. For more information, read [Destination Categories](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/destination-types.html?lang=en#destination-categories). Use one of the following values: `adobeSolutions, advertising, analytics, cdp, cloudStorage, crm, customerSuccess, database, dmp, ecommerce, email, emailMarketing, enrichment, livechat, marketingAutomation, mobile, personalization, protocols, social, streaming, subscriptions, surveys, tagManagers, voc, warehouses, payments` |
|`iconUrl` | String | Provide the logo that Adobe will display in the Adobe Experience Platform destinations catalog for your destination card. |
|`description` | String | Provide a description that Adobe will use in the Adobe Experience Platform destinations catalog for your destination card. Aim for no more than 4-5 sentences. |
|`connectionType` | String | In the alpha release phase of Destination SDK, `Server-to-server` is the only available option. |
|`frequency` | String | In the alpha release phase of Destination SDK, `Streaming` is the only available option. |

### Identities

Adobe needs to know which [!DNL Platform] identities customers will be able to export to your destination. Some examples are [!DNL Experience Cloud ID], hashed email, device ID ([!DNL IDFA], [!DNL GAID]). These are Platform identity namespaces that customers can map to identity namespaces from your destination.

Identity namespaces do not require a 1-to-1 correspondence between [!DNL Platform] and your destination.
For instance, customers could map a Platform [!DNL IDFA] namespace to an [!DNL IDFA] namespace from your destination, or they can map the same Platform [!DNL IDFA] namespace to a [!DNL Customer ID] namespace in your destination.

 Read more in the [Identity Namespace overview](https://docs.adobe.com/content/help/en/experience-platform/identity/namespaces.html).

|Parameter | Type | Description|
|---------|----------|------|
|`acceptsAttributes` | Boolean | Indicates if your destination accepts standard profile attributes. Usually these are highlighted in our partners' documentation. |
|`acceptsCustomNamespaces` | Boolean | Indicates if customers can set up custom namespaces in your destination. |

<!--

### Account information

If customers use a combination of account and password to log into your platform, please let us know what the expected format is. This will enable Adobe to set up validation checks when Adobe Experience Platform customers establish a connection to your destination, to ensure they authenticate correctly and the exported data lands in your platform.




A format is a saved template that uses macros to organize the contents of data sent to a destination. Format types include HTTP formats and file formats. HTTP formats send data in a JSON object with a POST or GET method. File formats send data in a file by SFTP. The macros used by each format let you set file names, define file headers, and organize the contents of a data file.

The `requestBody` in HTTP calls and the `dataRow` in exported files contain the activation information exported to your destination.

Adobe Real-time CDP provides great flexibility in the message format, so that it matches any format expected by your systems.

The message format supports:

* Exporting identities - a single identity or a list of identities (for example, exporting multiple emails for a single user)
* Exporting segments
* Exporting attributes known during destination creation - for example, sending the first name to Facebook 
* Exporting a variable list of attributes - mapping a variable list of attributes to a destination.

-->
