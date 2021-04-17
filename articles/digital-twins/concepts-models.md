---
title: DTDL-modeller
titleSuffix: Azure Digital Twins
description: Förstå hur Azure Digital Twins använder anpassade modeller för att beskriva entiteter i din miljö.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8942262c2e02670d57b1db324eb154dcc38f00f8
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575402"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Förstå tvillingmodeller i Azure Digital Twins

En viktig egenskap hos Azure Digital Twins är möjligheten att definiera en egen vokabulär och skapa tvillinggrafen i de självdefinierade villkoren i verksamheten. Den här funktionen tillhandahålls via modeller som tillhandahålls av **användaren.** Du kan tänka på modeller som substantiv i en beskrivning av din värld. 

En modell liknar en klass **i ett** objektorienterat programmeringsspråk och definierar en dataform för ett visst begrepp i din verkliga arbetsmiljö. Modeller har namn (till exempel *Room* eller *TemperatureSensor)* och innehåller element som egenskaper, telemetri/händelser och kommandon som beskriver vad den här typen av entitet i din miljö kan göra. Senare kommer du att använda dessa modeller för att skapa [**digitala tvillingar som representerar**](concepts-twins-graph.md) specifika entiteter som uppfyller den här typbeskrivningen.

Azure Digital Twins modeller representeras i JSON-LD-baserade **DTDL (Digital Twin Definition Language).**  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>DTDL (Digital Twin Definition Language) för modeller

Modeller för Azure Digital Twins definieras med hjälp Digital Twins DTDL (Definition Language). 

Du kan visa fullständiga språkspecifikter för DTDL i GitHub: [**Digital Twins Definition Language (DTDL) – version 2.**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)

DTDL är baserat på JSON-LD och är programmeringsspråksoberoende. DTDL är inte exklusivt för Azure Digital Twins, men används också för att representera enhetsdata i andra IoT-tjänster som [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). Azure Digital Twins DTDL **version 2** (användning av DTDL version 1 med Azure Digital Twins har nu gjorts inaktuell). 

Resten av den här artikeln sammanfattar hur språket används i Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Azure Digital Twins om DTDL-implementering

Alla tjänster som använder DTDL implementerar inte exakt samma funktioner i DTDL. IoT-Plug and Play till exempel inte de DTDL-funktioner som är för grafer, medan Azure Digital Twins inte för närvarande implementerar DTDL-kommandon. 

För att en DTDL-modell ska vara kompatibel Azure Digital Twins måste den uppfylla följande krav:

* Alla DTDL-element på den översta nivån i en modell måste vara av typen *interface*. Det beror på Azure Digital Twins modell-API:er kan ta emot JSON-objekt som representerar antingen ett gränssnitt eller en matris med gränssnitt. Därför tillåts inga andra DTDL-elementtyper på den översta nivån.
* DTDL för Azure Digital Twins inte definiera några *kommandon*.
* Azure Digital Twins tillåter endast en enda nivå av komponentkapsling. Det innebär att ett gränssnitt som används som en komponent inte kan ha några komponenter i sig. 
* Gränssnitt kan inte definieras infogade i andra DTDL-gränssnitt. De måste definieras som separata entiteter på den översta nivån med sina egna-ID:er. När ett annat gränssnitt sedan vill inkludera det gränssnittet som en komponent eller genom arv kan det referera till dess ID.

Azure Digital Twins observerar inte heller `writable` attributet för egenskaper eller relationer. Även om detta kan anges enligt DTDL-specifikationer används inte värdet av Azure Digital Twins. I stället behandlas de alltid som skrivbara av externa klienter som har allmänna skrivbehörigheter till Azure Digital Twins tjänsten.

## <a name="elements-of-a-model"></a>Element i en modell

Inom en modelldefinition är det översta kodobjektet ett **gränssnitt**. Det kapslar in hela modellen, och resten av modellen definieras i gränssnittet. 

Ett DTDL-modellgränssnitt kan innehålla noll, ett eller flera av följande fält:
* **Egenskap** – Egenskaper är datafält som representerar tillståndet för en entitet (som egenskaperna i många objektorienterade programmeringsspråk). Egenskaper har lagringsenheter och kan läsas när som helst.
* **Telemetri –** Telemetrifält representerar mått eller händelser och används ofta för att beskriva enhetens sensoravläsningar. Till skillnad från egenskaper lagras inte telemetri på en digital tvilling. Det är en serie med tidsbundna datahändelser som måste hanteras när de inträffar. Mer information om skillnaderna mellan egenskap och telemetri finns i [*avsnittet Egenskaper kontra telemetri*](#properties-vs-telemetry) nedan.
* **Komponent** – Med komponenter kan du skapa ditt modellgränssnitt som en sammansättning av andra gränssnitt om du vill. Ett exempel på en komponent är ett *frontCamera-gränssnitt* (och ett annat komponentgränssnitt *backCamera*) som används för att definiera en modell för en *telefon*. Du måste först definiera ett gränssnitt för *frontCamera* som om det vore en egen modell och sedan kan du referera till det när du definierar *Phone*.

    Använd en komponent för att beskriva något som är en viktig del av din lösning men som inte behöver en separat identitet och som inte behöver skapas, tas bort eller ordnas om i tvillingdiagrammet oberoende av varandra. Om du vill att entiteter ska ha oberoende förekomster i tvillingdiagrammet ska du representera dem som separata digitala tvillingar med olika modeller, anslutna med *relationer* (se nästa punkt).
    
    >[!TIP] 
    >Komponenter kan också användas för organisation, för att gruppera uppsättningar av relaterade egenskaper i ett modellgränssnitt. I så fall kan du tänka på varje komponent som ett namnområde eller en "mapp" i gränssnittet.
* **Relation** – Med relationer kan du representera hur en digital tvilling kan användas med andra digitala tvillingar. Relationer kan representera olika semantiska betydelser, t.ex. *contains* ("floor contains room"), cools ("hvac cools room"), *isBilledTo* ("kant *faktureras* till användaren") osv. Relationer gör att lösningen kan tillhandahålla ett diagram över relaterade entiteter.

> [!NOTE]
> Specifikationen för [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definierar också **Kommandon**, som är metoder som kan köras på en digital tvilling (till exempel ett återställningskommando eller ett kommando för att slå på eller stänga av en fläkt). Kommandon stöds *dock för närvarande inte i Azure Digital Twins.*

### <a name="properties-vs-telemetry"></a>Egenskaper jämfört med telemetri

Här är några ytterligare riktlinjer för att  skilja mellan DTDL-egenskap **och telemetrifält** i Azure Digital Twins.

Skillnaden mellan egenskaper och telemetri för Azure Digital Twins är följande:
* **Egenskaper** förväntas ha lagringsenheter. Det innebär att du kan läsa en egenskap när som helst och hämta dess värde. Om egenskapen är skrivbar kan du även lagra ett värde i egenskapen .  
* **Telemetri är** mer som en ström av händelser. Det är en uppsättning datameddelanden som har korta livslängder. Om du inte ställer in att lyssna efter händelsen och åtgärder som ska vidtas när den inträffar finns det ingen spårning av händelsen vid ett senare tillfälle. Du kan inte komma tillbaka till den och läsa den senare. 
  - I C#-termer är telemetri som en C#-händelse. 
  - I IoT-termer är telemetri vanligtvis ett enda mått som skickas av en enhet.

**Telemetri används** ofta med IoT-enheter eftersom många enheter inte kan eller är intresserade av att lagra de måttvärden som de genererar. De skickar bara ut dem som en ström av "telemetrihändelser". I det här fallet kan du inte när som helst fråga på enheten om det senaste värdet för telemetrifältet. I stället måste du lyssna på meddelandena från enheten och vidta åtgärder när meddelandena tas emot. 

När du utformar en modell i en Azure Digital Twins du därför förmodligen använda egenskaper i **de** flesta fall för att modellera dina tvillingar. På så sätt kan du ha lagringsenheter och möjlighet att läsa och köra frågor mot datafälten.

Telemetri och egenskaper fungerar ofta tillsammans för att hantera indata från enheter. Eftersom all ingress till Azure Digital Twins sker [via](how-to-use-apis-sdks.md)API:er använder du vanligtvis ingressfunktionen för att läsa telemetri- eller egenskapshändelser från enheter och ange en egenskap i Azure Digital Twins som svar. 

Du kan också publicera en telemetrihändelse från Azure Digital Twins API. Precis som med annan telemetri är det en kortlivad händelse som kräver att en lyssnare hanterar.

## <a name="model-inheritance"></a>Modellarv

Ibland kanske du vill specialisera en modell ytterligare. Det kan till exempel vara användbart att ha en generisk *modell, Room*, och specialiserade varianter *ConferenceRoom* och *ConferenceRoom* och Conference . För att uttrycka specialisering stöder DTDL arv: gränssnitt kan ärva från ett eller flera andra gränssnitt. 

I följande exempel åter föreställa sig *planetmodellen* från det tidigare DTDL-exemplet som en undertyp av en *störreBodyBody-modell.* Den "överordnade" modellen definieras först och sedan bygger den "underordnade" modellen på den med hjälp av fältet `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

I det här exemplet *bidrarBodyBody* med ett namn, en mass och en temperatur till *Planet*. Avsnittet är ett gränssnittsnamn eller en matris med gränssnittsnamn (vilket gör att det utökade gränssnittet kan `extends` ärva från flera överordnade modeller om det behövs).

När arv har tillämpats exponerar det utökande gränssnittet alla egenskaper från hela arvskedjan.

Det utökade gränssnittet kan inte ändra någon av definitionerna för de överordnade gränssnitten. Det kan bara lägga till i dem. Den kan inte heller omdefiniera en funktion som redan har definierats i något av dess överordnade gränssnitt (även om funktionerna har definierats att vara desamma). Om ett överordnat gränssnitt till exempel definierar en egenskapsvikt får det utökande gränssnittet inte innehålla en massdeklaration , även om `double` det också är en   `double` .

## <a name="model-code"></a>Modellkod

Tvillingtypmodeller kan skrivas i valfri textredigerare. DTDL-språket följer JSON-syntaxen, så du bör lagra modeller med filnamnstillägget *.json*. Med JSON-tillägget kan många programmeringstextredigerare tillhandahålla grundläggande syntaxkontroll och markering för dina DTDL-dokument. Det finns också ett [DTDL-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) för [Visual Studio Code](https://code.visualstudio.com/).

### <a name="possible-schemas"></a>Möjliga scheman

Enligt DTDL kan schemat  för egenskaps- och *telemetriattribut* vara av primitiva standardtyper– , , och – och andra typer `integer` som och `double` `string` `Boolean` `DateTime` `Duration` . 

Förutom primitiva typer kan fälten *Egenskap* *och Telemetri* ha följande komplexa typer:
* `Object`
* `Map`
* `Enum`

*Telemetrifält* har också stöd för `Array` .

### <a name="example-model"></a>Exempelmodell

Det här avsnittet innehåller ett exempel på en typisk modell som skrivits som ett DTDL-gränssnitt. Modellen beskriver **planeter**, var och en med ett namn, en massa och en temperatur.
 
Tänk på att planeter också kan **interagera med månar** som är deras satelliter och kan innehålla **månar**. I exemplet nedan uttrycker `Planet` modellen anslutningar till dessa andra entiteter genom att referera till två externa modeller– och `Moon` `Crater` . Dessa modeller definieras också i exempelkoden nedan, men hålls mycket enkla så att de inte försämrar det primära `Planet` exemplet.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Fälten i modellen är:

| Fält | Beskrivning |
| --- | --- |
| `@id` | En identifierare för modellen. Måste ha formatet `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifierar den typ av information som beskrivs. För ett gränssnitt är typen *Interface*. |
| `@context` | Anger [kontexten](https://niem.github.io/json/reference/json-ld/context/) för JSON-dokumentet. Modeller bör använda `dtmi:dtdl:context;2` . |
| `displayName` | [valfritt] Gör att du kan ge modellen ett eget namn om du vill. |
| `contents` | Alla återstående gränssnittsdata placeras här som en matris med attributdefinitioner. Varje attribut måste ange `@type` en ( *Egenskap,* *Telemetri*, Kommando , Relation eller *Komponent*) för att identifiera den typ av gränssnittsinformation som beskrivs, och sedan en uppsättning egenskaper som definierar det faktiska attributet (till exempel och för att definiera en  `name` `schema` *egenskap*). |

> [!NOTE]
> Observera att komponentgränssnittet (*I* det här exemplet ) definieras i samma matris som gränssnittet som använder det (*Planet*). Komponenterna måste definieras på det här sättet i API-anrop för att gränssnittet ska kunna hittas.

## <a name="best-practices-for-designing-models"></a>Metodtips för att utforma modeller

När du utformar modeller för att återspegla entiteterna i [](concepts-query-language.md) din miljö kan det vara bra att titta framåt och överväga frågekonsekvenserna av din design. Du kanske vill utforma egenskaper på ett sätt som undviker stora resultatuppsättningar från diagramgenrering. Du kanske också vill modellera relationer som ska besvaras i en enskild fråga som relationer på en nivå.

### <a name="validating-models"></a>Verifiera modeller

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Verktyg för modeller 

Det finns flera exempel som gör det ännu enklare att hantera modeller och ontologier. De finns på den här [lagringsplatsen: Verktyg Digital Twins DTDL (Definition Language).](https://github.com/Azure/opendigitaltwins-tools)

I det här avsnittet beskrivs den aktuella uppsättningen exempel i detalj.

### <a name="model-uploader"></a>Modelluppladdare 

_**För att ladda upp modeller till Azure Digital Twins**_

När du är klar med att skapa, utöka eller välja dina modeller kan du ladda upp dem till din Azure Digital Twins instans så att de blir tillgängliga för användning i din lösning. Detta görs med hjälp Azure Digital Twins [API:er](how-to-use-apis-sdks.md), enligt beskrivningen i [*How-to: Manage DTDL models*](how-to-manage-model.md#upload-models).

Men om du har många modeller att ladda upp– eller om de har många beroenden som skulle göra det svårt att ordna enskilda uppladdningar – kan du använda det här exemplet för att ladda upp många modeller samtidigt: [**Azure Digital Twins Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Följ anvisningarna i exemplet för att konfigurera och använda det här projektet för att ladda upp modeller till din egen instans.

### <a name="model-visualizer"></a>Modell visualiserare 

_**För visualisering av modeller**_

När du har laddat upp modeller till din Azure Digital Twins-instans kan du visa modellerna i din Azure Digital Twins-instans, inklusive arv och modellrelationer, med [**hjälp av Azure Digital Twins Model Visualizer**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Det här exemplet är för närvarande i utkasttillstånd. Vi uppmuntrar digital twins-utvecklings communityn att utöka och bidra till exemplet. 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att skapa modeller baserade på branschstandardbaserade ontologier: [ *Begrepp: Vad är en ontologi?*](concepts-ontologies.md)

* Fördjupa dig i att hantera modeller med [ *API-åtgärder: Så här hanterar du DTDL-modeller*](how-to-manage-model.md)

* Lär dig mer om hur modeller används för att skapa digitala tvillingar: [ *Koncept: Digitala tvillingar och tvillinggrafen*](concepts-twins-graph.md)

