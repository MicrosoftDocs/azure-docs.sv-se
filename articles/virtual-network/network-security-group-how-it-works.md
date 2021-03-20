---
title: Nätverks säkerhets grupp – hur det fungerar
titlesuffix: Azure Virtual Network
description: Lär dig hur nätverks säkerhets grupper hjälper dig att filtrera nätverks trafik mellan Azure-resurser.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537906"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Hur nätverks säkerhets grupper filtrerar nätverks trafik
<a name="network-security-groups"></a>

Du kan använda en Azure-nätverks säkerhets grupp för att filtrera nätverks trafik till och från Azure-resurser i ett virtuellt Azure-nätverk. En nätverkssäkerhetsgrupp innehåller [säkerhetsregler](./network-security-groups-overview.md#security-rules) som tillåter eller nekar inkommande nätverkstrafik till, eller utgående nätverkstrafik från, flera typer av Azure-resurser. För varje regel kan du ange källa och mål, port och protokoll.

Du kan distribuera resurser från flera Azure-tjänster till ett virtuellt Azure-nätverk. En fullständig lista finns i [Tjänster som kan distribueras till ett virtuellt nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Du kan associera en, eller ingen, nätverkssäkerhetsgrupp med varje [undernät](virtual-network-manage-subnet.md#change-subnet-settings) och [nätverksgränssnitt](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) i ett virtuellt nätverk på en virtuell dator. Du kan associera samma nätverkssäkerhetsgrupp med så många undernät och nätverksgränssnitt du vill.

Scenarierna i följande bild visar hur du kan distribuera nätverkssäkerhetsgrupper för att tillåta nätverkstrafik till och från Internet via TCP-port 80:

![NSG-bearbetning](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Bilden ovan, och texten nedan, beskriver hur Azure bearbetar inkommande och utgående regler för nätverkssäkerhetsgrupper:

## <a name="inbound-traffic"></a>Inkommande trafik

För inkommande trafik bearbetar Azure först reglerna i en nätverkssäkerhetsgrupp som är associerad med ett undernät, om ett sådant finns. Därefter bearbetas reglerna i en nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet, om ett sådant finns.

- **VM1**: Säkerhetsreglerna i *NSG1* bearbetas eftersom nätverkssäkerhetsgruppen är associerad med *Subnet1* och *VM1* finns i *Subnet1*. Såvida du inte har skapat en regel som tillåter inkommande trafik på port 80, nekas trafiken av standardsäkerhetsregeln [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) och utvärderas aldrig av *NSG2* eftersom *NSG2* är associerad med nätverksgränssnittet. Om *NSG1* har en säkerhetsregel som tillåter trafik på port 80, bearbetas trafiken av *NSG2*. För att trafik på port 80 ska tillåtas på den virtuella datorn måste både *NSG1* och *NSG2* ha en regel som tillåter trafik på port 80 från Internet.
- **VM2**: Reglerna i *NSG1* bearbetas eftersom *VM2* också finns i *Subnet1*. Eftersom ingen nätverkssäkerhetsgrupp är associerad med nätverksgränssnittet för *VM2*, tar den emot all trafik som tillåts via *NSG1* eller nekas all trafik som nekas av *NSG1*. Trafik antingen tillåts eller nekas till alla resurser i samma undernät när en nätverkssäkerhetsgrupp är associerad med ett undernät.
- **VM3**: Eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet2*, tillåts trafik till undernätet och bearbetas av *NSG2* eftersom *NSG2* är associerad med nätverksgränssnittet som är kopplat till *VM3*.
- **VM4**: Trafik tillåts till *VM4* eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet3* eller med nätverksgränssnittet på den virtuella datorn. All nätverkstrafik tillåts genom ett undernät eller nätverksgränssnitt om ingen nätverkssäkerhetsgrupp är associerad med undernätet eller nätverksgränssnittet.

## <a name="outbound-traffic"></a>Utgående trafik

För utgående trafik bearbetar Azure först reglerna i en nätverkssäkerhetsgrupp som är associerad med ett nätverksgränssnitt, om ett sådant finns. Därefter bearbetas reglerna i en nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet, om ett sådant finns.

- **VM1**: Säkerhetsreglerna i *NSG2* bearbetas. Såvida du inte skapar en säkerhetsregel som nekar utgående trafik till Internet via port 80, tillåts trafiken av standardsäkerhetsregeln [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) i både *NSG1* och *NSG2*. Om *NSG2* har en säkerhetsregel som nekar port 80, nekas trafiken och utvärderas aldrig av *NSG1*. För att trafik på port 80 ska nekas från den virtuella datorn måste den ena eller båda nätverkssäkerhetsgrupperna ha en regel som nekar trafik på port 80 till Internet.
- **VM2**: All trafik skickas via nätverksgränssnittet till undernätet eftersom nätverksgränssnittet som är kopplat till *VM2* inte har någon associerad nätverkssäkerhetsgrupp. Reglerna i *NSG1* bearbetas.
- **VM3**: Om *NSG2* har en säkerhetsregel som nekar trafik på port 80, nekas trafiken. Om *NSG2* har en säkerhetsregel som tillåter trafik på port 80, tillåts utgående trafik till Internet via port 80 eftersom ingen nätverkssäkerhetsgrupp är associerad med *Subnet2*.
- **VM4**: All nätverkstrafik tillåts från *VM4* eftersom ingen nätverkssäkerhetsgrupp är associerad med nätverksgränssnittet som är kopplat till den virtuella datorn eller till *Subnet3*.


## <a name="intra-subnet-traffic"></a>Intra-Subnet trafik

Det är viktigt att Observera att säkerhets regler i en NSG som är kopplade till ett undernät kan påverka anslutningen mellan den virtuella datorn i det. Om en regel till exempel läggs till i *NSG1* som nekar all inkommande och utgående trafik, kommer *VM1* och *VM2* inte längre att kunna kommunicera med varandra. En annan regel skulle behöva läggas till specifikt för att tillåta detta. 

Du kan enkelt granska vilka regler som tillämpas för ett nätverksgränssnitt genom att visa [gällande säkerhetsregler](virtual-network-network-interface.md#view-effective-security-rules) för ett nätverksgränssnitt. Du kan också använda funktionen [Kontrollera IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure Network Watcher för att ta reda på om kommunikation tillåts till eller från ett nätverksgränssnitt. Med kontrol lera IP-flöde får du information om huruvida en kommunikation tillåts eller nekas och vilken nätverks säkerhets regel som tillåter eller nekar trafiken.

> [!NOTE]
> Nätverks säkerhets grupper är kopplade till undernät eller virtuella datorer och moln tjänster som distribueras i den klassiska distributions modellen och till undernät eller nätverks gränssnitt i distributions modellen för Resource Manager. Läs mer i avsnittet [om Azures distributionsmodeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Om du inte har en speciell anledning till rekommenderar vi att du kopplar en nätverks säkerhets grupp till ett undernät eller ett nätverks gränssnitt, men inte båda. Eftersom regler i en nätverkssäkerhetsgrupp som är associerad med ett undernät kan stå i konflikt med regler i en nätverkssäkerhetsgrupp som är associerad med ett nätverksgränssnitt, kan det uppstå oväntade kommunikationsproblem som kräver felsökning.

## <a name="next-steps"></a>Nästa steg

* Information om vilka Azure-resurser du kan distribuera till ett virtuellt nätverk och associera med nätverkssäkerhetsgrupper finns i [Virtual network integration for Azure services](virtual-network-for-azure-services.md) (Integrering av virtuella nätverk för Azure-tjänster).
* Om du aldrig har skapat en nätverkssäkerhetsgrupp kan du gå en snabb [självstudie](tutorial-filter-network-traffic.md) för att få lite erfarenhet.
* Om du redan är bekant med nätverkssäkerhetsgrupper och vill lära dig hur du hanterar dem läser du [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md). 
* Om du har kommunikationsproblem och behöver felsöka nätverkssäkerhetsgrupper läser du [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnostisera problem med filtreringen av nätverkstrafik för virtuella nätverk). 
* Lär dig hur du aktiverar [flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att analysera nätverks trafik till och från resurser som har en associerad nätverks säkerhets grupp.