---
title: Ord lista – LUIS
description: Ord listan förklarar termer som du kan stöta på när du arbetar med LUIS-API-tjänsten.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 7c65c8272172cab9f5361d16141bf7b229037480
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786952"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Ord lista för språk förståelse för vanliga ord listor och koncept
Ord listan Language Understanding (LUIS) förklarar termer som du kan stöta på när du arbetar med LUIS-tjänsten.

## <a name="active-version"></a>Aktiv version

Den aktiva versionen är den [version](luis-how-to-manage-versions.md) av appen som uppdateras när du gör ändringar i modellen med hjälp av Luis-portalen. Om du vill göra ändringar i en version som inte är den aktiva versionen i LUIS-portalen måste du först ange den versionen som aktiv.

## <a name="active-learning"></a>Aktiv inlärning

Active Learning är en teknik för Machine Learning där den inlärda modellen används för att identifiera informativa nya exempel på etiketter. I LUIS refererar Active Learning till att lägga till yttranden från slut punkts trafiken vars aktuella förutsägelser är oklart för att förbättra din modell. Klicka på "granska slut punkts yttranden" om du vill visa yttranden till etikett.

Se även:
* [Konceptuell information](luis-concept-review-endpoint-utterances.md)
* [Självstudie för att granska slut punkt yttranden](luis-tutorial-review-endpoint-utterances.md)
* Förbättra LUIS-appen genom att [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Program (app)

I LUIS, ditt program eller din app, är en samling datorer som har lärts in varandra, som bygger på samma data uppsättning, och som fungerar tillsammans för att förutsäga avsikter och entiteter för ett visst scenario. Varje program har en separat förutsägelse slut punkt.

Om du skapar en HR-robot kan du ha en uppsättning syften, till exempel "Schemalägg tjänstledighet", "fråga om förmåner" och "uppdatera personlig information" och entiteter för var och en av dessa syften som du grupperar i ett enda program.

## <a name="authoring"></a>Redigering

Redigering är möjligheten att skapa, hantera och distribuera en LUIS-app, antingen med hjälp av LUIS-portalen eller redigerings-API: er.

### <a name="authoring-key"></a>Redigerings nyckel

[Redigerings nyckeln](luis-how-to-azure-subscription.md) används för att redigera appen. Används inte för slut punkts frågor på produktions nivå. Mer information finns i [nyckel gränser](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Skapar resurs

Din LUIS- [Authoring-resurs](luis-how-to-azure-subscription.md#azure-resources-for-luis) är ett hanterbart objekt som är tillgängligt via Azure. Resursen är din åtkomst till associerade redigerings-, utbildnings-och publicerings möjligheter för Azure-tjänsten. Resursen innehåller autentiserings-, auktoriserings-och säkerhets information som du behöver för att få åtkomst till den associerade Azure-tjänsten.

Redigerings resursen har en Azure-typ av `LUIS-Authoring` .

## <a name="batch-test"></a>Batch-test

Batch-testning är möjligheten att verifiera en aktuell LUIS-Apps modeller med en konsekvent och känd test uppsättning av användar-yttranden. Batch-testet definieras i en [JSON-formaterad fil](./luis-how-to-batch-test.md#batch-test-file).


Se även:
* [Begrepp](./luis-how-to-batch-test.md)
* [Så här kör du](luis-how-to-batch-test.md) ett batch-test
* [Självstudie](./luis-how-to-batch-test.md) – skapa och köra ett batch-test

### <a name="f-measure"></a>F-mått

I batch-testning är det ett mått på testets noggrannhet.

### <a name="false-negative-fn"></a>Falskt negativt (FN)

I batch-testning representerar data punkterna yttranden där appen felaktigt förutsäger frånvaron av mål avsikten/entiteten.

### <a name="false-positive-fp"></a>Falskt positivt (RP)

I batch-testning representerar data punkterna yttranden där appen felaktigt förutsäger förekomsten av mål avsikten/entiteten.

### <a name="precision"></a>Precision
I batch-testning är precision (även kallat positivt förutsägande värde) den andel av relevanta yttranden bland de hämtade yttranden.

Ett exempel på ett djur batch-test är antalet får som förutsägs dividerat med det totala antalet djur (får och inte får lika).

### <a name="recall"></a>Recall

I batch-testning kan du återkalla (även kallat känslighet) för att LUIS ska kunna generaliseras.

Ett exempel på ett djur batch-test är antalet får som förutsägs dividerat med det totala antalet tillgängliga får.

### <a name="true-negative-tn"></a>Sant negativt (TN)

Ett sant negativt värde är när din app korrekt förutsäger ingen matchning. Vid batch-testning inträffar ett sant negativt värde när appen förutsäger ett avsikts-eller entitets-exempel som inte har märkts med den avsikten eller entiteten.

### <a name="true-positive-tp"></a>Sant positivt (TP)

Sant positivt (TP) ett sant positivt värde när appen förutsäger en matchning korrekt. Vid batch-testning inträffar ett sant positivt värde när appen förutsäger ett avsikts-eller entitets-exempel som har märkts med den avsikten eller entiteten.

## <a name="classifier"></a>Klassificerare

En klassificerare är en modell för förbrukad dator som förutsäger vilken kategori eller klass en ineffekt passar in i.

[Avsikten](#intent) är ett exempel på en klassificerare.

## <a name="collaborator"></a>Medarbetare

En medarbetare är konceptuellt samma sak som en [deltagare](#contributor). En medarbetare beviljas åtkomst när en ägare lägger till medarbetares e-postadress till en app som inte styrs av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Om du fortfarande använder medarbetare, bör du migrera ditt LUIS-konto och använda LUIS Authoring-resurser för att hantera deltagare med Azure RBAC.

## <a name="contributor"></a>Deltagare

En deltagare är inte [ägare](#owner) till appen, men har samma behörighet för att lägga till, redigera och ta bort avsikter, entiteter, yttranden. En deltagare ger Azure-rollbaserad åtkomst kontroll (Azure RBAC) till en LUIS-app.

Se även:
* [Så här lägger du](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) till bidrags givare

## <a name="descriptor"></a>Script

En beskrivning är den term som tidigare användes för en Machine Learning- [funktion](#features).

## <a name="domain"></a>Domain

I LUIS-kontexten är en domän ett kunskaps området. Din domän är speciell för ditt scenario. Olika domäner använder specifika språk och termer som har betydelse i domänens kontext. Om du t. ex. skapar ett program för att spela musik, skulle ditt program ha villkor och språk som är specifika för musik – ord som "sång, spår, album, sång texter," på sidan ", artist". Exempel på domäner finns i [fördefinierade domäner](#prebuilt-domain).

## <a name="endpoint"></a>Slutpunkt

### <a name="authoring-endpoint"></a>Redigerar slut punkt

URL: en för LUIS Authoring är där du skapar, tränar och publicerar din app. Slut punkts-URL: en innehåller den region eller anpassade under domänen för den publicerade appen samt app-ID.

Lär dig mer om hur du redigerar din app program mässigt från [utvecklarens referens](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Förutsägelse slut punkt

URL: en för LUIS förutsägelse slut punkt är den plats där du skickar LUIS-frågor när [appen Luis](#application-app) har skapats och publicerats. Slut punkts-URL: en innehåller den region eller anpassade under domänen för den publicerade appen samt app-ID. Du hittar slut punkten på sidan **[Azure-resurser](luis-how-to-azure-subscription.md)** i din app, eller så kan du hämta slut punkts-URL: en från [Hämta app information](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

Din åtkomst till förutsägelse slut punkten auktoriseras med LUIS-förutsägelse nyckeln.

## <a name="entity"></a>Entitet

[Entiteter](luis-concept-entity-types.md) är ord i yttranden som beskriver information som används för att uppfylla eller identifiera en avsikt. Om din entitet är komplex och du vill att din modell ska identifiera vissa delar kan du dela upp modellen i underentiteter. Du kanske exempelvis vill att modellen ska förutsäga en adress, men även underentiteterna för gata, stad, delstat och Postummer. Entiteter kan också användas som funktioner i modeller. Ditt svar från LUIS-appen kommer att innehålla både det förväntade syftet och alla entiteter.

### <a name="entity-extractor"></a>Enhets-Extractor

En enhets extrahering som ibland endast är känd som en Extractor är den typ av dator som är inlärd och som LUIS använder för att förutse entiteter.

### <a name="entity-schema"></a>Enhets schema

Enhets schemat är den struktur som du definierar för enheter som har lärts in entiteter med underentiteter. Förutsägelse slut punkten returnerar alla extraherade entiteter och underentiteter som definierats i schemat.

### <a name="entitys-subentity"></a>Entitetens underentitet

En underordnad entitet är en underordnad entitet till en enhet för maskin inlärning.

### <a name="non-machine-learning-entity"></a>Enhet som inte är en maskin inlärning

En entitet som använder text matchning för att extrahera data:
* Lista entitet
* Entitet för reguljära uttryck

### <a name="list-entity"></a>Lista entitet

En [list-entitet](reference-entity-list.md) representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. List entiteter är exakta matchningar, till skillnad från tidigare inlärt entiteter.

Entiteten kommer att förutsägas om ett ord i listan entitet ingår i listan. Om du till exempel har en lista med namnet "storlek" och du har ordet "liten, medel, stor" i listan, kommer storleks enheten att förutsägas för alla yttranden där orden "små", "medium" eller "stor" används oavsett kontexten.

### <a name="regular-expression"></a>Reguljärt uttryck

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) representerar ett reguljärt uttryck. Reguljära uttrycks enheter är exakta matchningar, till skillnad från tidigare inlärt entiteter.
### <a name="prebuilt-entity"></a>Fördefinierad entitet

Se fördefinierad modell post för [fördefinierad entitet](#prebuilt-entity)

## <a name="features"></a>Funktioner

I Machine Learning är en funktion en egenskap som hjälper modellen att identifiera ett visst koncept. Det är ett tips som LUIS kan använda, men inte en hård regel.

Termen kallas även för **[maskin inlärnings funktion](luis-concept-feature.md)**.

Dessa tips används tillsammans med etiketterna för att lära dig hur du förutsäger nya data. LUIS stöder både fras listor och använder andra modeller som funktioner.

### <a name="required-feature"></a>Nödvändig funktion

En nödvändig funktion är ett sätt att begränsa utdata från en LUIS modell. När en funktion för en entitet har marker ATS som obligatorisk måste funktionen finnas i exemplet för att entiteten ska förutsägas, oavsett vilken dator som är inlärt modell förutsägelser.

Tänk dig ett exempel där du har en fördefinierad siffer funktion som du har markerat som krävs på entiteten kvantitet för en meny beställnings robot. När din robot ser `I want a bajillion large pizzas?` , kommer bajillion inte att förutsägas som en kvantitet oavsett i vilken kontext den visas. Bajillion är inte ett giltigt tal och kommer inte att förväntas av den fördefinierade nummer enheten.

## <a name="intent"></a>Avsikt

En [avsikt](luis-concept-intent.md) representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares indata, t. ex. bokning av en flygning eller betalning av en faktura. I LUIS klassificeras en uttryck som helhet, men delar av uttryck extraheras som entiteter

## <a name="labeling-examples"></a>Exempel på etiketter

Etikettering, eller markering, är en process för att associera ett positivt eller negativt exempel med en modell.

### <a name="labeling-for-intents"></a>Etiketter för avsikter
I LUIS är avsikter inom en app ömsesidigt uteslutande. Det innebär att när du lägger till en uttryck till ett avsikts sätt, betraktas det som ett _positivt_ exempel för den avsikten och ett _negativt_ exempel för alla andra syften. Negativa exempel bör inte förväxlas med "ingen"-avsikt, som representerar yttranden som ligger utanför appens omfång.

### <a name="labeling-for-entities"></a>Etiketter för entiteter
I LUIS [etiketterar](label-entity-example-utterance.md) du ett ord eller en fras i exempel uttryck med en entitet som ett _positivt_ exempel. Etiketter visar vad det ska förutse för den uttryck. De märkta yttranden används för att träna avsikten.

## <a name="luis-app"></a>LUIS-app

Se definitionen för [programmet (app)](#application-app).

## <a name="model"></a>Modell

En (maskin inlärt) modell är en funktion som gör en förutsägelse av indata. I LUIS refererar vi till intenta klassificerare och enhets utdrag allmänt som "modeller" och vi refererar till en samling modeller som är utbildade, publicerade och efterfrågade tillsammans som en "app".

## <a name="normalized-value"></a>Normaliserat värde

Du lägger till värden i dina [list](#list-entity) enheter. Vart och ett av dessa värden kan ha en lista över en eller flera synonymer. Endast det normaliserade värdet returneras i svaret.

## <a name="overfitting"></a>Överanpassning

Överanpassning sker när modellen är fixated för de här exemplen och kan inte generalisera sig bra.

## <a name="owner"></a>Ägare

Varje app har en ägare som är den person som skapade appen. Ägaren hanterar behörigheter för programmet i Azure Portal.

## <a name="phrase-list"></a>Fras lista

En [fras lista](luis-concept-feature.md) är en speciell typ av maskin inlärnings funktion som innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på liknande sätt (t. ex. namn på städer eller produkter).

## <a name="prebuilt-model"></a>Fördefinierad modell

En [fördefinierad modell](luis-concept-prebuilt-model.md) är en avsikts-, entitets-eller samling av båda, tillsammans med märkta exempel. Dessa vanliga förinställda modeller kan läggas till i din app för att minska modell utvecklings arbetet som krävs för din app.

### <a name="prebuilt-domain"></a>Fördefinierad domän

En fördefinierad domän är en LUIS-app som kon figurer ATS för en speciell domän, till exempel Home Automation (HomeAutomation) eller restaurang reservationer (RestaurantReservation). Avsikter, yttranden och entiteter har kon figurer ATS för den här domänen.

### <a name="prebuilt-entity"></a>Fördefinierad entitet

En fördefinierad entitet är en entitets LUIS som innehåller vanliga typer av information som t. ex. nummer, URL och e-post. Dessa skapas baserat på offentliga data. Du kan välja att lägga till en fördefinierad entitet som en fristående entitet, eller som en funktion i en entitet

### <a name="prebuilt-intent"></a>Fördefinierad avsikt

En fördefinierad avsikt är en avsikts LUIS som tillhandahåller vanliga typer av information och som levereras med sina egna märkta exempel yttranden.

## <a name="prediction"></a>Förutsägelse

En förutsägelse är en REST-begäran till tjänsten Azure LUIS förutsägelse som tar med nya data (User uttryck) och tillämpar det utbildade och publicerade programmet på dessa data för att avgöra vilka intentor och entiteter som hittas.

### <a name="prediction-key"></a>Förutsägelse nyckel

[Förutsägelse nyckeln](luis-how-to-azure-subscription.md) (som tidigare kallades prenumerations nyckeln) är nyckeln som är kopplad till den Luis-tjänst som du skapade i Azure och som godkänner användningen av förutsägelse slut punkten.

Den här nyckeln är inte redigerings nyckel. Om du har en förutsägelse slut punkts nyckel ska den användas för alla slut punkts begär anden i stället för redigerings nyckeln. Du kan se den aktuella förutsägelse nyckeln i slut punkts-URL: en längst ned på sidan med Azure-resurser på LUIS webbplats. Det är värdet för namn/värde-paret för prenumerations nyckel.

### <a name="prediction-resource"></a>Förutsägelse resurs

Din LUIS förutsägelse resurs är ett hanterbart objekt som är tillgängligt via Azure. Resursen är din åtkomst till den associerade förutsägelsen för Azure-tjänsten. Resursen innehåller förutsägelser.

Förutsägelse resursen har en Azure-typ av `LUIS` .

### <a name="prediction-score"></a>Förutsägelseresultat

[Poängen](luis-concept-prediction-score.md) är ett tal från 0 till 1 som är ett mått på hur trygg systemet är att en viss uttryck matchar ett visst avsikts sätt. En poäng närmare 1 innebär att systemet är mycket säkert om utdata och poängen närmare 0 innebär att systemet är säkert att indata inte matchar en viss utmatning. Poängen i mitten innebär att systemet är mycket osäkert på hur du fattar beslutet.

Ta till exempel en modell som används för att identifiera om en viss kund text innehåller en livsmedels order. Det kan ge en poäng på 1 för "Jag vill beställa en kaffe" (systemet är mycket säker på att det här är en order) och poängen 0 för "mitt team vann game The kväll" (systemet är säkert att det inte är en beställning). Och det kan ha poängen på 0,5 för "Låt oss har en del trietanolamin" (är inte säker på om det är en order eller inte).

## <a name="programmatic-key"></a>Programmerings nyckel

Har bytt namn till [redigerings nyckel](#authoring-key).

## <a name="publish"></a>Publicera

[Publicering](luis-how-to-publish-app.md) innebär att göra en Luis aktiv-version tillgänglig antingen för mellanlagrings-eller produktions [slut punkten](#endpoint).

## <a name="quota"></a>Kvot

LUIS-kvoten är begränsningen för Azure-prenumerations nivån. LUIS-kvoten kan begränsas av både begär Anden per sekund (HTTP-status 429) och totalt antal förfrågningar per månad (HTTP-status 403).

## <a name="schema"></a>Schema

Ditt schema innehåller dina avsikter och entiteter tillsammans med underentiteterna. Schemat planeras ursprungligen för att sedan itereras över tid. Schemat omfattar inte appinställningar, funktioner eller exempel yttranden.

## <a name="sentiment-analysis"></a>Attitydanalys
Sentiment-analys ger positiva eller negativa värden för yttranden som tillhandahålls av [textanalys](../text-analytics/overview.md).

## <a name="speech-priming"></a>Tal Prima

Tal Prima förbättrar igenkänningen av talade ord och fraser som ofta används i ditt scenario med [tal tjänster](../speech-service/overview.md). För tal Prima-aktiverade program används alla LUIS märkta exempel för att förbättra tal igenkännings precisionen genom att skapa en anpassad tal modell för detta specifika program. I ett schack spel vill du till exempel se till att när användaren säger "flytta Knight", tolkas den inte som "flytta natt". LUIS-appen bör innehålla exempel där "Knight" är märkt som en entitet.

## <a name="starter-key"></a>Start nyckel

En kostnads fri nyckel som ska användas när den först börjar med LUIS.

## <a name="synonyms"></a>Synonymer

I LUIS [lista entiteter](reference-entity-list.md)kan du skapa ett normaliserat värde, vilket kan vara en lista över synonymer. Om du till exempel skapar en storleks enhet som har normaliserade värden för små, medel stora och stora mängder. Du kan skapa synonymer för varje värde så här:

|Nomalized-värde| Synonymer|
|--|--|
|Liten| den lilla en, 8 gram|
|Medel| Regular, 12 ounce|
|Stor| stor, 16 ounce|
|Xtra stor| den största, 24 ounce|

Modellen returnerar det normaliserade värdet för entiteten när någon av synonymerna visas i indatamängden.

## <a name="test"></a>Test

Att [testa](luis-concept-test.md) en Luis app innebär att Visa modell förutsägelser.

## <a name="timezone-offset"></a>Förskjutning av tidszon

Slut punkten innehåller [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Detta är det tal i minuter som du vill lägga till eller ta bort från datetimeV2-fördefinierad entitet. Om uttryck till exempel är "vilken tid är den nu?" returneras den aktuella tiden för klient förfrågan. Om din klientbegäran kommer från en robot eller ett annat program som inte är samma som din robots användare, bör du överföra förskjutningen mellan roboten och användaren.

Se [ändra tidszon för en fördefinierad datetimeV2-entitet](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
En [token](luis-language-support.md#tokenization) är den minsta text enheten som Luis kan identifiera. Detta skiljer sig något mellan olika språk.

För **engelska** är en token ett kontinuerligt intervall (inga blank steg eller skiljetecken) av bokstäver och siffror. Ett blank steg är inte en token.

|Fras|Antal token|Förklaring|
|--|--|--|
|`Dog`|1|Ett enstaka ord utan skiljetecken eller blank steg.|
|`RMT33W`|1|Ett post lokaliserings nummer. Det kan bestå av siffror och bokstäver, men det finns inga skiljetecken.|
|`425-555-5555`|5|Ett telefonnummer. Varje skiljetecken är en token så att det blir  `425-555-5555` 5 tokens:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Träna

[Träning](luis-how-to-train.md) är en process för att lära sig Luis om eventuella ändringar i den aktiva versionen sedan den senaste utbildningen.

### <a name="training-data"></a>Träningsdata

Tränings data är den uppsättning information som behövs för att träna en modell. Detta inkluderar schemat, med etiketten yttranden, funktioner och program inställningar.

### <a name="training-errors"></a>Tränings fel

Inlärnings fel är förutsägelser på dina utbildnings data som inte överensstämmer med deras etiketter.

## <a name="utterance"></a>Yttrande

En [uttryck](luis-concept-utterance.md) är indata från användaren som är kort text representativ för en mening i en konversation. Det är en naturliga språk fras, till exempel "bok 2 biljetter till Seattle nästa tisdag". Exempel på yttranden läggs till för att träna modellen och modellen förutsäger på nya uttryck vid körning

## <a name="version"></a>Version

En LUIS- [version](luis-how-to-manage-versions.md) är en specifik instans av ett Luis-program som är associerat med ett Luis app-ID och den publicerade slut punkten. Varje LUIS-app har minst en version.
