---
title: Anslut Thycotic Secret-Server till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Thycotic Secret Server Data Connector för att hämta Thycotic-hemliga Server loggar till Azure Sentinel. Visa Thycotic Secret Server data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567988"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Anslut din Thycotic-hemliga server till Azure Sentinel

> [!IMPORTANT]
> Thycotic Secret Server Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Thycotic-hemliga server till Azure Sentinel. Med Thycotic Secret Server Data Connector kan du enkelt ansluta dina Thycotic-hemliga Server loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen. Integreringen mellan Thycotic och Azure Sentinel använder CEF data Connector för att korrekt kunna parsa och Visa meddelanden om meddelanden från hemliga Server.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan.

- Din Thycotic-hemliga Server måste konfigureras för att exportera loggar via syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Skicka Thycotic Secret Server-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du Thycotic Secret-servern så att syslog-meddelanden skickas i CEF-format till din Linux-baserade logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **Thycotic Secret Server (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. Under **2. Vidarebefordra CEF-loggar (common Event format) till syslog-agenten** – följa Thycotic-instruktionerna för att [Konfigurera den hemliga servern](https://thy.center/ss/link/syslog). Den här konfigurationen måste innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – **TCP 514** (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – CEF
        - Logg typer – alla tillgängliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga Thycotic Secret Server data in Log Analytics kopierar du följande till frågefönstret och använder andra filter som du väljer:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Se **Nästa steg** -fliken på kopplings sidan för några användbara arbets böcker och fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Thycotic Secret-servern till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.