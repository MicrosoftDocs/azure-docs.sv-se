---
title: Distribuera och konfigurera Azure VMware-lösning
description: Lär dig hur du använder den information som samlas in i planerings fasen för att distribuera Azure VMware-lösningens privata moln.
ms.topic: tutorial
ms.date: 12/24/2020
ms.openlocfilehash: f2b6f3c4ad82117fee96e0c2e5973a7011384d48
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760883"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>Distribuera och konfigurera Azure VMware-lösning

I den här artikeln ska du använda informationen från [planerings avsnittet](production-ready-deployment-steps.md) för att distribuera Azure VMware-lösningen. 

>[!IMPORTANT]
>Om du inte har definierat informationen ännu går du tillbaka till [planerings avsnittet](production-ready-deployment-steps.md) innan du fortsätter.

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>Distribuera Azure VMware Solution

Använd den information som du samlade in i artikeln [Planera distribution av Azure VMware-lösningar](production-ready-deployment-steps.md) :

>[!NOTE]
>Om du vill distribuera en Azure VMware-lösning måste du vara minst deltagar nivå i prenumerationen.

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

>[!NOTE]
>En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen: distributions](https://www.youtube.com/embed/gng7JjxgayI) video.

## <a name="create-the-jump-box"></a>Skapa hopp rutan

>[!IMPORTANT]
>Om du lämnade **Virtual Network** alternativet tomt under det första etablerings steget på skärmen **skapa ett privat moln** , slutför du själv studie kursen [Konfigurera nätverk för ditt privat moln](tutorial-configure-networking.md) i VMware **innan** du fortsätter med det här avsnittet.  

När du har distribuerat Azure VMware-lösningen skapar du det virtuella nätverkets hopp ruta som ansluter till vCenter och NSX. När du har konfigurerat ExpressRoute-kretsar och ExpressRoute Global Reach behöver inte hopp rutan.  Men det är praktiskt att uppnå vCenter och NSX i din Azure VMware-lösning.  

:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="Skapa hopp rutan för Azure VMware-lösningen" border="false" lightbox="media/pre-deployment/jump-box-diagram.png":::

Följ dessa instruktioner för att skapa en virtuell dator (VM) i det virtuella nätverk som du har [identifierat eller skapat som en del av distributions processen](production-ready-deployment-steps.md#attach-virtual-network-to-azure-vmware-solution): 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>Ansluta till ett virtuellt nätverk med ExpressRoute

>[!IMPORTANT]
>Om du redan har definierat ett virtuellt nätverk på distributions skärmen i Azure kan du gå vidare till nästa avsnitt.

Om du inte definierade ett virtuellt nätverk i distributions steget och avsikten är att ansluta Azure VMware-lösningens ExpressRoute till en befintlig ExpressRoute-Gateway, följer du dessa steg.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>Verifiera nätverks vägar som annonseras

Hopp rutan finns i det virtuella nätverk där Azure VMware-lösningen ansluter via sin ExpressRoute-krets.  I Azure går du till hopp rutans nätverks gränssnitt och [visar effektiva vägar](../virtual-network/manage-route-table.md#view-effective-routes).

I listan gällande vägar bör du se de nätverk som skapats som en del av distributionen av Azure VMware-lösningen. Du ser flera nätverk som har härletts från [ `/22` nätverket som du definierade](production-ready-deployment-steps.md#ip-address-segment) under [distributions steget](#deploy-azure-vmware-solution) tidigare i den här artikeln.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="Verifiera nätverks vägar som annonseras från Azure VMware-lösning till Azure Virtual Network" lightbox="media/pre-deployment/azure-vmware-solution-effective-routes.png":::

I det här exemplet har 10.74.72.0/22-nätverket inträffat under distributionen och nätverket är 24.  Om du ser något liknande kan du ansluta till vCenter i Azure VMware-lösningen.

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>Anslut och logga in på vCenter och NSX-T

Logga in i hopp rutan som du skapade i föregående steg. När du har loggat in öppnar du en webbläsare och går till och loggar in i både vCenter-och NSX-T-admin-konsolen.  

Du kan identifiera vCenter-och NSX-T-admin-konsolens IP-adresser och autentiseringsuppgifter i Azure Portal.  Välj ditt privata moln och välj sedan **identitet > standard** i vyn **Översikt** . 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>Skapa ett nätverks segment i Azure VMware-lösningen

Du använder NSX-T för att skapa nya nätverks segment i din Azure VMware-lösnings miljö.  Du definierade de nätverk som du vill skapa i [planerings avsnittet](production-ready-deployment-steps.md).  Om du inte har definierat dem går du tillbaka till [planerings avsnittet](production-ready-deployment-steps.md) innan du fortsätter.

>[!IMPORTANT]
>Kontrol lera att det CIDR-adressblock som du definierat inte överlappar något i dina Azure-eller lokala miljöer.  

Följ självstudien [skapa ett NSX-t-nätverk i Azure VMware Solution](tutorial-nsx-t-network-segment.md) för att skapa ett NSX-nätverks segment i Azure VMware-lösningen.

## <a name="verify-advertised-nsx-t-segment"></a>Verifiera annonserat NSX-T-segment

Gå tillbaka till steget [Verifiera nätverks vägar som annonser](#verify-network-routes-advertised) ATS. Du ser andra vägar i listan som representerar de nätverks segment som du skapade i föregående steg.  

För virtuella datorer tilldelar du segment som du skapade i steget [skapa ett nätverks segment i Azure VMware-lösningen](#create-a-network-segment-on-azure-vmware-solution) .  

Eftersom DNS krävs, identifiera vilken DNS-server som du vill använda.  

- Om du har ExpressRoute Global Reach konfigurerad använder du den DNS-server som du använder lokalt.  
- Om du har en DNS-server i Azure använder du den.  
- Om du inte har någon kan du använda den DNS-server som hopp rutan använder.

>[!NOTE]
>Det här steget är att identifiera DNS-servern och inga konfigurationer görs i det här steget.

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>Valfritt Tillhandahålla DHCP-tjänster till NSX-T nätverks segment

Om du planerar att använda DHCP på dina NSX-T-segment fortsätter du med det här avsnittet. Annars hoppar du till avsnittet [Lägg till en virtuell dator i avsnittet NSX-T-nätverks segment](#add-a-vm-on-the-nsx-t-network-segment) .  

Nu när du har skapat ditt NSX-T-nätverks segment kan du skapa och hantera DHCP i Azure VMware-lösningen på två sätt:

* Om du använder NSX-T för att vara värd för DHCP-servern måste du [skapa en DHCP-server](manage-dhcp.md#create-a-dhcp-server) och [vidarebefordra till den servern](manage-dhcp.md#create-dhcp-relay-service). 
* Om du använder en extern DHCP-server från tredje part i nätverket måste du [skapa DHCP Relay service](manage-dhcp.md#create-dhcp-relay-service).  För det här alternativet [utför du bara relä konfigurationen](manage-dhcp.md#create-dhcp-relay-service).


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>Lägga till en virtuell dator i NSX-T-nätverksanslutningen

I din Azure VMware-lösning vCenter distribuerar du en virtuell dator och använder den för att kontrol lera anslutningen från Azure VMware-lösningens nätverk till:

- Internet
-  Azure Virtual Networks
- Lokalt.  

Distribuera den virtuella datorn på samma sätt som i valfri vSphere-miljö.  Koppla den virtuella datorn till något av de nätverks segment som du skapade tidigare i NSX-T.  

>[!NOTE]
>Om du konfigurerar en DHCP-Server får du din nätverks konfiguration för den virtuella datorn från den (Glöm inte att konfigurera omfånget).  Om du ska konfigurera statiskt konfigurerar du som vanligt.

## <a name="test-the-nsx-t-segment-connectivity"></a>Testa NSX-T-segmentets anslutning

Logga in på den virtuella datorn som skapades i föregående steg och kontrol lera anslutningen.

1. Pinga en IP-adress på Internet.
2. I en webbläsare går du till en Internet-webbplats.
3. Pinga hopp rutan som finns på Azure-Virtual Network.

Azure VMware-lösningen är nu igång och du har upprättat anslutningen till och från Azure Virtual Network och Internet.

## <a name="next-steps"></a>Nästa steg

I nästa avsnitt ska du ansluta Azure VMware-lösningen till ditt lokala nätverk via ExpressRoute.
> [!div class="nextstepaction"]
> [Anslut Azure VMware-lösningen till din lokala miljö](azure-vmware-solution-on-premises.md)
