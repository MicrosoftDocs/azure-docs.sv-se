---
title: Felsöka anslutningar – Azure Portal
titleSuffix: Azure Network Watcher
description: Lär dig hur du använder anslutningen fel söknings funktion i Azure Network Watcher med hjälp av Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f33c5f0fdf69737df0d8bd83499ded1e0e0f4f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898121"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Felsöka anslutningar med Azure Network Watcher med hjälp av Azure Portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Lär dig hur du använder anslutnings fel sökning för att kontrol lera om en direkt TCP-anslutning från en virtuell dator till en specifik slut punkt kan upprättas.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har följande resurser:

* En instans av Network Watcher i den region där du vill felsöka en anslutning.
* Virtuella datorer för att felsöka anslutningar med.

> [!IMPORTANT]
> Fel sökning av anslutning kräver att den virtuella datorn som du felsöker från har `AzureNetworkWatcherExtension` VM-tillägget installerat. För att installera tillägget på en virtuell Windows-dator går du till [azure Network Watcher agent-tillägget virtuell dator för Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och för virtuella Linux-datorer gå till [Azure Network Watcher virtuell dator tillägg för Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på mål slut punkten.

## <a name="check-connectivity-to-a-virtual-machine"></a>Kontrol lera anslutningen till en virtuell dator

Det här exemplet kontrollerar anslutningen till en virtuell mål dator via port 80.

Navigera till Network Watcher och klicka på **anslutnings fel sökning**. Välj den virtuella dator som du vill kontrol lera anslutningen från. I avsnittet **mål** väljer du **Välj en virtuell dator** och väljer rätt virtuell dator och port att testa.

När du klickar på **kontrol lera kontrol** leras anslutningen mellan de virtuella datorerna på den angivna porten. I det här exemplet är den virtuella mål datorn inte nåbar, en lista över hopp visas.

![Kontrol lera anslutnings resultatet för en virtuell dator][1]

## <a name="check-remote-endpoint-connectivity"></a>Kontrollera anslutningen till fjärrslutpunkten

Om du vill kontrol lera anslutningen och svars tiden för en fjärrslutpunkt väljer du alternativ knappen **ange manuellt** i **mål** avsnittet, anger URL och port och klickar på **kontrol lera**.  Detta används för Fjärrslutpunkter som webbplatser och lagrings slut punkter.

![Kontrol lera anslutnings resultaten för en webbplats][2]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du automatiserar paket fångster med aviseringar för virtuella datorer genom att visa [skapa en varning utlöst paket fångst](network-watcher-alert-triggered-packet-capture.md)

Ta reda på om en viss trafik tillåts i eller från den virtuella datorn genom [att gå igenom kontrol lera IP-flöde verifiera](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png