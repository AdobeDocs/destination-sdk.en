---
description: This page describes the various OAuth2 authentication flows supported by Destination SDK, and provides instructions to set up OAuth2 authentication for your destination.
title: OAuth2 authentication
---
# OAuth2 authentication 


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview {#overview}

Use Destination SDK to allow Adobe Experience Platform to connect to your destination by using the [OAuth2 authentication framework](https://tools.ietf.org/html/rfc6749).

This page describes the various OAuth2 authentication flows supported by Destination SDK, and provides instructions to set up OAuth2 authentication for your destination.

## How to add OAuth2 authentication details to your destination configuration {#how-to-setup}

### Prerequisites in your system {#prerequisites}

As a first step, you must create an app in your system for Adobe Experience Platform, or otherwise register Experience Platform in your system. The goal is to generate a client ID and client secret, which are needed to authenticate Experience Platform to your destination. As part of this configuration in your system, you need an Adobe Experience Platform OAuth2 redirect/callback URL.

>[!IMPORTANT]
>
>The step to register a redirect/callback URL for Adobe Experience Platform in your system is required only for the [Oauth2 with Authorization Code](/help/oauth2-authentication.md#authorization-code) grant type. For the other two supported grant types (password and client credentials), you can skip this step.

Redirect/callback URL | Environment |
---------|----------|
 `https://platform.adobe.io/data/core/activation/oauth/api/v1/callback` | Production |
 `https://platform-stage.adobe.io/data/core/activation/oauth/api/v1/callback` | Staging |

 {style="table-layout:auto"}

At the end of this step, you should have:
* A client ID;
* A client secret;
* Adobe's callback URL (for the authorization code grant).


### What you need to do in Destination SDK {#to-do-in-destination-sdk}

To set up OAuth2 authentication for your destination in Experience Platform, you must add your OAuth2 details to the [destination configuration](/help/destination-configuration.md), under the `customerAuthenticationConfigurations` parameter, by using the `platform.adobe.io/data/core/activation/authoring/v1/destinations` API endpoint. See the [example configuration](/help/destination-configuration.md#example-configuration). Specific instructions about which fields you need to add to your configuration template, depending on your OAuth2 authentication grant type, are further below on this page.

## Supported OAuth2 grant types {#oauth2-grant-types}

Experience Platform supports the three OAuth2 grant types in the table below. If you have a custom OAuth2 setup, Adobe is able to support it with the help of custom fields in your integration. Refer to the sections for each grant type for more information.


>[!IMPORTANT]
>
>* You provide the input parameters as instructed in the sections below. Adobe-internal systems produce the output parameters, which are used to connect and maintain authentication to your destination.
>* The input parameters highlighted in bold in the table are required parameters in the Oauth2 authentication flow. The other parameters are optional. There are other custom input parameters that are not shown here, but are described at length in the sections [Customize your OAuth2 configuration](/help/oauth2-authentication.md#customize-configuration) and [Access token refresh](/help/oauth2-authentication.md#access-token-refresh). 

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
 Authorization Code | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>authorizationUrl</b></li><li><b>accessTokenUrl</b></li><li>refreshTokenUrl</li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |
 Password | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li><li><b>username</b></li><li><b>password</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |
 Client Credential | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

 {style="table-layout:auto"}

The above table lists the fields that are used in standard OAuth 2 flows. In addition to these standard fields, various partner integrations may require additional inputs and outputs. Adobe has designed a flexible OAuth 2 authentication/authorization framework for Destination SDK that can handle variations to the above standard fields pattern while supporting a mechanism to automatically regenerate invalid outputs, such as expired access tokens.

The output in all cases includes an access token, which is be used by Experience Platform to authenticate and maintain authentication to your destination.

The system that Adobe has designed for OAuth2 authentication:
* Supports all three OAuth 2 grants while accounting for any variations in them, such as additional data fields, non-standard API calls, and more.
* Supports access tokens with varying lifetime values, be it 90 days, 30 minutes, or any other lifetime value that you specify.
* Supports OAuth2 authorization flows with or without refresh tokens.

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
      "refreshTokenUrl": "https://www.moviestar.com/dialog/OAuth",
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

To set up this authentication method for your destination, add the following lines to your configuration, in the `/destinations` [endpoint](/help/destination-configuration.md):

``` json

{
//...
  "customerAuthenticationConfigurations": [
    {
      "authType": "OAuth2",
      "grant": "OAUTH2_PASSWORD",
      "accessTokenUrl": "https://www.moviestar.com/dialog/OAuth",// REQUIRED
      "refreshTokenUrl": "https://www.moviestar.com/dialog/OAuth", // will not be used since we will always get a brand new token
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

You can configure an Oauth2 Client Credential (read the [RFC standards specs](https://tools.ietf.org/html/rfc6749#section-4.3)) destination, which supports the standard inputs and outputs listed below. You have the ability to customize the values. See [Customize your OAuth2 configuration](/help/oauth2-authentication.md#customize-configuration) for details.

OAuth 2 Grant | Inputs | Outputs |
---------|----------|---------|
 Client Credential | <ul><li><b>clientId</b></li><li><b>clientSecret</b></li><li>scope</li><li><b>accessTokenUrl</b></li></ul> | <ul><li><b>accessToken</b></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul> |

 {style="table-layout:auto"}

To set up this authentication method for your destination, add the following lines to your configuration, in the `/destinations` [endpoint](/help/destination-configuration.md):

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

## Customize your OAuth2 configuration {#customize-configuration}

The configurations described in the sections above describe standard OAuth2 grants. However, the system designed by Adobe provides flexibility so you can use custom parameters for any variations in the OAuth2 grant. To customize the standard OAuth2 settings, use the `authenticationDataFields` parameters, as shown in the examples below.

Example 1: Using `authenticationDataFields` 

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
                "clientSecret": "client-secret-here",
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
                        "value": "client-id-value"
                    }
                ]
            }


```

**Example 2: Using `authenticationDataFields` to provide a special refresh token**  
In this example, a partner sets up their destination to provide a special refresh token. Furthermore, the expiration date for access tokens is not returned in the API response so they can hardcode a default value:

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

**Example 3: The user inputs client ID and client secret when they configure the destination**
In this example, instead of creating a global client ID and client secret as shown in the section [Prerequisites in your system](/help/oauth2-authentication.md#prerequisites), the customer is required to input client ID, client secret, and account ID (the ID that the customer uses to log into the destination)

```json

{
    //...
    "customerAuthenticationConfigurations": [
        {
            "authType": "OAUTH2",
            "grant": "CLIENT_CREDENTIAL",
            "authenticationDataFields": [
                {
                    "name": "clientId",
                    "title": "Client ID",
                    "description": "Client ID",
                    "type": "string",
                    "isRequired": true,
                    "fieldType": "CUSTOMER"
                },
                {
                    "name": "clientSecret",
                    "title": "Client Secret",
                    "description": "Client Secret",
                    "type": "string",
                    "isRequired": true,
                    "format": "password",
                    "fieldType": "CUSTOMER"
                },
                {
                    "name": "moviestarId",
                    "title": "Moviestar ID",
                    "description": "Moviestar ID",
                    "type": "string",
                    "isRequired": true,
                    "fieldType": "CUSTOMER"
                }
            ],
            "accessTokenRequest": {
                "destinationServerType": "URL_BASED",
                "urlBasedDestination": {
                    "url": {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "https://{{ authData.accountId }}.yourdestination.com/identity/oauth/token"
                    }
                },
                "httpTemplate": {
                    "requestBody": {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "{{ formUrlEncode('grant_type', 'client_credentials', 'client_id', authData.clientId, 'client_secret', authData.clientSecret) | raw }}"
                    },
                    "httpMethod": "POST",
                    "contentType": "application/x-www-form-urlencoded"
                },
                "responseFields": [
                    {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "{{ response.body.access_token }}",
                        "name": "accessToken"
                    },
                    {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "{{ response.body.scope }}",
                        "name": "scope"
                    },
                    {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "{{ response.body.token_type }}",
                        "name": "tokenType"
                    },
                    {
                        "templatingStrategy": "PEBBLE_V1",
                        "value": "{{ response.body.expires_in }}",
                        "name": "expiresIn"
                    }
                ]
            }
        }
    ]
//...
}

```



You can use the following parameters in `authenticationDataFields` to customize your OAuth2 configuration:

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

## Access token refresh {#access-token-refresh}

Adobe has designed a system which refreshes expired access tokens without requiring the user to log back into your platform. The system is able to generate a new token so that the activation to your destination will continue seamlessly for the customer.

To set up access token refresh, you may need to configure a templatized HTTP request that allows Adobe to get a new access token, using a refresh token. If the access token has expired, Adobe takes the templated request provided by you, adding the parameters you supplied. Use the `accessTokenRequest` parameter to configure an access token refresh mechanism.


```json

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

You can use the following parameters in `accessTokenRequest` to customize your token refresh process:

|Parameter | Type | Description|
|---------|----------|------|
|`accessTokenRequest.destinationServerType` | String | Use `URL_BASED` |
|`accessTokenRequest.urlBasedDestination.url.templatingStrategy` | String | Use `PEBBLE_V1`.  |
|`accessTokenRequest.urlBasedDestination.url.value` | String | The URL where Experience Platform requests the access token. |
|`accessTokenRequest.urlBasedDestination.splitUserById` | String |  |
|`accessTokenRequest.httpTemplate.requestBody.templatingStrategy` | String | Use `PEBBLE_V1`. |
|`accessTokenRequest.httpTemplate.requestBody.value` | String | Use templating language to customize fields in the HTTP request to the access token endpoint. For information on how to use templating to customize fields, refer to the [templating conventions](/help/oauth2-authentication.md#templating-conventions) section. |
|`accessTokenRequest.httpTemplate.httpMethod` | String | Specifies the HTTP method used to call your access token endpoint. Use `POST`. |
|`accessTokenRequest.httpTemplate.contentType` | String | Specifies the content type of the HTTP call to your access token endpoint. Use `application/x-www-form-urlencoded`. |
|`accessTokenRequest.httpTemplate.headers` | String | Specifies if any headers should be added to the HTTP call to your access token endpoint. |
|`accessTokenRequest.responseFields.templatingStrategy` | String | Use `PEBBLE_V1`. |
|`accessTokenRequest.responseFields.value` | String | Use templating language to access fields in the HTTP response from your access token endpoint. For information on how to use templating to customize fields, refer to the [templating conventions](/help/oauth2-authentication.md#templating-conventions) section. |
|`accessTokenRequest.validations.valid` | Boolean |  |
|`accessTokenRequest.responseFields.name` | String |  |
|`accessTokenRequest.responseFields.actualValue.templatingStrategy` | String |  |
|`accessTokenRequest.responseFields.actualValue.value` | String |  |
|`accessTokenRequest.responseFields.expectedValue.templatingStrategy` | String |  |
|`accessTokenRequest.responseFields.expectedValue.value` | String |  |

{style="table-layout:auto"}

## Templating conventions {#templating-conventions}

Depending on your authentication customization, you might need to access data fields in the authentication response, as shown in the previous section. To do that, refer to the templating conventions below to customize your OAuth2 implementation. 


Prefix | Description | Example |
---------|----------|---------|
 authData | Access any partner or customer data field's value. | ``{{ authData.accessToken }}`` |
 response.body | HTTP response body | ``{{ response.body.access_token }}`` |
 response.status | HTTP response status | ``{{ response.status }}`` |
 response.headers | HTTP response headers | ``{{ response.headers.server[0] }}`` |
 authContext | Access information about the current authentication attempt | <ul><li>`{{ authContext.sandboxName }} `</li><li>`{{ authContext.sandboxId }} `</li><li>`{{ authContext.imsOrgId }} `</li><li>`{{ authContext.client }} // the client executing the authentication attempt `</li></ul> |

 {style="table-layout:auto"}

## Next steps {#next-steps}

By reading this article, you now have an understanding of the OAuth2 authentication patterns supported by Adobe Experience Platform and know how to configure your destination with OAuth2 authentication support. Next, you can set up your OAuth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
