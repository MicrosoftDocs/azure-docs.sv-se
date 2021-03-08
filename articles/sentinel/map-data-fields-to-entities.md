---
title: Mappa data fält till Azure Sentinel-entiteter | Microsoft Docs
description: Mappa data fält i tabeller till Azure Sentinel-enheter i analys regler, för bättre incident information
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
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456335"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Mappa data fält till entiteter i Azure Sentinel 

> [!IMPORTANT]
>
> - Den nya versionen av funktionen för enhets mappning är i för **hands** version. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

> [!IMPORTANT]
>
> - I [kommentarer om den nya versionen](#notes-on-the-new-version) i slutet av det här dokumentet finns viktig information om bakåtkompatibilitet och skillnader mellan nya och gamla versioner av enhets mappning.

## <a name="introduction"></a>Introduktion

Enhets mappning är en integrerad del av konfigurationen av [schemalagda regler för analys av frågor](tutorial-detect-threats-custom.md). Den berikar reglernas utdata (aviseringar och incidenter) med viktig information som fungerar som bygg stenar av eventuella undersöknings processer och åtgärder som följer.

Proceduren som beskrivs nedan är en del av guiden skapa analys regel. Det behandlas separat för att lösa scenariot med att lägga till eller ändra enhets mappningar i en befintlig analys regel.

## <a name="how-to-map-entities"></a>Mappa entiteter

1. Från navigerings menyn i Azure Sentinel väljer du **analys**.

1. Välj en schemalagd frågeregel och klicka på **Redigera**. Eller skapa en ny regel genom att klicka på **skapa &#10132; schemalagd frågeregel** överst på skärmen.

1. Klicka på fliken **Ange regel logik** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Mappa fält till entiteter":::

1. I avsnittet **aviserings förbättringar** under **enhets mappning** väljer du en entitetstyp från List rutan **entitetstyp** .

1. Välj en **identifierare** för entiteten. Identifierare är attribut för en entitet som kan identifiera den tillräckligt. Välj en från List rutan **identifierare** och välj sedan ett data fält i list rutan **värde** som motsvarar identifieraren. Med vissa undantag fylls **värde** listan i av data fälten i tabellen som definieras som ämne för regel frågan.

    Du kan definiera **upp till tre identifierare** för en specifik entitet. Vissa identifierare krävs, andra är valfria. Du måste välja minst en identifierare som krävs. Om du inte gör det får du ett varnings meddelande som anger vilka identifierare som krävs. För bästa resultat – för maximal unik identifiering, bör du använda **starka identifierare** när så är möjligt och med hjälp av flera starka identifierare kan du ge större korrelation mellan data källor. Se den fullständiga listan över tillgängliga [entiteter och identifierare](entities-reference.md).

1. Klicka på **Lägg till ny entitet** för att mappa fler entiteter. Du kan mappa **upp till fem entiteter** i en enda analys regel. Du kan också mappa fler än en av samma typ. Du kan till exempel mappa två **IP-** entiteter, en från ett fält för *käll-IP-adress* och ett från ett *mål-IP-adress* fält. På så sätt kan du spåra dem båda.

    Om du ändrar dig eller om du har gjort ett misstag kan du ta bort en enhets mappning genom att klicka på pappers korgs ikonen bredvid List rutan entitet.

1. När du har mappat entiteterna klickar du på fliken **Granska och skapa** . När regel valideringen har slutförts klickar du på **Spara**.

## <a name="notes-on-the-new-version"></a>Anteckningar om den nya versionen

- Om du tidigare har definierat enhets mappningar för den här analys regeln med den gamla versionen visas dessa mappningar i frågesträngen. Enhets mappningar som definieras i den nya versionen **visas inte i frågesträngen**. Analys regler kan bara stödja en version av enhets mappningar i taget och den nya versionen har företräde. Därför kan alla mappningar som du definierar **här orsaka att alla** mappningar som definierats i frågesträngen **ignoreras när frågan** körs. 

- Om du fortfarande behöver använda den **gamla versionen** av enhets mappningen (så länge den nya versionen fortfarande är i för hands version) kan du fortfarande komma åt den med en funktions flagga i URL: en. Placera markören mellan `https://portal.azure.com/` och och `#blade` infoga texten `?feature.EntityMapping=false` .

  - Gränserna för den gamla versionen fortsätter att gälla. Du kan endast mappa användare, värd, IP-adress, URL och filhash-entiteter, och endast en av dem.

  - Du måste **ta bort** alla enhets mappningar som skapats med den nya versionen **innan** du återgår till den gamla versionen, annars **fungerar inte** alla enhets mappningar som använder den gamla versionen.

- När den nya versionen av Entity-mappningen är allmänt tillgänglig går det inte längre att använda den gamla versionen. Vi rekommenderar starkt att du migrerar dina gamla enhets mappningar till den nya versionen.


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du mappar data fält till entiteter i Azure Sentinel Analytics-regler. Mer information om Azure Sentinel finns i följande artiklar:
- Hämta den fullständiga bilden om [schemalagda regler för analys av frågor](tutorial-detect-threats-custom.md).
- Lär dig mer om [entiteter i Azure Sentinel](entities-in-azure-sentinel.md).
