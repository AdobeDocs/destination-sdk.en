---
description: Adobe Experience Platform Destination SDK provides you with self-serve API access, allowing you to build and maintain an integration with Adobe Experience Platform and receive profile attribute and audience data from Adobe with a partner-defined schema and a configurable and extensible framework.
seo-description: Adobe Experience Platform Destination SDK provides you with self-serve API access, allowing you to build and maintain an integration with Adobe Experience Platform and receive profile attribute and audience data from Adobe with a partner-defined schema and a configurable and extensible framework.
seo-title: Adobe Experience Platform Destination SDK
title: Adobe Experience Platform Destination SDK
exl-id: 7aca9f40-98c8-47c2-ba88-4308fc2b1798
---
# (Beta) Adobe Experience Platform Destination SDK

>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently a beta release. The documentation and the functionality are subject to change.

## Destination SDK for partners {#destinations-sdk}

Adobe Experience Platform Destination SDK provides you with self-serve API access, allowing you to build and maintain an integration with Adobe Experience Platform and receive profile attribute and audience data from Adobe with a partner-defined schema and a configurable and extensible framework.

This documentation set provides all the configuration options needed for *Destination Authoring*. For the elements listed in the image below in the *Destinations Authoring* section, use the Destination SDK API endpoints to configure your destination in Experience Platform. Find more information in [Configuration options for the Destination SDK](/help/configuration-options.md).

![Destinations framework architecture](/help/assets/aep-destination-framework.png)



## Adobe Real-time Customer Data Platform and the destinations service - an overview {#overview}

Built on [Adobe Experience Platform](https://www.adobe.com/experience-platform/documentation-and-developer-resources.html), [Adobe Real-time Customer Data Platform](https://docs.adobe.com/content/help/en/experience-platform/rtcdp/overview.html) (Real-time CDP) helps companies bring together known and unknown data to activate customer profiles with intelligent decisioning throughout the customer journey. Real-time CDP combines multiple enterprise data sources to create unified profiles in real time that can be used to provide one-to-one personalized customer experiences across all channels and devices.

In Adobe Real-time CDP, [destinations](https://docs.adobe.com/content/help/en/experience-platform/rtcdp/destinations/destinations-overview.html) are pre-built integrations with destination platforms that allow for the seamless activation of data.

Adobe connects to a large ecosystem of partners, not to mention native integrations with Adobe Experience Cloud, allowing customers to seamlessly activate these audiences and deliver great customer experiences across all channels, from on-site or in-app personalization to email, paid media, call centers, connected devices, and more.

This documentation set provides instructions for you to use the Adobe Experience Platform Destination SDK to integrate with Adobe Experience Platform as a partner, and have your destination become part of the ever-growing destinations catalog.

![Destinations overview for partners](/help/assets/destinations-overview.gif)


## Types of destinations in Adobe Experience Platform {#types-of-destinations}

In Adobe Experience Platform, we distinguish between two destination types - *connections* and *extensions*. In the user interface, customers can choose between two types of connection destinations, Profile Export destinations and Segment Export destinations. For more details around the difference between the different destination types, see [Destination Types and Categories](https://docs.adobe.com/content/help/en/experience-platform/rtcdp/destinations/destination-types.html#destination-types).

![Destination types](/help/assets/types-of-destinations.png)

This documentation set provides you with all the necessary information to add your destination to Adobe Experience Platform, as a *connection*, either Profile Export or Segment Export. To set up an extension, see the [Experience Platform Launch developer portal](https://developer.adobelaunch.com/extensions/).


## Prerequisites {#prerequisites}

We recommend you read and understand the following Adobe Experience Platform documentation:

* [Basis of XDM schema composition](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/composition.html)
* [Identity namespace overview](https://docs.adobe.com/content/help/en/experience-platform/identity/namespaces.html)
* [Adobe Real-time CDP destinations overview](https://docs.adobe.com/content/help/en/experience-platform/rtcdp/destinations/destinations-overview.html)


## Where to start {#where-to-start}

<!--

First, see the [integration patterns](/help/integration-methods.md). This page presents an overview of integration patterns and should help you decide which configuration options to select for your destination.

-->

Read [Configuration options for the Destination SDK](/help/configuration-options.md) and [Message format](/help/message-format.md) for information on the configuration options available to you.

<!--

Then, see [Destination Authoring Process & Lifecycle](/help/destinations-authoring-process.md) for timelines and steps to complete the configuration and set your destination live in Adobe Real-time CDP.

See the tech specs below for configuration options for each destination type in Adobe Real-time CDP.

* [Batch destinations](/help/batch-destinations.md)
* [Streaming destinations](/help/streaming-destinations.md)
* [OAuth destinations](/help/oauth-destinations.md)

-->

## Phased approach to the Destination SDK {#phased-approach}

The alpha phase of the Destination SDK is a configuration-driven approach. Partners should provide Adobe a list of configurations for their destinations. Adobe will use the configuration options on your behalf to set up your destination in Adobe Experience Platform.

In the first phase, read and understand the [configuration options](/help/configuration-options.md) and the [messaging template](/help/message-format.md) for destinations and let us know how we should configure your destination in Experience Platform.

At general availability release, the configuration options will be made available as a set of APIs, along with API documentation. This will allow you to set up the destination configurations yourself. You will be able to own this process yourself without much input by Adobe. Note that Adobe will review your configuration before setting it live.
