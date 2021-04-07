---
description: This page describes how to authenticate and start using Destination SDK. 
title: Getting started with Destination SDK
---
# Getting started 


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview 

This page describes how to authenticate and start using Destination SDK.

## Terminology

This guide uses Platform-specific concepts, such as IMS Organization and sandboxes. Consult the [Platform glossary](https://experienceleague.adobe.com/docs/experience-platform/landing/glossary.html) for definitions of these and other terms. 

## Obtain required authentication credentials

Partners in the Destination SDK program are assigned a common IMS Organization ID and individual sandboxes. 

Destination SDK uses the Adobe I/O gateway for authentication. Ask the Adobe Exchange team to set up authentication for you in [Adobe Developer Console](http://console.adobe.io/).

In order to successfully make calls to Platform endpoints, including Destination SDK, you are required to complete the authentication tutorial. Completing the authentication tutorial provides the values for each of the required headers in Experience Platform API calls, as shown below:

* `Authorization: Bearer {ACCESS_TOKEN}`
* `x-api-key: {API_KEY}`
* `x-gw-ims-org-id: {IMS_ORG}`

## Destination ownership and sandboxes

All resources in Experience Platform are isolated to specific virtual sandboxes. Requests to Platform APIs require a header that specifies the name of the sandbox the operation will take place in:

* `x-sandbox-name: {SANDBOX_NAME}`

The Adobe Exchange team will provide you with your sandbox name, which you are required to use in calls to the Destination SDK API endpoints.

## Allowlisting

To use the API endpoints described in the [reference documentation](/help/configuration-options.md), you must be added to an allowlist. Work with the Adobe Exchange team to be added to the allowlist.

## Next steps

By following the steps in this article, you obtained authentication credentials to Adobe I/O and were added to an allowlist. Next, you can set up a destination using the Destination SDK. Read [How to use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
