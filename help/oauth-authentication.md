---
description: This page describes the various Oauth2 authentication flows supported by Destination SDK, as well as instructions to set up Oauth2 authentication for your destination.
title: Oauth authentication
---
# Oauth2 authentication 


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview 

This page describes the various Oauth2 authentication flows supported by Destination SDK, as well as instructions to set up Oauth2 authentication for your destination.

Adobe Experience Platform supports various forms of authentication, which allow it to connect to destinations. Use Destination SDK to set up Oauth authentication between Adobe Experience Platform and your destination.

## How to add Oauth details to your Destination configuration

To set up Oauth2 authentication for your destination in Experience Platform, you must to add your Oauth configuration in the `/destinations` endpoint, under the `customerAuthenticationConfigurations` parameter. See [example configuration](/help/destination-configuration.md#example-configuration).

## Types of Oauth2 flows

Experience Platform supports the three Oauth2 grant types below. If you have a custom Oauth2 setup, Adobe will be able to support it with the help of custom fields in your integration. Refer to the sections for each grant type for more information.


>[!IMPORTANT]
>
> The input parameters highlighted in bold in the table are required 

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

The above table highlights in bold fields that are required in standard OAuth 2 flows. In addition to these standard fields, various partner integrations may require additional inputs and outputs. Adobe has designed a flexible OAuth 2 authentication/authorization framework that can handle variations in above standard fields pattern while supporting a mechanism to automatically regenerate invalid outputs, such as expired access tokens.

The system that Adobe has designed for Oauth2 authentication:
* Supports all three OAuth 2 grants while accounting for any variations in them, such as additional data fields, non-standard API calls, and more.
* Supports access tokens with varying lifetime values, be it 90 days, 30 minutes, or anything else that you require

## Oauth2 with Authorization Code

For a standard OAuth 2.0 Authorization Code flow, see the required and optional fields below:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>



``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAUTH2",
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
      "accessTokenUrl": "https://graph.facebook.com/v8.0/oauth/access_token", // REQUIRED
      "authorizationUrl": "https://www.facebook.com/dialog/oauth", // REQUIRED
      "refreshTokenUrl": "https://www.facebook.com/dialog/oauth/",
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write"
    }
  ]
//...
}

```

## Oauth2 with Password Grant 

The Password grant works in a similar way to the Authorization Code grant. Adobe makes use of the following standard inputs simplify destination configuration with ability to override values:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAUTH2",
      "grant": "PASSWORD",
      "accessTokenUrl": "https://api-stage.demdex.com/portal/oauth/token",// REQUIRED
      "refreshTokenUrl": "https://api-stage.demdex.com/portal/oauth/token", // will not be used since we will always get a brand new token
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write",
    },

```

## Oauth2 with Client Credential Grant

You can configure a Client Credential based destination, which supports the following general inputs/outputs:

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>



``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAUTH2",
      "grant": "CLIENT_CREDENTIAL",
      "accessTokenUrl": "https://graph.facebook.com/v8.0/oauth/access_token", // REQUIRED
      "refreshTokenUrl": "https://www.facebook.com/dialog/oauth/refresh",
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

## Templating conventions



## Next steps

By reading this article, you now have an understanding of the Oauth authentication patterns supported by Adobe Experience Platform. Next, you can set up your Oauth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
