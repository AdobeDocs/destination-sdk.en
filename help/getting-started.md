---
description: This page describes how to authenticate and start using Adobe Destination SDK. It includes instructions how to obtain Adobe I/O authentication credentials, a sandbox name and ID, and how to be added to an allowed partner list.
title: Getting started with Destination SDK
---
# Getting started 


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview 

This page describes how to authenticate and start using Adobe Destination SDK. It includes instructions how to obtain Adobe I/O authentication credentials, a sandbox name and ID, and how to be added to an allowed partner list.

## Terminology

This guide uses Platform-specific concepts, such as IMS Organization and sandboxes. Consult the [Experience Platform glossary](https://experienceleague.adobe.com/docs/experience-platform/landing/glossary.html) for definitions of these and other terms.

## Obtain required authentication credentials

Destination SDK uses the [Adobe I/O](https://www.adobe.io/) gateway for authentication. To make API calls to Destination SDK endpoints, you need to provide certain headers in your API calls. Work with the Adobe Exchange team to set up authentication for you to the [Adobe Developer Console](http://console.adobe.io/).

In order to successfully make calls to Destination SDK API endpoints, follow the [Experience Platform authentication tutorial](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html). Start the tutorial from the "[Generate access credentials in Adobe Developer Console](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html#generate-access-credentials-in-adobe-developer-console)" step, as the Adobe Exchange team will handle the steps before that for you. Completing the authentication tutorial provides the values for each of the required headers in Destination SDK API calls, as shown below:

* `x-api-key: {API_KEY}` (Client ID)
* `x-gw-ims-org-id: {IMS_ORG}` (Organization ID)
* `Authorization: Bearer {ACCESS_TOKEN}`. Read the next section to learn how to obtain this token.

### Obtain `Authorization: Bearer {ACCESS_TOKEN}`

To obtain the `{ACCESS_TOKEN}`, you must generate a JWT token and exchange it for the access token. To do this:

1. Follow the instructions in the [Generate JWT section](https://www.adobe.io/apis/experienceplatform/console/docs.html#!AdobeDocs/adobeio-console/master/credentials.md) in the credentials guide.
2. Follow the instructions in [Step 3: try it](https://www.adobe.io/authentication/auth-methods.html#!AdobeDocs/adobeio-auth/master/AuthenticationOverview/ServiceAccountIntegration.md) in the Service account connection guide.

You now have the required authentication headers `x-api-key: {API_KEY}`, `x-gw-ims-org-id: {IMS_ORG}`, and `Authorization: Bearer {ACCESS_TOKEN}`.

>[!NOTE]
>
>The access token has an expiration time of 24 hours, expressed in milliseconds (see `expires_in` field in the screenshot), so you will have to refresh it. To refresh the access token, simply repeat the steps outlined in this section.

## Destination ownership and sandboxes

All resources in Experience Platform are isolated to specific virtual sandboxes. Partners in the Destination SDK program are assigned a common IMS Organization ID and individual sandboxes. Requests to Destination SDK require headers that specify the name and ID of the sandbox the operation will take place in:

* `x-sandbox-name: {SANDBOX_NAME}`
* `x-sandbox-id: {SANDBOX_ID}`

The Adobe Exchange team will provide you with your sandbox name and ID, which you are required to use in calls to the Destination SDK API endpoints.

## Allowlisting

To use the API endpoints described in the [reference documentation](/help/configuration-options.md), you must be added to a list of allowed partners. Work with the Adobe Exchange team to be added to the allowlist.

## Next steps

By following the steps in this article, you obtained authentication credentials to Adobe I/O, a sandbox name and ID, and were added to an allowlist. Next, you can set up a destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
