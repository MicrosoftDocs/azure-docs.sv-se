---
title: Entitetstyper – LUIS
description: En entitet hämtar data från en användare uttryck vid förutsägelse körning. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten eller andra entiteter genom att använda entiteten som en funktion.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316570"
---
# <a name="extract-data-with-entities"></a>Extrahera data med entiteter

En entitet hämtar data från en användare uttryck vid förutsägelse körning. Ett _valfritt_, sekundärt syfte är att öka förutsägelsen för avsikten eller andra entiteter genom att använda entiteten som en funktion.

Det finns flera typer av entiteter:

* [enhet för maskin inlärning](reference-entity-machine-learned-entity.md) – det här är den primära entiteten. Du bör utforma ditt schema med den här entitetstypen innan du använder andra entiteter.
* Icke-Machine-Learning används som en nödvändig [funktion](luis-concept-feature.md) – för exakta text matchningar, mönster matchningar eller identifiering av fördefinierade entiteter
* [Pattern. any](#patternany-entity) -för att extrahera fri Forms text, till exempel bok titlar från ett [mönster](reference-entity-pattern-any.md)

enheter för maskin inlärning tillhandahåller det bredaste urvalet av data extraherings alternativ. Icke-arbetsinlärnings enheter fungerar med text matchning och används som en [nödvändig funktion](#design-entities-for-decomposition) för en enhet eller avsikt i en maskin inlärning.

## <a name="entities-represent-data"></a>Entiteter representerar data

Entiteter är data som du vill hämta från uttryck, till exempel namn, datum, produkt namn eller viktiga ord grupper. En uttryck kan innehålla flera entiteter eller ingen alls. Ett klient program _kan_ behöva data för att utföra dess uppgift.

Entiteter måste märkas konsekvent i alla utbildnings-yttranden för varje avsikt i en modell.

 Du kan definiera egna entiteter eller använda fördefinierade entiteter för att spara tid för vanliga koncept som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordnings](luis-reference-prebuilt-ordinal.md)tal, [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

|Yttrande|Entitet|Data|
|--|--|--|
|Köp 3 biljetter till New York|Fördefinierat nummer<br>Mål|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Entiteter är valfria men rekommenderas

När [avsikter](luis-concept-intent.md) krävs är entiteter valfria. Du behöver inte skapa entiteter för varje koncept i appen, men endast för de där klient programmet behöver data eller entiteten fungerar som ett tips eller en signal till en annan entitet eller avsikt.

När ditt program utvecklar och ett nytt behov av data identifieras kan du lägga till lämpliga entiteter i LUIS-modellen senare.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>Entitet representerar data extrahering

Entiteten representerar ett data koncept _i uttryck_. En avsikt klassificerar _hela uttryck_.

Tänk på följande fyra yttranden:

|Yttrande|Förväntad avsikt|Extraherade enheter|Förklaring|
|--|--|--|--|
|Help|Hjälp|-|Inget att extrahera.|
|Skicka något|sendSomething|-|Inget att extrahera. Modellen har inte någon nödvändig funktion för att extrahera `something` i den här kontexten och det finns ingen mottagare angiven.|
|Skicka Bob a present|sendSomething|`Bob`, `present`|Modellen extraheras `Bob` genom att lägga till en nödvändig funktion i en fördefinierad entitet `personName` . En enhet för maskin inlärning har använts för att extrahera `present` .|
|Skicka Bob en ruta med choklad|sendSomething|`Bob`, `box of chocolates`|De två viktiga delarna av data `Bob` och `box of chocolates` har extraherats av Machine Learning-enheter.|

## <a name="label-entities-in-all-intents"></a>Märk entiteter i alla avsikter

Entiteter extraherar data oavsett förväntad avsikt. Kontrol lera att du har märkt _alla_ exempel yttranden i alla avsikter. Den `None` avsikt som saknas för enhets etiketter orsakar förvirring även om det fanns mycket mer utbildnings yttranden för andra syften.

## <a name="design-entities-for-decomposition"></a>Utforma entiteter för dekomposition

med Machine Learning-enheter kan du utforma ditt program schema för dekomposition och dela upp ett stort koncept i underentiteter.

Genom att utforma för dekomposition kan LUIS returnera en djup grad av enhets matchning till klient programmet. Detta gör att klient programmet kan fokusera på affärs regler och lämna data upplösning till LUIS.

En dator inlärnings enhet utlöser baserat på den kontext som lärts genom exempel yttranden.

[**enheter för maskin inlärning**](tutorial-machine-learned-entity.md) är de viktigaste utdrags nivåerna. Underentiteter är underordnade entiteter till enheter för maskin inlärning.

## <a name="effective-machine-learned-entities"></a>Gällande entiteter som har lärts

Så här skapar du den inlärda enheten effektivt:

* Dina etiketter ska vara konsekventa för alla syften. Detta inkluderar även yttranden som du anger i **ingen** avsikt som inkluderar den här entiteten. Annars kommer modellen inte att kunna fastställa sekvenserarna effektivt.
* Om du har en dator som är inlärd med underentiteter, se till att de olika order och varianter av entiteten och underentiteter visas i den märkta yttranden. Etiketterat exempel yttranden ska innehålla alla giltiga formulär och innehålla entiteter som visas och som inte finns och som också beordnas i uttryck.
* Du bör undvika att övermontera entiteterna till en mycket fast uppsättning. **Överanpassning** sker när modellen inte generaliserar bra, och är ett vanligt problem i Machine Learning-modeller. Detta innebär att appen inte fungerar med nya data på ett korrekt sätt. I sin tur bör du variera det märkta exemplet yttranden så att appen kan generaliseras utöver de begränsade exempel som du anger. Du bör variera olika underentiteter med tillräckligt stor förändring för modellen för att se mer av konceptet i stället för bara exemplen som visas.

## <a name="effective-prebuilt-entities"></a>Effektiva, inbyggda entiteter

För att skapa effektiva entiteter som extraherar gemensamma data, till exempel de som tillhandahålls av de [fördefinierade enheterna](luis-reference-prebuilt-entities.md), rekommenderar vi följande process.

Förbättra data extraheringen genom att överföra dina egna data till en entitet som en funktion. På så sätt kan alla ytterligare etiketter från dina data lära sig kontexten där personens namn finns i ditt program.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Entitetstyper

En underordnad enhet till en överordnad måste vara en enhet för maskin inlärning. Underentiteten kan använda en enhet som inte är en maskin inlärning som [funktion](luis-concept-feature.md).

Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när den har extraherats.

|Entitetstyp|Syfte|
|--|--|
|[**Datorn – lärt sig**](tutorial-machine-learned-entity.md)|Extrahera kapslade, komplexa data som lärts från etiketterade exempel. |
|[**Lista**](reference-entity-list.md)|Lista över objekt och deras synonymer som extraheras med **exakt text matchning**.|
|[**Mönster. alla**](#patternany-entity)|Entiteten där det är svårt att hitta slutet på enheten, eftersom entiteten är kostnads fri. Endast tillgängligt i [mönster](luis-concept-patterns.md).|
|[**Fördefinierade**](luis-reference-prebuilt-entities.md)|Redan utbildad för att extrahera viss typ av data, till exempel URL eller e-post. Några av dessa fördefinierade entiteter definieras i ett [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Om din kultur eller entitet inte stöds för närvarande, bidrar du till projektet.|
|[**Reguljärt uttryck**](reference-entity-regular-expression.md)|Använder reguljärt uttryck för **exakt text matchning**.|


## <a name="extraction-versus-resolution"></a>Extrahering jämfört med lösning

Entiteter extraherar data när data visas i uttryck. Entiteter ändrar inte eller löser inte data. Entiteten ger ingen lösning om texten är ett giltigt värde för entiteten eller inte.

Det finns olika sätt att ta lösning i extraheringen, men du bör vara medveten om att detta begränsar appens möjlighet att vara immun mot variationer och misstag.

Lista entiteter och reguljära uttryck (text matchning) entiteter kan användas som [nödvändiga funktioner](luis-concept-feature.md#required-features) i en underentitet och som fungerar som ett filter för extraheringen. Du bör använda detta noggrant för att inte hindra appens förmåga att förutsäga.

## <a name="extracting-contextually-related-data"></a>Extrahera sammanhangsbaserade relaterade data

En uttryck kan innehålla två eller flera förekomster av en entitet där innebörden av data baseras på kontexten i uttryck. Ett exempel är en uttryck för att boka en flygning som har två geografiska platser, ursprung och mål.

`Book a flight from Seattle to Cairo`

De två platserna måste extraheras på ett sätt som klient programmet känner av typen av varje plats för att kunna slutföra biljett köpet.

Om du vill extrahera ursprung och mål skapar du två underentiteter som en del av entiteten biljett ordning Machine-Learning. Skapa en nödvändig funktion som använder geographyV2 för var och en av underentiteterna.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Använda de funktioner som krävs för att begränsa entiteter

Läs mer om de [funktioner som krävs](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entiteten Pattern.any

Ett mönster. alla finns bara i ett [mönster](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Överskrida app-gränser för entiteter

Kontakta supporten om du behöver mer än [gränsen](luis-limits.md#model-limits). Det gör du genom att samla in detaljerad information om systemet, gå till [Luis](luis-reference-regions.md#luis-website) -webbplatsen och sedan välja **support**. Om din Azure-prenumeration innehåller support tjänster kontaktar du [teknisk support för Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status-and-errors"></a>Enhets förutsägelse status och fel

LUIS-portalen visar när entiteten har en annan enhets förutsägelse än den entitet som du har valt för ett exempel-uttryck. Den här olika poängen baseras på den aktuella tränade modellen. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS-portalen visar när entiteten har en annan enhets förutsägelse än den entitet som du har valt för ett exempel-uttryck.":::

Den felaktiga texten är markerad i exemplet uttryck, och exempel uttryck-raden har en fel indikator till höger, som visas som en röd triangel. 

Använd den här informationen för att lösa enhets fel med hjälp av ett eller flera av följande:
* Den markerade texten är felmärkt. För att åtgärda, granska, korrigera och träna. 
* Skapa en [funktion](luis-concept-feature.md) för entiteten för att hjälpa till att identifiera entitetens koncept
* Lägg till fler [exempel på yttranden](luis-concept-utterance.md) och etikett med entiteten
* [Granska aktiva utbildnings förslag](luis-concept-review-endpoint-utterances.md) för alla yttranden som tas emot vid förutsägelse slut punkten som kan hjälpa dig att identifiera entitetens koncept.

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om utmärkt [yttranden](luis-concept-utterance.md).

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.

Se [självstudie: extrahera strukturerade data från User uttryck med Machine Learning-entiteter i language Understanding (Luis)](tutorial-machine-learned-entity.md) för att lära dig hur du extraherar strukturerade data från en uttryck med hjälp av enheten för maskin inlärning.

