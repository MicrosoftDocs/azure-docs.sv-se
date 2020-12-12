---
title: Skapa en FCI med en Premium-filresurs
description: Använd en Premium-filresurs (PFS) för att skapa en FCI-instans (failover Cluster instance) med SQL Server på virtuella Azure-datorer.
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
ms.openlocfilehash: 2fb9677f0874de1fb715082d58a0e354880e654b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358086"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Skapa en FCI med en Premium-filresurs (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln beskrivs hur du skapar en FCI (failover Cluster instance) med SQL Server på Azure Virtual Machines (VM) med hjälp av en [Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Premium-filresurser är Lagringsdirigering (SSD)-backade, konsekventa fil resurser med låg latens som stöds fullt ut för användning med kluster instanser för växling vid fel för SQL Server 2012 eller senare på Windows Server 2012 eller senare. Premium-filresurser ger dig större flexibilitet, så att du kan ändra storlek på och skala en fil resurs utan drift avbrott.

Mer information finns i Översikt över [FCI med SQL Server på Azure VM](failover-cluster-instance-overview.md) och [kluster metod tips](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Förutsättningar

Innan du slutför instruktionerna i den här artikeln bör du redan ha:

- En Azure-prenumeration.
- Ett konto som har behörighet att skapa objekt både på virtuella Azure-datorer och i Active Directory.
- [Två eller fler för beredda virtuella Windows Azure-datorer](failover-cluster-instance-prepare-vm.md) i en [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) eller olika [tillgänglighets zoner](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- En [Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md) som ska användas som den klustrade enheten, baserat på lagrings kvoten för din databas för dina datafiler.
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Montera Premium-filresurs

1. Logga in på [Azure-portalen](https://portal.azure.com). och gå till ditt lagrings konto.
1. Gå till **fil resurser** under **fil tjänst** och välj sedan den Premium-filresurs som du vill använda för din SQL-lagring.
1. Välj **Anslut** för att ta fram anslutnings strängen för din fil resurs.
1. I list rutan väljer du den enhets beteckning som du vill använda och kopierar sedan båda kod blocken till anteckningar.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopiera båda PowerShell-kommandona från fil resursen Connect-portalen":::

1. Använd Remote Desktop Protocol (RDP) för att ansluta till SQL Server VM med det konto som din SQL Server-FCI kommer att använda för tjänst kontot.
1. Öppna en administrativ PowerShell-kommandorad.
1. Kör de kommandon som du sparade tidigare när du arbetade i portalen.
1. Gå till resursen med hjälp av antingen Utforskaren eller dialog rutan **Kör** (Välj Windows + R). Använd Nätverks Sök vägen `\\storageaccountname.file.core.windows.net\filesharename` . Till exempel `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Skapa minst en mapp på den nyligen anslutna fil resursen för att placera dina SQL-datafiler i.
1. Upprepa de här stegen på varje SQL Server VM som ska ingå i klustret.

  > [!IMPORTANT]
  > - Överväg att använda en separat fil resurs för säkerhets kopior för att spara indata/utdata-åtgärder per sekund (IOPS) och utrymmes kapacitet för den här resursen för data-och loggfiler. Du kan använda antingen en Premium-eller standard fil resurs för säkerhets kopierings filer.
  > - Om du använder Windows 2012 R2 eller tidigare, följer du samma steg för att montera fil resursen som du tänker använda som fil resurs vittne. 
  > 


## <a name="add-windows-cluster-feature"></a>Lägg till Windows-kluster funktion

1. Anslut till den första virtuella datorn med RDP genom att använda ett domän konto som är medlem i den lokala administratören och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till kluster för växling vid fel på varje virtuell dator](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

## <a name="validate-cluster"></a>Verifiera kluster

Verifiera klustret i användar gränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användar gränssnittet genom att göra följande på en av de virtuella datorerna:

1. Under **Serverhanteraren** väljer du **verktyg** och väljer sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel** väljer du **åtgärd** och väljer sedan **Verifiera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster** anger du namnen på de båda virtuella datorerna.
1. Under **test alternativ** väljer **du kör endast test som jag väljer**. 
1. Välj **Nästa**.
1. Under **Val av test** väljer du alla tester förutom **lagring** och **Lagringsdirigering**, som du ser här:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Välj kluster verifierings test":::

1. Välj **Nästa**.
1. Under **bekräftelse** väljer du **Nästa**.

Verifierings testen körs i guiden **Verifiera en konfiguration** .

Om du vill verifiera klustret med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>Testa redundanskluster

Testa redundansväxlingen av klustret. I **Klusterhanteraren för växling vid fel** högerklickar du på klustret, väljer **fler åtgärder**  >  **Flytta kärn kluster resurs**  >  **Välj nod** och välj sedan den andra noden i klustret. Flytta kärn kluster resursen till varje nod i klustret och flytta tillbaka den till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testa redundanskluster genom att flytta kärn resursen till de andra noderna":::


## <a name="create-sql-server-fci"></a>Skapa SQL Server FCI

När du har konfigurerat klustret för växling vid fel kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Klusterhanteraren för växling vid fel** kontrollerar du att alla kärn kluster resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full` . 

1. Välj **installation**.

1. I **installations Center för SQL Server** väljer du **installation**.

1. Välj **ny SQL Server redundanskluster** och följ sedan anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas på Premium-filresursen. Ange den fullständiga sökvägen till resursen i det här formatet: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . En varning visas som talar om att du har angett en fil server som data katalog. Den här varningen förväntas. Se till att det användar konto du använde för att få åtkomst till den virtuella datorn via RDP när du sparade fil resursen är samma konto som den SQL Server tjänsten använder för att undvika eventuella problem.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Använd fil resurs som SQL data-kataloger":::

1. När du har slutfört stegen i guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **installations Center för SQL Server** och välj sedan **installation**.

1. Välj **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning med SQL Server, inkluderades SQL Server verktyg tillsammans med avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server verktyg separat. Mer information finns i [Ladda ned SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Upprepa de här stegen på alla andra noder som du vill lägga till i SQL Server-WSFC-klustrets instans. 

## <a name="register-with-the-sql-vm-rp"></a>Registrera dig för SQL VM RP

Om du vill hantera din SQL Server VM från portalen registrerar du den med SQL IaaS agent Extension (RP) i [läget för förenklad hantering](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), för närvarande det enda läge som stöds med FCI och SQL Server på virtuella Azure-datorer. 

Registrera en SQL Server VM i Lightweight-läge med PowerShell (-LicenseType kan vara `PAYG` eller `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurera anslutning 

Om du vill dirigera trafiken korrekt till den aktuella primära noden konfigurerar du anslutnings alternativet som är lämpligt för din miljö. Du kan skapa en [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) eller, om du använder SQL Server 2019 CU2 (eller senare) och Windows Server 2016 (eller senare), kan du använda funktionen [distribuerat nätverks namn](failover-cluster-instance-distributed-network-name-dnn-configure.md) i stället. 

## <a name="limitations"></a>Begränsningar

- Microsoft koordinator för distribuerad transaktion (MSDTC) stöds inte på Windows Server 2016 och tidigare. 
- FILESTREAM stöds inte för ett redundanskluster med en Premium-filresurs. Om du vill använda FILESTREAM distribuerar du klustret med hjälp av [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md) eller [Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md) i stället.
- Det finns bara stöd för registrering med SQL IaaS agent extension i [läget för förenklad hantering](sql-server-iaas-agent-extension-automate-management.md#management-modes) . 

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det konfigurerar du anslutningen till din FCI med ett [virtuellt nätverks namn och en Azure Load Balancer eller ett](failover-cluster-instance-vnn-azure-load-balancer-configure.md) [distribuerat nätverks namn (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Om Premium-filresurser inte är lämplig FCI lagrings lösning, kan du överväga att skapa din FCI med hjälp av [Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md) eller [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md) i stället. 

Mer information finns i en översikt över [FCI med SQL Server på](failover-cluster-instance-overview.md) bästa praxis för Azure VM och [kluster konfiguration](hadr-cluster-best-practices.md). 

Mer information finns i: 
- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
