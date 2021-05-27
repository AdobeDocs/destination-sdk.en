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
>* This feature is in limited Beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

<br>&nbsp;

## Overview {#overview}

The destinations service in Adobe Experience Platform uses configuration templates for several components that build up the destinations functionality. Combined, these components allow Experience Platform to connect to destination partners, send custom messages, and activate profile data across the digital ecosystem. The templates used in Adobe Experience Platform are:

* **Destination configuration**: contains basic information about your destination. This configuration includes the identity types that your destination can support, and various UI attributes for your destination card in the Adobe Experience Platform user interface.
* **Server and template specs**: Ties together information about the server specs and the templating used by Adobe to deliver payloads to your destination
  * **Server specs**: a template that stores your endpoint details.
  * **Template specs**: in this template, you can define how to transform profile attribute fields between XDM schema and the format that your platform supports. For in-depth information about supported templating languages, message formats, and the information required by Adobe to set up the integration with your platform, see [Message format](/help/message-format.md). 
* **Credentials**: This template defines how Adobe Experience Platform users connect to your destination.

![Self-service configuration](/help/assets/self-service-configuration.png)

The pages below provide more detail about the configuration options available for the templates.

* [Destination configuration](/help/destination-configuration.md)
* [Server and template specs](/help/server-and-template-configuration.md)
* [Credentials configuration](/help/credentials-configuration.md)
* [Message format](/help/message-format.md)