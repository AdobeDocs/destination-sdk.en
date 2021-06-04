---
description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-title: Configuration options in Destination SDK
title: Configuration options in Destination SDK
exl-id: d2d1ebd3-bc08-46bf-8ec7-09ab14e991b7
---
# Configuration options in Destination SDK


>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

<br>&nbsp;

## Overview {#overview}

The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem. The templates used in Adobe Experience Platform are:

* **Destination configuration**: Contains basic information about your destination. This configuration includes the identity types that your destination can support, and various UI attributes for your destination card in the Adobe Experience Platform user interface.
* **Server and template specs**: Ties together information about the server specs and the templating used by Adobe to deliver payloads to your destination
  * **Server specs**: A template that stores your endpoint details.
  * **Template specs**: In this template, you can define how to transform profile attribute fields between XDM schema and the format that your platform supports. For in-depth information about supported templating languages, message formats, and the information required by Adobe to set up the integration with your platform, see [Message format](/help/message-format.md).
* **Authentication configuration**: These settings define how Adobe Experience Platform users connect to your destination.
* **Audience metadata configuration**: This template allows you to configure how audiences/segments are programmatically created, updated, or deleted in your destination.

![Destination SDK templates and configurations](/help/assets/self-service-configuration.png)

The pages below provide more detail about the functionality and configuration options available in Destination SDK.

Functionality description | API reference |
---------|----------|
[Destination configuration](/help/destination-configuration.md) | [Destinations API endpoint operations](/help/destination-configuration-api.md) |
[Server and template specs](/help/server-and-template-configuration.md) | [Destination servers API endpoint operations](/help/destination-server-api.md) |
[Authentication configuration](/help/credentials-configuration.md) | [Credentials endpoint API operations](/help/credentials-configuration-api.md) |
[Audience metadata management](/help/audience-metadata-management.md) | [Audience metadata endpoint API operations](/help/audience-metadata-api.md) | 
[OAuth 2 configuration](/help/oauth2-authentication.md) | Configure using the `customerAuthenticationConfigurations` parameter in the [/destinations API endpoint](/help/destination-configuration-api.md). |
[Message format](/help/message-format.md) | - |
