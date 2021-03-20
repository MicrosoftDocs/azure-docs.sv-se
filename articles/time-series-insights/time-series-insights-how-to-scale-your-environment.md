---
title: Så här skalar du din miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du skalar din Azure Time Series Insights miljö med hjälp av Azure Portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570218"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Så här skalar du din Azure Time Series Insights gen1-miljö

> [!CAUTION]
> Det här är en gen1-artikel.

I den här artikeln beskrivs hur du ändrar kapaciteten för din Azure Time Series Insights miljö med hjälp av [Azure Portal](https://portal.azure.com). Kapaciteten är multiplikatorn som tillämpas på ingångs pris, lagrings kapacitet och kostnad som är kopplad till den valda SKU: n.

Du kan använda Azure Portal för att öka eller minska kapaciteten inom en specifik pris-SKU.

Det är dock inte tillåtet att ändra pris nivåns SKU. Till exempel kan en miljö med en pris-SKU för S1-prissättning inte konverteras till en S2 eller vice versa.

## <a name="ga-limits"></a>GA-gränser

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Ändra kapaciteten för din miljö

1. Leta upp och välj din Azure Time Series Insights-miljö i Azure Portal.

1. I menyn för din Azure Time Series Insights-miljö väljer du **lagrings konfiguration**.

   [![Konfigurera din Azure Time Series Insights-kapacitet](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Justera skjutreglaget **kapacitet** för att välja den kapacitet som uppfyller kraven för ingångs frekvens och lagrings kapacitet. Observera ingångs **frekvens**, **lagrings kapacitet** och **Beräknad kostnads** uppdatering dynamiskt för att visa effekten av ändringen.

   [![Konfigurera din miljö med skjutreglaget kapacitet](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Alternativt kan du ange antalet kapacitets multiplikatorer i text rutan till höger om skjutreglaget.

1. Välj **Spara** för att skala miljön. Förlopps indikatorn visas tills ändringen är genomförd, tillfälligt.

1. Kontrol lera att den nya kapaciteten [räcker för att förhindra begränsning](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Nästa steg

- Mer information finns [i förstå kvarhållning i Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Läs mer om hur du [konfigurerar datakvarhållning i Azure Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Lär dig [att planera ut din miljö](time-series-insights-environment-planning.md).
