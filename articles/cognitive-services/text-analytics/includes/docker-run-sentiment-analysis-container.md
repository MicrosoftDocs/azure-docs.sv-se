---
title: Kör container-exempel för kommandot Docker Run
titleSuffix: Azure Cognitive Services
description: Docker Run-kommandot för Attitydanalys container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 96b41c27b282bdc05dafa8c69398a11e8dbf3b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91779094"
---
Kör följande kommando för att köra *Attitydanalys v3* -behållaren `docker run` . Ersätt plats hållarna nedan med dina egna värden:

| Platshållare | Värde | Format eller exempel |
|-------------|-------|---|
| **{API_KEY}** | Nyckeln till din Textanalys-resurs. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Slut punkten för att få åtkomst till API för textanalys. Du hittar den på resursens nyckel- **och slut punkts** sida på Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *Attitydanalys* behållare från behållar avbildningen
* Allokerar en processor kärna och 8 GB minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.