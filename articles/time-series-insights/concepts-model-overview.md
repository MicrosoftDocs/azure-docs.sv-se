---
title: Tids serie modell-Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om tids serie modellen i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698101"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Tids serie modell i Azure Time Series Insights Gen2

I den här artikeln beskrivs tids serie modellen, funktionerna och hur du börjar skapa och uppdatera dina egna modeller i Azure Time Series Insights Gen2-miljön.

> [!TIP]
>
> * Gå till [Contosos demonstrations](https://insights.timeseries.azure.com/preview/samples) miljö för en real tids serie modell exempel.
> * Lär dig [hur du arbetar med tids serie modellen](./how-to-edit-your-model.md) med hjälp av Azure Time Series Insights Explorer.

## <a name="summary"></a>Sammanfattning

Traditionellt har data som samlas in från IoT-enheter saknar sammanhangsbaserad information, vilket gör det svårt att hitta och analysera sensorer snabbt. Det främsta skälet till tids serie modellen är att förenkla sökning och analys av IoT-eller Time Series-data. Den uppnår detta mål genom att aktivera hantering, underhåll och anrikning av Time Series-data för att förbereda konsument klara data uppsättningar för analys.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: Contosos nya smarta ugn

**Överväg det fiktiva scenariot för en smartphone Smart ugn.** I det här scenariot antar vi att varje contoso Smart ugn har fem temperatur sensorer, en för var och en av de fyra främsta brännarena och en för ugnen. Tills nyligen har varje contoso-temperatur sensor skickat, lagrat och visualiserat sina data individuellt. För att kunna övervaka köks enheten, contoso förlitar sig på Basic-diagram, en för varje enskild sensor.

Medan contoso var uppfyllt med sin första data-och visualiserings lösning visade sig flera begränsningar:

* Kunder ville veta hur frekvent den totala ugnen skulle få när de flesta av de främsta brännarena var på. Contoso hade mer svårt att analysera och presentera ett enhetligt svar om villkoren i den övergripande ugnen.
* Contoso-tekniker ville verifiera att de främsta brännare som körs samtidigt inte skulle resultera i ineffektiv energi spar läge. Det var svårt att referera till vilka temperatur-och spännings sensorer som var associerade med varandra och hur du hittar dem i butiken.
* Contosos kvalitets säkrings team ville granska och jämföra historiken mellan två sensor versioner. Det gick inte att avgöra vilka data som tillhörde vilken sensor version som hör.

Utan möjligheten att strukturera, organisera och definiera den överåtgående tids serie modellen för smart ugn, är varje temperatur sensor befinnad, isolerad och mindre informativa data punkter. Att omvandla dessa data punkter till användbara insikter var svårare eftersom varje data uppsättning levde oberoende av de andra.

De här begränsningarna visar vikten av Smart data agg regering och visualiserings verktyg som medföljer Contosos nya ugn:

* Data visualiseringen är användbar när du kan associera och kombinera data i en bekväm vy. Ett exempel visar spännings sensorer tillsammans med temperatur sensorer.
* Att hantera flerdimensionella data för flera entiteter tillsammans med jämförelse-, zoomnings-och tidsintervalls funktioner kan vara svåra att utföra.

**Time Series-modellen är en praktisk lösning** för många av de scenarier som påträffas i detta fiktiva exempel:

[![Exempel på Smart ugns diagram modell med tids serier](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Time Series-modellen spelar en viktig roll i frågor och navigering eftersom den contextualizes data genom att tillåta att jämförelser ritas över tidsintervall och mellan sensor-och enhets typer. (**A**)
* Data är ytterligare sammanhangsbaserade eftersom data som behålls i Time Series-modellen bevarar Time Series-frågans beräkningar som variabler och återanvänder dem vid tidpunkten för frågan.
* Time Series-modellen ordnar och samlar data för förbättrade visualiserings-och hanterings funktioner. (**B**)

### <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkelt att hantera Time Series-contextualization, aktiverar Time Series-modellen följande funktioner i Azure Time Series Insights Gen2. Det hjälper dig att:

* Redigera och hantera beräkningar eller formler som använder skalära funktioner, mängd åtgärder och så vidare.
* Definiera överordnade och underordnade relationer för att aktivera navigering, sökning och referens.
* Definiera egenskaper som är associerade med instanserna, definierade som *instans fält* och Använd dem för att skapa hierarkier.

### <a name="components"></a>Komponenter

Tids serie modellen har tre huvud komponenter:

* [Tids serie modell instanser](#time-series-model-instances)
* [Hierarkier för tids serie modell](#time-series-model-hierarchies)
* [Modell typer för tids serier](#time-series-model-types)

Dessa komponenter kombineras för att ange en tids serie modell och organisera dina data.

[![Översikts diagram över tids serie modellen](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

En tids serie modell kan skapas och hanteras via [Azure Time Series Insights Explorer](./concepts-ux-panels.md). Inställningarna för tids serie modellen kan hanteras via [API: t för modell inställningar](/rest/api/time-series-insights/reference-model-apis).

## <a name="time-series-model-instances"></a>Tids serie modell instanser

Tids serie modell *instanser* är virtuella representationer av själva tids serien.

I de flesta fall identifieras instanser unikt av **deviceId** eller **assetId**, som sparas som tids serie-ID: n.

Instanser har beskrivande information som är kopplad till dem kallas *instans egenskaper*, till exempel ett Time Series-ID, typ, namn, beskrivning, hierarkier och instans fält. Som minst innehåller instans egenskaper hierarki-information.

*Instans fält* är en samling beskrivande information som kan innehålla värden för hierarki nivåer, samt tillverkare, operatör och så vidare.

När en händelse källa har kon figurer ATS för Azure Time Series Insights Gen2-miljön identifieras och skapas instanser automatiskt i en tids serie modell. Instanserna kan skapas eller uppdateras via Azure Time Series Insights Explorer med hjälp av tids serie modell frågor.

[Contoso lindnings grupp demonstration](https://insights.timeseries.azure.com/preview/samples) innehåller flera exempel på Live-instansen.

[![Exempel på tids serie modell instans](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instans egenskaper

Instanser definieras av **timeSeriesId**, **typeId**, **Name**, **Description**, **hierarchyIds** och **instanceFields**. Varje instans mappar endast till en *typ*, och en eller flera *hierarkier*.

| Egenskap | Beskrivning |
| --- | ---|
| timeSeriesId | Unikt ID för tids serien som instansen är associerad med. I de flesta fall identifieras instanser unikt av en egenskap som deviceId eller assetId. I vissa fall kan ett mer särskilt sammansatt ID som kombinerar upp till 3 egenskaper användas. |
| ID | Det Skift läges känsliga unika sträng-ID: t för den tids serie modell typ som instansen är associerad med. Som standard blir alla identifierade nya instanser kopplade till en standard typ.
| name | Egenskapen **Name** är valfri och Skift läges känslig. Om **namnet** inte är tillgängligt används **timeSeriesId** som standard. Om ett namn anges är **timeSeriesId** fortfarande [tillgängligt.](./concepts-ux-panels.md#4-time-series-well) |
| beskrivning | En text Beskrivning av instansen. |
| hierarchyIds | Definierar vilka hierarkier som instansen tillhör. |
| instanceFields | Egenskaperna för en instans och eventuella statiska data som definierar en instans. De definierar värden för hierarki-eller icke-hierarkiska egenskaper och stöder även indexering för att utföra Sök åtgärder. |

> [!NOTE]
> Hierarkier skapas med hjälp av instans fält. Ytterligare **instanceFields** kan läggas till för definitioner av instans egenskaper.

Instanserna har följande JSON-representation:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Om du vill ha stöd för att skapa, läsa, uppdatera och ta bort (CRUD) kan du läsa artikeln om [data frågor](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) och [REST-API för instansen](/rest/api/time-series-insights/reference-model-apis#instances-api)API.

## <a name="time-series-model-hierarchies"></a>Hierarkier för tids serie modell

Tids serie modell *hierarkier* organiserar instanser genom att ange egenskaps namn och deras relationer.

Du kan konfigurera flera hierarkier i en specifik Azure Time Series Insights Gen2-miljö. En tids serie modell instans kan mappas till en enda hierarki eller flera hierarkier (många-till-många-relation).

I [Contosos demo grupp demonstration](https://insights.timeseries.azure.com/preview/samples) visas en standard instans och en hierarki.

[![Exempel på tids serie modell hierarki](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Definition av hierarki

Hierarkier definieras av hierarki **-ID**, **namn** och **källa**.

| Egenskap | Beskrivning |
| ---| ---|
| id | Den unika identifieraren för hierarkin, som används, till exempel när du definierar en instans. |
| name | En sträng som används för att ange ett namn för hierarkin. |
| källa | Anger organisationens hierarki eller sökväg, som är en överordnad överordnad-underordnad ordning i hierarkin som användare vill skapa. De överordnade-underordnade egenskaperna mappar instans fält. |

Hierarkier representeras i JSON som:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

I föregående JSON-exempel:

* `Location` definierar en hierarki med överordnad `states` och underordnad `cities` . Varje `location` kan ha flera `states` , som i sin tur kan ha flera `cities` .
* `ManufactureDate` definierar en hierarki med överordnad `year` och underordnad `month` . Varje `ManufactureDate` kan ha flera `years` , som i sin tur kan ha flera `months` .

> [!TIP]
> För hierarki-API skapa, läsa, uppdatera och ta bort (CRUD) support, Läs artikeln [data frågor](concepts-query-overview.md#time-series-model-query-tsm-q-apis) och [API rest-dokumentation för hierarkin](/rest/api/time-series-insights/reference-model-apis#hierarchies-api).

### <a name="hierarchy-example"></a>Exempel på hierarki

Överväg ett exempel där hierarkin **H1** har `building` , `floor` och `room` som en del av dess **instanceFieldNames** -definition:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Utifrån de instans fält som används i den föregående definitionen och flera tids serier visas hierarkins attribut och värden som visas i följande tabell:

| Time Series-ID | Instans fält |
| --- | --- |
| ID1 | "skapa" = "1000", "golv" = "10", "rum" = "55"  |
| ID2 | "skapa" = "1000", "Room" = "55" |
| ID3 | "golv" = "10" |
| ID4 | "skapa" = "1000", "golv" = "10"  |
| ID5 | Ingen av "byggnad", "golv" eller "Room" har angetts. |

Time Series- **id1** och **ID4** visas som en del av hierarkin **H1** i [Azure Time Series Insights Explorer](./concepts-ux-panels.md) eftersom de har fullständigt definierade och korrekt beställda parametrar för *bygge*, *golv* och *rum* .

De andra klassificeras under icke- *överordnade instanser* eftersom de inte överensstämmer med den angivna Datahierarkin.

## <a name="time-series-model-types"></a>Modell typer för tids serier

Med modell *typer* för tids serier kan du definiera variabler eller formler för att utföra beräkningar. Typer är associerade med en angiven instans.

En typ kan ha en eller flera variabler. En tids serie modell instans kan till exempel vara av typen *temperatur sensor*, som består av variablerna *medel temperatur*, *min temperatur* och *Maximal temperatur*.

[Contosos demo grupp demonstration](https://insights.timeseries.azure.com/preview/samples) visualiserar flera tids serie modell typer som är kopplade till sina respektive instanser.

[![Exempel på tids serie modell typ](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> För typerna API: Create, läsa, uppdatera och ta bort (CRUD) kan du läsa artikeln om [data frågor](concepts-query-overview.md#time-series-model-query-tsm-q-apis) och [rest-dokumentation för typ-API](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Typ egenskaper

Tids serie modell typer definieras av **ID**, **namn**, **Beskrivning** och **variabler**.

| Egenskap | Beskrivning |
| ---| ---|
| id | Det Skift läges känsliga unika sträng-ID: t för typen. |
| name | En sträng som används för att ange ett namn för typen. |
| beskrivning | En sträng beskrivning för typen. |
| användarvariabler | Ange variabler som är associerade med typen. |

Typerna följer följande JSON-exempel:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Tids serie modell typer kan ha många variabler som anger formel-och beräknings regler för händelser. Läs mer om [hur du definierar modell variabler för tids serier](./concepts-variables.md)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du redigerar modellen via API: er finns i referens dokumentationen för [Time Series-modellen](/rest/api/time-series-insights/reference-model-apis) .

* Utforska formler och beräkningar som du kan skapa med [tids serie modellens variabler](./concepts-variables.md)

* Lär dig mer om att [köra frågor mot data](concepts-query-overview.md) i Azure Time Series Insights Gen2