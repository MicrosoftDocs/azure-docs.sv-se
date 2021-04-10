---
title: Planera distributionen av Azure VMware-lösningen
description: Den här artikeln beskriver ett arbets flöde för distribution av Azure VMware-lösningar.  Det slutliga resultatet är en miljö som är redo för generering och migrering av virtuella datorer (VM).
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 2ded5d706ab71b3880633cd324fb366d0a1bccbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584643"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planera distributionen av Azure VMware-lösningen

Den här artikeln innehåller planerings processen för att identifiera och samla in den information som du ska använda under distributionen. När du planerar distributionen ska du se till att dokumentera den information som du samlar in för enkel referens under distributionen.

De steg som beskrivs i den här snabb starten ger dig en produktions klar miljö för att skapa virtuella datorer (VM) och migrering. 

>[!IMPORTANT]
>Innan du skapar din Azure VMware-lösning kan du följa artikeln [så här aktiverar du Azure VMware-lösningen](enable-azure-vmware-solution.md) för att skicka in ett support ärende för att låta dina värdar tilldelas. När support teamet får din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera dina värdar. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler värdar ska tilldelas, går du igenom samma process. 

## <a name="subscription"></a>Prenumeration

Identifiera den prenumeration som du planerar att använda för att distribuera Azure VMware-lösningen.  Du kan antingen skapa en ny prenumeration eller återanvända en befintlig.

>[!NOTE]
>Prenumerationen måste vara kopplad till en Microsoft Enterprise-avtal eller en Azure-leverantör av moln lösnings leverantörer. Mer information finns i [så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Resursgrupp

Identifiera den resurs grupp som du vill använda för din Azure VMware-lösning.  I allmänhet skapas en resurs grupp specifikt för Azure VMware-lösningen, men du kan använda en befintlig resurs grupp.

## <a name="region"></a>Region

Identifiera den region där du vill distribuera Azure VMware-lösningen.  Mer information finns i Azure- [produkter som är tillgängliga enligt region guide](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Resursnamn

Definiera resurs namnet som du ska använda under distributionen.  Resurs namnet är ett eget och beskrivande namn som du kan använda för att ge ditt privata moln i Azure VMware-lösningen.

>[!IMPORTANT]
>Namnet får inte överskrida 40 tecken. Om namnet överskrider den här gränsen kan du inte skapa offentliga IP-adresser för användning med det privata molnet. 

## <a name="size-hosts"></a>Storleks värdar

Identifiera de storleks värdar som du vill använda när du distribuerar Azure VMware-lösningen.  En fullständig lista finns i dokumentationen för [Azure VMware-lösningen privata moln och kluster](concepts-private-clouds-clusters.md#hosts) .

## <a name="number-of-clusters-and-hosts"></a>Antal kluster och värdar

Den första Azure VMware-lösningen för VMware-lösningen kommer att bestå av ett privat moln som innehåller ett enda kluster. För distributionen måste du definiera antalet värdar som du vill distribuera till det första klustret.

>[!NOTE]
>Det minsta antalet värdar per kluster är tre och det maximala värdet är 16. Det maximala antalet kluster per privat moln är fyra. 

Mer information finns i dokumentationen för [Azure VMware-lösningen privat moln och kluster](concepts-private-clouds-clusters.md#clusters) .

>[!TIP]
>Du kan alltid utöka klustret och lägga till ytterligare kluster senare om du behöver gå utöver det ursprungliga distributions numret.

## <a name="ip-address-segment-for-private-cloud-management"></a>IP-adress segment för hantering av privata moln

Det första steget när du planerar distributionen är att planera IP-segmentering. Azure VMware-lösningen kräver ett/22 CIDR-nätverk. Det här adress utrymmet är hämtas upp i mindre nätverks segment (undernät) och används för Azure VMware lösnings hanterings segment, inklusive vCenter-, VMware-HCX-, NSX-T-och vMotion-funktioner. Visualiseringen nedan visar var det här segmentet ska användas.

Detta/22 CIDR-adressblock i nätverket får inte överlappa några befintliga nätverks segment som du redan har lokalt eller i Azure.

**Exempel:** 10.0.0.0/22

En detaljerad beskrivning av hur/22 CIDR-nätverket är uppdelat per privat moln för [nätverks planerings check lista](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adress segment för virtuella dator arbets belastningar

Precis som med alla VMware-miljöer måste de virtuella datorerna ansluta till ett nätverks segment. I Azure VMware-lösningen finns det två typer av segment, utökade L2-segment (beskrivs senare) och NSX-T-nätverksanslutningar. När produktions distributionen av Azure VMware-lösningen expanderas, finns ofta en kombination av utökade L2-segment från lokala och lokala NSX-T-datasegment. För att planera den första distributionen, i Azure VMware-lösning, identifierar du ett enskilt nätverks segment (IP-nätverk). Detta nätverk får inte överlappa några nätverks segment lokalt eller inom resten av Azure och får inte ligga inom det/22-nätverks segment som definierats tidigare.

Det här nätverks segmentet används främst för test ändamål under den första distributionen.

>[!NOTE]
>Det här nätverket eller nätverken behövs inte under distributionen. De skapas som ett steg efter distributionen.
  
**Exempel:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifiera IP-adress segment för virtuella dator arbets belastningar" border="false":::     

## <a name="optional-extend-your-networks"></a>Valfritt Utöka dina nätverk

Du kan utöka nätverks segmenten från lokal till Azure VMware-lösning. om du gör det kan du identifiera dessa nätverk nu.  

Tänk på följande:

- Om du planerar att utöka nätverk lokalt måste dessa nätverk ansluta till en [vSphere-distribuerad växel (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) i din lokala VMware-miljö.  
- Om de nätverk som du vill utöka Live på en [vSphere standard växel](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), kan de inte utökas.

>[!NOTE]
>Dessa nätverk utökas som ett sista steg i konfigurationen, inte under distributionen.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Ansluta Azure Virtual Network till Azure VMware-lösning

För att tillhandahålla anslutning till Azure VMware-lösningen skapas en ExpressRoute från Azure VMware-lösningen privat moln till en ExpressRoute virtuell nätverksgateway.

Du kan använda en *befintlig* eller *ny* ExpressRoute virtuell nätverksgateway.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identitet – Azure Virtual Network för att ansluta Azure VMware-lösning" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Använd en befintlig ExpressRoute virtuell nätverksgateway

Om du planerar att använda en *befintlig* ExpressRoute virtuell nätverksgateway upprättas Azure VMware-lösningen ExpressRoute-kretsen som ett steg efter distribution. I det här fallet lämnar du fältet **Virtual Network** tomt.

Som en allmän rekommendation är det tillåtet att använda en befintlig ExpressRoute-Gateway för virtuella nätverk. I planerings syfte bör du anteckna vilken ExpressRoute virtuell nätverksgateway du ska använda och sedan fortsätta till nästa steg.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Skapa en ny ExpressRoute virtuell nätverksgateway

När du skapar en *ny* ExpressRoute virtuell nätverksgateway kan du använda en befintlig Azure-Virtual Network eller skapa en ny.  

- För ett befintligt virtuellt Azure-nätverk:
   1. Identifiera ett virtuellt Azure-nätverk där det inte finns några befintliga ExpressRoute-gatewayer för virtuella nätverk.
   2. Innan du distribuerar skapar du en [GatewaySubnet](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet) i Azure-Virtual Network.

- För en ny Azure-Virtual Network kan du skapa den i förväg eller under distributionen. Välj länken **Skapa ny** i listan **Virtual Network** .

På bilden nedan visas distributions skärmen **skapa ett privat moln** med fältet **Virtual Network** markerat.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Skärm bild av distributions skärmen för Azure VMware-lösningen med Virtual Network fältet markerat.":::

>[!NOTE]
>Alla virtuella nätverk som ska användas eller skapas kan ses av din lokala miljö och Azure VMware-lösning, så se till att det IP-segment som du använder i det här virtuella nätverket och undernät inte överlappar varandra.

## <a name="vmware-hcx-network-segments"></a>Nätverks segment för VMware HCX

VMware HCX är en teknik som ingår i Azure VMware-lösningen. De främsta användnings fallen för VMware HCX är migrering av arbets belastningar och haveri beredskap. Om du planerar att göra det, är det bäst att planera nätverk nu.   Annars kan du hoppa över och fortsätta till nästa steg.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har samlat in och dokumenterat den information som krävs fortsätter du till nästa avsnitt för att skapa ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera Azure VMware Solution](deploy-azure-vmware-solution.md)
