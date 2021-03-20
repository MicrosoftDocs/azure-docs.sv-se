---
title: Anslut DNS-data i Azure Sentinel | Microsoft Docs
description: Lär dig att ansluta valfri DNS-server (Domain Name Server) som körs på Windows till Azure Sentinel genom att installera en agent på DNS-datorn.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: abecddb6f5469cb4ef463e65d6c74149bf34dca9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590226"
---
# <a name="connect-your-domain-name-server"></a>Anslut din domän namns Server

> [!IMPORTANT]
> DNS data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta alla DNS-servrar (Domain Name Server) som körs på Windows till Azure Sentinel. Detta görs genom att installera en agent på DNS-datorn. Med hjälp av DNS-loggar kan du få säkerhet, prestanda och säkerhetsrelaterade insikter om organisationens DNS-infrastruktur genom att samla in, analysera och korrelera analys-och gransknings loggar och andra relaterade data från DNS-servrarna.

När du aktiverar DNS-logg anslutning kan du:
- Identifiera klienter som försöker lösa skadliga domän namn
- Identifiera inaktuella resurs poster
- Identifiera ofta efterfrågade domän namn och pratsam DNS-klienter
- Visa begär ande belastning på DNS-servrar
- Visa dynamiska DNS-registrerings fel

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten källa** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](../azure-monitor/agents/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../azure-monitor/vm/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in DNS-information från Direct Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](../azure-monitor/agents/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten Operations Manager hanterings grupp. En direkt anslutning från Operations Manager agent till Azure Monitor krävs inte. Data vidarebefordras från hanterings gruppen till Log Analytics-arbetsytan. |
| [Azure Storage-konto](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | Inga | Azure Storage används inte av lösningen. |

### <a name="data-collection-details"></a>Information om data insamling

Lösningen samlar in DNS-inventering och DNS-säkerhetsrelaterade data från DNS-servrarna där en Log Analytics-agent är installerad. Lagerrelaterade data, till exempel antalet DNS-servrar, zoner och resurs poster, samlas in genom att köra DNS PowerShell-cmdletar. Data uppdateras var två: e dag. Händelse-relaterade data samlas in nära real tid från de [analytiska och gransknings loggar](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) som tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Anslut din DNS-installation

1. I Azure Sentinel-portalen väljer du **data anslutningar** och sedan panelen **DNS (för hands version)** .
1. Om dina DNS-datorer finns i Azure:
    1. Klicka på **Installera agent på virtuell Azure Windows-dator**.
    1. I listan med **virtuella datorer** väljer du den DNS-dator som du vill strömma till Azure Sentinel. Se till att det här är en virtuell Windows-dator.
    1. I fönstret som öppnas för den virtuella datorn klickar du på **Anslut**.  
    1. Klicka på **Aktivera** i fönstret **DNS-anslutning** . 

2. Om din DNS-dator inte är en virtuell Azure-dator:
    1. Klicka på **Installera agent på datorer som inte är Azure-datorer**.
    1. I fönstret **direkt agent** väljer du antingen **Ladda ned Windows-agent (64 bitar)** eller **ladda ned Windows agent (32 bitar)**.
    1. Installera agenten på din DNS-dator. Kopiera **arbetsyte-ID**, **primär nyckel** och **sekundär nyckel** och Använd dem när du uppmanas att göra det under installationen.

3. Om du vill använda det relevanta schemat i Log Analytics för DNS-loggarna söker du efter **DnsEvents**.

## <a name="validate"></a>Verifiera 

Sök efter schemat **DnsEvents** i Log Analytics och se till att det finns händelser.

## <a name="troubleshooting"></a>Felsökning

Om Sök frågor inte visas i Azure Sentinel följer du dessa steg så att frågorna visas korrekt:
1. Aktivera [DNS-analys loggar på dina servrar](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Kontrol lera att DNSEvents visas i listan Log Analytics samling.
3. Aktivera [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. I Azure DNS Analytics, under **konfiguration**, ändra inställningarna, spara den och ändra den sedan igen, och spara den sedan igen.
5. Kontrol lera Azure DNS analys för att se till att frågorna nu visas.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter lokala DNS-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).