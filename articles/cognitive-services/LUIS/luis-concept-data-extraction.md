---
title: Understand data extraction concepts in LUIS - Azure | Microsoft Docs
description: Learn what kind of data can be extracted from Language Understanding (LUIS)
services: cognitive-services
author: v-geberr
manager: kamran.iqbal

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr;
---

# Data extraction
LUIS gives you the ability to get information from a user's natural language utterances. The information is extracted in a way that it can be used by a program, application, or chat bot to take action.

In the following sections, learn what data is returned from intents and entities with examples of JSON. The hardest data to extract is the machine-learned data because it is not an exact text match. Data extraction of the machine-learned [entities](luis-concept-entity-types.md) needs to be part of the [authoring cycle](luis-concept-app-iteration.md) until you are confident you receive the data you expect. 

## Data location and key usage
LUIS provides the data from the published [endpoint](luis-glossary.md#endpoint). The **HTTPS request** (POST or GET) contains the utterance as well as some optional configurations such as staging or production environments. You do not have to URL-encode utterances at the endpoint. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

The `appID` is available on the **Settings** page of your LUIS app as well as part of the URL (after `/apps/`) when you are editing that LUIS app. The `subscription-key` is the endpoint key used for querying your app. While you can use your free authoring/starter key while you are learning LUIS, it is important to change the subscription key to a key that supports your [expected LUIS usage](luis-boundaries.md#key-limits). The `timezoneOffset` unit is minutes.

The **HTTPS response** contains all the intent and entity information LUIS can determine based on the current published model of either the staging or production endpoint. The endpoint URL is found on the [LUIS][LUIS] website **Publish** page. 

## Data from intents
The primary data is the top scoring **intent name**. Using the `MyStore` [quickstart](luis-quickstart-intents-only.md), the endpoint response is:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Data Object|Data Type|Data Location|Value|
|--|--|--|--|
|Intent|String|topScoringIntent.intent|"GetStoreInfo"|

If your chat bot or LUIS-calling app makes a decision based on more than one intent score, return all the intents' scores by setting the querystring parameter, `verbose=true`. The endpoint response is:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

The intents are ordered from highest to lowest score.

|Data Object|Data Type|Data Location|Value|Score|
|--|--|--|--|:--|
|Intent|String|intents[0].intent|"GetStoreInfo"|0.984749258|
|Intent|String|intents[1].intent|"None"|0.0168218873|

If you add prebuilt domains, the intent name indicates the domain, such as `Utilties` or `Communication` as well as the intent:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```
    
|Domain|Data Object|Data Type|Data Location|Value|
|--|--|--|--|--|
|Utilities|Intent|String|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Communication|Intent|String|intents[1].intent|<b>Communication</b>.StartOver"|
||Intent|String|intents[2].intent|"None"|


## Data from entities
Most chat bots and applications need more than the intent name. This additional, optional data comes from entities discovered in the utterance. Each type of entity returns different information about the match. 

A single word or phrase in an utterance can match more than one entity. In that case, each matching entity is returned with its score. 

All entities are returned in the **entities** array of the response from the endpoint:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## Tokenized entity returned
Several [cultures](luis-supported-languages.md#tokenization) return the entity object with the `entity` value [tokenized](luis-glossary.md#token). The startIndex and endIndex returned by LUIS in the entity object do not map to the new, tokenized value but instead to the original query in order for you to extract the raw entity programmatically. 

For example, in German, the word `das Bauernbrot` is tokenized into `das bauern brot`. The tokenized value, `das bauern brot`, is returned and the original value can be programmatically determined from the startIndex and endIndex of the original query, giving you `das Bauernbrot`.

## Simple entity data

A simple entity is a machine-learned value. It can be a word or phrase. 

`Bob Jones wants 3 meatball pho`

In the previous utterance, `Bob Jones` is labeled as a simple `Customer` entity.

The data returned from the endpoint includes the entity name, the discovered text from the utterance, the location of the discovered text, and the score:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Data object|Entity name|Value|
|--|--|--|
|Simple Entity|"Customer"|"bob jones"|

## Hierarchical entity data

Hierarchical entities are machine-learned and can include a word or phrase. Children are identified by context. If you are looking for a parent-child relationship with exact text match, use a [List](#list-entity-data) entity. 

`book 2 tickets to paris`

In the previous utterance, `paris` is labeled a `Location::ToLocation` child of the `Location` hierarchical entity. 

The data returned from the endpoint includes the entity name and child name, the discovered text from the utterance, the location of the discovered text, and the score: 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Data object|Parent|Child|Value|
|--|--|--|--|--|
|Hierarchical Entity|Location|ToLocation|"paris"|

## Composite entity data
Composite entities are machine-learned and can include a word or phrase. For example, consider a composite entity of prebuilt `number` and `Location::ToLocation` with the following utterance:

`book 2 tickets to paris`

Notice that `2`, the number, and `paris`, the ToLocation have words between them that are not part of any of the entities. The green underline, used in a labeled utterance in the [LUIS][LUIS] website, indicates a composite entity.

![Composite Entity](./media/luis-concept-data-extraction/composite-entity.png)

Composite entities are returned in a `compositeEntities` array and all entities within the composite are also returned in the `entities` array:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Data object|Entity name|Value|
|--|--|--|
|Prebuilt Entity - number|"builtin.number"|"2"|
|Hierarchical Entity - Location|"Location::ToLocation"|"paris"|

## List entity data

A list entity is not machine-learned. It is an exact text match. A list represents items in the list along with synonyms for those items. LUIS marks any match to an item in any list as an entity in the response. A synonym can be in more than one list. 

Suppose the app has a list, named `Cities`, allowing for variations of city names including city of airport (Sea-tac), airport code (SEA), postal zip code (98101), and phone area code (206). 

|List item|Item synonyms|
|---|---|
|Seattle|sea-tac, sea, 98101, 206, +1 |
|Paris|cdg, roissy, ory, 75001, 1, +33|

`book 2 tickets to paris`

In the previous utterance, the word `paris` is mapped to the paris item as part of the `Cities` list entity. The list entity matches both the item's normalized name as well as the item synonyms. 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Another example utterance, using a synonym for Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## Prebuilt entity data
Prebuilt entities are discovered based on a regular expression match using the open-source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) project. Prebuilt entities are returned in the entities array and use the type name prefixed with `builtin::`. The following is an example utterance with the returned prebuilt entities:

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
``` 

## Data matching multiple entities
LUIS returns all entities discovered in the utterance. As a result, your chat bot may need to make decision based on the results. An utterance can have many entities in an utterance:

`book me 2 adult business tickets to paris tomorrow on air france`

The LUIS endpoint can discover the same data in different entities: 

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## Next steps

See [Add entities](Add-entities.md) to learn more about how to add entities to your LUIS app.

[LUIS]:luis-reference-regions.md