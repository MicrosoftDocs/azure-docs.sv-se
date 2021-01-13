---
title: Anpassade modeller
titleSuffix: Azure Digital Twins
description: Förstå hur Azure Digital-enheter använder användardefinierade modeller för att beskriva entiteter i din miljö.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 599bb93e747acf504a4ebf43aaea771ed5064886
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131397"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Förstå dubbla modeller i Azure Digitals flätas

En viktig egenskap för Azure Digitals dubbla är möjligheten att definiera en egen vokabulär och skapa ett dubbel diagram i de självdefinierade villkoren i din verksamhet. Den här funktionen tillhandahålls genom användardefinierade **modeller**. Du kan tänka på modeller som Substantiv i en beskrivning av din värld. 

En modell liknar en **klass** i ett objektorienterad programmeringsspråk som definierar en data form för ett visst koncept i din verkliga arbets miljö. Modeller har namn (t. ex. *rum* eller *TemperatureSensor*) och innehåller element som egenskaper, telemetri/händelser och kommandon som beskriver vad den här typen av entitet i din miljö kan göra. Senare kommer du att använda dessa modeller för att skapa [**digitala**](concepts-twins-graph.md) delar som representerar vissa entiteter som uppfyller den här typen beskrivning.

Azure Digitals-modeller visas i det JSON-LD-baserade **digitala DTDL-språket (definitions språk)**.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digitalt DTDL (Digital Definition Language) för modeller

Modeller för digitala Azure-dubbla grupper definieras med hjälp av DTDL (Digital enforming Definition Language). DTDL baseras på JSON-LD och är programmerings språk oberoende. DTDL är inte exklusiv för Azure Digitals, men används också för att representera enhets data i andra IoT-tjänster som [iot plug and Play](../iot-pnp/overview-iot-plug-and-play.md). 

Azure Digitals flätar använder **DTDL _version 2_**. Mer information om den här versionen av DTDL finns i Specifikations dokumentationen för GitHub: [*digital, Definition Language (DTDL)-version 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Användning av DTDL _version 1_ med Azure Digitals dubbla är nu föråldrad.

> [!NOTE] 
> Alla tjänster som använder DTDL implementerar exakt samma funktioner i DTDL. IoT Plug and Play använder till exempel inte de DTDL-funktioner som är för grafer, medan Azures digitala dubbla för närvarande inte implementerar DTDL-kommandon.
>
> Mer information om de DTDL-funktioner som är speciella för Azure Digitals dubbla finns i avsnittet längre fram i den här artikeln om [Azure Digitals DTDL-implementerings information](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Element i en modell

I en modell definition är kod elementet på den översta nivån ett **gränssnitt**. Detta kapslar in hela modellen och resten av modellen definieras i gränssnittet. 

Ett DTDL modell gränssnitt kan innehålla noll, ett eller flera av följande fält:
* **Egenskap** – egenskaper är data fält som representerar statusen för en entitet (som egenskaper i många objektorienterade programmeringsspråk). Egenskaperna har lagrings utrymme och kan läsas när som helst.
* **Telemetri** – telemetri-fält representerar mått eller händelser och används ofta för att beskriva enheternas sensor läsningar. Till skillnad från egenskaper lagras inte telemetri på ett digitalt, Det är en serie tidsbegränsade data händelser som måste hanteras när de inträffar. Mer information om skillnaderna mellan egenskaper och telemetri finns i avsnittet om [*egenskaper och telemetri*](#properties-vs-telemetry) nedan.
* **Komponent** – komponenter gör att du kan bygga ditt modell gränssnitt som en sammansättning av andra gränssnitt, om du vill. Ett exempel på en komponent är ett *frontCamera* -gränssnitt (och en annan *elkamera* för komponent gränssnitt) som används för att definiera en modell för en *telefon*. Du måste först definiera ett gränssnitt för *frontCamera* som om det vore en egen modell, och sedan kan du referera till det när du definierar *telefon*.

    Använd en komponent för att beskriva något som är en del av din lösning, men som inte behöver en separat identitet, och som inte behöver skapas, tas bort eller ordnas om i det dubbla diagrammet oberoende av varandra. Om du vill att entiteter ska ha oberoende förekomster i den dubbla grafen, representerar de som separata digitala delar av olika modeller, anslutna med *relationer* (se nästa punkt).
    
    >[!TIP] 
    >Komponenter kan också användas för organisationen för att gruppera uppsättningar med relaterade egenskaper i ett modell gränssnitt. I den här situationen kan du tänka på varje komponent som ett namn område eller "mapp" i gränssnittet.
* **Relations hip** -relationer gör att du kan representera hur ett digitalt garn kan användas med andra digitala dubbla. Relationer kan representera olika semantiska betydelser, till exempel *innehåller* ("golv innehåller rum"), *häftiga* ("HVAC cools Room"), *isBilledTo* ("kompressor är fakturerad till användare") osv. Relationer tillåter lösningen att tillhandahålla ett diagram över relaterade entiteter.

> [!NOTE]
> I [spec for DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definieras även **kommandon**, som är metoder som kan köras på ett digitalt värde (som ett återställnings kommando eller ett kommando för att aktivera eller inaktivera en fläkt). *Kommandon stöds dock inte för närvarande i Azure Digital-dubbla.*

### <a name="properties-vs-telemetry"></a>Egenskaper kontra telemetri

Här följer några ytterligare anvisningar om hur du skiljer mellan DTDL- **Egenskaper** och **telemetridata** i Azure Digitals.

Skillnaden mellan egenskaper och telemetri för Azures digitala dubbla modeller är följande:
* **Egenskaper** förväntas ha lagrings utrymme. Det innebär att du kan läsa en egenskap när som helst och hämta dess värde. Om egenskapen är skrivbar kan du också lagra ett värde i egenskapen.  
* **Telemetri** är mer som en data ström med händelser. Det är en uppsättning data meddelanden som har korta lifespans. Om du inte konfigurerar att lyssna efter händelsen och åtgärder som ska vidtas, finns det ingen spårning av händelsen vid ett senare tillfälle. Du kan inte komma tillbaka till den och läsa den senare. 
  - I C#-termer är telemetri som en C#-händelse. 
  - I IoT-termer är telemetri vanligt vis ett enda mått som skickas av en enhet.

**Telemetri** används ofta med IoT-enheter, eftersom många enheter inte kan eller är intresserade av och lagrar de mått värden som de genererar. De skickar dem bara ut som en ström av "telemetri"-händelser. I det här fallet kan du när som helst inte fråga på enheten efter det senaste värdet i fältet telemetri. I stället måste du lyssna på meddelandena från enheten och vidta åtgärder när meddelandena tas emot. 

När du designar en modell i Azure Digitals, kommer du förmodligen att använda **Egenskaper** i de flesta fall för att modellera dina dubbla. På så sätt kan du använda lagrings utrymmet och kunna läsa och fråga data fälten.

Telemetri och egenskaper fungerar ofta tillsammans för att hantera data från enheter. Eftersom alla ingångar till Azure Digitals dubbla är via [API: er](how-to-use-apis-sdks.md), använder du normalt din ingångs funktion för att läsa telemetri-eller egenskaps händelser från enheter och ange en egenskap i ADT som svar. 

Du kan också publicera en telemetri-händelse från Azure Digitals dubbla API: er. Precis som med andra telemetri är det en kort periods händelse som kräver att en lyssnare hanterar.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digitals dubbla DTDL implementerings information

För att en DTDL-modell ska vara kompatibel med Azure Digital-dubbla, måste den uppfylla dessa krav.

* Alla DTDL-element på översta nivån i en modell måste vara av typen *Interface*. Detta beror på att API: er för Azure Digitals dubbla modeller kan ta emot JSON-objekt som representerar antingen ett gränssnitt eller en matris med gränssnitt. Därför tillåts inga andra DTDL-element typer på den översta nivån.
* DTDL för Azure Digital-dubbla får inte definiera några *kommandon*.
* Azure Digital-dubbla är bara tillåta en enda nivå av komponent kapsling. Det innebär att ett gränssnitt som används som en komponent inte kan ha några själva komponenter. 
* Gränssnitt kan inte definieras infogade i andra DTDL-gränssnitt. de måste definieras som separata enheter på den översta nivån med sina egna ID: n. När ett annat gränssnitt vill inkludera det gränssnittet som en komponent eller genom arv kan det referera till dess ID.

Azure Digitals flätar tar inte heller hänsyn till `writable` attributet på egenskaper eller relationer. Även om detta kan ställas in enligt DTDL-specifikationerna används inte värdet av Azure Digital-dubbla. I stället behandlas de alltid som skrivbara av externa klienter som har allmänna Skriv behörigheter till tjänsten Azure Digitals dubbla.

## <a name="example-model-code"></a>Exempel modell kod

Dubbla typ modeller kan skrivas i valfri text redigerare. DTDL-språket följer JSON-syntax, så du bör lagra modeller med fil namns tillägget *. JSON*. Med JSON-tillägget kan många programmerings text redigerare tillhandahålla grundläggande syntaxkontroll och markeringar för dina DTDL-dokument. Det finns också ett [DTDL-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) tillgängligt för [Visual Studio Code](https://code.visualstudio.com/).

Det här avsnittet innehåller ett exempel på en typisk modell som skrivs som ett DTDL-gränssnitt. Modellen beskriver **planet**, var och en med ett namn, en massa och en temperatur.
 
Tänk på att planeterna också kan samverka med **måne** som är deras satelliter och kan innehålla **lådor**. I exemplet nedan `Planet` uttrycker modellen anslutningar till dessa andra entiteter genom att referera till två externa modeller – `Moon` och `Crater` . Dessa modeller definieras också i exempel koden nedan, men de är mycket enkla så att de inte subtraheras från det primära `Planet` exemplet.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Fälten i modellen är:

| Fält | Beskrivning |
| --- | --- |
| `@id` | En identifierare för modellen. Måste vara i formatet `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifierar vilken typ av information som beskrivs. För ett gränssnitt är typen *gränssnitt*. |
| `@context` | Ställer in [kontexten](https://niem.github.io/json/reference/json-ld/context/) för JSON-dokumentet. Modeller bör använda `dtmi:dtdl:context;2` . |
| `displayName` | valfritt Gör att du kan ge modellen ett eget namn om du vill. |
| `contents` | Alla återstående gränssnitts data placeras här, som en matris med attributdefinitioner. Varje attribut måste innehålla en `@type` (*egenskap*, *telemetri*, *kommando*, *relation* eller *komponent*) för att identifiera sorteringen av gränssnitts information som anges och sedan en uppsättning egenskaper som definierar det faktiska attributet (till exempel `name` och `schema` för att definiera en *egenskap*). |

> [!NOTE]
> Observera att komponent gränssnittet (*Crater* i det här exemplet) definieras i samma matris som gränssnittet som använder det (*planet*). Komponenter måste definieras på det här sättet i API-anrop för att gränssnittet ska kunna hittas.

### <a name="possible-schemas"></a>Möjliga scheman

Som per DTDL kan schemat för attribut för *egenskap* och *telemetri* vara av standard typer av primitiver,,, `integer` `double` `string` och `Boolean` – och andra typer som `DateTime` och `Duration` . 

Förutom primitiva typer kan fälten *egenskap* och *telemetri* ha följande komplexa typer:
* `Object`
* `Map`
* `Enum`

*Telemetri* -fält stöder också `Array` .

### <a name="model-inheritance"></a>Modell arv

Ibland kanske du vill specialisera en modell ytterligare. Det kan till exempel vara användbart att ha ett allmänt modell *rum* och specialiserade varianter *ConferenceRoom* och *gymmet*. För att uttrycka specialisering har DTDL stöd för arv: gränssnitt kan ärva från ett eller flera andra gränssnitt. 

I följande exempel förändrar du *planet* modellen från det tidigare DTDL-exemplet som en undertyp till en större *CelestialBody* -modell. Modellen "Parent" definieras först och sedan bygger modellen "Child" på den med hjälp av fältet `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

I det här exemplet bidrar *CelestialBody* till ett namn, en massa och en temperatur för *planet*. `extends`Avsnittet är ett gränssnitts namn eller en matris med gränssnitts namn (vilket gör att det utökade gränssnittet kan ärva från flera överordnade modeller om det behövs).

När arvet har tillämpats visar det utökade gränssnittet alla egenskaper från hela arvs kedjan.

Det utökade gränssnittet kan inte ändra någon av definitionerna för de överordnade gränssnitten. den kan bara läggas till i dem. Det går inte heller att omdefiniera en funktion som redan har definierats i något av dess överordnade gränssnitt (även om funktionerna har definierats som samma). Om ett överordnat gränssnitt till exempel definierar en `double` egenskaps *vikt* får inte det utökade gränssnittet innehålla en deklaration av *vikten*, även om det är en `double` .

## <a name="best-practices-for-designing-models"></a>Metod tips för att utforma modeller

När modeller konstrueras för att återspegla entiteterna i din miljö kan det vara praktiskt att se framåt och ta hänsyn till [frågans](concepts-query-language.md) konsekvenser för din design. Du kanske vill utforma egenskaper på ett sätt som gör det möjligt att undvika stora resultat uppsättningar från diagram genom gången. Du kanske också vill modellera relationer som ska besvaras i en enda fråga som relationer på en enda nivå.

### <a name="validating-models"></a>Validerar modeller

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="integrating-with-industry-standard-models"></a>Integrera med bransch standard modeller

Med hjälp av modeller som baseras på bransch standarder eller använder standard Ontology-representation, t. ex. RDF eller OWL, får du en rik start punkt när du utformar dina Azures digitala dubbla modeller. Genom att använda bransch modeller kan du också hjälpa till med standardisering och informations delning.

För att kunna användas med digitala Azure-datorer måste en modell representeras i DTDL (JSON-LD-based [**digital)**](concepts-models.md). För att använda en modell som är bransch standard måste du därför först konvertera den till DTDL så att Azure Digital-enheter kan använda den. DTDL-modellen fungerar sedan som källan till sanningen för modellen i Azure Digitals-modeller.

Det finns två huvudsakliga sökvägar som integrerar bransch standard modeller med DTDL, beroende på din situation:
* Om du ännu inte har skapat dina modeller kan du utforma dem runt **befintliga startDTDL-Ontologies** som innehåller språk som är specifika för din bransch.
* Om du redan har befintliga modeller som baseras på en bransch standard måste du **konvertera dem till DTDL** för att kunna ta dem till Azure Digital-dubbla.

Mer information om båda dessa processer finns i [*How-to: integrera bransch standard modeller*](how-to-integrate-models.md).

## <a name="next-steps"></a>Nästa steg

Se Hantera modeller med DigitalTwinModels-API: er:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)

Du kan också läsa mer om hur digitala dubbla modeller skapas baserat på modeller:
* [*Koncept: digitala och dubbla grafer*](concepts-twins-graph.md)

