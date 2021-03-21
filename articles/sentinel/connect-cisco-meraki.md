---
title: Anslut Cisco Meraki-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Cisco Meraki Connector för att hämta Cisco Meraki-loggar till Azure Sentinel. Visa Cisco Meraki-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747085"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Anslut Cisco-Meraki till Azure Sentinel

> [!IMPORTANT]
> Cisco Meraki-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter Cisco Meraki (MX/MS/MR)-program till Azure Sentinel. Med Cisco Meraki data Connector kan du enkelt ansluta dina Cisco Meraki-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Integreringen mellan Cisco Meraki och Azure Sentinel använder en Syslog-server med Log Analytics-agenten installerad. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Cisco Meraki-lösningen måste konfigureras för att exportera loggar via syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Skicka Cisco Meraki-loggar till Azure Sentinel via syslog-agenten  

Konfigurera Cisco-Meraki för att vidarebefordra syslog-meddelanden till din Azure Sentinel-arbetsyta via syslog-agenten.

1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du **Cisco-Meraki (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ anvisningarna på kopplings sidan för **Cisco Meraki** :

    1. Installera och publicera agenten för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell).

    1. Konfigurera loggarna som ska samlas in

        - Välj funktionerna och allvarlighets graderna i **konfigurationen för arbets ytans agenter**.

    1. Konfigurera och Anslut Cisco Meraki-enhet (er)

        - Följ [de här anvisningarna](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) för att konfigurera Cisco Meraki-enheten (s) för att vidarebefordra syslog. Använd IP-adressen för den Linux-dator som du installerade Linux-agenten på på fjärrservern.

## <a name="validate-connectivity-and-find-your-data"></a>Verifiera anslutningen och hitta dina data

Det kan ta upp till 20 minuter innan loggarna börjar visas i Azure Sentinel. 

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet *logg hantering* i tabellen *syslog* .

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. [Följ de här stegen](https://aka.ms/sentinel-ciscomeraki-parser) för att skapa **CiscoMeraki** Kusto-funktions Ali Aset. Sedan kan du skriva `CiscoMeraki` in frågefönstret för att fråga efter data.

Se **Nästa steg** -fliken på kopplings sidan för några användbara fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Cisco Meraki till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
