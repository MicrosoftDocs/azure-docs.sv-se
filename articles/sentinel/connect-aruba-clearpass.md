---
title: Anslut Aruba ClearPass-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Aruba ClearPass Connector för att hämta Aruba ClearPass-loggar till Azure Sentinel. Visa Aruba ClearPass-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 8050b4f173476d7af66cb858ff5f785e5a12af43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046580"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Anslut din Aruba-ClearPass till Azure Sentinel

> [!IMPORTANT]
> Aruba ClearPass-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter din Aruba ClearPass-installation till Azure Sentinel. Med Aruba ClearPass data Connector kan du enkelt ansluta dina Aruba ClearPass-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra undersökningen. Integreringen mellan Aruba ClearPass och Azure Sentinel använder CEF-formaterade syslog, en Linux-baserad logg vidarebefordrare och Log Analytics agenten. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha Läs behörighet till delade nycklar för arbets ytan. [Läs mer om nycklar för arbets ytor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Skicka Aruba ClearPass-loggar till Azure Sentinel

Om du vill hämta sina loggar till Azure Sentinel konfigurerar du Aruba ClearPass-installationen så att syslog-meddelanden skickas i CEF-format till en Linux-baserad logg vidarebefordrande server (som kör rsyslog eller syslog-ng). Den här servern kommer att ha Log Analytics-agenten installerad och agenten vidarebefordrar loggarna till Azure Sentinel-arbetsytan.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. Från **data kopplings** galleriet väljer du **Aruba ClearPass (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på fliken **instruktioner** under **konfiguration**:

    1. Under **1. Konfiguration av Linux syslog-agenten** – gör det här steget om du inte redan har en logg vidarebefordrare som körs eller om du behöver en annan. Se [steg 1: Distribuera logg vidarebefordraren](connect-cef-agent.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

    1. Under **2. Vidarebefordra Aruba ClearPass-loggar till en syslog-agent** – följa Aruba-instruktioner för att [Konfigurera ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Den här konfigurationen måste innehålla följande element:
        - Logg destination – värd namnet och/eller IP-adressen för servern för logg vidarebefordran
        - Protokoll och port – TCP 514 (om det är rekommenderat annat bör du se till att göra den parallella ändringen i syslog-daemonen på servern för logg vidarebefordran)
        - Logg format – CEF
        - Logg typer – alla tillgängliga eller alla lämpliga

    1. Under **3. Verifiera anslutning** – Verifiera data inmatning genom att kopiera kommandot på anslutnings sidan och köra det på logg vidarebefordraren. Se [steg 3: kontrol lera anslutningen](connect-cef-verify.md) i Azure Sentinel-dokumentationen för mer detaljerade instruktioner och förklaringar.

        Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet **Azure Sentinel** i tabellen *CommonSecurityLog* .

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. [Följ de här stegen](https://aka.ms/sentinel-arubaclearpass-parser) för att skapa **ArubaClearPass** Kusto-funktions Ali Aset.

Om du sedan vill fråga Aruba ClearPass data i Log Analytics anger du `ArubaClearPass` överst i frågefönstret.

Se **Nästa steg** -fliken på kopplings sidan för några användbara fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Aruba-ClearPass till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.