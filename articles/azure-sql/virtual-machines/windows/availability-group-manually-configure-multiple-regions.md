---
title: Konfigurera en SQL Server Always on-tillgänglighets grupp i olika regioner
description: Den här artikeln beskriver hur du konfigurerar en SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer med en replik i en annan region.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 7ef3535158c99226da135ad3726266023ac0690f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509412"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>Konfigurera en SQL Server Always on-tillgänglighets grupp i olika Azure-regioner

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du konfigurerar en SQL Server Always on-tillgänglighetsgrupper på virtuella Azure-datorer på en fjärran sluten Azure-plats. Använd den här konfigurationen för att stödja haveri beredskap.

Den här artikeln gäller för Azure Virtual Machines i Resource Manager-läge.

Följande bild visar en gemensam distribution av en tillgänglighets grupp på virtuella Azure-datorer:

   ![Diagram som visar Azure Load Balancer och tillgänglighets uppsättningen med ett "Windows Server-redundanskluster" och "Always on-tillgänglighetsgrupper".](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

I den här distributionen finns alla virtuella datorer i en Azure-region. Tillgänglighets gruppens repliker kan ha synkront genomförande med automatisk redundans på SQL-1 och SQL-2. Information om hur du skapar den här arkitekturen finns i [mallen för tillgänglighets grupper eller självstudier](availability-group-overview.md).

Den här arkitekturen är sårbar för stillestånds tid om Azure-regionen blir oåtkomlig. Du kan lösa problemet genom att lägga till en replik i en annan Azure-region. Följande diagram visar hur den nya arkitekturen skulle se ut:

   ![Tillgänglighets grupp DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

Föregående diagram visar en ny virtuell dator med namnet SQL-3. SQL-3 finns i en annan Azure-region. SQL-3 har lagts till i Windows Server-redundansklustret. SQL-3 kan vara värd för en tillgänglighets grupps replik. Lägg slutligen märke till att Azure-regionen för SQL-3 har en ny Azure Load Balancer.

>[!NOTE]
> Det krävs en tillgänglighets uppsättning för Azure när det finns mer än en virtuell dator i samma region. Om det bara finns en virtuell dator i regionen krävs inte tillgänglighets uppsättningen. Du kan bara placera en virtuell dator i en tillgänglighets uppsättning när du skapar den. Om den virtuella datorn redan finns i en tillgänglighets uppsättning kan du lägga till en virtuell dator för ytterligare en replik senare.

I den här arkitekturen konfigureras repliken i fjärrregionen normalt med läget för tillgänglighets läge för asynkront genomförande och manuellt växlings läge.

När tillgänglighets grupp repliker finns på virtuella Azure-datorer i olika Azure-regioner kräver varje region:

* En virtuell nätverksgateway
* Anslutning till virtuell nätverksgateway

Följande diagram visar hur nätverken kommunicerar mellan data Center.

   ![Diagram som visar de två virtuella nätverken i olika Azure-regioner som kommunicerar med hjälp av V P N-gatewayer.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Den här arkitekturen medför utgående data kostnader för data som replikeras mellan Azure-regioner. Se [prissättning för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Skapa fjärran sluten replik

Gör så här om du vill skapa en replik i ett fjärranslutet Data Center:

1. [Skapa ett virtuellt nätverk i den nya regionen](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Konfigurera en VNet-till-VNET-anslutning med hjälp av Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >I vissa fall kan du behöva använda PowerShell för att skapa VNet-till-VNet-anslutningen. Om du till exempel använder olika Azure-konton kan du inte konfigurera anslutningen i portalen. I det här fallet kan du se [Konfigurera en VNet-till-VNET-anslutning med hjälp av Azure Portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Skapa en domänkontrollant i den nya regionen](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100).

   Den här domänkontrollanten tillhandahåller autentisering om domänkontrollanten på den primära platsen inte är tillgänglig.

1. [Skapa en SQL Server virtuell dator i den nya regionen](create-sql-vm-portal.md).

1. [Skapa en Azure Load Balancer i nätverket på den nya regionen](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   Den här belastningsutjämnaren måste:

   - Vara i samma nätverk och undernät som den nya virtuella datorn.
   - Ha en statisk IP-adress för tillgänglighets gruppens lyssnare.
   - Inkludera en backend-pool som bara består av de virtuella datorerna i samma region som belastningsutjämnaren.
   - Använd en TCP-port avsökning som är speciell för IP-adressen.
   - Ha en regel för belastnings utjämning som är unik för SQL Server i samma region.  
   - Vara en Standard Load Balancer om de virtuella datorerna i backend-poolen inte är en del av antingen en enskild tillgänglighets uppsättning eller en skalnings uppsättning för virtuella datorer. För ytterligare informations granskning [Azure Load Balancer standard översikt](../../../load-balancer/load-balancer-overview.md).
   - Vara en Standard Load Balancer om de två virtuella nätverken i två olika regioner är peer-kopplade över global VNet-peering. Mer information finns i [vanliga frågor och svar om Azure Virtual Network](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

1. [Lägg till funktionen kluster för växling vid fel i den nya SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Anslut den nya SQL Server till domänen](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Ange det nya SQL Server tjänst kontot för att använda ett domän konto](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Lägg till den nya SQL Server i Windows Server-redundansklustret](availability-group-manually-configure-tutorial.md#addNode).

1. Lägg till en IP-adressresurs i klustret.

   Du kan skapa IP-adressresursen i Klusterhanteraren för växling vid fel. Välj namnet på klustret, högerklicka sedan på kluster namnet under **kluster kärn resurser** och välj **Egenskaper**: 

   ![Skärm bild som visar "Klusterhanteraren för växling vid fel" med ett kluster namn, "Server namn" och "Properties" markerat.](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   I dialog rutan **Egenskaper** väljer du **Lägg till** under **IP-adress** och lägger sedan till IP-adressen för kluster namnet från den fjärranslutna nätverks regionen. Välj **OK** i dialog rutan **IP-adress** och välj sedan **OK** igen i dialog rutan **kluster egenskaper** för att spara den nya IP-adressen. 

   ![Lägg till kluster-IP](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Lägg till IP-adressen som ett beroende för kärn kluster namnet.

   Öppna kluster egenskaperna en gång till och välj fliken **beroenden** . Konfigurera ett eller-beroende för de två IP-adresserna: 

   ![Kluster egenskaper](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Lägg till en IP-adressresurs i tillgänglighets grupps rollen i klustret. 

   Högerklicka på rollen tillgänglighets grupp i Klusterhanteraren för växling vid fel, Välj **Lägg till resurs**, **fler resurser** och välj **IP-adress**.

   ![Skapa IP-adress](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Konfigurera IP-adressen enligt följande:

   - Använd nätverket från fjärrplatsens Data Center.
   - Tilldela IP-adressen från den nya Azure Load Balancer. 

1. Lägg till IP-adressresursen som ett beroende för klient åtkomst punktens kluster (nätverks namn).

   Följande skärm bild visar en korrekt konfigurerad IP-adress kluster resurs:

   ![Tillgänglighetsgrupp](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Kluster resurs gruppen innehåller båda IP-adresserna. Båda IP-adresserna är beroende av åtkomst punkten för lyssnar klienten. Använd operatorn **or** i kluster beroende konfigurationen.

1. [Ange kluster parametrarna i PowerShell](availability-group-manually-configure-tutorial.md#setparam).

   Kör PowerShell-skriptet med klustrets nätverks namn, IP-adress och avsöknings port som du konfigurerade i belastningsutjämnaren i den nya regionen.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. [Aktivera Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)på den nya SQL Server i Konfigurationshanteraren för SQL Server.

1. [Öppna brand Väggs portar på den nya SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   De port nummer som du måste öppna beror på din miljö. Öppna portar för speglings slut punkten och hälso avsökningen för Azure Load Balancer.


1. [Lägg till en replik i tillgänglighets gruppen på den nya SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   För en replik i en fjärran sluten Azure-region ställer du in den för asynkron replikering med manuell redundans.  

## <a name="set-connection-for-multiple-subnets"></a>Ange anslutning för flera undernät

Repliken i det fjärranslutna data centret är en del av tillgänglighets gruppen men finns i ett annat undernät. Om den här repliken blir den primära repliken kan det uppstå tids gränser för program anslutningen. Detta är samma sak som en lokal tillgänglighets grupp i en distribution med flera undernät. Om du vill tillåta anslutningar från klient program uppdaterar du antingen klient anslutningen eller konfigurerar cachelagring av namn matchning i kluster nätverks namn resursen.

Du kan helst uppdatera de klient anslutnings strängar som ska anges `MultiSubnetFailover=Yes` . Se [ansluta till MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0).

Om du inte kan ändra anslutnings strängarna kan du konfigurera cachelagring av namn matchning. Se [timeout-fel och du kan inte ansluta till en SQL Server 2012 AlwaysOn tillgänglighets grupps lyssnare i en miljö med flera undernät](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Redundansväxla till fjärrregion

Om du vill testa lyssnare anslutningen till fjärrregionen kan du växla över repliken till den fjärranslutna regionen. När repliken är asynkron är redundansväxlingen sårbar för potentiell data förlust. Om du vill redundansväxla utan data förlust ändrar du tillgänglighets läget till synkront och anger läget för redundans till automatisk. Gör så här:

1. I **Object Explorer** ansluter du till den instans av SQL Server som är värd för den primära repliken.
1. Under **AlwaysOn-tillgänglighetsgrupper**, **tillgänglighets grupper**, högerklickar du på din tillgänglighets grupp och väljer **Egenskaper**.
1. På sidan **Allmänt** , under **tillgänglighets repliker**, anger du den sekundära repliken på Dr-platsen så att den använder **synkront genomförande** läge för tillgänglighet och **automatiskt** växlings läge.
1. Om du har en sekundär replik på samma plats som din primära replik för hög tillgänglighet anger du den här repliken till **asynkron incheckning** och **manuell**.
1. Välj OK.
1. I **Object Explorer** högerklickar du på tillgänglighets gruppen och väljer **Visa instrument panel**.
1. På instrument panelen kontrollerar du att repliken på DR-platsen är synkroniserad.
1. I **Object Explorer** högerklickar du på tillgänglighets gruppen och väljer **redundans...**. SQL Server Management Studios öppnar en guide för att redundansväxla SQL Server.  
1. Välj **Nästa** och välj SQL Server-instansen på Dr-platsen. Välj **Nästa** igen.
1. Anslut till SQL Server-instansen på DR-platsen och välj **Nästa**.
1. På sidan **Sammanfattning** kontrollerar du inställningarna och väljer **Slutför**.

När du har testat anslutningen flyttar du tillbaka den primära repliken till ditt primära Data Center och återställer tillgänglighets läget till sina normala drift inställningar. I följande tabell visas de normala drift inställningarna för den arkitektur som beskrivs i det här dokumentet:

| Location | Server instans | Roll | Tillgänglighets läge | Växlings läge
| ----- | ----- | ----- | ----- | -----
| Primärt Data Center | SQL-1 | Primär | Synkront | Automatiskt
| Primärt Data Center | SQL-2 | Sekundär | Synkront | Automatiskt
| Sekundärt eller fjärranslutna Data Center | SQL-3 | Sekundär | Asynkrona | Manuell


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Mer information om planerade och forced manual failover

Mer information finns i följande avsnitt:

- [Utföra en planerad manuell redundansväxling av en tillgänglighets grupp (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Utföra en framtvingad manuell redundansväxling av en tillgänglighets grupp (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Nästa steg

* [AlwaysOn-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Azure Virtual Machines](../../../virtual-machines/index.yml)
* [Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Tillgänglighets uppsättningar i Azure](../../../virtual-machines/availability.md)