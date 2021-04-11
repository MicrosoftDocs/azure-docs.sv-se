---
title: Identifiera hot med inbyggda analys regler i Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder färdiga hot identifierings regler, baserat på inbyggda mallar, som meddelar dig när något misstänkt inträffar.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773563"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Självstudie: Identifiera hot direkt

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel ska du bli meddelad när något misstänkt inträffar. Det är därför som Azure Sentinel tillhandahåller färdiga, inbyggda mallar som hjälper dig att skapa hot identifierings regler. De här mallarna utformades av Microsofts team med säkerhetsexperter och analytiker baserat på kända hot, vanliga angrepps vektorer och misstänkta aktivitets eskalerings kedjor. Regler som skapas från de här mallarna söker automatiskt igenom din miljö efter aktivitet som ser misstänkt ut. Många av mallarna kan anpassas för att söka efter aktiviteter eller filtrera dem efter dina behov. De aviseringar som genereras av dessa regler skapar incidenter som du kan tilldela och undersöka i din miljö.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel:

> [!div class="checklist"]
> * Använd inaktuella hot identifieringar
> * Automatisera hot svar

## <a name="about-out-of-the-box-detections"></a>Om fördefinierade identifieringar

Om du vill visa alla färdiga identifieringar går du till **analyser** och sedan **regel mallar**. Den här fliken innehåller alla inbyggda regler för Azure-kontroll.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Använd inbyggda identifieringar för att hitta hot med Azure Sentinel":::

Följande typer av mallar är tillgängliga:

- **Microsoft-säkerhet**
   
   Microsoft Security templates skapar automatiskt Azure Sentinel-incidenter från aviseringarna som genereras i andra Microsoft-säkerhetslösningar i real tid. Du kan använda Microsofts säkerhets regler som mall för att skapa nya regler med liknande logik. Mer information om säkerhets regler finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).

- **Fusion** 

    Med utgångs punkt i fusions tekniken används skalbara algoritmer för maskin inlärning i Azure Sentinel för att korrelera många varningar och händelser över flera produkter till hög åter givning och åtgärds bara incidenter. Fusion är aktiverat som standard. Eftersom logiken är dold och därför inte kan anpassas, kan du bara skapa en regel med den här mallen.

    > [!IMPORTANT]
    > Några av identifieringarna i fusions regel mal len är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.
    >
    > Om du vill se vilka identifieringar som finns i en för hands version kan du läsa mer i [Avancerad attack identifiering i Azure Sentinel](fusion.md).

- **Beteendeanalys för maskininlärning**

    De här mallarna baseras på tillverkarspecifika Microsoft Machine Learning-algoritmer, så du kan inte se den interna logiken för hur de fungerar och när de körs. Eftersom logiken är dold och därför inte kan anpassas, kan du bara skapa en regel med varje mall av den här typen.

    > [!IMPORTANT]
    > - Mallarna för att Analytics-regler för maskin inlärning är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.
    >
    > - Genom att skapa och aktivera regler baserade på analys funktionerna i ML, **ger du Microsoft behörighet att kopiera inmatade data utanför Azure Sentinel-arbetsytans geografi** vid behov för bearbetning av Machine Learning-motorer och-modeller.

- **Schemalagd**

    Regler för schemalagd analys baseras på inbyggda frågor som skrivits av Microsofts säkerhets experter. Du kan se fråge logiken och göra ändringar i den. Du kan använda mallen för schemalagda regler och anpassa inställningarna för fråge logik och schemaläggning för att skapa nya regler.

## <a name="use-out-of-the-box-detections"></a>Använd välkomst identifieringar

1. Om du vill använda en inbyggd mall klickar du på mallnamnet och sedan på knappen **Skapa regel** i informations fönstret för att skapa en ny aktiv regel baserat på mallen. Varje mall innehåller en lista över nödvändiga data källor. När du öppnar mallen kontrol leras data källorna automatiskt för tillgänglighet. Om det finns ett tillgänglighets problem kan knappen **Skapa regel** vara inaktive rad, eller så kan du se en varning om den här inställningen.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panelen för hands version av identifierings regel":::
 
1. Om du klickar på knappen **Skapa regel** öppnas guiden Skapa regel som baseras på den valda mallen. All information fylls i och med de **schemalagda** eller **Microsoft** -säkerhetsmallarna kan du anpassa logiken och andra regel inställningar så att de passar dina behov bättre. Du kan upprepa den här processen för att skapa ytterligare regler baserade på den inbyggda mallen. När du har gått igenom stegen i guiden Skapa regel till slutet, kommer du att ha skapat en regel som baseras på mallen. De nya reglerna visas på fliken **aktiva regler** .

    Mer information om hur du anpassar reglerna i guiden Skapa regel finns i [Självstudier: skapa anpassade analys regler för att identifiera hot](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel. 

Om du vill lära dig hur du automatiserar dina svar på hot [ställer du in automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).
