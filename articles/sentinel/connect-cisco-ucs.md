---
title: Ansluta Cisco Unified Computing System (UCS)-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Cisco UCS data Connector för att hämta Cisco UCS-loggar till Azure Sentinel. Visa Cisco-UCS-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530679"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Anslut ditt Cisco Unified Computing System (UCS) till Azure Sentinel

> [!IMPORTANT]
> Cisco UCS-anslutningen är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln beskriver hur du ansluter UCS-installationen (Cisco Unified Computing System) till Azure Sentinel. Med Cisco UCS data Connector kan du enkelt ansluta dina UCS-loggar med Azure Sentinel, så att du kan visa data i arbets böcker, använda dem för att skapa anpassade aviseringar och lägga till dem för att förbättra undersökningen. Integreringen mellan Cisco UCS och Azure Sentinel använder syslog.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Cisco UCS-lösningen måste konfigureras för att exportera loggar via syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Vidarebefordra Cisco UCS-loggar till syslog-agenten  

Konfigurera Cisco UCS för att vidarebefordra syslog-meddelanden till din Azure Sentinel-arbetsyta via syslog-agenten.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Cisco UCS-anslutaren (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ instruktionerna på sidan **Cisco UCS** Connector:

    1. Installera och publicera agenten för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell).

    1. Konfigurera loggarna som ska samlas in

        - Välj funktionerna och allvarlighets graderna i konfigurationen för arbets ytans agenter.

    1. Konfigurera och Anslut Cisco UCS

        - Följ [dessa anvisningar](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) för att konfigurera Cisco UCS för att vidarebefordra syslog. Använd IP-adressen för den Linux-dator som du installerade Linux-agenten på på fjärrservern.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under syslog.

Se **Nästa steg** -fliken på kopplings sidan för några användbara exempel frågor.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Cisco UCS till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
