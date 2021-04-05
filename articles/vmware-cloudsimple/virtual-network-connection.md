---
title: Anslut Azure Virtual Network till CloudSimple med ExpressRoute – Azure VMware-lösning från CloudSimple
description: Beskriver hur du hämtar peering-information för en anslutning mellan det virtuella Azure-nätverket och din CloudSimple-miljö
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3fedfbe55fd8ea3d2b4cc910df631e40bc74e210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899073"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Anslut Azure Virtual Network till CloudSimple med ExpressRoute

Du kan utöka ditt privata moln nätverk till ditt virtuella Azure-nätverk och Azure-resurser. Med en ExpressRoute-anslutning kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.

## <a name="request-authorization-key"></a>Verifierings nyckel för begäran

Det krävs en auktoriseringspost för ExpressRoute-anslutningen mellan ditt privata moln och det virtuella Azure-nätverket. För att hämta en nyckel, File a Ticket with <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">support</a>.  Använd följande information i begäran:

* Typ av problem: **teknisk**
* Prenumeration: **Välj den prenumeration där CloudSimple-tjänsten har distribuerats**
* Tjänst: **VMware-lösning av CloudSimple**
* Problem typ: **tjänstbegäran**
* Problem under typ: **auktoriseringsregel för Azure VNET-anslutning**
* Subject: **begäran om auktoriseringskod för Azure VNET-anslutning**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Hämta peering-information från CloudSimple-portalen

Om du vill konfigurera anslutningen måste du upprätta en anslutning mellan Azure Virtual Network och din CloudSimple-miljö.  Som en del av proceduren måste du ange peer-kretsens URI och verifierings nyckel. Hämta URI och verifierings nyckel från [CloudSimple-portalen](access-cloudsimple-portal.md).  Välj **nätverk** på sido menyn och välj sedan **Azure nätverks anslutning**. Eller Välj **konto** på sido menyn och välj sedan **Azure-nätverksanslutning**.

Kopiera peer-krets-URI och för verifierings nyckeln för var och en av de regioner som använder *Kopiera* -ikonen. För varje CloudSimple-region som du vill ansluta:

1. Kopiera URI: n genom att klicka på **Kopiera** . Klistra in den i en fil där den kan vara tillgänglig att läggas till i Azure Portal.  
2. Klicka på **Kopiera** för att kopiera verifierings nyckeln och klistra in den i filen även.

Kopiera verifierings nyckeln och den peer-krets-URI som är i tillståndet **tillgängligt** .  **Använd** status anger att nyckeln redan har använts för att skapa en virtuell nätverks anslutning.

![Sidan Virtual Network anslutning](media/virtual-network-connection.png)

Mer information om hur du konfigurerar det virtuella Azure-nätverket till CloudSimple-länken finns i [ansluta din CloudSimple-privata moln miljö till det virtuella Azure-nätverket med hjälp av ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Nästa steg

* [Azure Virtual Network-anslutning till privat moln](azure-expressroute-connection.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](on-premises-connection.md)
