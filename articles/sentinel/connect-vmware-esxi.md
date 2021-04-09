---
title: Anslut VMware ESXi data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder VMware ESXi data Connector för att hämta ESXi-loggar till Azure Sentinel. Visa ESXi-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700928"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Anslut din VMware ESXi till Azure Sentinel

> [!IMPORTANT]
> VMware ESXi Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Den här artikeln förklarar hur du ansluter VMware ESXi-installationen till Azure Sentinel. Med VMware ESXi data Connector kan du enkelt mata in dina VMware ESXi-loggar i Azure Sentinel, vilket ger dig mer insikt i organisationens ESXi-aktiviteter och hjälper till att förbättra dina säkerhets funktioner. Integreringen mellan VMware ESXi och Azure Sentinel använder en Syslog-server med den Log Analytics-agent som är installerad. Den använder också en anpassad, inbyggd logg parser som baseras på en Kusto-funktion.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Din VMware ESXi-lösning måste konfigureras för att exportera loggar via syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Skicka VMware ESXi loggar till syslog-agenten  

Konfigurera VMware ESXi att vidarebefordra syslog-meddelanden till din Azure Sentinel-arbetsyta via syslog-agenten.
3. Följ instruktionerna på sidan **VMware ESXi** .


1. I navigerings menyn i Azure Sentinel väljer du **data kopplingar**.

1. I galleriet **data anslutningar** väljer du anslutningen **VMware ESXi (för hands version)** och **öppnar sedan kopplings sidan**.

1. Följ instruktionerna på sidan **VMware ESXi** koppling:

    1. Installera och publicera agenten för Linux

        - Välj en virtuell Azure Linux-dator eller en icke-Azure Linux-dator (fysisk eller virtuell).

    1. Konfigurera loggarna som ska samlas in

        - Välj funktionerna och allvarlighets graderna i **konfigurationen för arbets ytans agenter**.

    1. Konfigurera och Anslut VMware ESXi

        - Följ dessa anvisningar för att konfigurera VMware ESXi att vidarebefordra syslog:
            - [VMware ESXi 3,5 och 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 och högre](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Använd IP-adressen för den Linux-dator som du installerade Linux-agenten på på fjärrservern.

## <a name="validate-connectivity-and-find-your-data"></a>Verifiera anslutningen och hitta dina data

Det kan ta upp till 20 minuter innan loggarna börjar visas i Azure Sentinel. 

När en lyckad anslutning har upprättats visas data i **loggarna** under avsnittet *logg hantering* i tabellen *syslog* .

Den här data kopplingen är beroende av en parser som baseras på en Kusto-funktion som fungerar som förväntat. [Följ de här stegen](https://aka.ms/sentinel-vmwareesxi-parser) för att skapa **VMwareESXi** Kusto-funktions Ali Aset. Sedan kan du skriva `VMwareESXi` in ett frågefönster i Azure Sentinel för att fråga data.

Se **Nästa steg** -fliken på kopplings sidan för några användbara fråge exempel.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter VMware ESXi till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
