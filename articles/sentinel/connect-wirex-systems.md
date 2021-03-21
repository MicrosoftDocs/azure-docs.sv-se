---
title: Ansluta WireX Network data utredning Platform (NFP) till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder WireX Systems NFP data Connector för att hämta WireX NFP-loggar till Azure Sentinel. Visa WireX NFP-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 9029b945eabd05b34306393b513e26ee9c1563f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541595"
---
# <a name="connect-your-wirex-network-forensics-platform-nfp-appliance-to-azure-sentinel"></a>Ansluta NFP-apparaten (WireX Network data utredning Platform) till Azure Sentinel

> [!IMPORTANT]
> WireX Systems NFP Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln beskriver hur du ansluter NFP-installationen (WireX Systems Network data utredning Platform) till Azure Sentinel. Med WireX NFP data Connector kan du enkelt ansluta NFP-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen. 

> [!NOTE] 
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan.

## <a name="send-wirex-nfp-logs-to-azure-sentinel"></a>Skicka WireX NFP-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du WireX Systems NFP-installationen så att syslog-meddelanden skickas i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **WireX Network data utredning Platform (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. **1. Linux syslog-agent konfiguration** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. **2. vidarebefordra CEF-loggar (common Event format) till syslog-agenten** – kontakta [WireX support](https://wirexsystems.com/contact-us/) om du vill ha rätt konfiguration av din WireX NFP-lösning. Den här konfigurationen måste innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – TCP 514 (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – CEF
        - Logg typer – alla rekommenderas av WireX

    1. **3. Verifiera anslutnings** -kontrol lera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga WireX NFP data i Log Analytics kopierar du följande till frågefönstret och använder andra filter som du väljer:

```kusto
CommonSecurityLog 
| where DeviceVendor == "WireX"
```

Se **Nästa steg** -fliken på kopplings sidan för fler fråge exempel.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter WireX Systems NFP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.