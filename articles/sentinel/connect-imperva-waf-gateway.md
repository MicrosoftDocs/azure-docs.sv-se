---
title: Anslut din Imperva WAF-gatewayenhet till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Imperva WAF Gateway Connector för att hämta Imperva WAF-loggar till Azure Sentinel. Visa Imperva WAF-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 3094d20a921f9aa13e111e7af60955ce934b91db
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566800"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Anslut din Imperva WAF-gatewayenhet till Azure Sentinel

> [!IMPORTANT]
> Imperva WAF Gateway Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Imperva WAF-gatewayenhet till Azure Sentinel. Med Imperva WAF Gateway data Connector kan du enkelt ansluta dina Imperva WAF Gateway-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen. Integreringen mellan Imperva WAF gateway och Azure Sentinel använder CEF syslog, en Linux-baserad logg vidarebefordrare och Log Analytics agenten.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Skicka Imperva WAF Gateway-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du Imperva WAF gateway-enheten för att skicka syslog-meddelanden i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **Imperva WAF Gateway (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. Under **2. Vidarebefordra CEF-loggar (common Event format) till syslog-agenten** – den här anslutningen kräver att ett **Åtgärds gränssnitt** och en **åtgärd har angetts** för att skapas i **Imperva SecureSphere MX** Management Console. Följ Imperva-instruktionerna för att [Aktivera IMPERVA WAF Gateway-aviserings loggning till Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Den här konfigurationen måste innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – TCP 514
        - Logg format – CEF
        - Logg typer – alla tillgängliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga Imperva WAF Gateway-data i Log Analytics kopierar du följande till frågefönstret och använder andra filter som du väljer:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Se **Nästa steg** -fliken på kopplings sidan för fler användbara fråge exempel.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Imperva WAF-gatewayen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
