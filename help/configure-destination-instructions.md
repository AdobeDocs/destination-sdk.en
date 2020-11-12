---
description: This page describes how to use the reference information in Configuration options for the Destinations SDK to configure your destination using Destination SDK.
seo-description: This page describes how to use the reference information in Configuration options for the Destinations SDK to configure your destination using Destination SDK.
seo-title: How to use the Destination SDK to configure your destination
title: How to use the Destination SDK to configure your destination
---

# How to use the Destination SDK to configure your destination


>[!IMPORTANT]
>
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

<br>&nbsp; 

## Overview 

This page describes how to use the reference information in [Configuration options for the Destinations SDK](/help/configuration-options.md) to configure your destination using Destination SDK. The steps are laid out in sequential order below.

>[!NOTE]
>
>In the alpha release phase of Destination SDK, we ask that you provide the payloads to our internal teams, and they will make API calls on your behalf to set up your destination.


## Use the Configuration options in Adobe Experience Platform Destination SDK to set up your destination

![Illustrated steps of using the Destination SDK endpoints](/help/assets/destination-sdk-steps.png)

### Step 1 -  Create transformation template - Use a templating language to specify the message output format

As a first step, based on the payloads that your destination supports, you must create a template that transforms the format of the exported data from Adobe XDM format into your supported format. See template examples in the section [Using a templating language for the identity, attributes, and segment membership transformations](/help/message-format.md#using-templating).

### Step 2 - Create a server and template configuration

Insert the template you created in step 1 in the `value` parameter, under `requestBody`.


```

POST /authoring/v1/destination-servers
{
    "name": "Moviestar Server",
    "destinationServerType": "URL_BASED",
    "urlBasedDestination": {
        "url": {
            "templatingStrategy": "PEBBLE_V1",
            "value": "https://go.{% if destination.partnerConfig.Moviestar_region == \"US\" %}Moviestar.com{% else %}Moviestar.eu{% endif%}"
        },
            "splitUserById": false
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


### Step 3 - Create credentials configuration

```

POST /authoring/v1/credentials

  "oauth2ClientAuthentication": {
    "url": "string",
    "clientId": "string",
    "clientSecret": "string",
    "header": "string",
    "developerToken": "string"
  }

```

### Step 4 - Create destination configuration

```

POST /authoring/v1/destination
 
{
  "name": "Moviestar",
  "description": "Moviestar Destination",
  "releaseNotes": "Test release",
  "status": "TEST",
  "customerAuthenticationConfigurations": [
    {
      "authType": "BEARER"
    },
    {
      "authType": "BASIC",
      "usernameAlias": "appKey",
      "passwordAlias": "masterSecret"
    }
  ],
  "customerTarget": {
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
    "documentationLink": "https://docs.adobe.com/content/help/en/experience-platform/rtcdp/destinations/destinations-overview.html",
    "category": "mobile",
    "iconUrl": "https://dc5tqsrhldvnl.cloudfront.net/2/90048/da276e30c730ce6cd666c8ca78360df2.png",
    "connectionType": "Server-to-server",
    "frequency": "Streaming"
},
  "identityNamespaces": {
    "channel": {
      "acceptsAttributes": true,
      "acceptsCustomNamespaces": true
    },
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
      "serverRule": "SERVER_ID",
      "serverId": "12347841-0c5c-4097-b872-33cd00708ddc",
      "templateRule": "TEMPLATE_ID",
      "templateId": "1234171f-a528-46e6-83da-00dbec3689d3"
    }
  ],
  "inputSchemaId": "935a7d07bf8b409db7688f8364f34e21"
}

```