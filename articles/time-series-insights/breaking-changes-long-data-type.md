---
title: Stöd för lång datatyp i Azure Time Series Insights Gen2 | Microsoft Docs
description: Stöd för långa data typer i Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: dpalled
ms.openlocfilehash: 0f7ac9844bfe7eac7094d3b7fdf653e07f236599
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780832"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Lägger till stöd för lång datatyp i Azure Time Series Insights Gen2

Tillägget av stöd för långa data typer påverkar hur vi lagrar och indexerar numeriska data i Azure Time Series Insights Gen2-miljöer. Om du har en gen1-miljö kan du bortse från dessa ändringar.

Från och med den 29 juni eller den 30 juni 2020, beroende på din region, kommer dina data att indexeras som **långa** och **dubbla**.  Om du har några frågor eller problem med den här ändringen kan du skicka ett support ärende via Azure Portal och nämna denna kommunikation.

Om du påverkas av något av följande fall gör du de rekommenderade ändringarna:

- **Fall 1**: du använder för närvarande tids serie modell variabler och skickar endast integrala data typer i dina telemetridata.
- **Fall 2**: du använder för närvarande tids serie modell variabler och skickar data typerna integral och inte integral i dina telemetridata.
- **Fall 3**: du kan använda kategoriska-variabler för att mappa heltals värden till kategorier.
- **Fall 4**: du kan använda Java Script SDK för att bygga ett anpassat klient program.
- **Fall 5**: du närmar dig gränsen för 1 000-egenskaps namn i varmt lager och skickar både heltals-och data som inte är Integral. Antalet egenskaper kan visas som ett mått i [Azure Portal](https://portal.azure.com/).

Om något av fallen gäller dig, gör du ändringar i din modell. Uppdatera Time Series-uttrycket (TSX) i variabel definitionen med de rekommenderade ändringarna. Uppdatera båda:

- Azure Time Series Insights-utforskaren
- Alla anpassade klienter som använder våra API: er

Beroende på din IoT-lösning och begränsningar kanske du inte har insyn i de data som skickas till din Azure Time Series Insights Gen2-miljö. Om du är osäker på om dina data endast är integrala eller både integrala och inintegrala har du några alternativ:

- Du kan vänta på att funktionen släpps. Utforska sedan dina obehandlade händelser i användar gränssnittet i Utforskaren för att förstå vilka egenskaper som sparas i två separata kolumner.
- Du kan förebyggande syfte göra de rekommenderade ändringarna för alla numeriska taggar.
- Du kan tillfälligt dirigera en delmängd av händelser till lagring för att bättre förstå och utforska ditt schema.

Om du vill lagra händelser aktiverar du [händelse fångst](../event-hubs/event-hubs-capture-overview.md) för Azure Event Hubs eller [dirigerar](../iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint) från din IoT Hub till Azure Blob Storage.

Data kan också observeras via [Event Hub Explorer](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)eller med hjälp av [händelse bearbetnings värden](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md#receive-events).

Om du använder IoT Hub går du till [läsa "enhet till molnet"-meddelanden från den inbyggda slut punkten](../iot-hub/iot-hub-devguide-messages-read-builtin.md) för att få åtkomst till den inbyggda slut punkten.

> [!NOTE]
> Du kan uppleva ett avbrott om du inte gör de rekommenderade ändringarna. Till exempel returnerar de påverkade Time Series Insights variablerna som nås via fråge-API: erna eller Time Series Insights Explorer **Null** (det vill säga inga data i Utforskaren).

## <a name="recommended-changes"></a>Rekommenderade ändringar

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>Fall 1: använda tids serie modell variabler och skicka endast integrala data typer i telemetridata

De rekommenderade ändringarna för fall 1 är desamma som för fall 2. Följ anvisningarna i avsnittet för fall 2.

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>Fall 2: använda modell variabler för tids serier och skicka både integrala och inintegrala typer i telemetridata

Om du för närvarande skickar språktelemetri-data, delas dina data in i två kolumner:

- **propertyValue_double**
- **propertyValue_long**

Dina heltals data skrivs till **propertyValue_long**. Tidigare inmatade numeriska data (och framtida inmatade) i **propertyValue_double** kopieras inte över.

Om du vill fråga efter data över de här två kolumnerna för egenskapen **propertyValue** måste du använda den skalära funktionen **sammanslagning ()** i din TSX. Funktionen accepterar argument av samma **datatyp** och returnerar det första värdet som inte är null i argument listan. Mer information finns i [Azure Time Series Insights Gen2 Data Access Concepts](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions).

#### <a name="variable-definition-in-tsx---numeric"></a>Variabel definition i TSX-numerisk

*Definition av föregående variabel:*

[![Skärm bild som visar dialog rutan Lägg till en ny variabel för PropertyValue-variabeln, numeric.](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Ny variabel definition:*

[![Skärm bild som visar dialog rutan Lägg till en ny variabel för PropertyValue-variabeln med ett anpassat värde, numeriskt.](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Du kan också använda **sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))** som uttryck för anpassad [tids serie](/rest/api/time-series-insights/reference-time-series-expression-syntax).

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>Infogad variabel definition med TSX-frågas-API: n-numeriskt

*Definition av föregående variabel:*

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

*Ny variabel definition:*

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

Du kan också använda **sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))** som uttryck för anpassad [tids serie](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Vi rekommenderar att du uppdaterar dessa variabler på alla platser som de kan användas. Dessa platser omfattar tids serie modellen, sparade frågor och Power BI anslutnings frågor.

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>Fall 3: använda kategoriska-variabler för att mappa heltals värden till kategorier

Om du för närvarande använder kategoriska-variabler som mappar heltals värden till kategorier, använder du förmodligen funktionen **toLong** för att konvertera data från **Double** -typ till **lång** typ. Precis som fall 1 och 2 måste du slå samman kolumnerna **Double** och **Long** **types** .

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>Variabel definition i Time Series Explorer – kategoriska

*Definition av föregående variabel:*

[![Skärm bild som visar dialog rutan Lägg till en ny variabel för variabeln PropertyValue, kategoriska.](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Ny variabel definition:*

[![Skärm bild som visar dialog rutan Lägg till en ny variabel för PropertyValue-variabeln med ett anpassat värde, kategoriska.](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Du kan också använda **sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))** som uttryck för anpassad [tids serie](/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

Kategoriska-variabler kräver fortfarande att värdet är av en heltals typ. **Data typen** för alla argument i **sammanslagning ()** måste vara av typen **Long** i det anpassade [tids serie uttrycket.](/rest/api/time-series-insights/reference-time-series-expression-syntax)

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>Infogad variabel definition med TSX-fråge-API: er – kategoriska

*Definition av föregående variabel:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

*Ny variabel definition:*

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

Kategoriska-variabler kräver fortfarande att värdet är av en heltals typ. **Data typen** för alla argument i **sammanslagning ()** måste vara av typen **Long** i det anpassade [tids serie uttrycket](/rest/api/time-series-insights/reference-time-series-expression-syntax).

> [!NOTE]
> Vi rekommenderar att du uppdaterar dessa variabler på alla platser som de kan användas. Dessa platser omfattar tids serie modellen, sparade frågor och Power BI anslutnings frågor.

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>Fall 4: använda Java Script SDK för att bygga ett anpassat klient program

Om du påverkas av fall 1 till 3 och skapar anpassade program måste du uppdatera dina frågor för att använda funktionen **sammanslagning ()** , vilket visas i föregående exempel.

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>Fall 5: närmar sig det varmt arkivet 1 000 egenskaps gräns

Om du är en varm användare med ett stort antal egenskaper och tror att den här ändringen skulle leda till att din miljö skickas över den 1 000 varmt Store-egenskapen namn, skicka ett support ärende via Azure Portal och nämna denna kommunikation.

## <a name="next-steps"></a>Nästa steg

- Visa en fullständig lista över [data typer som stöds](concepts-supported-data-types.md).
