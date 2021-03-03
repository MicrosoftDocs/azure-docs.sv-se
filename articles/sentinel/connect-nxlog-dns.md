---
title: Anslut NXLog Windows DNS-loggar data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder NXLog DNS-loggar data Connector för att hämta Windows DNS-händelser till Azure Sentinel. Visa Windows DNS-data i arbets böcker, skapa aviseringar och förbättra undersökningen.
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
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747082"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Anslut dina NXLog Windows DNS-loggar till Azure Sentinel

> [!IMPORTANT]
> NXLog DNS-loggar Connector är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

Med [NXLOG DNS logs](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) Connector kan du enkelt exportera alla dina Windows DNS-Server-händelser till Azure Sentinel i real tid, vilket ger dig insikter om domän namns Server aktivitet i hela organisationen. Det använder [ETW (High performance ETW (Event tracing for Windows))](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) för att samla in både gransknings-och analytiska DNS-Server händelser i real tid och har stöd för Event-anrikning med anpassade fält. Integrering mellan NXLog DNS-loggar och Azure Sentinel underlättar genom REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-nxlog-dns-logs"></a>Konfigurera och Anslut NXLog DNS-loggar

NXLog kan konfigureras för att skicka händelser i JSON-format direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **NXLog DNS logs** Connector.

1. Välj **Öppna kopplings sida**.

1. Följ de stegvisa anvisningarna i avsnittet *NXLog user guide* integration [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) för att konfigurera vidarebefordring via REST API.

## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i **loggar** under avsnittet  **anpassade loggar** i tabellen *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder NXLog för att mata in Windows DNS-loggar i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
