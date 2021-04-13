---
title: Krav och rekommendationer för behållare
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308787"
---
> [!NOTE]
> Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken.

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read OCR-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Läs 2,0 – för hands version | 1 kärna, 8 GB minne |    8 kärnor, 16 GB minne |
| Läs 3,2 | 8 kärnor, 16 GB minne | 8 kärnor, 24 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av `docker run` kommandot.
