---
title: Att Observera i Azure API Management | Microsoft Docs
description: Översikt över alla alternativ för alternativ i Azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87099779"
---
# <a name="observability-in-azure-api-management"></a>Observera att det går att Observera i Azure API Management

Begriplighet är möjligheten att förstå det interna läget för ett system från de data som produceras och möjligheten att utforska dessa data för att besvara frågor om vad som hände och varför. 

Azure API Management hjälper organisationer att centralisera hanteringen av alla API: er. Eftersom den fungerar som en enda post för all API-trafik, är det en idealisk plats att observera API: erna. 

## <a name="observability-tools"></a>Verktyg för att Observera

I tabellen nedan sammanfattas alla verktyg som stöds av API Management för att se API: er, var och en är användbar för ett eller flera scenarier:

| Verktyg        | Användbart för    | Data fördröjning | Kvarhållning | Samling | Data typ | Enabled|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API-kontroll](api-management-howto-api-inspector.md)** | Testa och felsöka | Direkt | Senaste 100 spårningarna | Aktive rad per begäran | Begär spårning | Always
| **Inbyggd analys** | Rapportering och övervakning | Minuter | Giltighet | 100 % | Rapporter och loggar | Always |
| **[Azure Monitor-statistik](api-management-howto-use-azure-monitor.md)** | Rapportering och övervakning | Minuter | 93 dagar (uppgradera till utökad) | 100 % | Mått | Always |
| **[Azure Monitor-loggar](api-management-howto-use-azure-monitor.md)** | Rapportering, övervakning och fel sökning | Minuter | 31 dagar/5 GB (uppgradera till utökning) | 100% (justerbar) | Loggar | Valfritt |
| **[Azure Application insikter](api-management-howto-app-insights.md)** | Rapportering, övervakning och fel sökning | Sekunder | 90 dagar/5 GB (uppgradera till utökad) | Anpassat | Loggar, mått | Valfritt |
| **[Logga via Azure Event Hub](api-management-howto-log-event-hubs.md)** | Anpassade scenarier | Sekunder | Hanteras av användare | Anpassat | Anpassat | Valfritt |

### <a name="self-hosted-gateway"></a>Egen värd-Gateway

Alla verktyg som nämns ovan stöds av den hanterade gatewayen i molnet. Den [egen värdbaserade gatewayen](self-hosted-gateway-overview.md) skickar för närvarande inte diagnostikloggar till Azure Monitor. Det är dock möjligt att konfigurera och Spara loggar lokalt där gatewayen för egen värd distribueras. Mer information finns i [Konfigurera moln mått och loggar för lokal gateway](how-to-configure-cloud-metrics-logs.md) och [Konfigurera lokala mått och loggar för egen värdbaserade Gateway](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Nästa steg

* [Följ självstudierna för att lära dig mer om API Management](import-and-publish.md)
