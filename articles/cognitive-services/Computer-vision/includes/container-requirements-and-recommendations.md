---
title: Krav och rekommendationer för behållare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006923"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Läs 2,0 – för hands version | 1 kärna, 8 GB minne |  8 kärnor, 16 GB minne |
| Läs 3,2 – för hands version | 8 kärnor, 16 GB minne | 8 kärnor, 24 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.
