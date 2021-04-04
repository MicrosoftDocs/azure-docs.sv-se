---
title: Kommande ändringar i inmatnings-och förenklings reglerna i Azure Time Series Insights Gen2 | Microsoft Docs
description: Ändringar i inmatnings regeln
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995241"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>Kommande ändringar av JSON-förenkling och undantags regler för nya miljöer

> [!IMPORTANT]
> Dessa ändringar kommer att tillämpas på *nyligen skapade* Microsoft Azure Time Series Insights Gen2-miljöer. Ändringarna gäller inte för gen1-miljöer.

Din Azure Time Series Insights Gen2-miljö skapar dynamiskt dina lagrings kolumner genom att följa en viss uppsättning namngivnings konventioner. När en händelse matas in använder Time Series Insights en uppsättning regler för JSON-nyttolasten och egenskaps namnen. Ändringar i hur JSON-data förenklas och lagras i praktiken för nya Azure Time Series Insights Gen2-miljöer i juli 2020. Den här ändringen påverkar dig i följande fall:

* Din JSON-nyttolast innehåller kapslade objekt.
* Din JSON-nyttolast innehåller matriser.
* Du använder något av följande fyra specialtecken i ett JSON-egenskaps namn: `[` `\` `.``'`
* En eller flera av egenskaperna för din tids serie (TS) ID är i ett kapslat objekt.

Om du skapar en ny miljö och en eller flera av dessa fall gäller för din händelse nytto Last, kommer dina data att förenklas och lagras på olika sätt. I följande tabell sammanfattas ändringarna:

| Aktuell regel | Ny regel | Exempel-JSON | Föregående kolumn namn | Nytt kolumnnamn
|---|---| ---| ---|  ---|
| Kapslad JSON förenklas med ett under streck som avgränsare. |Kapslad JSON förenklas med en punkt som avgränsare.  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Specialtecken är inte undantags tecken. | JSON-egenskaps namn som innehåller specialtecknen `.` `[`   `\` och som `'` är undantagna med `['` och `']` . Inom `['` och `']` finns det ytterligare undantag av enkla citat tecken och omvänt snedstreck. Ett enkelt citat sätt skrivs som `\'` och ett omvänt snedstreck som `\\` .  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| Matriser med primitiver lagras som en sträng. | Matriser med primitiva typer lagras som en dynamisk typ.  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Objekts mat ris förenklas alltid och genererar flera händelser. | Om objekten i en matris inte har något av egenskaperna för TS-ID eller tidsstämpel, lagras matrisen med objekt som en dynamisk typ. | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Rekommenderade ändringar för nya miljöer

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Om ditt TS-ID och/eller timestamp-egenskapen är kapslade i ett objekt

Alla nya distributioner måste matcha de nya inmatnings reglerna. Om ditt TS-ID t. ex. är måste `telemetry_tagId` du uppdatera Azure Resource Manager mallar eller automatiserade distributions skript för att konfigurera `telemetry.tagId` som TS-ID för miljön. Du behöver också denna ändring för händelse källans tidsstämplar i kapslad JSON.

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Om nytto lasten innehåller kapslad JSON eller specialtecken och du automatiserar redigering av [tids serie modell](./concepts-model-overview.md) variabel uttryck

Uppdatera din klient kod som kör [TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) för att matcha de nya inmatnings reglerna. Du bör till exempel uppdatera ett tidigare [tids serie uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax) på `"value": {"tsx": "$event.series_value.Double"}` något av följande alternativ:

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure Time Series Insights Gen2-lagring och ingress](./concepts-ingestion-overview.md).

* Lär dig hur du frågar dina data med hjälp av [API: er för Time Series-frågor](./concepts-query-overview.md).

* Läs mer om den [nya syntaxen för Time Series-uttryck](/rest/api/time-series-insights/reference-time-series-expression-syntax).