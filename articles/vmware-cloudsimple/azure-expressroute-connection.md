---
title: Azure VMware-lösning av CloudSimple-Anslut privat moln till Azure-nätverk med ExpressRoute
description: Beskriver hur du ansluter din CloudSimple privata moln miljö till det virtuella Azure-nätverket med ExpressRoute
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895996"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Anslut din CloudSimple privata moln miljö till det virtuella Azure-nätverket med ExpressRoute

Ditt privata CloudSimple-moln kan anslutas till ditt virtuella Azure-nätverk med Azure ExpressRoute.  Med den här hög bandbredden kan du få åtkomst till tjänster som körs i din Azure-prenumeration från din privata moln miljö.

Med anslutning till virtuellt nätverk kan du:

* Använd Azure som ett säkerhets kopierings mål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln virtuellt San data lager.
* Använd hybrid program där webb nivån för programmet körs i det offentliga molnet medan program-och databas nivåerna körs i ditt privata moln.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Konfigurera en virtuell nätverks anslutning

Om du vill konfigurera den virtuella nätverks anslutningen till ditt privata moln, behöver du en verifierings nyckel, en peer-krets-URI och åtkomst till din Azure-prenumeration. Den här informationen finns på sidan Virtual Network anslutning på CloudSimple-portalen. Instruktioner finns i [Hämta peering-information för Azure Virtual Network till CloudSimple-anslutning](virtual-network-connection.md). Om du har problem med att hämta informationen kan du skicka in en <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">support förfrågan</a>.

> [!TIP]
> Om du redan har ett virtuellt Azure-nätverk, Gateway-undernät och virtuell nätverksgateway kan du gå vidare till steg 4.

1. Skapa ett virtuellt nätverk i din Azure-prenumeration och kontrol lera att det adress utrymme du väljer skiljer sig från det privata molnets adress utrymme.  Om du redan har ett virtuellt Azure-nätverk kan du använda det befintliga.  Mer information finns i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md).
2. Skapa Gateway-undernätet i det virtuella Azure-nätverket.  Om du redan har ett Gateway-undernät i det virtuella Azure-nätverket kan du använda det befintliga. Mer information finns i [skapa Gateway-undernätet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Skapa den virtuella Nätverksgatewayen på det virtuella nätverket.  Om du har en befintlig virtuell nätverksgateway kan du använda den befintliga. Mer information finns i [skapa den virtuella Nätverksgatewayen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Skapa anslutningen mellan ditt virtuella nätverk och ditt privata moln genom att lösa in verifierings nyckeln enligt beskrivningen i [ansluta ett virtuellt nätverk till en krets-annan prenumeration](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Om du använder en befintlig virtuell nätverksgateway och den har en ExpressRoute-anslutning till samma plats som CloudSimple ExpressRoute-kretsen upprättas inte anslutningen.  Skapa ett nytt virtuellt nätverk och följ föregående steg.

## <a name="test-the-virtual-network-connection"></a>Testa anslutningen till det virtuella nätverket

När anslutningen har skapats kan du kontrol lera status för anslutningen genom att välja **Egenskaper** under **Inställningar**.  Status och etablerings status **bör visas.**

![Anslutnings status](media/azure-expressroute-connection.png)

Så här testar du den virtuella nätverks anslutningen:

1. Skapa en virtuell dator i din Azure-prenumeration.
2. Hitta IP-adressen för ditt privata moln vCenter (se Välkommen-e-postmeddelandet).
3. Pinga din moln-vCenter från den virtuella datorn som skapades i det virtuella Azure-nätverket.
4. Pinga din virtuella Azure-dator från en virtuell dator som körs i ditt privata moln vCenter.

Om du har problem med att upprätta anslutningen skickar du en <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supportbegäran</a>.
