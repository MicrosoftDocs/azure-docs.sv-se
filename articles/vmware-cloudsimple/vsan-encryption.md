---
title: Azure VMware-lösning av CloudSimple – konfigurera virtuellt San-kryptering för privat moln
description: Beskriver hur du konfigurerar virtuellt San Software Encryption-funktionen så att ditt CloudSimple privata moln kan arbeta med en nyckel hanterings server som körs i ditt virtuella Azure-nätverk.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5da05c7f3c6878b0804799360e512676b9002d3
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899056"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>Konfigurera virtuellt San-kryptering för CloudSimple privat moln

Du kan konfigurera funktionen för program kryptering i virtuellt San så att ditt CloudSimple privata moln kan arbeta med en nyckel hanterings server som körs i ditt virtuella Azure-nätverk.

VMware kräver att du använder en extern KMIP 1,1-kompatibel server för nyckel hanterings Server (KMS) från tredje part när du använder virtuellt San-kryptering. Du kan utnyttja alla KMS-certifikat som stöds och som certifierats av VMware och är tillgänglig för Azure.

Den här guiden beskriver hur du använder HyTrust-KMS-KMS som körs i ett virtuellt Azure-nätverk. En liknande metod kan användas för alla andra certifierade KMS-lösningar från tredje part för virtuellt San.

Denna KMS-lösning kräver att du:

* Installera, konfigurera och hantera ett VMware-certifierat KMS-verktyg från tredje part i ditt virtuella Azure-nätverk.
* Ange dina egna licenser för KMS-verktyget.
* Konfigurera och hantera virtuellt San-kryptering i ditt privata moln med hjälp av KMS-verktyget från tredje part som körs i det virtuella Azure-nätverket.

## <a name="kms-deployment-scenario"></a>Scenario för KMS-distribution

KMS-serverkluster körs i det virtuella Azure-nätverket och det går att komma åt IP från det privata molnet vCenter över den konfigurerade Azure ExpressRoute-anslutningen.

![.. /media/KMS-kluster i Azure Virtual Network](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>Så här distribuerar du lösningen

Distributions processen innehåller följande steg:

1. [Kontrol lera att kraven är uppfyllda](#verify-prerequisites-are-met)
2. [CloudSimple-portalen: Hämta peering-information för ExpressRoute](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure Portal: Anslut ditt virtuella nätverk till det privata molnet](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure Portal: Distribuera ett HyTrust-kluster för i ditt virtuella nätverk](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI: Konfigurera KMIP-Server](#hytrust-webui-configure-the-kmip-server)
6. [vCenter-gränssnitt: Konfigurera virtuellt San-kryptering för att använda KMS-kluster i ditt virtuella Azure-nätverk](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>Kontrol lera att förutsättningarna är uppfyllda

Kontrol lera följande innan du distribuerar:

* Den valda KMS-leverantören, verktyget och versionen finns i listan virtuellt San Compatibility.
* Den valda leverantören har stöd för en version av verktyget som kan köras i Azure.
* Azure-versionen av KMS-verktyget är KMIP 1,1-kompatibel.
* En Azure Resource Manager och ett virtuellt nätverk har redan skapats.
* Ett privat CloudSimple-moln har redan skapats.

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple-portalen: Hämta peering-information för ExpressRoute

Om du vill fortsätta med installationen behöver du verifierings nyckeln och peer-kretsens URI för ExpressRoute plus åtkomst till din Azure-prenumeration. Den här informationen finns på sidan Virtual Network anslutning på CloudSimple-portalen. Instruktioner finns i [Konfigurera en virtuell nätverks anslutning till det privata molnet](virtual-network-connection.md). Om du har problem med att hämta informationen öppnar du en [support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure Portal: Anslut ditt virtuella nätverk till ditt privata moln

1. Skapa en virtuell nätverksgateway för ditt virtuella nätverk genom att följa anvisningarna i [Konfigurera en virtuell nätverksgateway för ExpressRoute med hjälp av Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
2. Länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen genom att följa anvisningarna i [ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av portalen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
3. Använd informationen om CloudSimple ExpressRoute-kretsen som togs emot i välkomst meddelandet från CloudSimple för att länka ditt virtuella nätverk till CloudSimple ExpressRoute-kretsen i Azure.
4. Ange verifierings nyckeln och peer-kretsens URI, ge anslutningen ett namn och klicka på **OK**.

![Ange CS peer-krets-URI när det virtuella nätverket skapas](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure Portal: Distribuera ett HyTrust-kluster för i Azure Resource Manager i det virtuella nätverket

Om du vill distribuera ett HyTrust-i Azure Resource Manager det virtuella nätverket utför du följande uppgifter. Mer information finns i [HyTrust-dokumentationen](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0) .

1. Skapa en Azure-nätverks säkerhets grupp (NSG-HyTrust) med angivna regler för inkommande trafik genom att följa anvisningarna i HyTrust-dokumentationen.
2. Generera ett SSH-nyckelpar i Azure.
3. Distribuera den inledande kontrollen av en-nod från avbildningen i Azure Marketplace.  Använd den offentliga nyckeln för det nyckel par som genererades och välj **NSG-HyTrust** som nätverks säkerhets grupp för noden nyckel kontroll.
4. Konvertera den privata IP-adressen för en kontroll till en statisk IP-adress.
5. SSH till den virtuella nyckel styrnings datorn med dess offentliga IP-adress och den privata nyckeln för det tidigare nämnda nyckel paret.
6. När du uppmanas till SSH-gränssnittet väljer `No` du att ange noden som den första noden för att kontrol lera.
7. Lägg till ytterligare Control-noder genom att upprepa steg 3-5 i den här proceduren och välja `Yes` när du tillfrågas om du vill lägga till i ett befintligt kluster.

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI: Konfigurera KMIP-servern

Gå till https://*Public-IP*, där *offentlig* IP-adress är den offentliga IP-adressen för den virtuella. Följ de här stegen i [HyTrust-dokumentationen](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0).

1. [Konfigurera en KMIP-Server](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [Skapa ett certifikat paket för VMware-kryptering](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter-gränssnitt: Konfigurera virtuellt San-kryptering för att använda KMS-kluster i ditt virtuella Azure-nätverk

Följ HyTrust-instruktionerna för att [skapa ett KMS-kluster i vCenter](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4).

![Lägg till information om KMS-kluster i vCenter](media/vsan-config01.png)

I vCenter går du till **kluster > konfigurera** och väljer **Allmänt** alternativ för virtuellt San. Aktivera kryptering och välj det KMS-kluster som tidigare har lagts till i vCenter.

![Aktivera virtuellt San-kryptering och konfigurera KMS-kluster i vCenter](media/vsan-config02.png)

## <a name="references"></a>Referenser

### <a name="azure"></a>Azure

[Konfigurera en virtuell nätverksgateway för ExpressRoute med hjälp av Azure Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[Länka ett virtuellt nätverk till en ExpressRoute-krets med Azure-portalen](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl och Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[Konfigurera en KMPI-Server](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[Skapa ett certifikat paket för VMware-kryptering](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[Skapa KMS-klustret i vSphere](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
