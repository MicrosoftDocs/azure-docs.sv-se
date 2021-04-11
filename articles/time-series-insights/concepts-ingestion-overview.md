---
title: Översikt över intag – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig mer om data inmatning i Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505458"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Översikt över Azure Time Series Insights Gen2 data inmatning

Din Azure Time Series Insights Gen2-miljö innehåller en inmatnings *motor* för att samla in, bearbeta och lagra data för strömnings tids serier. När data anländer till din händelse källa (er) kommer Azure Time Series Insights Gen2 att förbruka och lagra dina data i nära real tid.

[![Översikt över datainmatning](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Avsnitt om inmatning

Följande artiklar behandlar data bearbetning i djupet, inklusive bästa praxis att följa:

* Läs om [händelse källor](./concepts-streaming-ingestion-event-sources.md) och vägledning om hur du väljer en tids stämpling för händelse källan.

* Granska de [data typer](./concepts-supported-data-types.md) som stöds

* Förstå hur inmatnings motorn använder en uppsättning [regler](./concepts-json-flattening-escaping-rules.md) för dina JSON-egenskaper för att skapa dina lagrings konto kolumner.

* Granska begränsningarna för din miljö [genom strömning](./concepts-streaming-ingress-throughput-limits.md) för att planera för dina skalnings behov.

## <a name="next-steps"></a>Nästa steg

* Fortsätt till om du vill veta mer om [händelse källor](./concepts-streaming-ingestion-event-sources.md) för din Azure Time Series Insights Gen2-miljö.
