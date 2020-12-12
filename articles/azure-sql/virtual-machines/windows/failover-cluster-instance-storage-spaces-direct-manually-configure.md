---
title: Skapa en FCI med Lagringsdirigering
description: Använd Lagringsdirigering för att skapa en instans av redundanskluster (FCI) med SQL Server på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 6ed5e11a8492314e99b9f105d259fa910dcdb77d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357815"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Skapa en FCI med Lagringsdirigering (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln förklaras hur du skapar en instans av en redundanskluster (FCI) med hjälp av [Lagringsdirigering](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) med SQL Server på Azure-Virtual Machines (VM). Lagringsdirigering fungerar som en programvarubaserad virtuell storage area network (virtuellt SAN) som synkroniserar lagringen (data diskar) mellan noderna (virtuella Azure-datorer) i ett Windows-kluster. 

Mer information finns i Översikt över [FCI med SQL Server på Azure VM](failover-cluster-instance-overview.md) och [kluster metod tips](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Översikt 

[Lagringsdirigering (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) stöder två typer av arkitekturer: konvergerade och konvergerade. En överkonvergerad infrastruktur placerar lagringen på samma servrar som är värdar för det klustrade programmet, så att lagringen finns på varje SQL Server FCI-nod. 

Följande diagram visar den kompletta lösningen, som använder konvergerade Lagringsdirigering med SQL Server på virtuella Azure-datorer: 

![Diagram över den kompletta lösningen med hjälp av den konvergerade Lagringsdirigering](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Föregående diagram visar följande resurser i samma resurs grupp:

- Två virtuella datorer i ett Windows Server-redundanskluster. När en virtuell dator finns i ett redundanskluster kallas den även för en *klusternod* eller *nod*.
- Varje virtuell dator har två eller flera data diskar.
- Lagringsdirigering synkroniserar data på data diskarna och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen visar en klusterdelad volym (CSV) för redundansklustret.
- Kluster rollen SQL Server FCI använder CSV-filen för data enheterna.
- En Azure Load Balancer för att lagra IP-adressen för SQL Server FCI.
- En tillgänglighets uppsättning i Azure innehåller alla resurser.

   > [!NOTE]
   > Du kan skapa hela lösningen i Azure från en mall. Ett exempel på en mall finns på sidan GitHub för [Azure snabb starts mallar](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) . Det här exemplet är inte utformat eller testat för någon speciell arbets belastning. Du kan köra mallen för att skapa en SQL Server-FCI med Lagringsdirigering lagring som är ansluten till din domän. Du kan utvärdera mallen och ändra den så att den passar dina behov.


## <a name="prerequisites"></a>Förutsättningar

Innan du slutför instruktionerna i den här artikeln bör du redan ha:

- En Azure-prenumeration. Kom igång [kostnads fritt](https://azure.microsoft.com/free/). 
- [Två eller fler för beredda virtuella Windows Azure-datorer](failover-cluster-instance-prepare-vm.md) i en [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Ett konto som har behörighet att skapa objekt både på virtuella Azure-datorer och i Active Directory.
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>Lägg till funktionen Windows-kluster

1. Anslut till den första virtuella datorn genom att använda Remote Desktop Protocol (RDP) med ett domän konto som är medlem i den lokala administratören och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. Lägg till kluster för växling vid fel på varje virtuell dator.

   Om du vill installera kluster för växling vid fel från användar gränssnittet gör du följande på båda de virtuella datorerna:

   1. I **Serverhanteraren** väljer du **Hantera** och väljer sedan **Lägg till roller och funktioner**.
   1. I guiden **Lägg till roller och funktioner** väljer du **Nästa** tills du kommer igång med att **välja funktioner**.
   1. I **Välj funktioner** väljer du **kluster för växling vid fel**. Ta med alla nödvändiga funktioner och hanterings verktyg. 
   1. Välj **Lägg till funktioner**.
   1. Välj **Nästa** och välj sedan **Slutför** för att installera funktionerna.

   Om du vill installera kluster för växling vid fel med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Mer information om nästa steg finns i anvisningarna i avsnittet "steg 3: Konfigurera Lagringsdirigering" i den [konvergerade lösningen med Lagringsdirigering i Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Verifiera klustret

Verifiera klustret i användar gränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användar gränssnittet genom att göra följande på en av de virtuella datorerna:

1. Under **Serverhanteraren** väljer du **verktyg** och väljer sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel** väljer du **åtgärd** och väljer sedan **Verifiera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster** anger du namnen på de båda virtuella datorerna.
1. Under **test alternativ** väljer **du kör endast test som jag väljer**. 
1. Välj **Nästa**.
1. Under **Val av test** väljer du alla tester förutom **lagring**, som du ser här:

   ![Välj kluster verifierings test](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Välj **Nästa**.
1. Under **bekräftelse** väljer du **Nästa**.

    Verifierings testen körs i guiden **Verifiera en konfiguration** .

Om du vill verifiera klustret med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du klustret för växling vid fel.


## <a name="create-failover-cluster"></a>Skapa redundanskluster

Du behöver följande om du vill skapa ett kluster för växling vid fel:

- Namnen på de virtuella datorer som kommer att bli klusternoder.
- Ett namn för redundansklustret.
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2012 via Windows Server 2016. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019.  Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Mer information finns i [redundansklustret: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurera kvorum

Konfigurera den kvorumresurs som passar dina affärs behov bäst. Du kan konfigurera ett [disk vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), ett [moln vittne](/windows-server/failover-clustering/deploy-cloud-witness)eller ett [fil resurs vittne](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Mer information finns i [kvorum med SQL Server virtuella datorer](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Lägg till lagringsutrymme

Diskarna för Lagringsdirigering måste vara tomma. De får inte innehålla partitioner eller andra data. Om du vill rensa diskarna följer du anvisningarna i [distribuera Lagringsdirigering](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Aktivera Lagringsdirigering](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Följande PowerShell-skript aktiverar Lagringsdirigering:  

   ```powershell
   Enable-ClusterS2D
   ```

   I **Klusterhanteraren för växling vid fel** kan du nu se lagringspoolen.

1. [Skapa en volym](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Lagringsdirigering skapar automatiskt en lagringspool när du aktiverar den. Nu är du redo att skapa en volym. PowerShell-cmdleten `New-Volume` automatiserar skapande processen för volymer. Den här processen inkluderar formatering, tillägg av volymen i klustret och skapande av en CSV-fil. I det här exemplet skapas en 800 gigabyte (GB) CSV:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När du har kört föregående kommando monteras en volym på 800 GB som en kluster resurs. Volymen är på `C:\ClusterStorage\Volume1\` .

   Den här skärm bilden visar en CSV med Lagringsdirigering:

   ![Skärm bild av en klusterdelad volym med Lagringsdirigering](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testa redundanskluster

Testa redundansväxlingen av klustret. I **Klusterhanteraren för växling vid fel** högerklickar du på klustret, väljer **fler åtgärder**  >  **Flytta kärn kluster resurs**  >  **Välj nod** och välj sedan den andra noden i klustret. Flytta kärn kluster resursen till varje nod i klustret och flytta tillbaka den till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testa redundanskluster genom att flytta kärn resursen till de andra noderna":::

## <a name="create-sql-server-fci"></a>Skapa SQL Server FCI

När du har konfigurerat redundansklustret och alla kluster komponenter, inklusive lagring, kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Klusterhanteraren för växling vid fel** kontrollerar du att alla kärn kluster resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full` . Välj **installation**.

1. I **SQL Server installations Center** väljer du **installation**.

1. Välj **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas i klustrad lagring. Med Lagringsdirigering är det inte en delad disk men en monterings punkt för en volym på varje server. Lagringsdirigering synkroniserar volymen mellan båda noderna. Volymen visas för klustret som en KLUSTERDELAD volym. Använd CSV-monterings punkten för data katalogerna.

   ![Data kataloger](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. När du har slutfört anvisningarna i guiden installerar installations programmet en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **installations Center för SQL Server**. Välj **installation**.

1. Välj **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning som innehåller SQL Server, inkluderades SQL Server verktyg med avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server verktyg separat. Mer information finns i [Ladda ned SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Registrera dig för SQL VM RP

Om du vill hantera din SQL Server VM från portalen registrerar du den med SQL IaaS agent Extension (RP) i [läget för förenklad hantering](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), för närvarande det enda läge som stöds med FCI och SQL Server på virtuella Azure-datorer. 


Registrera en SQL Server VM i Lightweight-läge med PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurera anslutning 

Om du vill dirigera trafiken korrekt till den aktuella primära noden konfigurerar du anslutnings alternativet som är lämpligt för din miljö. Du kan skapa en [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) eller, om du använder SQL Server 2019 CU2 (eller senare) och Windows Server 2016 (eller senare), kan du använda funktionen [distribuerat nätverks namn](failover-cluster-instance-distributed-network-name-dnn-configure.md) i stället. 

## <a name="limitations"></a>Begränsningar

- Azure Virtual Machines stöder Microsoft koordinator för distribuerad transaktion (MSDTC) på Windows Server 2019 med lagring på CSV: er och en [standard belastningsutjämnare](../../../load-balancer/load-balancer-overview.md).
- Diskar som har bifogats som NTFS-formaterade diskar kan bara användas med Lagringsdirigering om alternativet för disk behörighet är omarkerat eller avmarkerat när lagring läggs till i klustret. 
- Det finns bara stöd för registrering med SQL IaaS agent extension i [läget för förenklad hantering](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det konfigurerar du anslutningen till din FCI med ett [virtuellt nätverks namn och en Azure Load Balancer eller ett](failover-cluster-instance-vnn-azure-load-balancer-configure.md) [distribuerat nätverks namn (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Om Lagringsdirigering inte är lämplig lagrings lösning för FCI, kan du överväga att skapa FCI med hjälp av [Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md) eller [Premium-filresurser](failover-cluster-instance-premium-file-share-manually-configure.md) i stället. 

Mer information finns i en översikt över [FCI med SQL Server på](failover-cluster-instance-overview.md) bästa praxis för Azure VM och [kluster konfiguration](hadr-cluster-best-practices.md). 

Mer information finns i: 
- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)