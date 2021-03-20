---
title: Anslut Citrix WAF-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Citrix WAF data Connector för att hämta loggar till Azure Sentinel. Visa Citrix WAF-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93103128"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Anslut din Citrix-WAF till Azure Sentinel

> [!IMPORTANT]
> Citrix Web Application Firewall (WAF) data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln förklaras hur du ansluter WAF-installationen (Citrix Web Application Firewall) till Azure Sentinel. Med Citrix WAF data Connector kan du enkelt ansluta dina Citrix WAF-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att ansluta Citrix WAF CEF-loggar till Azure Sentinel kan du utnyttja sökning, korrelation, avisering och hot information för varje logg.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Vidarebefordra Citrix WAF-loggar till syslog-agenten  

Citrix WAF skickar syslog-meddelanden i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng) med Log Analytics-agenten installerad, som vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. Om du inte har en sådan logg vidarebefordrande server kan du läsa [de här anvisningarna](connect-cef-agent.md) för att skapa en igång.

1. Följ Citrixs instruktioner för att [Konfigurera WAF](https://support.citrix.com/article/CTX234174), [Konfigurera CEF-loggning](https://support.citrix.com/article/CTX136146)och konfigurera loggarna [för logg vidarebefordraren](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Se till att skicka loggarna till TCP-port 514 på logg vidarebefordraren datorns IP-adress.

1. Verifiera anslutningen och kontrol lera data inmatningen med hjälp av [de här anvisningarna](connect-cef-verify.md). Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga Citrix WAF-loggarna i Log Analytics anger du `CommonSecurityLog` överst i frågefönstret.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Citrix WAF till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.