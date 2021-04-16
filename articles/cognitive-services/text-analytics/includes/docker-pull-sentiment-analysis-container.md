---
title: Docker-pull för Attitydanalys containern
titleSuffix: Azure Cognitive Services
description: Docker-pullkommando för Attitydanalys container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564618"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-pull för Attitydanalys v3-containern

Containern attitydanalys v3 är tillgänglig på flera språk. Om du vill ladda ned containern för den engelska containern använder du kommandot nedan. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Om du vill ladda ned containern för ett annat språk `en` ersätter du med någon av språkkoderna nedan. 

| Textanalys container | Språkkod |
|--|--|
| Chinese-Simplified    |   `zh-hans`   |
| Chinese-Traditional   |   `zh-hant`   |
| Nederländska                 |     `nl`      |
| Engelska               |     `en`      |
| Franska                |     `fr`      |
| Tyska                |     `de`      |
| Hindi                 |    `hi`       |
| Italienska               |     `it`      |
| Japanska              |     `ja`      |
| Koreanska                |     `ko`      |
| Norska (bokmål)   |     `no`      |
| Portugisiska (Brasilien)   |    `pt-BR`    |
| Portugisiska (Portugal) |    `pt-PT`    |
| Spanska               |     `es`      |
| Turkiska               |     `tr`      |

En fullständig beskrivning av tillgängliga taggar för Textanalys containrar finns [i Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
