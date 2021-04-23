---
description: This page describes how to authenticate and start using Adobe Destination SDK. It includes instructions on how to obtain Adobe I/O authentication credentials, a sandbox name and ID, and how to be added to an allowed partner list.
title: Oauth authentication
---
# Oauth authentication


>[!IMPORTANT]
>
>* Contact [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) if you are interested in using Destination SDK.
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Overview 

Adobe Experience Platform supports various forms of authentication, which allow it to connect to destinations. Use the Destination SDK to set up Oauth authentication between Adobe Experience Platform and your destination

## How to add Oauth details to your Destination configuration

You need to add your Oauth configuration in the /destinations endpoint, using the 

<table class="relative-table wrapped confluenceTable"><colgroup><col style="width: 26.2554%;" /><col style="width: 33.8594%;" /><col style="width: 39.8852%;" /></colgroup><tbody><tr><th class="confluenceTh">OAuth 2 Grant</th><th class="confluenceTh">Inputs</th><th class="confluenceTh">Outputs</th></tr><tr><td class="confluenceTd">Authorization Code</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>authorizationUrl</strong></li><li><strong>accessTokenUrl</strong></li><li>refreshTokenUrl</li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Password</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li><li><strong>username</strong></li><li><strong>password</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr><tr><td class="confluenceTd">Client Credential</td><td class="confluenceTd"><ul><li><strong>clientId</strong></li><li><strong>clientSecret</strong></li><li>scope</li><li><strong>accessTokenUrl</strong></li></ul></td><td class="confluenceTd"><ul><li><strong>accessToken</strong></li><li>expiresIn</li><li>refreshToken</li><li>tokenType</li></ul></td></tr></tbody></table>


## Next steps

By reading this article, you now have an understanding of the Oauth authentication patterns supported by Adobe Experience Platform. Next, you can set up your Oauth2-supported destination using the Destination SDK. Read [Use the Destination SDK to configure your destination](./configure-destination-instructions.md) for next steps.
