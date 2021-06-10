---
description: This configuration determines how Adobe Experience Platform users authenticate to your destination endpoint to activate data.
title: Configuration options for credentials in Destination SDK
exl-id: 63ed9a03-eb4d-46c6-85e9-6c1d84acdbad
---
# Credentials configuration {#credentials}

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

**API endpoint**: `platform.adobe.io/data/core/activation/authoring/v1/credentials` 

## When to use the `/credentials` API endpoint {#when-to-use}

>[!IMPORTANT]
>
>In most cases, you *do not* need to use the `/credentials` API endpoint. Instead, you can configure the authentication information for your destination via the `customerAuthenticationConfigurations` parameters of the `/destinations` endpoint.

Use this API endpoint and select `PLATFORM_AUTHENTICATION` in the [destination configuration](/help/destination-configuration.md#destination-delivery) if there is a global authentication system between Adobe and your destination and the [!DNL Platform] customer does not need to provide any authentication credentials to connect to your destination. In this case, you must create a credentials object using the `/credentials` API endpoint. Read [Credentials API endpoint operations](/help/credentials-configuration-api.md) for a complete list of operations you can perform on the `/credentials` endpoint.

## Supported authentication types {#supported-authentication-types}

Adobe Experience Platform supports several authentication types:

* Basic authentication
* Bearer authentication
* OAuth 2 with authorization code
* OAUth 2 with password grant
* OAuth 2 with client credentials grant

For the various supported OAuth 2 flows, as well as for custom OAuth 2 support, read the Destination SDK documentation on [OAuth 2 authentication](/help/oauth2-authentication.md).