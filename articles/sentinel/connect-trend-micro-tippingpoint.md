---
title: Ansluta Trend Micro TippingPoint till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Trend Micro TippingPoint data Connector för att hämta TippingPoint SMS-loggar till Azure Sentinel. Visa TippingPoint-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: 989520e079988e1821d8bb9a936f857e1f62c11a
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567764"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Anslut din Trend Micro TippingPoint-lösning till Azure Sentinel

> [!IMPORTANT]
> Trend Micro TippingPoint-anslutningen är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din lösning för Trend Micro TippingPoint Threat Protection till Azure Sentinel. Med Trend Micro TippingPoint data Connector kan du enkelt ansluta dina SMS-loggar (TippingPoint Security Management System) med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och införliva dem för att förbättra undersökningen. 

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Krav

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Skicka Trend Micro TippingPoint-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du din TippingPoint TPS-lösning för att skicka syslog-meddelanden i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Trend Micro TippingPoint (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. Under **2. Vidarebefordra Micro TippingPoint SMS-loggar till syslog-agenten** – den här konfigurationen ska innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – **TCP 514** (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – **ARCSIGHT CEF format v 4.2**
        - Logg typer – alla tillgängliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Om du vill fråga TrendMicro TippingPoint data i Log Analytics kopierar du följande till frågefönstret och använder andra filter som du väljer:

```kusto
CommonSecurityLog 
| where DeviceVendor == "TrendMicroTippingPoint"
```

Se **Nästa steg** -fliken på kopplings sidan för fler fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Trend Micro TippingPoint till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.