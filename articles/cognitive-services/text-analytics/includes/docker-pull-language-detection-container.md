---
title: Docker-hämtning för Språkidentifiering container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Språkidentifiering container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906037"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-hämtning för Språkidentifiering container

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållar avbildning från Microsoft container Registry.

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759) container på Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
