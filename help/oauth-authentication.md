---
description: This page describes the various OAuth2 authentication flows supported by Destination SDK, as well as instructions to set up OAuth2 authentication for your destination.
title: OAuth authentication
---
# OAuth2 authentication 


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview 

Adobe Experience Platform supports various forms of authentication, which allow it to connect to destinations. Use Destination SDK to allow Adobe Experience Platform to connect to your destination using the [OAuth2 authentication framework](https://tools.ietf.org/html/rfc6749).

This page describes the various OAuth2 authentication flows supported by Destination SDK, as well as instructions to set up OAuth2 authentication for your destination.

## How to add OAuth2 details to your Destination configuration

To set up OAuth2 authentication for your destination in Experience Platform, you must add your OAuth2 configuration in the `/destinations` endpoint, under the `customerAuthenticationConfigurations` parameter. See [example configuration](/help/destination-configuration.md#example-configuration). Instructions on which fields you need to add to your configuration template, depending on your OAuth2 authentication mechanism, are further below on this page.

## Types of OAuth2 flows

Experience Platform supports the three OAuth2 grant types below. If you have a custom OAuth2 setup, Adobe will be able to support it with the help of custom fields in your integration. Refer to the sections for each grant type for more information.


>[!IMPORTANT]
>
>* You provide the input parameters as instructed in the sections below. Adobe-internal systems will produce the output parameters, which will be used to connect and maintain authentication to your destination.
>* The input parameters highlighted in bold in the table are required parameters in the Oauth2 authentication flow. The other parameters are optional.

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

The above table enumerates the fields that are used in standard OAuth 2 flows. In addition to these standard fields, various partner integrations may require additional inputs and outputs. Adobe has designed a flexible OAuth 2 authentication/authorization framework for Destination SDK that can handle variations to the above standard fields pattern while supporting a mechanism to automatically regenerate invalid outputs, such as expired access tokens.

The output in all cases includes an access token, which will be used by Adobe Experience Platform to authenticate and maintain authentication to your destination. 

The system that Adobe has designed for OAuth2 authentication:
* Supports all three OAuth 2 grants while accounting for any variations in them, such as additional data fields, non-standard API calls, and more. 
* Supports access tokens with varying lifetime values, be it 90 days, 30 minutes, or anything else that you require.

## OAuth2 with Authorization Code

If your destination supports a standard OAuth 2.0 Authorization Code flow (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.1)) or a variation of it, see the required and optional fields below:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

To set up this authentication method for your destination, add the following lines to your configuration, in the /destinations endpoint:

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "AUTHORIZATION_CODE",
      "options": {
        "enableProof": true,
        "profileFields": [
          "id",
          "name",
          "picture",
          "email"
        ]
      },
      "accessTokenUrl": "https://api.moviestar.com/OAuth/access_token", // REQUIRED
      "authorizationUrl": "https://www.moviestar.com/dialog/OAuth", // REQUIRED
      "refreshTokenUrl": "https://www.moveistar.com/dialog/OAuth/",
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write"
    }
  ]
//...
}

```

## OAuth2 with Password Grant 

The OAuth2 Password grant (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.3)) works in a similar way to the Authorization Code grant. Experience Platform requires the user's username and password and exchanges them for an access token and, optionally, a refresh token.
Adobe makes use of the standard inputs below to simplify destination configuration, with the ability to override values:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

To set up this authentication method for your destination, add the following lines to your configuration, in the /destinations endpoint: 

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "PASSWORD",
      "accessTokenUrl": "https://api-stage.moveistar.com/portal/OAuth/token",// REQUIRED
      "refreshTokenUrl": "https://api-stage.moviestar.com/portal/OAuth/token", // will not be used since we will always get a brand new token
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write",
    },

```

## OAuth2 with Client Credential Grant

You can configure an Oauth2 Client Credential (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.3)) destination, which supports the following general inputs/outputs:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

To set up this authentication method for your destination, add the following lines to your configuration, in the /destinations endpoint:

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "CLIENT_CREDENTIAL",
      "accessTokenUrl": "https://api.moveistar.com/OAuth/access_token", // REQUIRED
      "refreshTokenUrl": "https://www.moviestar.com/dialog/OAuth/refresh",
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write"
    },
  ]
//...
}

```

## Token refresh

//add information about how we have a mechanism to refresh tokens.

## Adobe Experience Platform callback URL

In your system, you need to set up Adobe's OAuth2 redirect/callback URL

Redirect/callback URL | Environment |
---------|----------|
 `https://platform.adobe.io/data/core/activation/oauth/api/v1/callback` | Production |
 `https://platform-stage.adobe.io/data/core/activation/oauth/api/v1/callback` | Staging |


## Templating conventions



## Next steps

By reading this article, you now have an understanding of the OAuth2 authentication patterns supported by Adobe Experience Platform. Next, you can set up your OAuth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
