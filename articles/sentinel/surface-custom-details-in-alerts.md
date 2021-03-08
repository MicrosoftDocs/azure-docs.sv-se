---
title: Information om anpassade ytor i Azure Sentinel-aviseringar | Microsoft Docs
description: Extrahera och Visa anpassad händelse information i aviseringar i Azure Sentinel Analytics-regler för bättre och mer fullständig incident information
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456320"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Information om anpassade händelser i aviseringar i Azure Sentinel 

> [!IMPORTANT]
>
> - Funktionen anpassad information är i för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="introduction"></a>Introduktion

[Schemalagda analys regler](tutorial-detect-threats-custom.md) analyserar **händelser** från data källor som är anslutna till Azure Sentinel och genererar **aviseringar** när innehållet i dessa händelser är betydande från ett säkerhets perspektiv. Dessa aviseringar analyseras ytterligare, grupperas och filtreras efter olika motorer i Azure Sentinel och tas i bruk i **incidenter** som garanterar en SOC analytikers uppmärksamhet. När analytikern visar incidenten syns dock bara egenskaperna för själva komponent aviseringarna. Att komma till det faktiska innehållet – informationen som finns i händelser – kräver en del utforska.

Med hjälp av funktionen **anpassad information** i **guiden Analytics-regel** kan du Visa händelse data i de aviseringar som har skapats från dessa händelser, vilket gör händelse data delen av aviserings egenskaperna. Detta ger dig en omedelbar händelse av innehålls synlighet i dina incidenter, så att du kan prioritering, undersöka, rita slut satser och svara med mycket högre hastighet och effektivitet.

Proceduren som beskrivs nedan är en del av guiden skapa analys regel. Det behandlas separat för att lösa scenariot med att lägga till eller ändra anpassad information i en befintlig analys regel.

## <a name="how-to-surface-custom-event-details"></a>Så här visar du information om anpassade händelser

1. Från navigerings menyn i Azure Sentinel väljer du **analys**.

1. Välj en schemalagd frågeregel och klicka på **Redigera**. Eller skapa en ny regel genom att klicka på **skapa &#10132; schemalagd frågeregel** överst på skärmen.

1. Klicka på fliken **Ange regel logik** .

1. I avsnittet **aviserings förbättringar** väljer du **anpassad information**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Sök efter och välj anpassad information":::

1. I avsnittet nu – expanderad **anpassad information** lägger du till nyckel/värde-par som motsvarar den information som du vill använda:

    1. I fältet **nyckel** anger du ett namn på ditt val som ska visas som fält namn i aviseringar.

    1. I fältet **värde** väljer du den händelse parameter som du vill använda i aviseringarna i den nedrullningsbara listan. Den här listan fylls i av värden som motsvarar fälten i de tabeller som omfattas av regel frågan.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Lägg till anpassad information":::

1. Klicka på **Lägg till ny** på yta mer information, upprepa de senaste stegen för att definiera nyckel/värde-par. 

    Om du ändrar dig, eller om du har gjort ett misstag, kan du ta bort en anpassad detalj genom att klicka på pappers korgs ikonen bredvid List rutan **värde** för den informationen.

1. När du är klar med att definiera anpassad information klickar du på fliken **Granska och skapa** . När regel valideringen har slutförts klickar du på **Spara**.

    > [!NOTE]
    > 
    >**Tjänstbegränsningar**
    > - Du kan definiera **upp till 20 anpassade uppgifter** i en enda analys regel.
    >
    > - Storleks gränsen för all anpassad information sammantaget är **2 KB**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du använder anpassad information i aviseringar med hjälp av Azure Sentinel Analytics-regler. Mer information om Azure Sentinel finns i följande artiklar:
- Hämta den fullständiga bilden om [schemalagda regler för analys av frågor](tutorial-detect-threats-custom.md).
- Lär dig mer om [entiteter i Azure Sentinel](entities-in-azure-sentinel.md).
