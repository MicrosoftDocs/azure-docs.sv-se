---
title: Instrument panel – Language Understanding-LUIS
titleSuffix: Azure Cognitive Services
description: Åtgärda avsikter och entiteter med din tränade Apps instrument panel. På instrument panelen visas övergripande AppData med högdagrar av avsikter som bör åtgärdas.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541415"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Hur du använder instrument panelen för att förbättra din app

Hitta och åtgärda problem med dina utbildade appars avsikter när du använder exempel yttranden. På instrument panelen visas övergripande AppData med högdagrar av avsikter som bör åtgärdas.

Granska instrument panels analys är en iterativ process, upprepa när du ändrar och förbättrar din modell.

Den här sidan kommer inte att ha relevant analys för appar som inte har något exempel på yttranden i avsikter, som kallas _endast för Pattern-_ appar.

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Vilka problem kan åtgärdas från instrument panelen?

De tre problemen som åtgärdas i instrument panelen är:

|Problem|Diagram färg|Förklaring|
|--|--|--|
|Data obalans|-|Detta inträffar när antalet exempel yttranden varierar kraftigt. Alla avsikter måste ha _ungefär_ samma antal exempel yttranden – förutom ingen avsikt. Det bör bara ha 10% – 15% av den totala antalet yttranden i appen.<br><br> Om data är obalanserade men noggrannhets noggrannheten är högre än vissa tröskelvärde rapporteras inte den här obalansen som ett problem.<br><br>**Börja med det här problemet – det kan vara rotor saken till de andra problemen.**|
|Ta bort förutsägelser|Orange|Detta inträffar när den främsta avsikten och nästa avsikts resultat är tillräckligt nära att de kan vända sig på nästa utbildning, på grund av en [negativ sampling](luis-how-to-train.md#train-with-all-data) eller fler exempel på yttranden som har lagts till i avsikten. |
|Felaktiga förutsägelser|Red|Detta inträffar när ett exempel på en uttryck inte förutsägs för den märkta avsikten (det som är i).|

Korrekta förutsägelser visas med blå färg.

På instrument panelen visas de här problemen och du får information om vilka avsikter som påverkas och föreslår vad du bör göra för att förbättra appen.

## <a name="before-app-is-trained"></a>Innan appen tränas

Innan du tränar appen innehåller instrument panelen inte några förslag på korrigeringar. Träna din app för att se dessa förslag.

## <a name="check-your-publishing-status"></a>Kontrol lera publicerings status

Kortet **publicerings status** innehåller information om den aktiva versionens senaste publicering.

Kontrol lera att den aktiva versionen är den version som du vill åtgärda.

![Instrument panelen visar appens externa tjänster, publicerade regioner och sammanställda slut punkts träffar.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Detta visar även externa tjänster, publicerade regioner och sammanställda slut punkts träffar.

## <a name="review-training-evaluation"></a>Granska utvärdering av utbildning

**Kurs utvärderings** kortet innehåller den sammanställda sammanfattningen av appens övergripande exakthet per yta. Poängen indikerar avsikts kvalitet.

![Utvärderings kortet för utbildning innehåller det första informations avsnittet om appens övergripande exakthet.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Diagrammet visar korrekt förväntade intentor och problemområden med olika färger. När du förbättrar appen med förslagen ökar resultatet.

De föreslagna korrigeringarna separeras av problem typen och är de viktigaste för din app. Om du föredrar att granska och åtgärda problem per avsikt, använder du kortet för **[avsikter med fel](#intents-with-errors)** längst ned på sidan.

Varje problems Area har intenter som måste åtgärdas. När du väljer namnet på avsikten öppnas sidan **avsikt** med ett filter som tillämpas på yttranden. Med det här filtret kan du fokusera på yttranden som orsakar problemet.

### <a name="compare-changes-across-versions"></a>Jämför ändringar över versioner

Skapa en ny version innan du gör ändringar i appen. I den nya versionen gör du de föreslagna ändringarna i exempel yttranden för avsikten och träna sedan igen. På instrument panels sidan **utbildnings utvärderings** kort använder du alternativet **Visa ändring från tränad version** för att jämföra ändringarna.

![Jämför ändringar över versioner](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Korrigera version genom att lägga till eller redigera exempel yttranden och omträning

Den primära metoden för att åtgärda appen är att lägga till eller redigera exempel yttranden och omträna. Den nya eller ändrade yttranden måste följa rikt linjerna för [varierande yttranden](luis-concept-utterance.md).

Att lägga till exempel yttranden bör göras av någon som:

* har en hög grad av förståelse för vilka yttranden som finns i olika avsikter.
* vet hur yttranden i ett syfte kan förväxlas med ett annat avsikts sätt.
* kan bestämma om två avsikter, som ofta förväxlas med varandra, ska komprimeras till ett enda avsikts sätt. I så fall måste olika data hämtas med entiteter.

### <a name="patterns-and-phrase-lists"></a>Listor med mönster och fraser

Analytics-sidan visar inte när [mönster](luis-concept-patterns.md) eller [fras listor](luis-concept-feature.md)används. Om du lägger till dem kan det hjälpa till med felaktiga eller oklara förutsägelser, men kan inte hjälpa till med data obalans.

### <a name="review-data-imbalance"></a>Granska data obalans

Börja med det här problemet – det kan vara rotor saken till de andra problemen.

**Data obalanss** listan visar intentor som behöver fler yttranden för att korrigera dataobalansen.

**Så här åtgärdar du felet**:

* Lägg till fler yttranden i avsikten och träna sedan igen.

Lägg inte till yttranden i ingen avsikt om den inte föreslås på instrument panelen.

> [!Tip]
> Använd det tredje avsnittet på sidan, **yttranden per avsikt** med inställningen **yttranden (tal)** som en snabb visuell guide över vilka avsikter som behöver fler yttranden.
    ![Använd "yttranden (Number)" för att hitta avsikter med data obalans.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Granska felaktiga förutsägelser

I **fel** listan över förutsägelse avsikt visas intentor som har yttranden, som används som exempel för ett särskilt syfte, men som förutsägs för olika syften.

**Så här åtgärdar du felet**:

* Redigera yttranden så att det blir mer särskilt för avsikten och träna igen.
* Kombinera avsikter om yttranden är för tätt justerade och tränar igen.

### <a name="review-unclear-predictions"></a>Granska oklara förutsägelser

I den **otvetydiga förutsägelse** listan visas avsikter med yttranden med förutsägelse resultat som inte är tillräckligt långt från närmaste rival, så att den översta avsikten för uttryck kan ändras vid nästa utbildning, på grund av [negativ provtagning](luis-how-to-train.md#train-with-all-data).

**Så här löser du det här problemet**.

* Redigera yttranden så att det blir mer särskilt för avsikten och träna igen.
* Kombinera avsikter om yttranden är för tätt justerade och tränar igen.

## <a name="utterances-per-intent"></a>Yttranden per avsikt

Det här kortet visar den övergripande appens hälsa i avsikten. När du korrigerar avsikter och omtränar kan du fortsätta att ta en titt på det här kortet för problem.

Följande diagram visar en väl bal anse rad app med nästan inga problem att åtgärda.

![Följande diagram visar en väl bal anse rad app med nästan inga problem att åtgärda.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Följande diagram visar en dåligt bal anse rad app med många problem att åtgärda.

![Skärm bild som visar förutsägelser per avsikt med flera otydliga eller felaktigt förutsägande resultat.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Hovra över varje avsikts fält för att få information om avsikten.

![Skärm bild som visar förutsägelser per avsikt med detaljer om tydliga eller felaktigt förutsägande resultat.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Använd funktionen **Sortera efter** för att ordna avsikter efter ärende typ, så att du kan fokusera på de mest problematiska avsikterna med det problemet.

## <a name="intents-with-errors"></a>Avsikter med fel

Med det här kortet kan du granska problem för ett bestämt avsikts sätt. Standardvyn för det här kortet är de mest problematiska insikterna så att du vet var du vill fokusera på dina insatser.

![Med kortet för avsikter med fel kan du granska problem för ett bestämt avsikts sätt. Kortet filtreras till de mest problematiska avsikterna som standard, så att du vet var du vill fokusera på dina insatser.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Det övre ring diagrammet visar problem med avsikten mellan de tre problem typerna. Om det finns problem i de tre problem typerna har varje typ sitt eget diagram, tillsammans med alla rival-intenter.

### <a name="filter-intents-by-issue-and-percentage"></a>Filter avsikter per ärende och procent

I det här avsnittet av kortet kan du hitta exempel yttranden som faller utanför fel tröskeln. Vi rekommenderar att du vill att rätt förutsägelser ska vara betydande. Procent andelen är verksamhets-och kund driven.

Fastställ de procent andelen av tröskelvärdet som du är van vid för ditt företag.

Med filtret kan du hitta avsikter med ett speciellt problem:

|Filtrera|Föreslagen procent|Syfte|
|--|--|--|
|De flesta problematiska avsikter|-|**Börja här** – åtgärda yttranden i det här syftet är att förbättra appen mer än andra korrigeringar.|
|Korrigera förutsägelser nedan|60 %|Detta är den procentuella andelen yttranden i vald avsikt som är korrekt men har en förtroende poäng under tröskelvärdet. |
|Ta bort förutsägelser ovan|15 %|Detta är procent andelen yttranden i den valda avsikten som förväxlas med närmaste rival-avsikt.|
|Felaktigt förutsägelser över|15 %|Detta är den procentuella andelen yttranden i den valda avsikten som är felaktigt förväntad. |

### <a name="correct-prediction-threshold"></a>Korrekt förutsägelse tröskel

Vad är en säker förutsägelse av Tillförlitlighets poängen? I början av utveckling av appar kan 60% vara målet. Använd **rätt förutsägelser nedan** med procent andelen av 60% för att hitta eventuella yttranden i vald avsikt som måste åtgärdas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Oklart eller felaktigt förutsägelse tröskel

Med dessa två filter kan du hitta yttranden i den valda avsikten utanför ditt tröskelvärde. Du kan tänka på dessa två procent satser som fel procent. Om du är van med 10-15% fel frekvens för förutsägelser, ställer du in filter tröskeln på 15% för att hitta alla yttranden över det här värdet.

## <a name="next-steps"></a>Nästa steg

* [Hantera dina Azure-resurser](luis-how-to-azure-subscription.md)
