---
description: This page describes how to use the reference information in Configuration options for the Destinations SDK to configure your destination using Destination SDK.
seo-description: This page describes how to use the reference information in Configuration options for the Destinations SDK to configure your destination using Destination SDK.
seo-title: How to use the Destination SDK to configure your destination
title: How to use the Destination SDK to configure your destination
exl-id: 8fe68f3e-972a-40c5-9b63-8a51d5eff820
---
# How to use the Destination SDK to configure your destination


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently a beta release. The documentation and the functionality are subject to change.

<br>&nbsp;

## Overview 

This page describes how to use the reference information in [Configuration options for the Destinations SDK](/help/configuration-options.md) to configure your destination using Destination SDK. The steps are laid out in sequential order below.

## Prerequisites

Before advancing to the steps illustrated below, please read the [Destination SDK getting started](/help/getting-started.md) page for information on obtaining the necessary Adobe I/O authentication credentials and being added to an allowed list.

## Use the Configuration options in Adobe Experience Platform Destination SDK to set up your destination

![Illustrated steps of using the Destination SDK endpoints](/help/assets/destination-sdk-steps.png)

## Step 1: Create transformation template - Use a templating language to specify the message output format

As a first step, based on the payloads that your destination supports, you must create a template that transforms the format of the exported data from Adobe XDM format into your supported format. See template examples in the section [Using a templating language for the identity, attributes, and segment membership transformations](/help/message-format.md#using-templating).

## Step 2: Create a server and template configuration

Insert the template that you created in step 1 in the `value` parameter, under `requestBody`, in the `/destination-servers` configuration.

For more information about the server and template configuration, refer to [Server and template specs](/help/configuration-options.md#server-and-template) in the reference section.

Shown below is an example configuration, with the message transformation template added in the `requestBody.value` parameter.

```json

POST platform.adobe.io/data/core/activation/authoring/v1/destination-servers
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

## Step 3: Create audience metadata configuration

For some destinations, Destination SDK requires that you configure an audience metadata template to programmatically create, update, or delete audiences in your destination. Refer to [Audience metadata management](/help/audience-metadata-management.md) for information on when you need to set up this configuration and how to do it.

## Step 4: Create destination configuration

Shown below is an example configuration for a destination template, created by using the `/destinations` API endpoint. For more information about this template, refer to [Destination configuration](/help/destination-configuration.md) in the reference documentation. 

```json

POST platform.adobe.io/data/core/activation/authoring/v1/destinations
 
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
      "pattern": ""
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

## Step 5: Create credentials configuration / Set up authentication

Depending on whether you specify `"authenticationRule": "CUSTOMER_AUTHENTICATION"` or `"authenticationRule": "PLATFORM_AUTHENTICATION"` in the destination configuration above, you can set up authentication for your destination using the `/destination` or the `/credentials` endpoint. 

* If you selected `"authenticationRule": "CUSTOMER_AUTHENTICATION"` and your destination supports the OAuth 2 authentication method, read [OAuth 2 authentication](/help/oauth2-authentication.md).
* If you selected `"authenticationRule": "PLATFORM_AUTHENTICATION"`, refer to [Credentials configuration](/help/credentials-configuration.md) in the reference documentation.

<!--

Commenting out this part, as the configuration shown here is outdated. Will very likely delete this part for beta. 

Shown below is an example configuration for a destination which supports OAuth2 authentication. For more information, refer to [Credentials configuration](/help/credentials-configuration.md) in the reference documentation.

```json

POST platform.adobe.io/data/core/activation/authoring/v1/credentials

  "oauth2ClientAuthentication": {
    "url": "string",
    "clientId": "string",
    "clientSecret": "string",
    "header": "string",
    "developerToken": "string"
  }

```

Commenting out this part at the request of Product Management

### Step 5 - Provide your configurations to Adobe

Share the configurations you created in steps 1-4 with Adobe's engineering team over email. The Adobe engineering team will set up your destination in Adobe Experience Platform.

-->

## Next steps

Adobe will set your destination live behind a feature flag in Experience Platform. You can now test workflows to export data from Experience Platform to your destination. After confirming that everything works as intended, use our [self-service documentation process](/help/docs-framework/documentation-instructions.md) to create a documentation page for your destination.
