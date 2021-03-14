---
title: Självstudie – Konfigurera nätverk för ditt privata VMware-moln i Azure
description: Lär dig att skapa och konfigurera de nätverk som behövs för att distribuera ditt privata moln i Azure
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 0fc934cfec17d8d3bb69c21d324f06bee3f9515c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462382"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Självstudie: Konfigurera nätverk för ditt privata VMware-moln i Azure

Ett privat moln i Azure VMware-lösningen kräver en Azure-Virtual Network. Eftersom Azure VMware-lösningen inte stöder din lokala vCenter krävs ytterligare steg för integrering med din lokala miljö. Du måste också konfigurera en ExpressRoute-krets och en virtuell nätverksgateway.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en virtuell nätverksgateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

## <a name="prerequisites"></a>Förutsättningar 
Ett virtuellt nätverk som du har skapat ett [privat moln för Azure VMware-lösningen](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Navigera till den resurs grupp som du skapade i [självstudien skapa ett privat moln](tutorial-create-private-cloud.md) och välj **+ Lägg** till för att definiera en ny resurs. 

1. I text rutan **Sök i Marketplace skriver du** **Virtual Network**. Hitta Virtual Network resursen och markera den.

1. På sidan **Virtual Network** väljer du **skapa** för att konfigurera ditt virtuella nätverk för ditt privata moln.

1. På sidan **skapa Virtual Network** anger du information om det virtuella nätverket.

1. På fliken **grundläggande** anger du ett namn för det virtuella nätverket, väljer lämplig region och väljer **Nästa: IP-adresser**.

1. På fliken **IP-adresser** , under **IPv4-adress utrymme**, anger du det adress utrymme som du skapade i föregående självstudie.

   > [!IMPORTANT]
   > Du måste använda ett adress utrymme som **inte** överlappar adress utrymmet som du använde när du skapade ditt privata moln i föregående självstudie.

1. Välj **+ Lägg till undernät** och ge under nätet ett namn och lämpligt adress intervall på sidan **Lägg till undernät** . Välj **Lägg till** när det är klart.

1. Välj **Granska + skapa**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Välj granska + skapa." border="true":::

1. Verifiera informationen och välj **skapa**. När distributionen är klar visas ditt virtuella nätverk i resurs gruppen.

## <a name="create-a-virtual-network-gateway"></a>Skapa en virtuell nätverksgateway

Nu när du har skapat ett virtuellt nätverk skapar du en virtuell nätverksgateway.

1. I resurs gruppen väljer du **+ Lägg** till för att lägga till en ny resurs.

1. I text rutan **Sök i Marketplace anger du** **virtuell nätverksgateway**. Hitta Virtual Network resursen och markera den.

1. På sidan **Virtual Network Gateway** väljer du **skapa**.

1. På fliken grundläggande på sidan **Skapa virtuell nätverksgateway** anger du värden för fälten och väljer sedan **Granska + skapa**. 

   | Fält | Värde |
   | --- | --- |
   | **Prenumeration** | Förifyllt värde med den prenumeration som resurs gruppen tillhör. |
   | **Resursgrupp** | Ifyllt värde för den aktuella resurs gruppen. Värdet ska vara den resurs grupp som du skapade i ett tidigare test. |
   | **Namn** | Ange ett unikt namn för den virtuella Nätverksgatewayen. |
   | **Region** | Välj den geografiska platsen för den virtuella Nätverksgatewayen. |
   | **Typ av Gateway** | Välj **ExpressRoute**. |
   | **SKU** | Låt standardvärdet vara **standard:** |
   | **Virtuellt nätverk** | Välj det virtuella nätverk som du skapade tidigare. Om du inte ser det virtuella nätverket kontrollerar du att gatewayens region stämmer överens med regionen för det virtuella nätverket. |
   | **Adress intervall för gateway-undernät** | Det här värdet fylls i när du väljer det virtuella nätverket. Ändra inte standardvärdet. |
   | **Offentlig IP-adress** | Välj **Skapa ny**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Ange värden för fälten och välj sedan granska + skapa." border="true":::

1. Kontrol lera att informationen är korrekt och välj **skapa** för att starta distributionen av den virtuella Nätverksgatewayen. 
1. När distributionen är klar går du vidare till nästa avsnitt för att ansluta din ExpressRoute-anslutning till den virtuella Nätverksgatewayen som innehåller ditt privata moln i Azure VMware-lösningen.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Anslut ExpressRoute till den virtuella Nätverksgatewayen

Nu när du har distribuerat en virtuell nätverksgateway lägger du till en anslutning mellan den och ditt privata moln i Azure VMware-lösningen.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Leta upp URL: erna för vCenter och NSX Manager

Om du vill logga in på vCenter och NSX Manager behöver du URL: erna till vCenter-webbklienten och NSX-T Manager-platsen. 

Navigera till Azure VMware-lösningen privat moln, under **Hantera**, Välj **identitet**, här hittar du den information som behövs.

:::image type="content" source="./media/tutorial-access-private-cloud/generate-vcenter-nsxt-passwords.png" alt-text="Skärm bild av autentiseringsuppgifterna vCenter och NSX-T och webb klientens URL: er." border="true" lightbox="media/tutorial-access-private-cloud/generate-vcenter-nsxt-passwords.png":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en virtuell nätverksgateway
> * Anslut din ExpressRoute-krets till gatewayen
> * Leta upp URL: erna för vCenter och NSX Manager

Fortsätt till nästa självstudie och lär dig hur du skapar de NSX-T-datasegment som används för virtuella datorer i vCenter.

> [!div class="nextstepaction"]
> [Skapa ett NSX-T-nätverkssegment](tutorial-nsx-t-network-segment.md)