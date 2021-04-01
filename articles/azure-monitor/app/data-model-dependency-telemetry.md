---
title: Azure Monitor Application Insights beroende data modell
description: Application Insights data modell för beroende telemetri
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87315979"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Beroende telemetri: Application Insights data modell

Beroende telemetri (i [Application Insights](./app-insights-overview.md)) representerar en interaktion av den övervakade komponenten med en fjärrkomponent, till exempel SQL eller en http-slutpunkt.

## <a name="name"></a>Name

Namnet på kommandot som initierades med det här beroende anropet. Lågt kardinal värde. Exempel på lagrade procedurer för namn och URL-sökväg.

## <a name="id"></a>ID

Identifierare för en beroende anrops instans. Används för korrelation med objektet begär telemetri som motsvarar det här beroende anropet. Mer information finns på sidan [korrelation](./correlation.md) .

## <a name="data"></a>Data

Kommandot initierades av det här beroende anropet. Exempel är SQL-instruktion och HTTP-URL med alla frågeparametrar.

## <a name="type"></a>Typ

Namn på beroende typ. Lågt kardinal värde för logisk gruppering av beroenden och tolkning av andra fält som commandName och resultCode. Exempel är SQL, Azure Table och HTTP.

## <a name="target"></a>Mål

Mål plats för ett beroende anrop. Exempel är Server namn, värd adress. Mer information finns på sidan [korrelation](./correlation.md) .

## <a name="duration"></a>Varaktighet

Varaktighet för begäran i formatet: `DD.HH:MM:SS.MMMMMM` . Måste vara mindre än `1000` dagar.

## <a name="result-code"></a>Resultatkod

Resultat kod för ett beroende anrop. Exempel är SQL-felkod och HTTP-statuskod.

## <a name="success"></a>Klart

Indikering för lyckat eller misslyckat anrop.

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Nästa steg

- Konfigurera beroende spårning för [.net](./asp-net-dependencies.md).
- Konfigurera beroende spårning för [Java](./java-agent.md).
- [Skriv anpassad beroende telemetri](./api-custom-events-metrics.md#trackdependency)
- Se [data modell](data-model.md) för Application Insights typer och data modell.
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

