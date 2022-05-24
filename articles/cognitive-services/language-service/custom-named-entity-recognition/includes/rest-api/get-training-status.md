---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 05/05/2022
ms.author: aahi
---

Use the following **GET** request to get the status of your model's training progress. Replace the placeholder values below with your own values. 

### Request URL

```rest
{ENDPOINT}/language/authoring/analyze-text/projects/{PROJECT-NAME}/train/jobs/{JOB-ID}?api-version={API-VERSION}
```

|Placeholder  |Value  | Example |
|---------|---------|---------|
|`{ENDPOINT}`     | The endpoint for authenticating your API request.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | The name of your project. This value is case-sensitive.   | `myProject` |
|`{JOB-ID}`     | The ID for locating your model's training status. This value is in the `location` header value you received in the previous step.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |
|`{API-VERSION}`     | The version of the API you are calling. The value referenced here is for the latest version released. Learn more about other available [API versions](../../../concepts/model-lifecycle.md#choose-the-model-version-used-on-your-data)  | `2022-03-01-preview` |


#### Headers

Use the following header to authenticate your request. 

|Key|Value|
|--|--|
|`Ocp-Apim-Subscription-Key`| The key to your resource. Used for authenticating your API requests.|

#### Response Body

Once you send the request, you’ll get the following response. 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "{MODEL-NAME}",
        "trainingConfigVersion": "string",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2022-04-12T12:13:28.771Z",
      "lastUpdatedDateTime": "2022-04-12T12:13:28.771Z",
      "expirationDateTime": "2022-04-12T12:13:28.771Z",
      "status": "unknown",
      "warnings": [
        {
          "code": "unknown",
          "message": "string"
        }
      ],
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ]
}

```
