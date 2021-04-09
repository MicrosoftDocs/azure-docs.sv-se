---
title: 'Välj kolumn omvandling: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Transform-modulen Select columns i Azure Machine Learning designer för att utföra en SELECT-omvandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420724"
---
# <a name="select-columns-transform"></a>Välja kolumntranformering

Den här artikeln beskriver hur du använder Transform-modulen Välj kolumner i Azure Machine Learning designer. Syftet med modulen Välj kolumn omvandling är att säkerställa att en förutsägbar, konsekvent uppsättning kolumner används i efterföljande Machine Learning-åtgärder.

Den här modulen är användbar för uppgifter som poängsättning, som kräver vissa kolumner. Ändringar i de tillgängliga kolumnerna kan bryta pipelinen eller ändra resultatet.

Du använder transformeringen Välj kolumner för att skapa och spara en uppsättning kolumner. Använd sedan modulen [Använd omvandling](apply-transformation.md) för att tillämpa dessa val på nya data.

## <a name="how-to-use-select-columns-transform"></a>Så här använder du Välj kolumner-transformering

Det här scenariot förutsätter att du vill använda funktions val för att generera en dynamisk uppsättning kolumner som ska användas för att träna en modell. För att se till att kolumn valen är desamma för bedömnings processen använder du modulen Välj kolumner för att avbilda kolumn valen och tillämpa dem på andra platser i pipelinen.

1. Lägg till en data uppsättning i din pipeline i designern.

2. Lägg till en instans av [filtrering baserat funktions val](filter-based-feature-selection.md).

3. Anslut modulerna och konfigurera modulen för funktions val för att automatiskt hitta ett antal bästa funktioner i indata-datauppsättningen.

4. Lägg till en instans av [träna modell](train-model.md) och Använd utmatningen av [filter baserat funktions val](filter-based-feature-selection.md) som indata för träning.

    > [!IMPORTANT]
    > Eftersom funktions prioriteten baseras på värdena i kolumnen kan du inte veta i förväg vilka kolumner som kan vara tillgängliga för inmatningen till [träna modell](train-model.md).  
5. Koppla en instans av modulen Välj kolumner Transform. 

    Det här steget genererar en kolumn markering som en omvandling som kan sparas eller tillämpas på andra data uppsättningar. Det här steget ser till att kolumnerna som identifieras i funktions val sparas för andra moduler för åter användning.

6. Lägg till modulen [Poäng modell](score-model.md) . 

   *Anslut inte data uppsättningen för indata.* Lägg i stället till modulen [Använd omvandling](apply-transformation.md) och Anslut utdata från omvandling av funktions val.

   Pipeline-strukturen bör vara som följer:

   > [!div class="mx-imgBorder"]
   > ![Exempel på pipeline](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > Du kan inte vänta på att använda [filter baserat funktions val](filter-based-feature-selection.md) för poängsättnings data uppsättningen och få samma resultat. Eftersom val av funktioner baseras på värden kan det välja en annan uppsättning kolumner, vilket skulle resultera i att åtgärden misslyckades.
    
7. Skicka pipelinen.

Den här processen för att spara och tillämpa en kolumn markering säkerställer att samma data schema är tillgängligt för utbildning och bedömning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
