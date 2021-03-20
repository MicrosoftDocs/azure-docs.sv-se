---
title: Dela bildkatalog
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen dela avbildnings katalog i designern för att dela upp avbildningarna i en avbildnings katalog i två distinkta mängder.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421200"
---
# <a name="split-image-directory"></a>Dela bildkatalog

I det här avsnittet beskrivs hur du använder modulen dela avbildnings katalog i Azure Machine Learning designer för att dela upp avbildningarna av en avbildnings katalog i två distinkta mängder.

Den här modulen är särskilt användbar när du behöver separera bilddata till utbildnings-och testnings uppsättningar. 

## <a name="how-to-configure-split-image-directory"></a>Konfigurera en delad avbildnings katalog

1. Lägg till modulen **dela avbildnings katalog** i din pipeline. Du hittar den här modulen under kategorin Visuellt innehåll/bild data omvandling.

2. Anslut den till den modul där utdata är avbildnings katalog.

3. Ange en **bråkdel av bilder i de första utdata** för att ange den procent andel av data som ska infogas i den vänstra delningen, som standard 0,9. Om bråk resultatet inte är Integer använder modulen det mindre nära heltalet.


## <a name="technical-notes"></a>Tekniska anteckningar

### <a name="expected-inputs"></a>Förväntade indata

| Namn                  | Typ           | Beskrivning              |
| --------------------- | -------------- | ------------------------ |
| Avbildnings katalog för indata | ImageDirect | Avbildnings katalog att dela |

### <a name="module-parameters"></a>Parametrar för modul

| Namn                                   | Typ  | Intervall | Valfritt | Beskrivning                            | Standardvärde |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Delar av bilder i de första utdata | Float | 0-1   | Obligatorisk | Delar av bilder i de första utdata | 0,9     |

### <a name="outputs"></a>Utdata

| Namn                    | Typ           | Beskrivning                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Directory1 för utgående bild | ImageDirect | Avbildnings katalog som innehåller valda bilder |
| Directory2 för utgående bild | ImageDirect | Avbildnings katalog som innehåller alla andra bilder |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

