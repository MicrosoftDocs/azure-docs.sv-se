---
title: Skapa en FCI med en premiumfilresurs
description: Använd en Premium-filresurs (PFS) för att skapa en redundansklusterinstans (FCI) med SQL Server på virtuella Azure-datorer.
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
ms.openlocfilehash: ddd25c605ef159bddfb8a9c7cb4d02ac7094c511
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482219"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Skapa en FCI med en Premium-filresurs (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du skapar en redundansklusterinstans (FCI) med SQL Server på Azure Virtual Machines (VM) med hjälp av en [premiumfilresurs](../../../storage/files/storage-how-to-create-file-share.md).

Premium-filresurser är Lagringsdirigering (SSD)-säkerhetskopierade filresurser med konsekvent låg latens som stöds fullt ut för användning med redundansklusterinstanser för SQL Server 2012 eller senare på Windows Server 2012 eller senare. Premium-filresurser ger större flexibilitet så att du kan ändra storlek på och skala en filresurs utan avbrott.

Mer information finns i en översikt över [FCI med SQL Server på virtuella Azure-datorer och](failover-cluster-instance-overview.md) [metodtips för kluster.](hadr-cluster-best-practices.md) 

## <a name="prerequisites"></a>Förutsättningar

Innan du slutför anvisningarna i den här artikeln bör du redan ha:

- En Azure-prenumeration.
- Ett konto som har behörighet att skapa objekt på både virtuella Azure-datorer och i Active Directory.
- [Två eller flera förberedda virtuella Windows Azure-datorer](failover-cluster-instance-prepare-vm.md) i en [tillgänglighetsuppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) eller olika [tillgänglighetszoner.](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)
- En [premiumfilresurs](../../../storage/files/storage-how-to-create-file-share.md) som ska användas som klustrad enhet, baserat på databasens lagringskvot för dina datafiler.
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Montera premiumfilresurs

1. Logga in på [Azure-portalen](https://portal.azure.com). och gå till ditt lagringskonto.
1. Gå till **Filresurser** under **Filtjänst** och välj sedan den premiumfilresurs som du vill använda för SQL-lagringen.
1. Välj **Anslut** för att öppna anslutningssträngen för filresursen.
1. I listrutan väljer du den enhetsbeteckning som du vill använda och kopierar sedan båda kodblocken till Anteckningar.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopiera båda PowerShell-kommandona från anslutningsportalen för filresursen":::

1. Använd Remote Desktop Protocol (RDP) för att ansluta till SQL Server VM med det konto som din SQL Server FCI kommer att använda för tjänstkontot.
1. Öppna en administrativ PowerShell-kommandokonsol.
1. Kör de kommandon som du sparade tidigare när du arbetade i portalen.
1. Gå till resursen med hjälp av antingen Utforskaren eller **dialogrutan** Kör (välj Windows + R). Använd nätverkssökvägen `\\storageaccountname.file.core.windows.net\filesharename` . Till exempel `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Skapa minst en mapp på den nyligen anslutna filresursen för att placera DINA SQL-datafiler i.
1. Upprepa dessa steg på varje SQL Server VM som ska delta i klustret.

  > [!IMPORTANT]
  > - Överväg att använda en separat filresurs för säkerhetskopierade filer för att spara indata-/utdataåtgärder per sekund (IOPS) och utrymmeskapacitet för den här resursen för data- och loggfiler. Du kan använda antingen en Premium- eller Standard-filresurs för säkerhetskopieringsfiler.
  > - Om du använder Windows 2012 R2 eller tidigare följer du samma steg för att montera filresursen som du ska använda som filresursvittne. 
  > 


## <a name="add-windows-cluster-feature"></a>Lägg till funktion för Windows-kluster

1. Anslut till den första virtuella datorn med RDP med ett domänkonto som är medlem i de lokala administratörerna och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till redundansklustring för varje virtuell dator](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Om du vill installera redundansklustring från användargränssnittet gör du följande på båda virtuella datorerna:
   1. I **Serverhanteraren** väljer du **Hantera** och sedan Lägg till roller **och funktioner.**
   1. I guiden **Lägg till roller och** funktioner väljer du **Nästa** tills du kommer till **Välj funktioner.**
   1. I **Välj funktioner** väljer du **Redundanskluster.** Inkludera alla nödvändiga funktioner och hanteringsverktygen. 
   1. Välj **Lägg till funktioner.**
   1. Välj **Nästa** och välj sedan **Slutför för** att installera funktionerna.

   Om du vill installera redundansklustring med hjälp av PowerShell kör du följande skript från en PowerShell-administratörssession på en av de virtuella datorerna:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Verifiera kluster

Verifiera klustret i användargränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användargränssnittet genom att göra följande på en av de virtuella datorerna:

1. Under **Serverhanteraren** väljer **du Verktyg** och sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel** väljer **du Åtgärd** och sedan Verifiera **konfiguration.**
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster** anger du namnen på båda virtuella datorerna.
1. Under **Testalternativ** väljer du **Kör endast tester som jag väljer.** 
1. Välj **Nästa**.
1. Under **Testval** väljer du alla tester utom **Storage** och **Lagringsdirigering**, som du ser här:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Välj klustervalideringstester":::

1. Välj **Nästa**.
1. Under **Bekräftelse** väljer du **Nästa.**

**Verifieringstesterna körs** i guiden Verifiera en konfiguration.

Verifiera klustret med hjälp av PowerShell genom att köra följande skript från en PowerShell-administratörssession på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du redundansklustret.


## <a name="create-failover-cluster"></a>Skapa redundanskluster

För att skapa redundansklustret behöver du:

- Namnen på de virtuella datorer som ska bli klusternoder.
- Ett namn för redundansklustret.
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används i samma virtuella Azure-nätverk och undernät som klusternoderna.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 – 2016](#tab/windows2012)

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2012 till och med Windows Server 2016. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019.  Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Mer information finns i [Redundanskluster: Klusternätverksobjekt.](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)

---


## <a name="configure-quorum"></a>Konfigurera kvorum

Konfigurera den kvorumlösning som bäst passar dina affärsbehov. Du kan konfigurera ett [diskvittne,](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)ett [molnvittne](/windows-server/failover-clustering/deploy-cloud-witness)eller ett [filresursvittne.](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) Mer information finns i [kvorum med SQL Server virtuella datorer.](hadr-cluster-best-practices.md#quorum) 


## <a name="test-cluster-failover"></a>Testa kluster redundans

Testa redundansen för klustret. I **Klusterhanteraren för växling vid fel** högerklickar du på klustret, väljer Fler åtgärder Flytta kärnklusterresurs Välj nod och väljer sedan den andra  >    >  noden i klustret. Flytta kärnklusterresursen till varje nod i klustret och flytta den sedan tillbaka till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testa kluster redundans genom att flytta kärnresursen till de andra noderna":::


## <a name="create-sql-server-fci"></a>Skapa SQL Server FCI

När du har konfigurerat redundansklustret kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med HJÄLP av RDP.

1. I **Klusterhanteraren för växling vid fel** kontrollerar du att alla kärnklusterresurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den här virtuella datorn.

1. Leta upp installationsmediet. Om den virtuella datorn använder en av Azure Marketplace avbildningar finns mediet på `C:\SQLServer_<version number>_Full` . 

1. Välj **Konfigurera**.

1. I SQL Server **Installation Center** väljer du **Installation**.

1. Välj **Ny SQL Server installation av redundanskluster** och följ sedan anvisningarna i guiden för att installera SQL Server FCI.

   FCI-datakatalogerna måste finnas på premiumfilresursen. Ange den fullständiga sökvägen till resursen i det här formatet: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . En varning visas som anger att du har angett en filserver som datakatalog. Den här varningen är väntad. Kontrollera att användarkontot som du använde för att komma åt den virtuella datorn via RDP när du beständiga filresursen är samma konto som SQL Server-tjänsten använder för att undvika eventuella fel.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Använda filresurs som SQL-datakataloger":::

1. När du har slutfört stegen i guiden installerar installationsprogrammet en SQL Server FCI på den första noden.

1. När installationsprogrammet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **SQL Server Installation Center** och välj sedan **Installation**.

1. Välj **Lägg till nod i SQL Server ett redundanskluster.** Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde Azure Marketplace galleriavbildning med SQL Server SQL Server-verktyg med avbildningen. Om du inte använder någon av dessa avbildningar installerar du SQL Server verktyg separat. Mer information finns i [Download SQL Server Management Studio (SSMS) .](/sql/ssms/download-sql-server-management-studio-ssms)

1. Upprepa dessa steg på alla andra noder som du vill lägga till i SQL Server redundansklusterinstansen. 

## <a name="register-with-the-sql-vm-rp"></a>Registrera med SQL VM-RP

Om du vill hantera SQL Server VM från portalen registrerar du den med SQL IaaS Agent-tillägget (RP) i förenklat hanteringsläge [,](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)för närvarande det enda läget som stöds med FCI och SQL Server på virtuella Azure-datorer. 

Registrera en SQL Server VM i förenklat läge med PowerShell (-LicenseType kan vara `PAYG` eller `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurera anslutning 

Om du vill dirigera trafik på rätt sätt till den aktuella primära noden konfigurerar du det anslutningsalternativ som passar din miljö. Du kan skapa en [Azure-lastbalanserare.](failover-cluster-instance-vnn-azure-load-balancer-configure.md) Om du använder SQL Server 2019 CU2 (eller senare) och Windows Server 2016 (eller senare) kan du använda funktionen [för](failover-cluster-instance-distributed-network-name-dnn-configure.md) namn på distribuerat nätverk i stället. 

Mer information om alternativ för klusteranslutning finns i [Dirigera HADR-anslutningar till SQL Server på virtuella Azure-datorer.](hadr-cluster-best-practices.md#connectivity) 

## <a name="limitations"></a>Begränsningar

- Microsoft Distributed Transaction Coordinator (MSDTC) stöds inte på Windows Server 2016 och tidigare. 
- Filestream stöds inte för ett redundanskluster med en premiumfilresurs. Om du vill använda filström distribuerar du klustret med hjälp [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md) eller [delade Azure-diskar i](failover-cluster-instance-azure-shared-disks-manually-configure.md) stället.
- Endast registrering med SQL IaaS Agent-tillägget i [förenklat hanteringsläge](sql-server-iaas-agent-extension-automate-management.md#management-modes) stöds. 
- Ögonblicksbilder av databasen stöds för närvarande inte med [Azure Files på grund av begränsningar för glesa filer.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)  

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det konfigurerar [](failover-cluster-instance-vnn-azure-load-balancer-configure.md) du anslutningen till din FCI med ett virtuellt nätverksnamn och en Azure-lastbalanserare eller [ett distribuerat nätverksnamn (DNN).](failover-cluster-instance-distributed-network-name-dnn-configure.md) 


Om Premium-filresurser inte är rätt FCI-lagringslösning kan du skapa din FCI med hjälp av [delade Azure-diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md) eller [Lagringsdirigering i](failover-cluster-instance-storage-spaces-direct-manually-configure.md) stället. 

Mer information finns i en översikt över [FCI med SQL Server virtuella Azure-datorer och](failover-cluster-instance-overview.md) metodtips [för klusterkonfiguration.](hadr-cluster-best-practices.md) 

Mer information finns i: 
- [Windows-klustertekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
