---
title: Data modell för Azure Application Insights-telemetri – Event-telemetri | Microsoft Docs
description: Application Insights data modell för telemetri av händelser
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 69685afa14352a22b58bccbea342038e4273696e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320620"
---
# <a name="event-telemetry-application-insights-data-model"></a>Event telemetri: Application Insights data modell

Du kan skapa objekt för telemetri (i [Application Insights](./app-insights-overview.md)) för att representera en händelse som har inträffat i ditt program. Vanligt vis är det en användar interaktion som knapp tryckning eller order utcheckning. Det kan också vara ett programs livs cykel händelse som initiering eller konfigurations uppdatering. 

Semantiskt, händelser kan eventuellt inte korreleras med begär Anden. Men om det används korrekt är event telemetri viktigare än begär Anden eller spårningar. Händelser representerar affärstelemetri och bör vara föremål för separat, mindre aggressiv [sampling](./api-filtering-sampling.md).

## <a name="name"></a>Name

Händelse namn. Begränsa ditt program så att det genererar ett litet antal separata händelse namn för att tillåta rätt gruppering och användbara mått. Använd till exempel inte ett separat namn för varje genererad instans av en händelse.

Maxlängd: 512 tecken

## <a name="custom-properties"></a>Anpassade egenskaper

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Anpassade mått

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nästa steg

- Se [data modell](data-model.md) för Application Insights typer och data modell.
- [Skriv anpassad telemetri för händelser](./api-custom-events-metrics.md#trackevent)
- Kolla ut [plattformar](./platforms.md) som stöds av Application Insights.

