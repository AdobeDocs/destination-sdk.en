---
description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-description: The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem.
seo-title: Configuration options for the Destination SDK
title: Configuration options for the Destination SDK
exl-id: d2d1ebd3-bc08-46bf-8ec7-09ab14e991b7
---
# Configuration options for the Destination SDK


>[!IMPORTANT]
>
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

<br>&nbsp;

## Overview {#overview}

The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem. The templates used in Adobe Experience Platform are:

* **Destination configuration**: contains basic information about your destination. This configuration includes the identity types that your destination can support, and various UI attributes for your destination card in the Adobe Experience Platform user interface.
* **Server and template specs**: Ties together information about the server specs and the templating used by Adobe to deliver payloads to your destination
  * **Server specs**: a template that stores your endpoint details.
  * **Template specs**: in this template, you can define how to transform profile attribute fields between XDM schema and the format that your platform supports. For in-depth information about supported templating languages, message formats, and the information required by Adobe to set up the integration with your platform, see [Message format](/help/message-format.md). 
* **Authentication configuration**: These settings define how Adobe Experience Platform users connect to your destination.
* **Audience metadata configuration**: This template allows you to configure how audiences/segments are programmatically created, updated, or deleted in your destination.

![Destination SDK templates and configurations](/help/assets/self-service-configuration.png)

The pages below provide more detail about the configuration options available for the templates.

* [Destination configuration](/help/destination-configuration.md)
* [Server and template specs](/help/server-and-template-configuration.md)
* [Credentials configuration](/help/credentials-configuration.md)
* [Audience metadata configuration](/help/audience-metadata-management.md)
* [OAuth 2 configuration](/help/oauth2-authentication.md)
* [Message format](/help/message-format.md)