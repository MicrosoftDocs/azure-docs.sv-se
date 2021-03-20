---
title: Migrera till nya Azure Time Series Insights Gen2 API-versioner | Microsoft Docs
description: Så här uppdaterar du Azure Time Series Insights Gen2-miljöer för att använda nya, allmänt tillgängliga versioner.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 15f1a814b302611029cf6459b8d2df93a32a2d36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97740563"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrera till nya Azure Time Series Insights Gen2 API-versioner

## <a name="overview"></a>Översikt

Om du har skapat en Azure Time Series Insights Gen2-miljö när den var i en offentlig för hands version (före den 16 juli 2020) kan du uppdatera din TSD-miljö för att använda de nya allmänt tillgängliga versionerna av API: er genom att följa stegen som beskrivs i den här artikeln. Den här ändringen påverkar inte användare som använder gen1-versionen av Azure Time Series Insights.

> [!IMPORTANT]
> Uppdateringarna som beskrivs i den här artikeln uppgraderar bara de API-versioner som används i TSD-miljön. Den här ändringen är inte relaterad till nya [JSON-förenklingar och undantags regler](./concepts-json-flattening-escaping-rules.md) som introduceras i Gen2-miljöer.

Den nya API-versionen är `2020-07-31` och använder en uppdaterad [syntax för Time Series-uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Användare måste migrera sin Miljös [tids serie modell variabler](./concepts-variables.md), sparade frågor, Power BI frågor och eventuella anpassade verktyg som gör anrop till API-slutpunkterna. Om du har frågor eller problem med migreringsprocessen skickar du ett support ärende via Azure Portal och nämner det här dokumentet.

> [!IMPORTANT]
> För hands versionen av API `2018-11-01-preview` -versionen fortsätter att stödjas fram till den 31 oktober 2020. Slutför alla tillämpliga steg i den här migreringen innan du gör det för att undvika eventuella avbrott i tjänsten.

## <a name="migrate-time-series-model-and-saved-queries"></a>Migrera tids serie modellen och sparade frågor

För att hjälpa användarna att migrera [tids serie modellens variabler](./concepts-variables.md) och sparade frågor, finns det ett inbyggt verktyg som är tillgängligt via [Azure Time Series Insights Explorer](https://insights.timeseries.azure.com). Navigera till den miljö som du vill migrera och följ stegen nedan. **Du kan slutföra migreringen delvis och återgå till att slutföra den vid ett senare tillfälle, men ingen av uppdateringarna kan återställas.**

> [!NOTE]
> Du måste vara deltagare i miljön för att kunna uppdatera tids serie modellen och sparade frågor. Om du inte är en deltagare kommer du bara att kunna migrera dina personliga sparade frågor. Granska [miljö åtkomst principer](./concepts-access-policies.md) och din åtkomst nivå innan du fortsätter.

1. Du uppmanas att uppdatera den syntax som används av dina tids serie modell variabler och sparade frågor i Explorer.

    [![Prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Om du råkar stänga meddelandet av misstag kan du hitta det i meddelande panelen.

1. Klicka på **Visa uppdateringar** för att öppna migreringsverktyget.

1. Klicka på **hämtnings typer**. Eftersom migreringen kommer att skriva över dina aktuella typer för att ändra variabelns syntax måste du spara en kopia av dina aktuella typer. Verktyget meddelar dig när typer har laddats ned.

    [![Hämtnings typer](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicka på **Uppdatera variabler**. Verktyget meddelar dig när variabler har uppdaterats.

    > [!IMPORTANT]
    > Om du uppdaterar dina typer måste anpassade program som använder en äldre API-version ( `2018-11-01-preview` ) använda den nya API-versionen ( `2020-07-31` ) för att fortsätta arbeta. Om du inte är säker på vilken API-version du använder kan du kontakta administratören innan du uppdaterar. Du kan stänga migrations verktyget och gå tillbaka till de här stegen senare. Läs mer om [hur du migrerar anpassade program](#migrate-custom-applications).

    [![Uppdatera variabler](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicka på **Uppdatera sparade frågor**. Verktyget meddelar dig när sparade frågor har uppdaterats.

    [![Uppdatera sparade frågor](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicka på **Klar**.

    [![Slutförd migrering](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Granska den uppdaterade miljön genom att Diagrama några av de nyligen skapade variablerna och sparade frågor. Om du ser oväntade funktions sätt när du använder diagrammet kan du skicka feedback via feedback-verktyget i Utforskaren.

## <a name="migrate-power-bi-queries"></a>Migrera Power BIs frågor

Om du har genererat frågor med hjälp av Power BI anslutningen, gör de anrop till Azure Time Series Insights med hjälp av syntaxen för Preview API-versionen och den gamla tids serien. Dessa frågor fortsätter att hämta data till dess att för hands versions-API: et är föråldrat.

För att du ska kunna uppdatera frågorna för att använda den nya API-versionen och den nya tids serie uttrycks syntaxen måste frågorna återskapas från Utforskaren. Läs mer om hur du [skapar frågor med hjälp av Power BI-anslutningen](./how-to-connect-power-bi.md).

> [!NOTE]
> Du måste använda minst juli 2020-versionen av Power BI Desktop. Om du inte gör det kan fel meddelandet "Ogiltig fråga om nytto Last version" visas.

## <a name="migrate-custom-applications"></a>Migrera anpassade program

Om det anpassade programmet gör anrop till följande REST-slutpunkter, räcker det att uppdatera API-versionen till `2020-07-31` i URI: n:

- API: er för Time Series-modellen
  - API: er för modell inställningar
    - [Hämta](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Uppdatera](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Instans-API: er
    - [Alla batch-åtgärder](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Sök](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Föreslå](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - API: er för hierarki
    - [Alla batch-åtgärder](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Typer av API: er
    - [Ta bort, Hämta åtgärder](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Lista](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

För följande REST-slutpunkter måste du uppdatera API-versionen till `2020-07-31` i URI: n och kontrol lera att alla förekomster av `tsx` egenskapen använder den uppdaterade [tids serie uttrycks syntaxen](/rest/api/time-series-insights/reference-time-series-expression-syntax).

- Typer av API: er
  - [Placerings åtgärd](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- API:er för frågor
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Exempel

#### <a name="typesbatchput"></a>TypesBatchPut

Gammal brödtext för begäran (används av `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Uppdaterad begär ande text (används av `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Alternativt `filter` kan även vara `$event.Mode.String = 'outdoor'` . `value`Måste använda hakparenteserna för att kringgå specialtecknet ( `_` ).

#### <a name="getevents"></a>GetEvents

Gammal brödtext för begäran (används av `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Uppdaterad begär ande text (används av `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Alternativt `filter` kan även vara `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>GetSeries

Gammal brödtext för begäran (används av `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Uppdaterad begär ande text (används av `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Alternativt `value` kan även vara `$event['Bar-Pressure-Offset'].Double` . Om ingen datatyp anges antas data typen alltid vara dubbel. Klammerns notation måste användas för att undvika specialtecknet ( `-` ).

#### <a name="aggregateseries"></a>AggregateSeries

Gammal brödtext för begäran (används av `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Uppdaterad begär ande text (används av `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Alternativt `value` kan även vara `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>Potentiella fel

#### <a name="invalidinput"></a>InvalidInput

Om du ser följande fel använder du den nya API-versionen (), `2020-07-31` men TSX-syntaxen har inte uppdaterats. Kontrol lera [syntaxen för Time Series-uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax) och migrering ovan. Se till att alla `tsx` Egenskaper uppdateras korrekt innan du skickar API-begäran igen.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Testa din miljö via [Azure Time Series Insights Explorer](./concepts-ux-panels.md) eller via ditt anpassade program.