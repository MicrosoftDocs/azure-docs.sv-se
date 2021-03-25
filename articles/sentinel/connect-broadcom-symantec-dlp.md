---
title: Anslut Broadcom Symantec data förlust skydd (DLP) till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Broadcom Symantec DLP data Connector för att hämta Symantec DLP-loggar till Azure Sentinel. Visa Symantec DLP-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 4be182b3eee59f7f9d2ef704a3d0f57c6718b45d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044982"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Anslut din Broadcom Symantec data förlust skydd (DLP) till Azure Sentinel

> [!IMPORTANT]
> Broadcom Symantec DLP-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Broadcom Symantec DLP-installation till Azure Sentinel. Med Broadcom Symantec DLP data Connector kan du enkelt ansluta dina Symantec DLP-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Den här funktionen ger dig mer information om din organisations information, var den reser och förbättrar dina funktioner för säkerhets åtgärder. Integrering mellan Broadcom Symantec DLP och Azure Sentinel använder CEF syslog, en Linux-baserad logg vidarebefordrare och den Log Analytics agenten. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Skicka Broadcom Symantec DLP-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du din Broadcom Symantec DLP-enhet för att skicka syslog-meddelanden i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Broadcom Symantec DLP (för hands version)** och öppnar sedan **kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. Under **2. Vidarebefordra Symantec DLP-loggar till en syslog-agent** – Följ Broadcom: s instruktioner för att [Konfigurera Symantec DLP](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Den här konfigurationen måste innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – TCP 514 (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – CEF
        - Logg typer – alla tillgängliga eller alla lämpliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. [Följ de här stegen](https://aka.ms/sentinel-symantecdlp-parser) för att skapa **SymantecDLP** Kusto-funktions Ali Aset.

Om du sedan vill fråga Broadcom Symantec DLP-data i Log Analytics anger du `SymantecDLP` överst i frågefönstret.

Se **Nästa steg** -fliken på kopplings sidan för några användbara fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Symantec DLP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.