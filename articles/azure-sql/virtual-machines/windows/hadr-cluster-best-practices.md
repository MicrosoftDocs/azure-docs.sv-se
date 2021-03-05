---
title: Metod tips för kluster konfiguration
description: Lär dig mer om de klusterkonfigurationer som stöds när du konfigurerar hög tillgänglighet och haveri beredskap (HADR) för SQL Server på Azure Virtual Machines, till exempel kvorum som stöds eller alternativ för anslutnings dirigering.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201778"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Metod tips för klusterkonfiguration (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ett kluster används för hög tillgänglighet och haveri beredskap (HADR) med SQL Server på Azure Virtual Machines (VM). 

Den här artikeln innehåller metod tips för kluster konfiguration för både [skyddas (failover Cluster instances)](failover-cluster-instance-overview.md) och [tillgänglighets grupper](availability-group-overview.md) när du använder dem med SQL Server på virtuella Azure-datorer. 


## <a name="networking"></a>Nätverk

Använd ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster i en virtuell Azure-dator. I kluster verifierings rapporten visas en varning om att noderna bara kan kommas åt i ett enda nätverk. Du kan ignorera den här varningen på Azure Virtual Machine Guest-kluster.

### <a name="tuning-failover-cluster-network-thresholds"></a>Justera nätverks trösklar för redundanskluster

När du kör Windows-redundanskluster i virtuella Azure-datorer med SQL Server AlwaysOn, rekommenderas att ändra kluster inställningen till ett mer avslappnad övervaknings tillstånd.  Detta gör klustret mycket mer stabilt och tillförlitligt.  Mer information om detta finns i [IaaS med SQL AlwaysOn-finjustera tröskelvärden för redundanskluster](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Kvorum

Även om ett kluster med två noder fungerar utan en [kvorumresurs](/windows-server/storage/storage-spaces/understand-quorum), krävs det strikta kunder att använda en kvorumresurs för att få produktions support. Kluster valideringen skickar inte några kluster utan en kvorumresurs. 

Ett kluster med tre noder kan på ett enkelt sätt överleva en enskild nod (ned till två noder) utan en kvorumresurs. Men när klustret har gått ned till två noder, finns det en risk att de klustrade resurserna kommer att kopplas från vid en nod förlust eller kommunikations fel för att förhindra ett delat-hjärna-scenario.

Genom att konfigurera en kvorumresurs kan klustret fortsätta att vara online med bara en nod online.

I följande tabell visas de tillgängliga alternativen i den ordning som rekommenderas för användning med en virtuell Azure-dator, med det disk vittne som du föredrar: 


||[Disk vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Moln vittne](/windows-server/failover-clustering/deploy-cloud-witness)  |[Fil resurs vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Operativsystem som stöds**| Alla |Windows Server 2016 +| Alla|


### <a name="disk-witness"></a>Diskvittne

Ett disk vittne är en liten kluster disk i den tillgängliga lagrings gruppen för klustret. Disken är hög tillgänglig och kan växlas över mellan noder. Den innehåller en kopia av kluster databasen, med en standard storlek som vanligt vis är mindre än 1 GB. Disk vittnet är det bästa alternativet för kvorum för alla kluster som använder Azure delade diskar (eller någon lösning för delad disk som delad SCSI, iSCSI eller Fibre Channel SAN).  Det går inte att använda en klusterdelad volym som ett disk vittne.

Konfigurera en Azure-delad disk som disk vittne. 

Information om hur du kommer igång finns i [Konfigurera ett disk vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Operativ system som stöds**: alla   


### <a name="cloud-witness"></a>Molnvittne

Ett moln vittne är en typ av ett kvorum för redundanskluster som använder Microsoft Azure för att ge en röst på klusterkvorum. Standard storleken är ungefär 1 MB och innehåller bara tidstämpeln. Ett moln vittne är idealiskt för distributioner på flera platser, flera zoner och flera regioner.

Information om hur du kommer igång finns i [Konfigurera ett moln vittne](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Operativ system som stöds**: Windows Server 2016 och senare   


### <a name="file-share-witness"></a>Filresursvittne

Ett fil resurs vittne är en SMB-filresurs som vanligt vis konfigureras på en fil server som kör Windows Server. Den upprätthåller kluster informationen i en vittne. log-fil, men lagrar inte en kopia av kluster databasen. I Azure kan du konfigurera en fil resurs på en separat virtuell dator.

Information om hur du kommer igång finns i [Konfigurera ett fil resurs vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Operativ system som stöds**: Windows Server 2012 och senare   

## <a name="connectivity"></a>Anslutning

I en traditionell lokal nätverks miljö verkar en instans av en SQL Server-redundanskluster vara en enda instans av SQL Server som körs på en enda dator. Eftersom växlings kluster instansen växlar över från nod till nod, tillhandahåller det virtuella nätverks namnet (VNN) för instansen en enhetlig anslutnings punkt och gör det möjligt för program att ansluta till den SQL Server-instansen utan att veta vilken nod som för närvarande är aktiv. När en redundansväxling inträffar registreras det virtuella nätverks namnet på den nya aktiva noden när den har startats. Den här processen är transparent för den klient eller det program som ansluter till SQL Server, och detta minimerar stillestånds tiden som klienten eller program upplever vid ett haveri. På samma sätt använder tillgänglighets gruppens lyssnare en VNN för att dirigera trafik till lämplig replik. 

Använd en VNN med Azure Load Balancer eller ett distribuerat nätverks namn (DNN) för att dirigera trafik till VNN för kluster instansen för växling vid fel med SQL Server på virtuella Azure-datorer eller för att ersätta den befintliga VNN-lyssnaren i en tillgänglighets grupp. 


I följande tabell jämförs HADR-anslutnings support: 

| |**Virtual Network namn (VNN)**  |**Distribuerat nätverks namn (DNN)**  |
|---------|---------|---------|
|**Lägsta version av operativsystemet**| Alla | Windows Server 2016 |
|**Lägsta SQL Server-version** |Alla |SQL Server 2019 CU2 (för FCI)<br/> SQL Server 2019 CU8 (för AG)|
|**HADR-lösning som stöds** | Redundansklusterinstans <br/> Tillgänglighetsgrupp | Redundansklusterinstans <br/> Tillgänglighetsgrupp|


### <a name="virtual-network-name-vnn"></a>Namn på virtuellt nätverk (VNN)

Eftersom den virtuella IP-FCI fungerar annorlunda i Azure måste du konfigurera [Azure Load Balancer](../../../load-balancer/index.yml) för att dirigera trafik till IP-adressen för-noderna eller tillgänglighets gruppens lyssnare. I Azure Virtual Machines innehåller en belastningsutjämnare IP-adressen för VNN som klustrade SQL Server resurser förlitar sig på. Belastningsutjämnaren distribuerar inkommande flöden som anländer till klient delen och dirigerar sedan trafiken till de instanser som definieras av backend-poolen. Du konfigurerar trafikflöde genom att använda regler för belastnings utjämning och hälso avsökningar. Med SQL Server FCI är backend-instanserna de virtuella Azure-datorerna som kör SQL Server. 

Det finns en liten växlings fördröjning när du använder belastningsutjämnaren, eftersom hälso avsökningen utför Alive-kontroller var 10: e sekund som standard. 

För att komma igång, lär dig hur du konfigurerar Azure Load Balancer för [kluster instans för växling vid fel](failover-cluster-instance-vnn-azure-load-balancer-configure.md) eller en [tillgänglighets grupp](availability-group-vnn-azure-load-balancer-configure.md)

**Operativ system som stöds**: alla   
**SQL-version som stöds**: alla   
**Hadr lösning som stöds**: kluster instans för växling vid fel och tillgänglighets grupp   


### <a name="distributed-network-name-dnn"></a>Namn på distribuerat nätverk (DNN)

Distribuerat nätverks namn är en ny Azure-funktion för SQL Server 2019. DNN tillhandahåller ett alternativt sätt för SQL Server klienter att ansluta till SQL Server redundanskluster eller tillgänglighets grupp utan att använda en belastningsutjämnare. 

När en DNN-resurs skapas binder klustret DNS-namnet med IP-adresserna för alla noder i klustret. SQL-klienten försöker ansluta till varje IP-adress i listan för att hitta vilken resurs som ska anslutas till.  Du kan påskynda den här processen genom `MultiSubnetFailover=True` att ange i anslutnings strängen. Den här inställningen instruerar providern att testa alla IP-adresser parallellt, så att klienten kan ansluta till FCI eller lyssnaren direkt. 

Ett distribuerat nätverks namn rekommenderas över en belastningsutjämnare när det är möjligt, eftersom: 
- Lösningen från slut punkt till slut punkt är stabilare eftersom du inte längre behöver underhålla belastnings Utjämnings resursen. 
- Om du tar bort belastnings Utjämnings avsökningarna minimeras varaktigheten för redundans. 
- DNN fören klar etableringen och hanteringen av instansen av redundanskluster eller tillgänglighets grupps lyssnaren med SQL Server på virtuella Azure-datorer. 

De flesta SQL Server funktioner fungerar transparent med FCI och tillgänglighets grupper när du använder DNN, men det finns vissa funktioner som kan kräva särskilda överväganden. Läs mer i [FCI-och DNN-interoperabilitet](failover-cluster-instance-dnn-interoperability.md) och [AG-och DNN-interoperabilitet](availability-group-dnn-interoperability.md) . 

För att komma igång, lär dig att konfigurera en distribuerad nätverks namn resurs för [en instans av ett redundanskluster](failover-cluster-instance-distributed-network-name-dnn-configure.md) eller en [tillgänglighets grupp](availability-group-distributed-network-name-dnn-listener-configure.md)

**Operativ system som stöds**: Windows Server 2016 och senare   
**SQL-version som stöds**: SQL Server 2019 CU2 (FCI) och SQL Server 2019 CU8 (AG)   
**Hadr lösning som stöds**: kluster instans för växling vid fel och tillgänglighets grupp   


## <a name="limitations"></a>Begränsningar

Tänk på följande begränsningar när du arbetar med FCI-eller tillgänglighets grupper och SQL Server på Azure Virtual Machines. 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines stöder Microsoft Distributed Transaction Coordinator (MSDTC) på Windows Server 2019 med lagring på klusterdelade volymer (CSV) och [Azure standard Load Balancer](../../../load-balancer/load-balancer-overview.md) eller på SQL Server virtuella datorer som använder Azure delade diskar. 

I Azure Virtual Machines stöds inte MSDTC för Windows Server 2016 eller tidigare med klustrade delade volymer på grund av följande:

- Det går inte att konfigurera den klustrade MSDTC-resursen att använda delad lagring. Om du skapar en MSDTC-resurs i Windows Server 2016 visas inga delade lagrings enheter som är tillgängliga för användning, även om lagring är tillgängligt. Det här problemet har åtgärd ATS i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.


## <a name="next-steps"></a>Nästa steg

När du har fastställt lämpliga metod tips för din lösning, kom igång genom att [förbereda din SQL Server VM för FCI](failover-cluster-instance-prepare-vm.md) eller genom att skapa din tillgänglighets grupp med hjälp av [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)eller [Azures snabb starts mallar](availability-group-quickstart-template-configure.md).
