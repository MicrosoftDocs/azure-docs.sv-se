---
title: Välj rätt pris nivå för Microsoft Azure Maps
description: Läs mer om pris nivåer för Azure Maps. Se vilka funktioner som erbjuds på vilka nivåer och se viktiga överväganden för att välja en pris nivå.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905306"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Välj rätt pris nivå i Azure Maps

Azure Maps erbjuder två pris nivåer: S0 och S1. Syftet med den här artikeln är att hjälpa dig att välja rätt pris nivå för dina behov. Du kan välja rätt pris nivå genom att ställa följande två frågor.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hur många samtidiga användare planerar jag att stödja?

Pris nivåerna S0 och S1 hanterar olika mängder data genom strömning. Pris nivån S0 hanterar upp till **50 frågor per sekund**. S1-nivån hanterar **fler än 50 frågor per sekund**.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Vilka geospatiala funktioner planerar jag att använda?

Om de grundläggande geospatiala API: erna uppfyller tjänst kraven väljer du pris nivån S0. Om du vill ha mer avancerade funktioner för ditt program kan du välja att använda pris nivån S1. Avancerade funktioner är: flyg-plus hybrid bilder, hämtning av väg intervall och grupp-och-kod. Om du vill välja den pris nivå som passar bäst för ditt program går du igenom tabellen med **pris nivå funktioner** nedan:

### <a name="pricing-tier-capabilities"></a>Pris nivå funktioner

| Funktion                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Mappa åter givning                              | ✓                   | ✓       |
| Satellit bilder                       |                     | ✓        |
| Sök                                  | ✓                    | ✓        |
| Batch-sökning                            |                     | ✓        |
| Väg                                   | ✓                    |✓        |
| Batchbearbetning                            |                    | ✓        |
| Mat ris routning                          |                     | ✓        |
| Flödes intervall (ISO Kron)                |                     | ✓        |
| Trafik                                |✓                    |✓        |
| Tidszon                               |✓                    |✓        |
| Geolokalisering (förhands granskning)                    |✓                   |✓        |
| Rums åtgärder                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Azure Maps data (förhands granskning)                |                     | ✓        |
| Mobilitet (för hands version)                       |                     | ✓        |
| Väder (för hands version)                        |✓                    |✓        |
|  Skapare (förhands granskning)                         |                   |✓        |
|  Höjning (förhands granskning)                        |                   |✓        |

Överväg följande ytterligare punkter:

* Vilken typ av företag har du?
* Hur kritiskt är ditt program?

### <a name="pricing-tier-targeted-customers"></a>Pris nivå riktade kunder

Se **pris nivån riktade kunder** för att få en bättre uppfattning om pris nivåerna S0 och S1. Mer information finns i [Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/). 

| Prisnivå  |     Riktad kund                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    Pris nivån S0 används för program i alla stadier av produktion: från koncept koncept utveckling och tidigt test till program produktion och distribution. Den här nivån är dock utformad för storskalig utveckling eller kunder med låga samtidiga användare eller både och. 
| S1            |    Pris nivån S1 är för kunder med storskaliga företags program, verksamhets kritiska program eller stora volymer av samtidiga användare. Det är också för de kunder som behöver avancerade geospatiala tjänster.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att visa och ändra pris nivåer:

> [!div class="nextstepaction"]
> [Hantera en pris nivå](how-to-manage-pricing-tier.md)
