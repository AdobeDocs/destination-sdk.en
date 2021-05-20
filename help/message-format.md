---
description: Use the content on this page together with the rest of the configuration options for partner destinations. This page addresses the messaging format of data exported from Adobe Experience Platform to destinations, while the other page addresses specifics about connecting and authenticating to your destination.
seo-description: Use the content on this page together with the rest of the configuration options for partner destinations. This page addresses the messaging format of data exported from Adobe Experience Platform to destinations, while the other page addresses specifics about connecting and authenticating to your destination.
seo-title: Message format
title: Message format
exl-id: bd348e99-56f7-449e-a639-8da6fd2cafaf
---
# Message format


>[!IMPORTANT]
>
>* The content on this page is Adobe confidential information, please do not share outside of your company.
>* The Adobe Experience Platform Destination SDK is currently an alpha release. The documentation and the functionality are subject to change.

## Prerequisites - Adobe Experience Platform concepts {#prerequisites}

To understand the process on the Adobe side, please familiarize yourself with the following Experience Platform concepts:

* Experience Data Model (XDM). [XDM overview](https://docs.adobe.com/content/help/en/experience-platform/xdm/home.html) and  [How to create an XDM schema in Adobe Experience Platform](https://docs.adobe.com/content/help/en/experience-platform/xdm/tutorials/create-schema-ui.html).
* Class. [How to assign a class to a schema](https://docs.adobe.com/content/help/en/experience-platform/xdm/tutorials/create-schema-ui.html#assign-a-class).
* Field group. [Field group definition](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/composition.html#field-group) and [more information about field groups](https://experienceleague.adobe.com/docs/experience-platform/xdm/tutorials/create-schema-ui.html?lang=en#field-group).
* IdentityMap. The identity map represents a map of all end-user identities in Adobe Experience Platform. Refer to `xdm:identityMap` in the [XDM field dictionary](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/field-dictionary.html).
* SegmentMembership. The [segmentMembership](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/field-dictionary.html) XDM attribute informs which segments a profile is a member of. For the three different values in the `status` field, read the documentation on [Segment Membership Details schema field group](https://experienceleague.adobe.com/docs/experience-platform/xdm/field-groups/profile/segmentation.html).

## Overview {#overview}

Use the content on this page together with the rest of the [configuration options for partner destinations](/help/configuration-options.md). This page addresses the messaging format of data exported from Adobe Experience Platform to destinations, while the other page addresses specifics about connecting and authenticating to your destination.

Adobe Experience Platform exports data to a significant number of destinations, in various data formats. Some examples of destination types are advertising platforms (Google), social networks (Facebook), cloud storage locations (Amazon S3, Azure Event Hubs).

Experience Platform can adjust the exported message format to match the expected format on your side. To understand this customization, the following concepts are important:
* The customer (1) and partner (2) XDM schema in Adobe Experience Platform
* The message format on the partner side (3), and 
* The transformation layer between the two.

![Schema to JSON transformation](/help/assets/transformations-3-steps.png)

Experience Platform uses schemas to describe the structure of data in a consistent and reusable way.

Users who want to activate data to your destination need to map the fields that they use for their datasets in Experience Platform to a schema that translates to your destination's expected format. Adobe will create a custom field group for your company to add to the partner schema. The fields in the field group depend on the profile attribute fields that you can receive.

**Customer XDM schema (1)**: This refers to the schema that a customer uses in Experience Platform. In Experience Platform, in the [activate destination workflow](https://experienceleague.adobe.com/docs/experience-platform/rtcdp/destinations/dest-tutorials/activate-destinations.html), customers would map fields from their schema to the partner schema that Adobe create for your destination (2).

**Partner XDM schema (2)**: Based on the JSON standard schema (3) that you share with Adobe, the team at Adobe will create a custom schema for your destination. Note that in a [future phase of the project](/help/overview.md#phased-approach), you will be able to create the custom schema for your destination on your own.

**JSON standard schema of your destination profile attributes (3)**: Please share with us a [JSON schema](https://json-schema.org/learn/miscellaneous-examples.html) of all the profile attributes that your platform supports and their types (for example: object, string, array). Example fields that your destination could support could be `firstName`, `lastName`, `gender`, `email`, `phone`,  `productId`, `productName`, and so on.

Based on the schema transformations described above, here is how the structure of a message changes between the XDM schema and the sample schema on the partner side:

![Transformations message example](/help/assets/transformations-with-examples.png)

<!--

**Message output (4)**: This represents a standard message that your destination can receive. In the example above, the output expected by the destination is JSON structured HTTP. However, the intention is to support any kind of destination format, for example CSV, XML, FORM-URL-ENCODED etc.

-->

<br>&nbsp;


## Getting started - transforming three basic attributes {#getting-started}

To demonstrate the transformation process, the example below uses three common profile attributes in Adobe Experience Platform: **first name**, **last name**, and **email address**.

>[!NOTE]
>
>The customer maps the attributes from the Adobe XDM schema to the partner XDM schema in the Adobe Experience Platform UI, in the **Mapping** step of the [activate destination workflow](https://experienceleague.adobe.com/docs/experience-platform/destinations/ui/activate-destinations.html#identity-mapping).

Let's say your platform can receive a message format like:

```curl

POST https://YOUR_REST_API_URL/users/
Content-Type: application/json
Authorization: Bearer YOUR_REST_API_KEY

{
  "attributes":
    {
      "first_name": "Yours",
      "last_name": "Truly",
      "external_id": "yourstruly@adobe.com"
    }
}

```

Considering the message format, the corresponding transformations are as follows:

Attribute in partner XDM schema on the Adobe side| Transformation | Attribute in HTTP message on your side
---------|----------|---------|
 `_your_custom_schema.firstName` |` attributes.first_name` | `first_name` |
 `_your_custom_schema.lastName` | `attributes.last_name` | `last_name` |
 `personalEmail.address` | `attributes.external_id` | `external_id` |



<!--

Note that the `identityMap` attribute in Adobe Experience Platform can contain several identities. In the example in the table, the first identity is mapped to the `external_id` attribute on your side.

-->


## Using a templating language for the identity, attributes, and segment membership transformations {#using-templating}

Adobe uses a templating language similar to [Jinja](https://jinja.palletsprojects.com/en/2.11.x/) to transform the fields from the XDM schema into a format supported by your destination.

This section provides several examples of how these transformations are made, from the input XDM schema, through the template, and outputting into payload formats accepted by your destination. The examples below are sorted by increasing complexity, as follows:

1. Simple transformation examples. Learn how templating works with simple transformations for [Profile attributes](/help/message-format.md#attributes), [Segment membership](/help/message-format.md#segment-membership), and [Identity](/help/message-format.md#identities) fields.
2. Increased complexity examples of templates that combine the fields above: [Create a template that sends segments and identities](/help/message-format.md#segments-and-identities) and [Create a template that sends segments, identities, and profile attributes](/help/message-format.md#segments-identities-attributes).
3. Deepest dive, showing two examples of templates from industry partners.

<!--

This section describes the templates that you can use for the transformations mentioned above.

```python

{% for user in users %}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{% endfor %}

```

Some Pebble features are not supported, such as `{% include % }` and `{% extends % }`. 

### Context

The context provided to the template contains input  (the profiles / data that are exported in this call) and destination (data about the destination that data is sent to, valid for all profiles).

#### Input

There are two kinds of input, single-profile and multi-profile. Single-profile inputs have the following properties:

* `profile`: the profile as a [JsonNode](http://fasterxml.github.io/jackson-databind/javadoc/2.11/com/fasterxml/jackson/databind/node/JsonNodeType.html)
* `identityNamespace`: *Optional*. For destinations that only accept one identity per request, the identity namespace of the one identity in this request.
* `id`: *Optional*. For destinations that only accept one identity per request, the one identity in this request. This is not strictly required, as that identity could be accessed through profile.identityMap, but it simplifies the templates.

Multi-profile inputs have:

* `profiles`: A list of profiles, each one represented as a [JsonNode](http://fasterxml.github.io/jackson-databind/javadoc/2.11/com/fasterxml/jackson/databind/node/JsonNodeType.html)
* `aggregationKey`: *Optional*. For destinations that require/accept aggregation by more than orderId, the list of properties and their values used for aggregating these particular profiles.


The profiles will be in the partner schema for the destination, so whatever properties are there in the schema can be accessed in the template (including `identityMap` and `segmentMembership` if present.

#### Destination

* id - the id of the data export order
* `segmentAliases` - map from segment IDs in the Experience Platform Unified Profile Service namespace to segment aliases in your system. Example usage:

  ```python

  {% for segment in input.profile.segmentMembership.ups %}
      segmentAlias = "{{ destination.segmentAliases[segment.key] }}";
  {% endfor %}

  ```

  This is a shortcut for `destination.allSegmentAliases.ups[segment.key]`.

* `allSegmentAliases`: a map from segment IDs in all Experience Platform namespaces to segment aliases in your system. The example above is equivalent to:  

  ```python

  {% for segment in input.profile.segmentMembership.ups %}
      segmentAlias = "{{ destination.allSegmentAliases.ups[segment.key] }}";
  {% endfor %}

  ```


* further parameters will follow  



### Adobe-specific Functions

In addition to the functions already present in Pebble, the Adobe Destination SDK provides the following:

* `now()` - the current date and time, useful for destinations that require a timestamp
* `addedSegments(listOfSegments)` - returns just the segments that have status `realized` or `existing`. For example:

  ```python

  {% for segment in addedSegments(input.profile.segmentMembership.ups) %}
    ...
  {% endfor %}

  ```

  
  This is also available as a filter:

  ```python

  {% for segment in input.profile.segmentMembership.ups | added %}
    ...
  {% endfor %}

  ```

* `removedSegments(listOfSegments)` - same as above, for segments that are exited

-->



### Profile Attributes {#attributes}

To transform the profile attributes exported to your destination, see the JSON and code samples below.


>[!IMPORTANT]
>
>For a list of all available profile attributes in Adobe Experience Platform, see the [XDM field dictionary](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/field-dictionary.html). 



**Input**

```json

{
  "attributes": {
    "firstName": {
      "value": "Hermione"
    },
    "birthDate": {}
  }
}

```

**Template**

>[!IMPORTANT]
>
>For all templates that you use, you must escape the illegal characters, such as double quotes `""` before inserting the template in the [destination configuration](/help/configuration-options.md). For more information on escaping double quotes, see Chapter 9 in the [JSON standard](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).

```python

{
    {% for attribute in input.profile.attributes %}
        "{{ attribute.key }}":
            {% if attribute.value is empty %}
                null
            {% else %}
                "{{ attribute.value.value }}"
            {% endif %}
        {% if not loop.last %},{% endif %}
    {% endfor %}
}

```

**Result**


```json

{
    "firstName": "Hermione",
    "birthDate": null
}

```

### Segment membership {#segment-membership}

The [segmentMembership](https://docs.adobe.com/content/help/en/experience-platform/xdm/schema/field-dictionary.html) XDM attribute informs which segments a profile is a member of.
For the three different values in the `status` field, read the documentation on [Segment Membership Details schema field group](https://experienceleague.adobe.com/docs/experience-platform/xdm/field-groups/profile/segmentation.html).

**Input**

```json

{
  "segmentMembership": {
    "ups": {
      "36a51c13-9dd6-4d2c-8aa3-07d785ea5075": {
        "lastQualificationTime": "2019-11-20T13:15:49Z",
        "status": "realized"
      },
      "788d8874-8007-4253-92b7-ee6b6c20c6f3": {
        "lastQualificationTime": "2019-11-20T13:15:49Z",
        "status": "existing"
      },
      "8f812592-3f06-416b-bd50-e7831848a31a": {
        "lastQualificationTime": "2019-11-20T13:15:49Z",
        "status": "exited"
      }
    }
  }
}

```

**Template**


>[!IMPORTANT]
>
>For all templates that you use, you must escape the illegal characters, such as double quotes `""` before inserting the template in the [destination configuration](/help/configuration-options.md). For more information on escaping double quotes, see Chapter 9 in the [JSON standard](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).

```python

{
    "AdobeExperiencePlatformSegments": {
        "add": [
            {% for segment in input.profile.segmentMembership.ups | added %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ],
        "remove": [
            {# Alternative syntax for filtering segments by status: #}
            {% for segment in removedSegments(input.profile.segmentMembership.ups) %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ]
    }
}

```

**Result**

```json

{
    "AdobeExperiencePlatformSegments": {
        "add": [
            "36a51c13-9dd6-4d2c-8aa3-07d785ea5075",
            "788d8874-8007-4253-92b7-ee6b6c20c6f3"
        ],
        "remove": [
            "8f812592-3f06-416b-bd50-e7831848a31a"
        ]
    }
}

```

### Identities {#identities}

For information about identities in Experience Platform, see the [Identity namespace overview](https://docs.adobe.com/content/help/en/experience-platform/identity/namespaces.html).

**Input**

```json

{
    "identityMap": {
        "email": [
            {
                "id": "johndoe@example.com"
            },
            {
                "id": "jd@example.com"
            }
        ],
        "external_id": [
            {
                "id": "123456"
            }
        ]
    }
}

```

**Template**


>[!IMPORTANT]
>
>For all templates that you use, you must escape the illegal characters, such as double quotes `""` before inserting the template in the [destination configuration](/help/configuration-options.md). For more information on escaping double quotes, see Chapter 9 in the [JSON standard](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).

```python

{
    "identities": [
        {% for email in input.profile.identityMap.email %}
            {
                "type": "email",
                "id": "{{ email.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
         
        {# Add a comma only if you have both emails and external_ids. #}
        {% if input.profile.identityMap.email is not empty and input.profile.identityMap.external_id is not empty %}
            ,
        {% endif %}
         
        {% for external in input.profile.identityMap.external_id %}
            {
                "type": "external_id",
                "id": "{{ external.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
    ]
}

```

**Result**

```json

{
    "identities": [
        {
            "type": "email",
            "id": "johndoe@example.com"
        },
        {
            "type": "email",
            "id": "jd@example.com"
        },
        {
            "type": "external_id",
            "id": "123456"
        }
    ]
}

```


### Create a template that sends segments and identities {#segments-and-identities}

This section provides an example of a commonly used transformation between the Adobe XDM schema and partner destination schema.
The example below shows you how to transform the segment membership and identities format and output them to your destination.

**Input**

```json

{
    "identityMap": {
        "email": [
            {
                "id": "johndoe@example.com"
            },
            {
                "id": "jd@example.com"
            }
        ],
        "external_id": [
            {
                "id": "123456"
            }
        ]
    },
    "segmentMembership": {
        "ups": {
            "36a51c13-9dd6-4d2c-8aa3-07d785ea5075": {
                "lastQualificationTime": "2019-11-20T13:15:49Z",
                "status": "realized"
            },
            "788d8874-8007-4253-92b7-ee6b6c20c6f3": {
              "lastQualificationTime": "2019-11-20T13:15:49Z",
              "status": "existing"
            },
            "8f812592-3f06-416b-bd50-e7831848a31a": {
                "lastQualificationTime": "2019-11-20T13:15:49Z",
                "status": "exited"
            }
        }
    }
}

```

**Template**

>[!IMPORTANT]
>
>For all templates that you use, you must escape the illegal characters, such as double quotes `""` before inserting the template in the [destination configuration](/help/configuration-options.md). For more information on escaping double quotes, see Chapter 9 in the [JSON standard](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).

```python

{
    "identities": [
        {% for email in input.profile.identityMap.email %}
            {
                "type": "email",
                "id": "{{ email.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
         
        {# Add a comma only if you have both emails and external_ids. #}
        {% if input.profile.identityMap.email is not empty and input.profile.identityMap.external_id is not empty %}
            ,
        {% endif %}
         
        {% for external in input.profile.identityMap.external_id %}
            {
                "type": "external_id",
                "id": "{{ external.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
    ],
    "AdobeExperiencePlatformSegments": {
        "add": [
            {% for segment in input.profile.segmentMembership.ups | added %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ],
        "remove": [
            {# Alternative syntax for filtering segments by status: #}
            {% for segment in removedSegments(input.profile.segmentMembership.ups) %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ]
    }
}

```

**Result**

The `json` below represents the data exported out of Adobe Experience Platform.

```json

{
    "identities": [
        {
            "type": "email",
            "id": "johndoe@example.com"
        },
        {
            "type": "email",
            "id": "jd@example.com"
        },
        {
            "type": "external_id",
            "id": "123456"
        }
    ],
    "AdobeExperiencePlatformSegments": {
        "add": [
            "36a51c13-9dd6-4d2c-8aa3-07d785ea5075",
            "788d8874-8007-4253-92b7-ee6b6c20c6f3"
        ],
        "remove": [
            "8f812592-3f06-416b-bd50-e7831848a31a"
        ]
    }
}

```

### Create a template that sends segments, identities, and profile attributes {#segments-identities-attributes}

This section provides an example of a commonly used transformation between the Adobe XDM schema and partner destination schema.

Another common use case is exporting data that contains segment membership, identities (for example: email address, phone number, advertising ID), and profile attributes. To export data in this manner, see the example below:

**Input**

```json

{
    "attributes": {
        "firstName": {
            "value": "Hermione"
        },
        "birthDate": {}
    },
    "identityMap": {
        "email": [
            {
                "id": "johndoe@example.com"
            },
            {
                "id": "jd@example.com"
            }
        ],
        "external_id": [
            {
                "id": "123456"
            }
        ]
    },
    "segmentMembership": {
        "ups": {
            "36a51c13-9dd6-4d2c-8aa3-07d785ea5075": {
                "lastQualificationTime": "2019-11-20T13:15:49Z",
                "status": "realized"
            },
            "788d8874-8007-4253-92b7-ee6b6c20c6f3": {
              "lastQualificationTime": "2019-11-20T13:15:49Z",
              "status": "existing"
            },
            "8f812592-3f06-416b-bd50-e7831848a31a": {
                "lastQualificationTime": "2019-11-20T13:15:49Z",
                "status": "exited"
            }
        }
    }
}

```

**Template**

>[!IMPORTANT]
>
>For all templates that you use, you must escape the illegal characters, such as double quotes `""` before inserting the template in the [destination configuration](/help/configuration-options.md). For more information on escaping double quotes, see Chapter 9 in the [JSON standard](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).

```python

{
    "attributes": {
        {% for attribute in input.profile.attributes %}
            "{{ attribute.key }}":
                {% if attribute.value is empty %}
                    null
                {% else %}
                    "{{ attribute.value.value }}"
                {% endif %}
            {% if not loop.last %},{% endif %}
        {% endfor %}
    },
    "identities": [
        {% for email in input.profile.identityMap.email %}
            {
                "type": "email",
                "id": "{{ email.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
         
        {# Add a comma only if we have both emails and external_ids. #}
        {% if input.profile.identityMap.email is not empty and input.profile.identityMap.external_id is not empty %}
            ,
        {% endif %}
         
        {% for external in input.profile.identityMap.external_id %}
            {
                "type": "external_id",
                "id": "{{ external.id }}"
            }{% if not loop.last %},{% endif %}
        {% endfor %}
    ],
    "AdobeExperiencePlatformSegments": {
        "add": [
            {% for segment in input.profile.segmentMembership.ups | added %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ],
        "remove": [
            {# Alternative syntax for filtering segments by status: #}
            {% for segment in removedSegments(input.profile.segmentMembership.ups) %}
                "{{ segment.key }}"{% if not loop.last %},{% endif %}
            {% endfor %}
        ]
    }
}

```

**Result**

The `json` below represents the data exported out of Adobe Experience Platform.

```json

{
    "attributes": {
        "firstName": "Hermione",
        "birthDate": null
    },
    "identities": [
        {
            "type": "email",
            "id": "johndoe@example.com"
        },
        {
            "type": "email",
            "id": "jd@example.com"
        },
        {
            "type": "external_id",
            "id": "123456"
        }
    ],
    "AdobeExperiencePlatformSegments": {
        "add": [
            "36a51c13-9dd6-4d2c-8aa3-07d785ea5075",
            "788d8874-8007-4253-92b7-ee6b6c20c6f3"
        ],
        "remove": [
            "8f812592-3f06-416b-bd50-e7831848a31a"
        ]
    }
}

```

### Reference: Context and functions used in the transformation templates

The context provided to the template contains `input`  (the profiles / data that is exported in this call) and `destination` (data about the destination that Adobe is sending data to, valid for all profiles).

The table below provides descriptions for the functions in the examples above.

Function | Description |
---------|----------|
 `input.profile` | The profile, represented as a [JsonNode](http://fasterxml.github.io/jackson-databind/javadoc/2.11/com/fasterxml/jackson/databind/node/JsonNodeType.html). Follows the partner XDM schema mentioned further above on this page.|
 `destination.segmentAliases` | Map from segment IDs in the Adobe Experience Platform namespace to segment aliases in the partner's system. |
 `destination.segmentNames` | Map from segment names in the Adobe Experience Platform namespace to segment names in the partner's system. |
 `addedSegments(listOfSegments)` | Returns only the segments that have status `realized` or `existing`. |
 `removedSegments(listOfSegments)` | Returns only the segments that have status `exited`. |


## What Adobe needs from you to set up your destination {#what-adobe-needs}

Based on the transformations outlined in the sections above, Adobe needs the following information to set up your destination:

* Considering *all* the fields that your platform can receive, Adobe needs the standard JSON schema that corresponds to your expected message format. Having the template allows Adobe to define transformations and to create a custom XDM schema for your company, which customers would use to export data to your destination.

<!--


```

{
  "$id": "https://example.com/person.schema.json",
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Person",
  "type": "object",
  "properties": {
    "firstName": {
      "type": "string",
      "description": "The person's first name."
    },
    "lastName": {
      "type": "string",
      "description": "The person's last name."
    },
    "age": {
      "description": "Age in years which must be equal to or greater than zero.",
      "type": "integer",
      "minimum": 0
    }
  }
}


```

```
POST https://YOUR_REST_API_URL/users
Content-Type: application/json
Authorization: Bearer YOUR_REST_API_KEY

{
  "id": "yourstruly@adobe.com",
  shoppingList: [
    {
      "attribute_name": "product_id"
      "attribute_value": "12345"
    },
    {
      "attribute_name": "product_name"
      "attribute_value": "Toy Dinosaur"
    }
  ],
  {
    "attribute_name": "first_name"
    "attribute_value": "Yours"
  },
  {
    "attribute_name": "last_name"
    "attribute_value": "Truly"
  }
}

```

```

{
  "id": "$input._yourcompanyschema_xdm.id@adobe.com",
  shoppingList: [
    {
      "attribute_name": "product_id"
      "attribute_value": "$input.productId"
    },
    {
      "attribute_name": "product_name"
      "attribute_value": "$input.productName"
    }
  ],
  {
    "attribute_name": "first_name"
    "attribute_value": "$input.firstName"
  },
  {
    "attribute_name": "last_name"
    "attribute_value": "$input.lastName"
  },
  {
    "attribute_name": "another_field"
    "attribute_value": "$input.anotherField" // any other fields in your standard schema
  }
}

```



```

{
  "title": "Schema title",
  "type": "object",
  "properties": {
    "email": {
      "type": "string"
    },
    "shoppingList": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "productId": {
            "type": "integer"
          },
          "productName": {
            "type": "string"
          }
        }
      }
    },
    "personalDetails": {
      "type": "object",
      "properties": {
        "firstName": {
          "type": "string"
        },
        "lastName": {
          "type": "string"
        }
      }
    }
  }
}

```

```

{
  "segmentMembership": {
    "ups": {
      "72ddd79b-6b0a-4e97-a8d2-332ccd81bd02": {
        "lastQualificationTime": "2019-11-22T16:48:06Z",
        "status": "existing"
      }
    }
  },
  "identityMap": {
    "email": [
      {
        "id": "yourstruly@adobe.com"
      }
    ]
  },
  "person": {
    "name": {
      "firstName": "Yours",
      "lastName": "Truly"
    }
  },
  "_customer_company": {
    "cart": [
      {
        "id": 12345,
        "name": "Toy Dinosaur",
        "price": 99.99
      },
      {
        "id": 23456,
        "name": "Baby Giraffe Toy",
        "price": 50
      }
    ]
  }
}

```

```

{
  "identityMap": {
    "email": [
      {
        "id": "yourstruly@adobe.com"
      }
    ]
  },
  "_your_company_name": {
    "shoppingList": [
      {
        "productId": 12345,
        "productName": "Toy Dinosaur"
      },
      {
        "productId": 23456,
        "productName": "Baby Giraffe Toy"
      }
    ],
    "firstName": "Yours",
    "lastName": "Truly"
  }
}

```


```


{
  "email": "yourstruly@adobe.com",
  "shoppingList": [
    {
      "productId": 12345,
      "productName": "Toy Dinosaur"
    },
    {
      "productId": 23456,
      "productName": "Baby Giraffe Toy"
    }
  ],
  "personalDetails": {
    "firstName": "Yours",
    "lastName": "Truly"
  }
}

```

-->
