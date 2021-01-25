---
title: Planera distributionen av Azure VMware-lösningen
description: Den här artikeln beskriver ett arbets flöde för distribution av Azure VMware-lösningar.  Det slutliga resultatet är en miljö som är redo för generering och migrering av virtuella datorer (VM).
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 8b1d69f3f953b43177a3b1d0611b51ca2cfb1a75
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762862"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planera distributionen av Azure VMware-lösningen

Den här artikeln innehåller planerings processen för att identifiera och samla in data som används under distributionen. När du planerar distributionen ska du se till att dokumentera den information som du samlar in för enkel referens under distributionen.

Processerna för den här snabb starten leder till en produktions klar miljö för att skapa virtuella datorer och migrering. 

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

## <a name="number-of-hosts"></a>Antal värdar

Definiera antalet värdar som du vill distribuera till Azures privata moln för VMware-lösningen.  Det minsta antalet värdar är tre och det maximala värdet är 16 per kluster.  Mer information finns i dokumentationen för [Azure VMware-lösningen privat moln och kluster](concepts-private-clouds-clusters.md#clusters) .

Du kan alltid utöka klustret senare om du behöver gå bortom det ursprungliga distributions numret.

## <a name="vcenter-admin-password"></a>administratörs lösen ord för vCenter
Definiera vCenter-administratörens lösen ord.  Under distributionen skapar du ett vCenter admin-lösenord. Lösen ordet är till cloudadmin@vsphere.local Administratörs kontot under vCenter-versionen. Du använder den för att logga in på vCenter.

## <a name="nsx-t-admin-password"></a>NSX-T admin-lösenord
Definiera NSX-T admin-lösenordet.  Under distributionen skapar du ett NSX-T-administratörs lösen ord. Lösen ordet tilldelas administratörs användaren i NSX-kontot under NSX-versionen. Du använder den för att logga in i NSX Manager.

## <a name="ip-address-segment"></a>IP-adress segment

Det första steget när du planerar distributionen är att planera IP-segmentering.  Azure VMware-lösningen matar in ett/22-nätverk som du anger. Carves sedan upp i mindre segment och använder sedan de här IP-segmenten för vCenter, VMware HCX, NSX-T och vMotion.

Azure VMware-lösningen ansluter till din Microsoft Azure Virtual Network via en intern ExpressRoute-krets. I de flesta fall ansluter den till ditt data Center via ExpressRoute Global Reach. 

Azure VMware-lösning, din befintliga Azure-miljö och din lokala miljö alla Exchange-vägar (vanligt vis). Det vill säga att det CIDR-adressblock för CIDR-nätverket som du definierar i det här steget inte överlappar något som du redan har lokalt eller Azure.

**Exempel:** 10.0.0.0/22

Mer information finns i [Check lista för nätverks planering](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adress segment för virtuella dator arbets belastningar

Identifiera ett IP-segment för att skapa ditt första nätverk (NSX-segment) i ditt privata moln.  Med andra ord vill du skapa ett nätverks segment i Azure VMware-lösningen så att du kan distribuera virtuella datorer till Azure VMware-lösningen.   

Även om du bara planerar att utöka L2-nätverk skapar du ett nätverks segment som validerar miljön.

Kom ihåg att alla IP-segment som skapats måste vara unika i Azure och lokalt.  

**Exempel:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifiera IP-adress segment för virtuella dator arbets belastningar" border="false":::     

## <a name="optional-extend-networks"></a>Valfritt Utöka nätverk

Du kan utöka nätverks segmenten från lokal till Azure VMware-lösning. om du gör det kan du identifiera dessa nätverk nu.  

Tänk på följande:

- Om du planerar att utöka nätverk lokalt måste dessa nätverk ansluta till en [vSphere-distribuerad växel (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) i din lokala VMware-miljö.  
- Om de nätverk som du vill utöka Live på en [vSphere standard växel](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), kan de inte utökas.

## <a name="attach-virtual-network-to-azure-vmware-solution"></a>Koppla virtuella nätverk till Azure VMware-lösning

I det här steget ska du identifiera en ExpressRoute virtuell nätverksgateway och stöd för Azure-Virtual Network som används för att ansluta till ExpressRoute-kretsen för Azure VMware-lösningen.  ExpressRoute-kretsen fören klar anslutningen till och från Azure VMware-lösningens privata moln till andra Azure-tjänster, Azure-resurser och lokala miljöer.

Du kan använda en *befintlig* eller *ny* ExpressRoute virtuell nätverksgateway.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identitet – Azure Virtual Network för att ansluta Azure VMware-lösning" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Använd en befintlig ExpressRoute virtuell nätverksgateway

Om du använder en *befintlig* ExpressRoute virtuell nätverksgateway upprättas Azure VMware-lösningen ExpressRoute-kretsen efter att du har distribuerat det privata molnet. I det här fallet lämnar du fältet **Virtual Network** tomt.  

Anteckna vilken ExpressRoute virtuell nätverksgateway du ska använda och fortsätt till nästa steg.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Skapa en ny ExpressRoute virtuell nätverksgateway

När du skapar en *ny* ExpressRoute virtuell nätverksgateway kan du använda en befintlig Azure-Virtual Network eller skapa en ny.  

- För ett befintligt virtuellt Azure-nätverk:
   1. Kontrol lera att det inte finns några befintliga ExpressRoute virtuella nätverksgateway i det virtuella nätverket. 
   1. Välj den befintliga Azure-Virtual Network i listan **Virtual Network** .

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
