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

Use Destination SDK to allow Adobe Experience Platform to connect to your destination by using the [OAuth2 authentication framework](https://tools.ietf.org/html/rfc6749).

This page describes the various OAuth2 authentication flows supported by Destination SDK, and provides instructions to set up OAuth2 authentication for your destination.

## How to add OAuth2 authentication details to your destination configuration

### Prerequisites in your system

As a first step, you must create an app in your system for Adobe Experience Platform, or otherwise register Experience Platform in your system. The app  generates a client ID and client secret, which are needed to authenticate Experience Platform to your destination. As part of this configuration in your system, you need Adobe's OAuth2 redirect/callback URL.

>[!IMPORTANT]
>
>The step to register a redirect/callback URL for Adobe Experience Platform in your system is required only for the [Oauth2 with Authorization Code](/help/oauth-authentication.md#authorization-code) grant type. For the other two supported grant types (password and client credentials), you can skip this step.

Redirect/callback URL | Environment |
---------|----------|
 `https://platform.adobe.io/data/core/activation/oauth/api/v1/callback` | Production |
 `https://platform-stage.adobe.io/data/core/activation/oauth/api/v1/callback` | Staging |

 {style="table-layout:auto"}

At the end of this step, you should have:
* A client ID
* A client secret
* Adobe's callback URL (for the authorization code grant)

### What you need to do in Destination SDK

To set up OAuth2 authentication for your destination in Experience Platform, you must add your OAuth2 configuration in the `/destinations` [endpoint](/help/destination-configuration.md), under the `customerAuthenticationConfigurations` parameter. See the [example configuration](/help/destination-configuration.md#example-configuration). Specific instructions about which fields you need to add to your configuration template, depending on your OAuth2 authentication grant type, are further below on this page.

## OAuth2 grant types

Experience Platform supports the three OAuth2 grant types below. If you have a custom OAuth2 setup, Adobe is able to support it with the help of custom fields in your integration. Refer to the sections for each grant type for more information.


>[!IMPORTANT]
>
>* You provide the input parameters as instructed in the sections below. Adobe-internal systems will produce the output parameters, which are used to connect and maintain authentication to your destination.
>* The input parameters highlighted in bold in the table are required parameters in the Oauth2 authentication flow. The other parameters are optional. There are other custom input parameters that are not shown here, but are described at length in the sections for each authorization grant type. 

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
 Authorization Code | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>authorizationUrl</b></li><li><b>accessTokenUrl</b></li><li>refreshTokenUrl</li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |
 Password | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li><li><b>username</b></li><li><b>password</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |
 Client Credential | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

 {style="table-layout:auto"}

The above table lists the fields that are used in standard OAuth 2 flows. In addition to these standard fields, various partner integrations may require additional inputs and outputs. Adobe has designed a flexible OAuth 2 authentication/authorization framework for Destination SDK that can handle variations to the above standard fields pattern while supporting a mechanism to automatically regenerate invalid outputs, such as expired access tokens.

The output in all cases includes an access token, which will be used by Experience Platform to authenticate and maintain authentication to your destination. 

The system that Adobe has designed for OAuth2 authentication:
* Supports all three OAuth 2 grants while accounting for any variations in them, such as additional data fields, non-standard API calls, and more. 
* Supports access tokens with varying lifetime values, be it 90 days, 30 minutes, or anything else that you require.

## OAuth2 with Authorization Code {#authorization-code}

If your destination supports a standard OAuth 2.0 Authorization Code flow (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.1)) or a variation of it, consult the required and optional fields below:

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
 Authorization Code | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>authorizationUrl</b></li><li><b>accessTokenUrl</b></li><li>refreshTokenUrl</li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

 {style="table-layout:auto"}

To set up this authentication method for your destination, add the following lines to your configuration, in the `/destinations` [endpoint](/help/destination-configuration.md):

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "OAUTH2_AUTHORIZATION_CODE",
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
|`authType` | String | Use "OAuth2" |
|`grant` | String | Use "OAUTH2_AUTHORIZATION_CODE" |
|`accessTokenUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token.|
|`authorizationUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token. |
|`refreshTokenUrl` | String | *Optional.* The URL of your authorization server, which issues an optional refresh token. |
|`clientId` | String | The client ID that your system assigns to Adobe Experience Platform |
|`clientSecret` | String | The client secret that your system assigns to Adobe Experience Platform |
|`scope` | String | *Optional*. Set the scope of what the access token allows Experience Platform to perform on your resources. Example: "read, write". |

{style="table-layout:auto"}

## OAuth2 with Password Grant

For the OAuth2 Password grant (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.3)), Experience Platform requires the user's username and password. In the authentication flow, Experience Platform  exchanges these credentials for an access token and, optionally, a refresh token.
Adobe makes use of the standard inputs below to simplify destination configuration, with the ability to override values:

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
Password | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li><li><b>username</b></li><li><b>password</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

{style="table-layout:auto"}

To set up this authentication method for your destination, add the following lines to your configuration, in the /destinations endpoint: 

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "OAUTH2_PASSWORD",
      "accessTokenUrl": "https://api-stage.moveistar.com/portal/OAuth/token",// REQUIRED
      "refreshTokenUrl": "https://api-stage.moviestar.com/portal/OAuth/token", // will not be used since we will always get a brand new token
      "clientId": "my-client-id", // REQUIRED
      "clientSecret": "my-client-secret", // REQUIRED
      "scope": "read write",
    },

```

|Parameter | Type | Description|
|---------|----------|------|
|`authType` | String | Use "OAuth2" |
|`grant` | String | Use "OAUTH2_PASSWORD" |
|`accessTokenUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token.|
|`refreshTokenUrl` | String | *Optional.* The URL of your authorization server, which issues an optional refresh token. |
|`clientId` | String | The client ID that your system assigns to Adobe Experience Platform.  |
|`clientSecret` | String | The client secret that your system assigns to Adobe Experience Platform |
|`scope` | String | *Optional*. Set the scope of what the access token allows Experience Platform to perform on your resources. Example: "read, write". |

{style="table-layout:auto"}

## OAuth2 with Client Credential Grant

You can configure an Oauth2 Client Credential (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.3)) destination, which supports the following general inputs/outputs:

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
 Client Credential | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

 {style="table-layout:auto"}

To set up this authentication method for your destination, add the following lines to your configuration, in the /destinations endpoint:

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "OAUTH2_CLIENT_CREDENTIAL",
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

|Parameter | Type | Description|
|---------|----------|------|
|`authType` | String | Use "OAuth2" |
|`grant` | String | Use "OAUTH2_CLIENT_CREDENTIAL" |
|`accessTokenUrl` | String | The URL of your authorization server, which issues an access token and an optional refresh token.|
|`refreshTokenUrl` | String | *Optional.* The URL of your authorization server, which issues an optional refresh token. |
|`clientId` | String | The client ID that your system assigns to Adobe Experience Platform.  |
|`clientSecret` | String | The client secret that your system assigns to Adobe Experience Platform |
|`scope` | String | *Optional*. Set the scope of what the access token allows Experience Platform to perform on your resources. Example: "read, write". |

{style="table-layout:auto"}

## Customize your OAuth2 configuration

The configurations described in the sections above describe standard OAuth2 grants. However, the system designed by Adobe provides flexibility so you can use custom parameters for any variations in the OAuth2 grant. To customize the standard Oauth2 settings, use the `authenticationDataFields` parameters, as shown below.



```json

      "customerAuthenticationConfigurations": [
            {
                "authType": "OAUTH2",
                "options": {},
                "grant": "OAUTH2_AUTHORIZATION_CODE",
                "accessTokenUrl": "https://api.moviestar.com/OAuth/access_token",
                "authorizationUrl": "https://api.moviestar.com/OAuth/authorization",
                "scope": [
                    "read",
                    "write",
                    "delete"
                ],
                "refreshTokenUrl": "https://api.moviestar.com/OAuth/accessToken",
                "clientSecret": "https://va7stageactivationakv.vault.azure.net/secrets/96b45bb3465a49f0bc0801df32eedf3e/445842decc544a9aa04c5bf3ad0ad49b",
                "authenticationDataFields": [
                    {
                        "name": "refreshTokenExpiration",
                        "title": "Refresh Token Expires In",
                        "description": "Time in seconds when the refresh token will expire",
                        "type": "string",
                        "isRequired": false,
                        "readOnly": false,
                        "hidden": false,
                        "source": "CUSTOMER",
                        "authenticationResponsePath": "refresh_token_expires_in"
                    },
                    {
                        "name": "idToken",
                        "title": "ID Token",
                        "description": "ID Token",
                        "type": "string",
                        "isRequired": false,
                        "readOnly": false,
                        "hidden": false,
                        "source": "CUSTOMER",
                        "authenticationResponsePath": "idToken"
                    },
                    {
                        "name": "clientId",
                        "title": "Client Id",
                        "description": "Client Id",
                        "type": "string",
                        "isRequired": false,
                        "format": "password",
                        "readOnly": false,
                        "hidden": false,
                        "source": "PARTNER",
                        "value": "https://va7stageactivationakv.vault.azure.net/secrets/0107510ef6954f43af0036037da111fe/e38b468ee7dd416b95b71bf7341517e6"
                    }
                ]
            }


```

```json
      "authenticationDataFields": [
        {
            "name": "refreshToken",
            "value": "special_refresh_token"
        },
        {
            "name": "expiresIn",
            "value": 3600
        } 
      ]
```



You can use the following parameters in `authenticationDataFields` to customize the configuration

|Parameter | Type | Description|
|---------|----------|------|
|`authenticationDataFields.name` | String | The name of the custom field. |
|`authenticationDataFields.title` | String | A title that you can provide for the custom field. |
|`authenticationDataFields.description` | String | A description of the custom data field that you set up. |
|`authenticationDataFields.type` | String | Defines the type of the custom data field. <br> Accepted values: `string`, `boolean`, `integer` |
|`authenticationDataFields.isRequired` | Boolean | Specifies whether the custom data field is required in the authentication flow. |
|`authenticationDataFields.format` | String |  |
|`authenticationDataFields.readOnly` | Boolean |  |
|`authenticationDataFields.hidden` | Boolean |  |
|`authenticationDataFields.source` | String | Indicates whether the input comes from the partner (you) or from the user, when they set up your destination in Experience Platform.  |
|`authenticationDataFields.value` | String | The value of the custom data field. For example, if you  |
|`authenticationDataFields.authenticationResponsePath` | String |  |

{style="table-layout:auto"}

## Access token refresh

Adobe has designed a system which refreshes expired access tokens without requiring the user to log back into your platform. The system is able to generate a new token so that the activation to your destination will continue seamlessly for the customer.

To set up access token refresh, you may need to configure a templatized HTTP request that allows Adobe to get a new access token, using a refresh token. If the access token has expired, Adobe takes the templated request provided by you, adding the parameters you supplied. Use the `accessTokenRequest` parameter to configure an access token refresh mechanism.


```

        "customerAuthenticationConfigurations": [
            {
                "authType": "OAUTH2",
                "grant": "OAUTH2_CLIENT_CREDENTIALS",
                "accessTokenRequest": {
                    "destinationServerType": "URL_BASED",
                    "urlBasedDestination": {
                        "url": {
                            "templatingStrategy": "PEBBLE_V1",
                            "value": "https://{{authData.customerId}}.yourdestination.com/identity/oauth/token"
                        },
                        "splitUserById": false
                    },
                    "httpTemplate": {
                        "requestBody": {
                            "templatingStrategy": "PEBBLE_V1",
                            "value": "{{ formUrlEncode('grant_type', 'client_credentials', 'client_id', authData.clientId, 'client_secret', authData.clientSecret) | raw }}"
                        },
                        "httpMethod": "POST",
                        "contentType": "application/x-www-form-urlencoded",
                        "headers": []
                    },
                    "responseFields": [
                        {
                            "templatingStrategy": "PEBBLE_V1",
                            "value": "{{ response.body.expires_in }}",
                            "name": "expiresIn"
                        },
                        {
                            "templatingStrategy": "PEBBLE_V1",
                            "value": "{{ response.body.access_token }}",
                            "name": "accessToken"
                        }
                    ],
                    "validations": [
                        {
                            "valid": false,
                            "name": "access_token validation",
                            "actualValue": {
                                "templatingStrategy": "PEBBLE_V1",
                                "value": "{{response.body.access_token is empty }}"
                            },
                            "expectedValue": {
                                "templatingStrategy": "PEBBLE_V1",
                                "value": "false"
                            }
                        },
                        {
                            "valid": false,
                            "name": "response status",
                            "actualValue": {
                                "templatingStrategy": "PEBBLE_V1",
                                "value": "{{ response.status }}"
                            },
                            "expectedValue": {
                                "templatingStrategy": "PEBBLE_V1",
                                "value": "200"
                            }
                        }
                    ]
                },

```


## Templating conventions

Depending on your authentication customization, you might need to access data fields in the authentication response, as shown in the previous section. To do that, refer to the templating conventions below if you need to customize your OAuth2 implementation. 


Prefix | Description | Example |
---------|----------|---------|
 authData | Access any partner or customer data field's value. | ``{{ authData.accessToken }}`` |
 response.body | HTTP response body | ``{{ response.body.access_token }}`` |
 response.status | HTTP response status | ``{{ response.status }}`` |
 response.headers | HTTP response headers | ``{{ response.headers.server[0] }}`` |
 authContext | Access information about the current authentication attempt | <ul><li>`{{ authContext.sandboxName }} `</li><li>`{{ authContext.sandboxId }} `</li><li>`{{ authContext.imsOrgId }} `</li><li>`{{ authContext.client }} // the client executing the authentication attempt `</li></ul> |

 {style="table-layout:auto"}

## Next steps

By reading this article, you now have an understanding of the OAuth2 authentication patterns supported by Adobe Experience Platform and know how to configure your destination with OAuth2 authentication support. Next, you can set up your OAuth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
