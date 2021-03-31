---
title: Migrera till v3-enhet för maskin inlärning
description: V3-redigeringen innehåller en ny entitetstyp, en enhet för maskin inlärning, tillsammans med möjligheten att lägga till relationer i enheten för maskin inlärning och andra entiteter eller funktioner i programmet.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91324696"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrera till v3-redigering av entitet

V3-redigeringen innehåller en ny entitetstyp, en enhet för maskin inlärning, tillsammans med möjligheten att lägga till relationer i enheten för maskin inlärning och andra entiteter eller funktioner i programmet.

## <a name="entities-are-decomposable-in-v3"></a>Entiteter är sammanställnings bara i v3

Entiteter som skapats med v3-redigering av API: er, antingen med hjälp av [API: er](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) eller med portalen, gör att du kan skapa en modell för skiktad enhet med en överordnad och underordnad Den överordnade enheten är känd som en **enhet för maskin inlärning** och de underordnade är kända som **underentiteter** till den inlärda enheten.

Varje underentitet är också en maskin inlärnings enhet, men med de tillagda konfigurations alternativen för funktioner.

* De **funktioner som krävs** är regler som garanterar att en entitet extraheras när den matchar en funktion. Regeln definieras av den obligatoriska funktionen i modellen:
    * [Fördefinierad entitet](luis-reference-prebuilt-entities.md)
    * [Entitet för reguljära uttryck](reference-entity-regular-expression.md)
    * [Lista entitet](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hur kan de här nya relationerna jämföra med v2-redigering

V2-redigering tillhandahåller hierarkiska och sammansatta entiteter tillsammans med roller och funktioner för att utföra samma uppgift. Eftersom entiteter, funktioner och roller inte uttryckligen är relaterade till varandra, var det svårt att förstå hur LUIS underförstådda relationen under förutsägelse.

Med v3 är relationen explicit och utformad av appens författare. På så sätt kan du, som appens författare, till:

* Visuellt se hur LUIS förutsäger dessa relationer i exempel yttranden
* Testa för dessa relationer antingen med det [interaktiva test fönstret](luis-interactive-test.md) eller på slut punkten
* Använd de här relationerna i klient programmet, via ett välstrukturerat, namngivet, kapslat [JSON-objekt](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planering

När du migrerar bör du tänka på följande i migrerings planen:

* Säkerhetskopiera LUIS-appen och utför migreringen på en separat app. Med en v2-och v3-app tillgänglig samtidigt kan du verifiera de ändringar som krävs och konsekvenserna av förutsägelse resultatet.
* Avbilda aktuella mått för förutsägelse av förutsägelse
* Avbilda aktuell instrument panels information som en ögonblicks bild av appens status
* Granska befintliga avsikter, entiteter, fras listor, mönster och batch-tester
* Följande element kan migreras **utan ändring**:
    * Avsikter
    * Entiteter
        * Entitet för reguljära uttryck
        * Lista entitet
    * Funktioner
        * Fras lista
* Följande element måste migreras **med ändringar**:
    * Entiteter
        * Hierarkisk entitet
        * Sammansatt entitet
    * Roller – roller kan bara tillämpas på en dator-inlärning (överordnad) entitet. Roller kan inte tillämpas på underentiteter
    * Batch-tester och mönster som använder hierarkiska och sammansatta entiteter

När du utformar din migrering, lämna tid för att granska de slutliga enheterna för maskin inlärning när alla hierarkiska och sammansatta entiteter har migrerats. När en rak migrering fungerar, efter att du har gjort ändringen och granskat batch-testresultat och förutsägelse-JSON, kan det mer enhetliga JSON-nätverket leda till att du gör ändringar så att den slutliga informationen som skickas till klient sidan är ordnad annorlunda. Detta liknar kod omstrukturering och bör behandlas med samma gransknings process som organisationen har på plats.

Om du inte har batch-test för din v2-modell och migrerar batch-test till v3-modellen som en del av migreringen, kommer du inte att kunna verifiera hur migreringen påverkar resultatet av slut punkten.

## <a name="migrating-from-v2-entities"></a>Migrera från v2 entiteter

När du börjar flytta till v3-redigerings modellen bör du fundera över hur du flyttar till entiteten för maskin inlärning och dess underentiteter och funktioner.

I följande tabell noterar du vilka entiteter som behöver migrera från en v2 till en v3-enhets design.

|V2 redigerings enhets typ|V3-redigering av enhets typ|Exempel|
|--|--|--|
|Sammansatt entitet|Enheten har lärts|[Lära sig mer](#migrate-v2-composite-entity)|
|Hierarkisk entitet|enhets roll för Machine-Learning|[Lära sig mer](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrera den sammansatta v2-entiteten

Varje underordnad av v2-kompositen bör representeras av en underordnad enhet för v3-enheten för maskin inlärning. Om den sammansatta underordnade är ett fördefinierat, reguljärt uttryck eller en List-entitet, ska detta användas som en nödvändig funktion på underentiteten.

Att tänka på när du planerar att migrera en sammansatt entitet till en enhet för maskin inlärning:
* Det går inte att använda underordnade entiteter i mönster
* Underordnade entiteter delas inte längre
* Underordnade entiteter måste märkas om de inte har använts av datorn

### <a name="existing-features"></a>Befintliga funktioner

Alla fras listor som används för att öka ord i den sammansatta entiteten ska användas som en funktion för antingen den (överordnade) entiteten, underentiteten (underordnad) entiteten eller avsikten (om fras listan bara gäller för ett avsikt). Planera att lägga till funktionen i entiteten där den bör öka markant. Lägg inte till funktionen allmänt i den Machine-Learning (överordnade) entiteten, om den inte kommer att öka förutsägelsen för en underordnad enhet (underordnad).

### <a name="new-features"></a>Nya funktioner

I v3-redigering lägger du till ett planerings steg för att utvärdera entiteter som möjliga funktioner för alla entiteter och avsikter.

### <a name="example-entity"></a>Exempel entitet

Den här entiteten är endast ett exempel. Din egen enhets migrering kan kräva andra överväganden.

Överväg en v2-sammansatt för att ändra en pizza `order` som använder:
* fördefinierad datetimeV2 för leverans tid
* fras lista för att öka vissa ord, till exempel pizza, cirkel, crust och topping
* lista entiteter för att identifiera toppings, till exempel svamp, oliver, pepperoni.

Ett exempel på en uttryck för den här entiteten är:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad komponent enhet med namnet `Order`|Överordnad maskin inlärnings enhet med namnet `Order`|
|Underordnade-fördefinierade datetimeV2|* Migrera en fördefinierad entitet till en ny app.<br>* Lägg till nödvändig funktion på överordnad datetimeV2.|
|Entitet med underordnad lista för toppings|* Migrera List entiteten till en ny app.<br>* Lägg sedan till en nödvändig funktion på den överordnade entiteten lista.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrera v2-hierarkisk entitet

I v2-redigering tillhandahölls en hierarkisk entitet innan befintliga roller i LUIS. Båda har samma syfte att extrahera entiteter baserat på Sammanhangs användning. Om du har hierarkiska entiteter kan du tänka på dem som enkla entiteter med roller.

I v3-redigering:
* En roll kan tillämpas på den dator-inlärning (överordnade) entiteten.
* Det går inte att använda en roll för några underentiteter.

Den här entiteten är endast ett exempel. Din egen enhets migrering kan kräva andra överväganden.

Överväg en hierarkisk entitet i v2 för att ändra en pizza `order` :
* var varje underordnad bestämmer antingen en ursprunglig topping eller den slutliga topping

Ett exempel på en uttryck för den här entiteten är:

`Change the topping from mushrooms to olives`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad komponent enhet med namnet `Order`|Överordnad maskin inlärnings enhet med namnet `Order`|
|Underordnad-hierarkisk entitet med ursprunglig och slutgiltig pizza-topping|* Lägg till roll för `Order` varje topping.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>API-ändrings begränsning ersatt med nödvändig funktion

Den här ändringen gjordes i maj 2020 på Build-konferensen och gäller endast v3-redigerings-API: erna där en app använder en begränsad funktion. Om du migrerar från v2-redigering till v3-redigering eller inte har använt v3-begränsade funktioner, hoppar du över det här avsnittet.

**Funktioner** – möjlighet att kräva en befintlig entitet som en funktion till en annan modell och bara extrahera den modellen om entiteten identifieras. Funktionen har inte ändrats men API och terminologi har ändrats.

|Föregående terminologi|Ny terminologi|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Automatisk migrering

Från och med **juni 19 2020** får du inte skapa begränsningar program mässigt med hjälp av det tidigare redigerings-API: t som visade den här funktionen.

Alla befintliga villkors funktioner kommer automatiskt att migreras till den obligatoriska funktions flaggan. Inga program ändringar krävs för ditt förutsägelse-API och ingen resulterande ändring av kvaliteten på förutsägelse noggrannhet.

#### <a name="luis-portal-changes"></a>LUIS-portalen ändras

LUIS för Preview-portalen refererade till den här funktionen som en **begränsning**. Den aktuella LUIS-portalen anger att den här funktionen är en **nödvändig funktion**.

#### <a name="previous-authoring-api"></a>Föregående redigerings-API

Den här funktionen användes i för hands versionen av **[skapa underordnad entitet](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** som del av en entitets definition med hjälp av `instanceOf` egenskapen för en entitets underordnade:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nytt redigerings-API

Den här funktionen används nu med **[Relations-API: t för entiteten Lägg till entitet](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** med hjälp av `featureName` `isRequired` egenskaperna och. `featureName`Egenskapens värde är namnet på modellen.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Nästa steg

* [Resurser för utvecklare](developer-reference-resource.md)
