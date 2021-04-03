---
title: Docker-hämtning för Attitydanalys container
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Attitydanalys container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906065"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-hämtning för Attitydanalys v3-behållaren

Sentiment Analysis container v3-behållaren finns på flera olika språk. Om du vill ladda ned behållaren för den engelska behållaren använder du kommandot nedan. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Om du vill hämta behållaren för ett annat språk ersätter du `en` med en av språk koderna nedan. 

| Textanalys behållare | Språkkod |
|--|--|
| Engelska | `en` |
| Spanska | `es` |
| Franska | `fr` |
| Italienska | `it` |
| Tyska | `de` |
| Kinesiska – Förenklad | `zh` |
| Kinesiska (traditionell) | `zht` |
| Japanska | `ja` |
| Portugisiska | `pt` |
| Nederländska | `nl` |

En fullständig beskrivning av tillgängliga taggar för Textanalys behållare finns i [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).