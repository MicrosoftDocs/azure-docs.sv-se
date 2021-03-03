---
title: Anslut NXLog LinuxAudit-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder NXLog LinuxAudit data Connector för att hämta LinuxAudit-loggar till Azure Sentinel. Visa LinuxAudit-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700938"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Anslut din NXLog-LinuxAudit till Azure Sentinel

> [!IMPORTANT]
> NXLog LinuxAudit-anslutningsprogrammet är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) Connector kan du enkelt exportera Linux-säkerhetshändelser till Azure Sentinel i real tid, vilket ger dig insikter om domän namns Server aktivitet i hela organisationen. NXLog LinuxAudit-modulen stöder anpassade gransknings regler och samlar in loggar utan granskade eller andra användar utrymmes program. IP-adresser och grupp-/användar-ID: n matchas mot respektive namn som gör [Linux-gransknings](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) loggar mer begripliga för säkerhetsanalytiker. Integreringen mellan NXLog LinuxAudit och Azure Sentinel under lättas via REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Konfigurera och ansluta NXLog-LinuxAudit

NXLog kan konfigureras för att skicka händelser i JSON-format direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **NXLog LinuxAudit** Connector.

1. Välj **Öppna kopplings sida**.

1. Följ de stegvisa anvisningarna i avsnittet *NXLog user guide* integration [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) för att konfigurera vidarebefordring via REST API.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggar** under avsnittet  **anpassade loggar** i tabellen *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder NXLog LinuxAudit för att mata in Linux-säkerhetshändelser i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
