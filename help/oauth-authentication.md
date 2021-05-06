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

This page describes the various OAuth2 authentication flows supported by Destination SDK, and provides instructions to set up OAuth2 authentication for your destination.

## How to add OAuth2 details to your Destination configuration

### Prerequisites in your system

First, you must create an app in your system for Adobe Experience Platform. This app will generate a client ID and client secret that users will use to authenticate from Experience Platform to your destination. In your system, you need to set up Adobe's OAuth2 redirect/callback URL.

>[!IMPORTANT]
>
>The step to create an app for Adobe Experience Platform in your system is required only for the [Oauth2 with Authorization Code](/help/oauth-authentication.md#authorization-code) grant type . For other the other grant types, you can skip this step.

Redirect/callback URL | Environment |
---------|----------|
 `https://platform.adobe.io/data/core/activation/oauth/api/v1/callback` | Production |
 `https://platform-stage.adobe.io/data/core/activation/oauth/api/v1/callback` | Staging |

### To do in Destination SDK

To set up OAuth2 authentication for your destination in Experience Platform, you must add your OAuth2 configuration in the `/destinations` endpoint, under the `customerAuthenticationConfigurations` parameter. See [example configuration](/help/destination-configuration.md#example-configuration). Instructions on which fields you need to add to your configuration template, depending on your OAuth2 authentication mechanism, are further below on this page.



## Types of OAuth2 flows

Experience Platform supports the three OAuth2 grant types below. If you have a custom OAuth2 setup, Adobe will be able to support it with the help of custom fields in your integration. Refer to the sections for each grant type for more information.


>[!IMPORTANT]
>
>* You provide the input parameters as instructed in the sections below. Adobe-internal systems will produce the output parameters, which will be used to connect and maintain authentication to your destination.
>* The input parameters highlighted in bold in the table are required parameters in the Oauth2 authentication flow. The other parameters are optional.

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>

The above table lists the fields that are used in standard OAuth 2 flows. In addition to these standard fields, various partner integrations may require additional inputs and outputs. Adobe has designed a flexible OAuth 2 authentication/authorization framework for Destination SDK that can handle variations to the above standard fields pattern while supporting a mechanism to automatically regenerate invalid outputs, such as expired access tokens.

The output in all cases includes an access token, which will be used by Adobe Experience Platform to authenticate and maintain authentication to your destination. 

The system that Adobe has designed for OAuth2 authentication:
* Supports all three OAuth 2 grants while accounting for any variations in them, such as additional data fields, non-standard API calls, and more. 
* Supports access tokens with varying lifetime values, be it 90 days, 30 minutes, or anything else that you require.

## OAuth2 with Authorization Code {#authorization-code}

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

|Parameter | Type | Description|
|---------|----------|------|
|`authType` | String | Use "Oauth2" |
|`grant` | String | Use "AUTHORIZATION_CODE" |
|`options` | String | To be discussed. What does this param do? |
|`accessTokenUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token.|
|`authorizationUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token. |
|`refreshTokenUrl` | String | The URL of your authorization server, which issues an optional refresh token. |
|`clientId` | String | The Client ID that your system assigns to Adobe Experience Platform |
|`clientSecret` | String | The Client secret that your system assigns to Adobe Experience Platform |
|`scope` | String | *Optional*. Set the scope of what the access token allows Experience Platform to preform on your resources. Example: "read, write" |



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
Adobe has designed a system which refreshes expired access tokens without asking the user to come in to the system and log in. The system is able to generate a new token so that the activation to your destination will continue seamlessly for the customer. 

You would configure a templatized HTTP request that allows Adobe to get a new access token, using a refresh token. If the access token has expired, Adobe takes the templated request provided by you, adding the parameters you supplied.

Access token 

## Templating conventions

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 12.6151%;" /><col style="width: 39.3186%;" /><col style="width: 48.0663%;" /></colgroup><tbody><tr><th class="confluenceTh">Prefix</th><th class="confluenceTh">Description</th><th class="confluenceTh">Example</th></tr><tr><td class="confluenceTd">authData</td><td class="confluenceTd">Access any partner or customer data field's value.</td><td class="confluenceTd"><div class="content-wrapper"><table class="wysiwyg-macro" style="background-image: url('https://wiki.corp.adobe.com/plugins/servlet/confluence/placeholder/macro-heading?definition=e2NvZGU6bGFuZ3VhZ2U9anN9&amp;locale=en_US&amp;version=2'); background-repeat: no-repeat;" data-macro-name="code" data-macro-id="3393d476-d048-41c2-bb64-a3e458cd3a68" data-macro-parameters="language=js" data-macro-schema-version="1" data-macro-body-type="PLAIN_TEXT"><tbody><tr><td class="wysiwyg-macro-body"><pre>{{ authData.accessToken }}</pre></td></tr></tbody></table></div></td></tr><tr><td class="confluenceTd">response.body</td><td class="confluenceTd">HTTP response body</td><td class="confluenceTd"><div class="content-wrapper"><table class="wysiwyg-macro" style="background-image: url('https://wiki.corp.adobe.com/plugins/servlet/confluence/placeholder/macro-heading?definition=e2NvZGU6bGFuZ3VhZ2U9anN9&amp;locale=en_US&amp;version=2'); background-repeat: no-repeat;" data-macro-name="code" data-macro-id="fe0bbe11-4b39-41d6-84bd-17072ddfe073" data-macro-parameters="language=js" data-macro-schema-version="1" data-macro-body-type="PLAIN_TEXT"><tbody><tr><td class="wysiwyg-macro-body"><pre>{{ response.body.access_token }}</pre></td></tr></tbody></table></div></td></tr><tr><td class="confluenceTd">response.status</td><td class="confluenceTd">HTTP response status</td><td class="confluenceTd"><div class="content-wrapper"><table class="wysiwyg-macro" style="background-image: url('https://wiki.corp.adobe.com/plugins/servlet/confluence/placeholder/macro-heading?definition=e2NvZGU6bGFuZ3VhZ2U9anN9&amp;locale=en_US&amp;version=2'); background-repeat: no-repeat;" data-macro-name="code" data-macro-id="2913af53-6844-4538-9fc9-5d1b309ad485" data-macro-parameters="language=js" data-macro-schema-version="1" data-macro-body-type="PLAIN_TEXT"><tbody><tr><td class="wysiwyg-macro-body"><pre>{{ response.status }}</pre></td></tr></tbody></table></div></td></tr><tr><td class="confluenceTd">response.headers</td><td class="confluenceTd">HTTP response headers</td><td class="confluenceTd"><div class="content-wrapper"><table class="wysiwyg-macro" style="background-image: url('https://wiki.corp.adobe.com/plugins/servlet/confluence/placeholder/macro-heading?definition=e2NvZGU6bGFuZ3VhZ2U9anN9&amp;locale=en_US&amp;version=2'); background-repeat: no-repeat;" data-macro-name="code" data-macro-id="7fe6fbda-efb2-450e-8778-5ca4cdd05c2b" data-macro-parameters="language=js" data-macro-schema-version="1" data-macro-body-type="PLAIN_TEXT"><tbody><tr><td class="wysiwyg-macro-body"><pre>{{ response.headers.server[0] }}</pre></td></tr></tbody></table></div></td></tr><tr><td class="confluenceTd">authContext</td><td class="confluenceTd">Access information about the current authentication attempt</td><td class="confluenceTd"><div class="content-wrapper"><table class="wysiwyg-macro" style="background-image: url('https://wiki.corp.adobe.com/plugins/servlet/confluence/placeholder/macro-heading?definition=e2NvZGU6bGFuZ3VhZ2U9anN9&amp;locale=en_US&amp;version=2'); background-repeat: no-repeat;" data-macro-name="code" data-macro-id="b677f048-1f70-4760-be30-1fefadec2834" data-macro-parameters="language=js" data-macro-schema-version="1" data-macro-body-type="PLAIN_TEXT"><tbody><tr><td class="wysiwyg-macro-body"><pre>{{ authContext.sandboxName }}&nbsp;

{{ authContext.sandboxId }}&nbsp;

{{ authContext.imsOrgId }}&nbsp;

{{ authContext.client }} // the client executing the authentication attempt(e.g. DATA_PLANE, CONTROL_PLANE, etc)</pre></td></tr></tbody></table></div></td></tr></tbody></table>

## Next steps

By reading this article, you now have an understanding of the OAuth2 authentication patterns supported by Adobe Experience Platform. Next, you can set up your OAuth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
