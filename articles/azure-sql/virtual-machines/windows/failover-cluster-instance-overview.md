---
title: Instanser av kluster för växling vid fel
description: Lär dig mer om kluster instanser för växling vid fel (skyddas) med SQL Server på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 33be57832d9364b859042cd38349c2437bcfcb18
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358154"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Instanser av kluster för växling vid fel med SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver funktions skillnader när du arbetar med FCI (failover Cluster instances) för SQL Server på Azure Virtual Machines (VM). 

## <a name="overview"></a>Översikt

SQL Server på virtuella Azure-datorer använder WSFC-funktioner (Windows Server Failover Clustering) för att tillhandahålla lokal hög tillgänglighet genom redundans på Server instans nivå: en instans av en redundanskluster. En FCI är en instans av SQL Server som installeras i WSFC-noder (eller helt enkelt klustret) och eventuellt över flera undernät. I nätverket verkar en FCI vara en instans av SQL Server som körs på en enda dator. Men FCI tillhandahåller redundans från en WSFC-nod till en annan om den aktuella noden blir otillgänglig.

Resten av artikeln fokuserar på skillnaderna för kluster instanser för växling vid fel när de används med SQL Server på virtuella Azure-datorer. Mer information om tekniken för redundanskluster finns i: 

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Kvorum

Instanser av redundanskluster med SQL Server på Azure Virtual Machines stöd för att använda ett disk vittne, ett moln vittne eller ett fil resurs vittne för klusterkvorum.

Mer information finns i [metod tips för kvorum med SQL Server virtuella datorer i Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Lagring

I traditionella lokala klustrade miljöer använder ett Windows-redundanskluster en storage area network (SAN) som är tillgänglig för båda noderna som den delade lagringen. SQL Server filer finns i den delade lagringen och bara den aktiva noden kan komma åt filerna samtidigt. 

SQL Server på virtuella Azure-datorer erbjuder olika alternativ som en lösning för delad lagring för en distribution av SQL Server kluster instanser för växling vid fel: 

||[Delade diskar i Azure](../../../virtual-machines/disks-shared.md)|[Premium fil resurser](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Lägsta version av operativsystemet**| Alla |Windows Server 2012|Windows Server 2016|
|**Lägsta SQL Server-version**|Alla|SQL Server 2012|SQL Server 2016|
|**Tillgänglighet för VM som stöds** |Tillgänglighets uppsättningar med närhets placerings grupper (för Premium SSD) </br> Samma tillgänglighets zon (för Ultra SSD) |Tillgänglighets uppsättningar och tillgänglighets zoner|Tillgänglighetsuppsättningar |
|**Stöder FileStream**|Ja|Nej|Ja |
|**Azure Blob-cache**|Nej|Nej|Ja|

Resten av det här avsnittet visar fördelarna och begränsningarna för varje lagrings alternativ som är tillgängligt för SQL Server på virtuella Azure-datorer. 

### <a name="azure-shared-disks"></a>Delade diskar i Azure

[Azure delade diskar](../../../virtual-machines/disks-shared.md) är en funktion i [Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Windows Server-redundanskluster stöder användning av Azure delade diskar med en instans av redundanskluster. 

**Operativ system som stöds**: alla   
**SQL-version som stöds**: alla     

**Fördelar**: 
- Användbart för program som vill migrera till Azure samtidigt som du behåller HADR-arkitekturen (hög tillgänglighet och haveri beredskap) som är. 
- Kan migrera klustrade program till Azure på grund av stödet för SCSI-PR (SCSI persistent reservation). 
- Har stöd för delade Azure-Premium SSD och Azure Ultra disk Storage.
- Kan använda en enda delad disk eller Stripa flera delade diskar för att skapa en delad lagringspool. 
- Stöder FILESTREAM.
- Premium-SSD har stöd för tillgänglighets uppsättningar. 


**Begränsningar**: 
- Vi rekommenderar att du placerar de virtuella datorerna i samma tillgänglighets uppsättning och närhets placerings grupp.
- Ultra disks stöder inte tillgänglighets uppsättningar. 
- Tillgänglighets zoner stöds för Ultra disks, men de virtuella datorerna måste finnas i samma tillgänglighets zon, vilket minskar tillgängligheten för den virtuella datorn. 
- Oavsett den valda maskin varu tillgänglighets lösningen är tillgängligheten för redundansklustret alltid 99,9% när du använder Azure delade diskar. 
- Premium SSD diskcachelagring stöds inte.

 
Information om hur du kommer igång finns [SQL Server kluster instans med Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Lagringsutrymmen direkt

[Lagringsdirigering](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) är en Windows Server-funktion som stöds med kluster för växling vid fel i Azure Virtual Machines. Den innehåller en programvarubaserad virtuell SAN-server.

**Operativ system som stöds**: Windows Server 2016 och senare   
**SQL-version som stöds**: SQL Server 2016 och senare   


**Funktioner** 
- Tillräcklig nätverks bandbredd möjliggör en robust och mycket effektiv lösning för delad lagring. 
- Stöder Azure Blob cache, så läsningar kan hanteras lokalt från cachen. (Uppdateringar replikeras samtidigt till båda noderna.) 
- Stöder FileStream. 

**Hos**
- Endast tillgängligt för Windows Server 2016 och senare. 
- Tillgänglighets zoner stöds inte.
- Kräver samma disk kapacitet som är kopplad till båda virtuella datorerna. 
- Hög nätverks bandbredd krävs för att uppnå höga prestanda på grund av en pågående diskallokering. 
- Kräver en större VM-storlek och dubbel betalning för lagring eftersom lagringen är kopplad till varje virtuell dator. 

Information om hur du kommer igång finns [SQL Server kluster instans med Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Premium-filresurs

[Premium File-resurser](../../../storage/files/storage-how-to-create-premium-fileshare.md) är en funktion i [Azure Files](../../../storage/files/index.yml). Premium-filresurserna är SSD-baserade och har en konsekvent låg latens. De stöds fullt ut för användning med kluster instanser för växling vid fel för SQL Server 2012 eller senare på Windows Server 2012 eller senare. Premium-filresurser ger dig större flexibilitet eftersom du kan ändra storlek och skala en fil resurs utan drift avbrott.

**Operativ system som stöds**: Windows Server 2012 och senare   
**SQL-version som stöds**: SQL Server 2012 och senare   

**Funktioner** 
- Endast delad lagrings lösning för virtuella datorer sprids över flera tillgänglighets zoner. 
- Fullständigt hanterat fil system med ensiffriga latens och prestanda med burst I/O. 

**Hos**
- Endast tillgängligt för Windows Server 2012 och senare. 
- FileStream stöds inte. 


Information om hur du kommer igång finns [SQL Server kluster instans för växling vid fel med Premium-filresurs](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

Det finns partner kluster lösningar med stöd för lagring. 

**Operativ system som stöds**: alla   
**SQL-version som stöds**: alla   

I ett exempel används SIOS DataKeeper som lagrings plats. Mer information finns i blogg inlägget failover- [kluster och SIOS-DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI och ExpressRoute

Du kan också exponera ett iSCSI-mål delat block lagrings utrymme via Azure ExpressRoute. 

**Operativ system som stöds**: alla   
**SQL-version som stöds**: alla   

NetApp Private Storage (NPS) visar till exempel ett iSCSI-mål via ExpressRoute med Equinix till virtuella Azure-datorer.

För delade lösningar för lagring och datareplikering från Microsoft-partners kontaktar du leverantören för eventuella problem som rör åtkomst till data vid redundans.

## <a name="connectivity"></a>Anslutning

Instanser av kluster för växling vid fel med SQL Server på Azure Virtual Machines använda ett [distribuerat nätverks namn (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) eller ett [virtuellt nätverks namn (VNN) med Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) för att dirigera trafik till SQL Server-instansen, oavsett vilken nod som för närvarande äger de klustrade resurserna. Det finns ytterligare överväganden när du använder vissa funktioner och DNN med en SQL Server-FCI. Mer information finns i [DNN-interoperabilitet med SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) . 

Mer information om kluster anslutnings alternativ finns i [dirigera hadr-anslutningar till SQL Server på virtuella Azure-datorer](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Begränsningar

Tänk på följande begränsningar för kluster instanser för växling vid fel med SQL Server på Azure Virtual Machines. 

### <a name="lightweight-extension-support"></a>Stöd för Lightweight-tillägg   

För närvarande stöds SQL Server redundanskluster på Azure Virtual Machines endast med [läget för förenklad hantering](sql-server-iaas-agent-extension-automate-management.md#management-modes) i SQL Server IaaS agent-tillägget. Om du vill ändra från fullständigt tilläggs läge till Lightweight tar du bort den **virtuella SQL-datorns** resurs för motsvarande virtuella datorer och registrerar dem sedan med SQL IaaS agent-tillägget i Lightweight-läge. När du tar bort den virtuella **SQL-datorns** resurs med hjälp av Azure Portal avmarkerar du kryss rutan bredvid rätt virtuell dator för att undvika att ta bort den virtuella datorn. 

Det fullständiga tillägget har stöd för funktioner som automatisk säkerhets kopiering, uppdatering och avancerad Portal hantering. Dessa funktioner fungerar inte för SQL Server virtuella datorer som är registrerade i Lightweight Management mode.

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines stöder Microsoft koordinator för distribuerad transaktion (MSDTC) på Windows Server 2019 med lagring på klusterdelade volymer (CSV) och [Azure standard Load Balancer](../../../load-balancer/load-balancer-overview.md) eller på SQL Server virtuella datorer som använder Azure delade diskar. 

I Azure Virtual Machines stöds inte MSDTC för Windows Server 2016 eller tidigare med klustrade delade volymer på grund av följande:

- Det går inte att konfigurera den klustrade MSDTC-resursen att använda delad lagring. Om du skapar en MSDTC-resurs i Windows Server 2016 visas inga delade lagrings enheter som är tillgängliga för användning, även om lagring är tillgängligt. Det här problemet har åtgärd ATS i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.


## <a name="next-steps"></a>Nästa steg

Granska [kluster konfigurationernas metod tips](hadr-cluster-best-practices.md)och sedan kan du [förbereda din SQL Server VM för FCI](failover-cluster-instance-prepare-vm.md). 

Mer information finns i: 

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)