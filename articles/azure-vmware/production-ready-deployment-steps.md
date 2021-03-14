---
title: Planera distributionen av Azure VMware-lösningen
description: Den här artikeln beskriver ett arbets flöde för distribution av Azure VMware-lösningar.  Det slutliga resultatet är en miljö som är redo för generering och migrering av virtuella datorer (VM).
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462430"
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

I Azure VMware-lösningen distribuerar du ett privat moln och skapar flera kluster. För distributionen måste du definiera antalet kluster och de f-värdar som du vill distribuera i varje kluster. Det minsta antalet värdar per kluster är tre och det maximala värdet är 16. Det maximala antalet kluster per privat moln är fyra. Det maximala antalet noder per privat moln är 64.

Mer information finns i dokumentationen för [Azure VMware-lösningen privat moln och kluster](concepts-private-clouds-clusters.md#clusters) .

>[!TIP]
>Du kan alltid utöka klustret senare om du behöver gå bortom det ursprungliga distributions numret.

## <a name="vcenter-admin-password"></a>administratörs lösen ord för vCenter
Definiera vCenter-administratörens lösen ord. Under distributionen skapar du ett vCenter admin-lösenord. Lösen ordet tilldelas cloudadmin@vsphere.local Administratörs kontot under vCenter-versionen. Du använder dessa autentiseringsuppgifter för att logga in på vCenter.

## <a name="nsx-t-admin-password"></a>NSX-T admin-lösenord
Definiera NSX-T admin-lösenordet. Under distributionen skapar du ett NSX-T-administratörs lösen ord. Lösen ordet tilldelas administratörs användaren i NSX-kontot under NSX-versionen. Du använder dessa autentiseringsuppgifter för att logga in på NSX-T-hanteraren.

## <a name="ip-address-segment-for-private-cloud-management"></a>IP-adress segment för hantering av privata moln

Det första steget när du planerar distributionen är att planera IP-segmentering. Azure VMware-lösningen kräver ett/22 CIDR-nätverk. Det här adress utrymmet carves det i mindre nätverks segment (undernät) och används för funktionerna vCenter, VMware HCX, NSX-T och vMotion.

Detta/22 CIDR-adressblock i nätverket får inte överlappa något befintligt nätverks segment som du redan har lokalt eller i Azure.

**Exempel:** 10.0.0.0/22

Azure VMware-lösningen ansluter till din Microsoft Azure Virtual Network via en intern ExpressRoute Global Reach-krets (D-MSEE: N i nedanstående visualisering). Den här funktionen är en del av Azure VMware Solution service och debiteras inte.

Mer information finns i [Check lista för nätverks planering](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identifiera IP-adress segment" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>IP-adress segment för virtuella dator arbets belastningar

Identifiera ett IP-segment för att skapa ditt första nätverk för arbets belastningar (NSX-segment) i ditt privata moln. Med andra ord måste du skapa ett nätverks segment i Azure VMware-lösningen så att du kan distribuera virtuella datorer i Azure VMware-lösningen.

Även om du planerar att utöka nätverk lokalt till Azure VMware-lösningen (L2) måste du ändå skapa ett nätverks segment som validerar miljön.

Kom ihåg att alla IP-segment som skapats måste vara unika i Azure och lokalt.
  
**Exempel:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identifiera IP-adress segment för virtuella dator arbets belastningar" border="false":::     

## <a name="optional-extend-networks"></a>Valfritt Utöka nätverk

Du kan utöka nätverks segmenten från lokal till Azure VMware-lösning. om du gör det kan du identifiera dessa nätverk nu.  

Tänk på följande:

- Om du planerar att utöka nätverk lokalt måste dessa nätverk ansluta till en [vSphere-distribuerad växel (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) i din lokala VMware-miljö.  
- Om de nätverk som du vill utöka Live på en [vSphere standard växel](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), kan de inte utökas.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Ansluta Azure Virtual Network till Azure VMware-lösning

I det här steget ska du identifiera en ExpressRoute virtuell nätverksgateway och de stödda Azure-Virtual Network som används för att ansluta till ExpressRoute-kretsen för Azure VMware-lösningen.  ExpressRoute-kretsen fören klar anslutningen till och från Azure VMware-lösningens privata moln till andra Azure-tjänster, Azure-resurser och lokala miljöer.

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
