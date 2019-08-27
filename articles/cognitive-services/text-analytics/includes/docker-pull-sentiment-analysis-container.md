---
title: Docker pull for the Sentiment Analysis container
titleSuffix: Azure Cognitive Services
description: Docker pull command for Sentiment Analysis container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include 
ms.date: 08/20/2019
ms.author: dapine
---

## Pull the Sentiment Analysis container

Container images for Text Analytics are available on the Microsoft Container Registry.

| Container | Container Registry / Repository / Image Name |
|-----------|------------|
| Sentiment Analysis | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Use the [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Microsoft Container Registry.

For a full description of available tags for the Text Analytics containers, see the [Sentiment Analysis](https://go.microsoft.com/fwlink/?linkid=2018654) container on the Docker Hub.

### Docker pull for the Sentiment Analysis container

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
