---
title: Begrepp för att felsöka sessioner (för hands version)
titleSuffix: Azure Cognitive Search
description: Felsök sessioner, som nås via Azure Portal, tillhandahåller en IDE som miljö där du kan identifiera och åtgärda fel, verifiera ändringar och skicka ändringar till färdighetsuppsättningar i AI-pipeline. Debug-sessioner är i för hands version.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92057628"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Felsöka sessioner i Azure Kognitiv sökning

Debug-sessioner är en visuell redigerare som fungerar med en befintlig färdigheter i Azure Portal. Inom en felsökningssession kan du identifiera och lösa fel, verifiera ändringar och skicka ändringar till en produktions-färdigheter i AI-pipeline för anrikning.

> [!Important]
> Debug-sessioner är en förhands gransknings funktion utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Använda debug-sessioner

När du startar en session skapar tjänsten en kopia av färdigheter, indexeraren och indexet där ett enskilt dokument används för att testa färdigheter. Ändringar som görs i sessionen sparas i sessionen. De ändringar som gjorts inom felsökningssessionen kommer inte att påverka produktions-färdigheter, om inte ändringarna utförs. Om du genomför ändringar skrivs produktions färdigheter över.

Under en felsökningssession kan du redigera en färdigheter, inspektera och validera varje nod i det berikande trädet i kontexten för ett särskilt dokument. När pipeline-problemen har lösts kan ändringar sparas i sessionen och allokeras till färdigheter i produktion. 

Om det inte finns några fel i pipelinen kan du använda en felsökningssession för att stegvis utöka ett dokument, testa och validera varje ändring innan du genomför ändringarna.

## <a name="creating-a-debug-session"></a>Skapa en felsökningssession

Om du vill starta en felsökningssession måste du ha en befintlig AI-pipeline, inklusive; en data källa, en färdigheter, en indexerare och ett index. Om du vill konfigurera en felsökningssession måste du namnge sessionen och tillhandahålla ett allmänt lagrings konto som används för att cachelagra färdighets körningarna under körningen av indexeraren. Du måste också välja den indexerare som ska köras. Indexeraren har referenser lagrade till data källan, färdigheter och indexet. Felsökningssessionen använder som standard det första dokumentet i data källan, eller så kan du ange ett dokument i data källan som du vill gå igenom.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Skapa en felsökningssession":::

## <a name="debug-session-features"></a>Funktioner för att felsöka sessioner

Felsökningssessionen börjar genom att köra färdigheter på det valda dokumentet. Felsökningssessionen registrerar ytterligare metadata som är kopplade till varje åtgärd i färdigheter. Metadata som skapats av pipelinens kompetens körningar informerar följande funktioner som sedan används för att identifiera och åtgärda problem med färdigheter.

## <a name="ai-enrichments"></a>AI-anrikning

I takt med att färdigheterna kör ett träd med anrikninger som representerar dokumentet växer. Genom att använda ett träd för att visualisera utmatningar av färdigheter eller berikningar får du en omfattande titt på alla berikade som utförs. Du kan titta i hela dokumentet och kontrol lera varje nod i ditt anriknings träd. Det här perspektivet gör det lättare att forma objekt. Det här formatet ger också visuella tips till typ, sökväg och innehåll för varje nod i trädet.

## <a name="skill-graph"></a>Kunskaps diagram

Vyn **kunskaps diagram** innehåller en hierarkisk och visuell representation av färdigheter. Grafen är en uppifrån och ned-representation i den ordning som färdigheterna utförs. Kunskaper som är beroende av utdata från andra färdigheter visas i diagrammet. Kunskaper på samma nivå i hierarkin kan köras parallellt. 

Om du väljer en färdighet i diagrammet markeras de kunskaper som är anslutna till den, noderna som skapar indata och de noder som accepterar dess utdata. Varje kvalifikations nod visar dess typ, fel eller varningar och antalet körningar. **Kunskaps diagrammet** är där du väljer vilken kunskap som ska felsöka eller förbättra. När du väljer en färdighet visas informationen i fönstret kunskaps information till höger om diagrammet.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Kunskaps diagram":::

## <a name="skill-details"></a>Kunskaps information

I fönstret kunskaps information visas en uppsättning områden för att arbeta med en speciell färdighet, när den här kunskapen är markerad i **färdighets diagrammet**. Du kan granska och redigera information om färdighetens inställningar. Färdighetens JSON-definition tillhandahålls. Information om färdighets körningen och felen och varningarna visas också. Fliken **skicklighets inställningar** & **FÄRDIGHETs-JSON-redigeraren** tillåter direkt redigering av kunskapen. I [`</>`](#expression-evaluator) fönstret öppnas ett fönster för att visa och redigera uttrycken för kunskaper och utdata.

Kapslade inmatnings kontroller i fönstret kunskaps inställningar kan användas för att bygga komplexa former för projektioner, fält mappningar för utdata för ett komplext typ fält eller indata till en färdighet. När de används med **uttrycks utvärderaren** ger kapslade indata ett enkelt test och validera uttrycks verktyg.

## <a name="skill-execution-history"></a>Historik för körning av kunskaper

En färdighet kan köras flera gånger i en färdigheter för ett enda dokument. OCR-kunskaper körs till exempel en gång för varje bild som extraheras från varje dokument. I fönstret kunskaps information visar fliken **körningar** den sökandes körnings historik som ger en djupare titt på varje anrop av färdigheten. 

Körnings historiken gör det möjligt att spåra en bestämd anrikning tillbaka till den kunskap som genererade den. Om du klickar på en färdighets inblandning navigerar du till den kunskap som genererade indatamängden. Detta gör det möjligt att identifiera rotor saken till ett problem som kan uppstå i en underordnad färdighet. 

När ett potentiellt problem identifieras visar körnings historiken länkar till de kunskaper som genererade de angivna indata, vilket ger en stack spårning som funktion. Om du klickar på den kunskap som är kopplad till ett inmatat, navigerar du till kunskapen för att åtgärda eventuella buggar eller fortsätta att spåra det aktuella problemet.

När du skapar en anpassad färdighet eller felsöker ett fel med en anpassad kunskap, finns det möjlighet att generera en begäran om ett kunskaps anrop i körnings historiken.

## <a name="enriched-data-structure"></a>Omfattande data struktur

I fönstret fördelad **data struktur** visas dokumentets berikning genom färdigheter, som innehåller information om sammanhanget för varje anrikning och den ursprungliga kunskapen. **Uttrycks utvärderaren** kan också användas för att visa innehållet för varje berikning.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Omfattande data struktur":::

## <a name="expression-evaluator"></a>Uttrycks utvärderare

**Uttrycks utvärderaren** ger en snabb överblick över värdet för valfri sökväg. Det gör att du kan redigera sökvägen och testa resultaten innan du uppdaterar alla indata eller kontexter för en färdighet eller projektion.

## <a name="errorswarnings"></a>Fel/varningar

I det här fönstret visas alla fel och varningar som färdigheter genererar när den körs mot dokumentet i felsökningssessionen.

## <a name="limitations"></a>Begränsningar

Fel söknings sesisons fungerar med alla allmänt tillgängliga data källor AMD-förhands gransknings data källor. MongoDB-API (för hands version) och API för Cassandra (för hands version) av Cosmos DB stöds inte för närvarande.

## <a name="next-steps"></a>Nästa steg

Nu när du förstår elementen i fel söknings sessioner, prova själv studie kursen om du vill ha en praktisk upplevelse.

> [!div class="nextstepaction"]
> [Utforska själv studie kursen om funktionen för att felsöka sessioner](./cognitive-search-tutorial-debug-sessions.md)