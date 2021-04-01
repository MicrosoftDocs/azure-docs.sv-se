---
title: Analysera användningsmönster för Azure CDN
description: I den här artikeln beskrivs de olika typerna av analys rapporter som är tillgängliga för Azure CDN produkter.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483996"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster för Azure CDN

När du har aktiverat CDN för ditt program kan du övervaka CDN-användning, kontrol lera hälso tillståndet för leveransen och felsöka eventuella problem. Azure CDN tillhandahåller dessa funktioner på följande sätt: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Obehandlade loggar för Azure CDN från Microsoft
Med en vanlig Microsoft-profil kan du aktivera obehandlade loggar och välja att strömma loggar till:

* Azure Storage
* Händelsehubbar
* Azure Log Analytics

Med Azure Log Analytics kan du Visa övervaknings mått och konfigurera aviseringar. 

Mer information finns i [Azure CDN http RAW-loggar](monitoring-and-access-log.md).


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Core Analytics via Azure Diagnostic-loggar

Core Analytics är tillgängligt för CDN-slutpunkter för alla pris nivåer. Azure Diagnostics-loggar låter kärn analyser exporteras till Azure Storage, Event Hub eller Azure Monitor loggar. Azure Monitor-loggar erbjuder en lösning med grafer som kan konfigureras av användaren och anpassas. Mer information om Azure Diagnostic-loggar finns i [Azure Diagnostic-loggar](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon Core-rapporter

**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profiler tillhandahåller kärn rapporter. Du kan visa kärn rapporter i den kompletterande portalen för Verizon. Verizon Core-rapporter är tillgängliga via alternativet **Hantera** från Azure Portal och erbjuder olika typer av diagram och vyer. Mer information finns i [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Anpassade Verizon-rapporter

**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profiler tillhandahåller anpassade rapporter. Du kan visa anpassade rapporter i den kompletterande portalen för Verizon. Anpassade Verizon-rapporter är tillgängliga via alternativet **Hantera** från Azure Portal. 

I anpassade rapporter visas antalet träffar eller data som överförts för varje Edge CNAME. Data grupperas efter HTTP-svarskod eller cache-status under en viss tids period. Mer information finns i [anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium från Verizon-rapporter

Med **Azure CDN Premium från Verizon** kan du också komma åt följande rapporter:
   * [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
   * [Realtidsstatistik](cdn-real-time-stats.md)
   * [Prestanda för Azure CDN Edge-nod](cdn-edge-performance.md)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om de olika alternativen för analys rapporter för Azure CDN.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Vad är Azure CDN?](cdn-overview.md)
* [Azure CDN HTTP RAW-loggar](monitoring-and-access-log.md)
