---
description: The destinations configuration allows you to indicate basic information like your destination name, category, description, logo, and more. The settings in this configuration also determine how Experience Platform users authenticate to your destination, how it appears in the Experience Platform user interface and the identities that can be exported to your destination.
title: Destination configuration options for Destination SDK
exl-id: 2ecd0bdf-55c3-4946-a304-0147bc16ff39
---
# Destination configuration {#destination-configuration}

>[!IMPORTANT]
>
>* This feature is in limited beta and is only available to select [Adobe Exchange](https://partners.adobe.com/exchangeprogram/creativecloud.html) members. If you are interested in using Destination SDK, please contact Adobe Exchange. 
>* The documentation and the functionality are subject to change.

This configuration allows you to indicate basic information like your destination name, category, description, logo, and more. The settings in this configuration also determine how Experience Platform users authenticate to your destination, how it appears in the Experience Platform user interface and the identities that can be exported to your destination.

The functionality described in this document can be configured using the `/authoring/v1/destinations` API endpoint. Read [Destinations API endpoint operations](/help/destination-configuration-api.md) for a complete list of operations you can perform on the endpoint.


## Example configuration {#example-configuration}

Below is an example configuration for a fictional destination, Moviestar, which has endpoints in four locations on the globe. The destination belongs to the mobile destinations category. The sections further below break down how this template is constructed.

```json

{
  "name": "Moviestar",
  "description": "Moviestar is a fictional destination, used for this example.",
  "releaseNotes": "Test release",
  "status": "TEST",
  "customerAuthenticationConfigurations": [
    {
      "authType": "BEARER"
    }
  ],
  "customerDataFields": [
      {
        "name": "endpointsInstance",
        "type": "string",
        "title": "Select Endpoint",
        "description": "Moviestar manages several instances across the globe for REST endpoints that our customers are provisioned for. Select your endpoint in the dropdown list.",
        "isRequired": true,
        "enum": [
          "US",
          "EU",
          "APAC",
          "NZ"
        ]
      },
      {
      "name": "customerID",
      "type": "string",
      "title": "Moviestar Customer ID",
      "description": "Your customer ID in the Moviestar destination (e.g. abcdef).",
      "isRequired": true,
      "pattern": "^[A-Za-z]+$"
      }
    ],
  "uiAttributes": {
    "documentationLink": "http://www.adobe.com/go/destinations-moviestar-en",
    "category": "mobile",
    "iconUrl": "https://address-to-your-logo.png",
    "connectionType": "Server-to-server",
    "frequency": "Streaming"
  },
  "identityNamespaces": {
    "external_id": {
      "acceptsAttributes": true,
      "acceptsCustomNamespaces": true
    },
    "another_id": {
      "acceptsAttributes": true,
      "acceptsCustomNamespaces": true
    }
  },
  "destinationDelivery": [
    {
      "authenticationRule": "CUSTOMER_AUTHENTICATION",
      "destinationServerId": "9c77000a-4559-40ae-9119-a04324a3ecd4"
    }
  ],
  "inputSchemaId": "cc8621770a9243b98aba4df79898b1ed"
}

```

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Indicates the title of your destination in the Experience Platform catalog |
|`description` | String | Provide a description that Adobe will use in the Experience Platform destinations catalog for your destination card. Aim for no more than 4-5 sentences. |
|`releaseNotes` | String | This field is not necessary in the beta phase of Destination SDK. |
|`status` | String | Indicates the lifecycle status of the destination card. Accepted values are `TEST`, `PUBLISHED`, and `DELETED`. Use `TEST` when you first configure your destination. |

## Customer authentication configurations {#customer-authentication-configurations}

This section generates the account page in Experience Platform user interface, where users connect Experience Platform to the accounts they have with your destination. Depending on which authentication option you indicate in the `authType` field, the Experience Platform page is generated for the users as follows:

**Bearer authentication**

Users are required to input the bearer token that they obtain from your destination.

![UI render with bearer authentication](/help/assets/bearer-authentication-ui.png)

**OAuth 2 authentication**

Users select **[!UICONTROL Connect to destination]** to trigger the OAuth 2 authentication flow to your destination.

![UI render with OAuth 2 authentication](/help/assets/oauth2-authentication-ui.png)


|Parameter | Type | Description|
|---------|----------|------|
|`customerAuthenticationConfigurations` | String | Indicates the configuration used to authenticate Experience Platform customers to your server. See `authType` below for accepted values. |
|`authType` | String | Accepted values are `OAUTH2, BASIC, BEARER`. <br><ul><li> If your destination supports OAuth 2 authentication, select the `OAUTH2` value and add the required fields for OAuth 2, as shown in the Destination SDK OAuth 2 authentication page. Additionally, you should select `authenticationRule=CUSTOMER_AUTHENTICATION` in the [destination delivery section](/help/destination-configuration.md). </li><li>For basic or bearer authentication, select `BASIC` or `BEARER` and select `authenticationRule=CUSTOMER_AUTHENTICATION` in the [destination delivery section](/help/destination-configuration.md).</li></ul> |

## Customer data fields {#customer-data-fields}

This section allows partners to introduce custom fields. In the example configuration above, `customerDataFields` requires users to select an endpoint in the authentication flow and indicate their customer ID with the destination. The configuration is reflected in the authentication flow as shown below:

![Custom field authentication flow](/help/assets/custom-field-authentication-flow.png)

|Parameter | Type | Description|
|---------|----------|------|
|`name` | String | Provide a name for the custom field you are introducing. |
|`type` | String | Indicates what type of custom field you are introducing. Accepted values are `string`, `object`, `integer` |
|`title` | String | Indicates the name of the field, as it is seen by customers in the Experience Platform user interface |
|`description` | String | Provide a description for the custom field. |
|`isRequired` | Boolean | Indicates if this field is required in the destination setup workflow. |
|`enum` | String | Renders the custom field as a dropdown menu and lists the options available to the user. |
|`pattern` | String | Enforces a pattern for the custom field, if needed. Use regular expressions to enforce a pattern. For example, if your customer IDs don't include numbers or underscores, enter `^[A-Za-z]+$` in this field. |

## UI attributes {#ui-attributes}

This section refers to the UI elements in the configuration template above that Adobe should use for your destination in the Adobe Experience Platform user interface. See below:

|Parameter | Type | Description|
|---------|----------|------|
|`documentationLink` | String | Refers to the documentation page in the [Destinations Catalog](https://experienceleague.adobe.com/docs/experience-platform/destinations/catalog/overview.html?lang=en#catalog) for your destination. Use `http://www.adobe.com/go/destinations-YOURDESTINATION-en`, where `YOURDESTINATION` is the name of your destination. For a destination called Moviestar, you would use `http://www.adobe.com/go/destinations-moviestar-en` |
|`category` | String | Refers to the category assigned to your destination in Adobe Experience Platform. For more information, read [Destination Categories](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/destination-types.html?lang=en#destination-categories). Use one of the following values: `adobeSolutions, advertising, analytics, cdp, cloudStorage, crm, customerSuccess, database, dmp, ecommerce, email, emailMarketing, enrichment, livechat, marketingAutomation, mobile, personalization, protocols, social, streaming, subscriptions, surveys, tagManagers, voc, warehouses, payments` |
|`iconUrl` | String | Provide the logo that Adobe will display in the Adobe Experience Platform destinations catalog for your destination card. |
|`connectionType` | String | In the beta release phase of Destination SDK, `Server-to-server` is the only available option. |
|`frequency` | String | In the beta release phase of Destination SDK, `Streaming` is the only available option. |

## Identities and attributes {#identities-and-attributes}

The parameters in this section determine how the target identities and attributes are populated in the mapping step of the Experience Platform user interface, where users map their XDM schemas to the schema in your destination.

Adobe needs to know which [!DNL Platform] identities customers will be able to export to your destination. Some examples are [!DNL Experience Cloud ID], hashed email, device ID ([!DNL IDFA], [!DNL GAID]). These values are [!DNL Platform] identity namespaces that customers can map to identity namespaces from your destination.

Identity namespaces do not require a 1-to-1 correspondence between [!DNL Platform] and your destination.
For instance, customers could map a [!DNL Platform] [!DNL IDFA] namespace to an [!DNL IDFA] namespace from your destination, or they can map the same [!DNL Platform] [!DNL IDFA] namespace to a [!DNL Customer ID] namespace in your destination.

 Read more in the [Identity Namespace overview](https://experienceleague.adobe.com/docs/experience-platform/identity/namespaces.html?lang=en).

![Render target identites in the UI](/help/assets/target-identities-ui.png) 

|Parameter | Type | Description|
|---------|----------|------|
|`acceptsAttributes` | Boolean | Indicates if your destination accepts standard profile attributes. Usually, these attributes are highlighted in our partners' documentation. |
|`acceptsCustomNamespaces` | Boolean | Indicates if customers can set up custom namespaces in your destination. |
|`allowedAttributesTransformation` | String | _Not shown in example configuration_. Used, for example, when the [!DNL Platform] customer has plain email addresses as an attribute and your platform only accepts hashed emails. This is where you would provide the transformation that needs to be applied (for example, transform the email to lowercase, then hash).   |
|`acceptedGlobalNamespaces` | - | _Not shown in example configuration_. Used for cases when your platform accepts [standard identity namespaces](https://experienceleague.adobe.com/docs/experience-platform/identity/namespaces.html?lang=en#standard-namespaces) (for example, IDFA), so you can restrict Platform users to only selecting these identity namespaces. |

## Destination delivery {#destination-delivery}

|Parameter | Type | Description|
|---------|----------|------|
|`authenticationRule` | String | Indicates how [!DNL Platform] customers connect to your destination. Accepted values are `CUSTOMER_AUTHENTICATION`, `PLATFORM_AUTHENTICATION`, `NONE`. <br> <ul><li>Use `CUSTOMER_AUTHENTICATION` if Platform customers log into your system via a username and password, a bearer token, or another method of authentication. For example, you would select this option if you also selected `authType: OAUTH2` or `authType:BEARER` in `customerAuthenticationConfigurations`. </li><li> Use `PLATFORM_AUTHENTICATION` if there is a global authentication system between Adobe and your destination and the [!DNL Platform] customer does not need to provide any authentication credentials to connect to your destination. In this case, you must create a credentials object using the [Credentials](/help/credentials-configuration.md) configuration. </li><li>Use `NONE` if no authentication is required to send data to your destination platform. </li></ul> |
|`destinationServerId` | String | The `instanceId` of the [destination server template](/help/destination-server-api.md) used for this destination. |
|`inputSchemaId` | String | This field is not required in the beta phase of Destination SDK. |
|`backfillHistoricalProfileData` | Boolean | Controls whether historical profile data is exported when segments are activated to the destination. <br> <ul><li> `true`: [!DNL Platform] sends the historical user profiles that qualified for the segment before the segment is activated. </li><li> `false`: [!DNL Platform] only includes user profiles that qualify for the segment after the segment is activated. </li></ul> |


<!--

commenting out the `backfillHistoricalProfileData` parameter, which will only be used after an April release

|`backfillHistoricalProfileData` | Boolean | Controls whether historical profile data is exported when segments are activated to the destination. <br> <ul><li> `true`: [!DNL Platform] sends the historical user profiles that qualified for the segment before the segment is activated. </li><li> `false`: [!DNL Platform] only includes user profiles that qualify for the segment after the segment is activated. </li></ul> |

-->
