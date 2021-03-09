---
title: SAP ASCS/SCS multi-SID HA med WSFC och Azure delad disk | Microsoft Docs
description: Hög tillgänglighet för flera SID för en SAP ASCS/SCS-instans med WSFC och Azure Shared disk
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cef0630cfc7ec7d080073085e577153ae7a47ecf
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504464"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>SAP ASCS/SCS-instans multi-SID hög tillgänglighet med Windows Server-redundanskluster och Azure-delad disk

> ![Windows OS][Logo_Windows] Windows
>

Den här artikeln fokuserar på hur du flyttar från en enda ASCS/SCS-installation till en SAP multi-SID-konfiguration genom att installera ytterligare SAP ASCS/SCS-klustrade instanser i ett befintligt WSFC-kluster (Windows Server Failover Clustering) med Azure Shared disk. När den här processen har slutförts har du konfigurerat ett SAP multi-SID-kluster.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

För närvarande kan du använda Azure Premium SSD-diskar som en Azure-delad disk för SAP ASCS/SCS-instansen. Följande begränsningar är på plats:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) stöds inte som Azure-delad disk för SAP-arbetsbelastningar. För närvarande går det inte att placera virtuella Azure-datorer med Azure Ultra disk i tillgänglighets uppsättning
-  [Azure Shared disk](../../disks-shared.md) med Premium SSD-diskar stöds bara med virtuella datorer i tillgänglighets uppsättningen. Det stöds inte i Tillgänglighetszoner-distribution. 
-  Värdet för Azure-delad disk [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) avgör hur många klusternoder som kan använda den delade disken. Normalt för SAP ASCS/SCS-instans konfigurerar du två noder i Windows-redundanskluster, och därför måste värdet för `maxShares` vara inställt på två.
-  Alla virtuella SAP ASCS/SCS-kluster måste distribueras i samma [placerings grupp för Azure närhet](../../windows/proximity-placement-groups.md).   
   Även om du kan distribuera virtuella Windows-kluster i tillgänglighets uppsättning med Azure delad disk utan PPG, ser PPG till att det går nära fysiskt nära Azure-delade diskar och de virtuella datorerna i klustret, vilket ger kortare latens mellan de virtuella datorerna och lagrings skiktet.    

Mer information om begränsningar för Azure Shared disk finns i avsnittet [begränsningar](../../disks-shared.md#limitations) i dokumentationen för Azure Shared disk.  

> [!IMPORTANT]
> När du distribuerar SAP ASCS/SCS Windows-redundanskluster med Azure Shared disk bör du vara medveten om att distributionen kommer att fungera med en enda delad disk i ett lagrings kluster. Din SAP ASCS/SCS-instans påverkas, i händelse av problem med lagrings klustret, där den Azure-delade disken distribueras.  

> [!IMPORTANT]
> Installationen måste uppfylla följande villkor:
> * Varje databas hanterings system (DBMS) SID måste ha ett eget dedikerat WSFC-kluster.  
> * SAP-programservrar som tillhör ett SAP-system-SID måste ha egna dedikerade virtuella datorer.  
> * Det finns inte stöd för en blandning av kötjänsten för Server 1 och Queue server 2 i samma kluster.  


## <a name="supported-os-versions"></a>OS-versioner som stöds

Både Windows Server 2016 och Windows Server 2019 stöds (Använd de senaste data Center avbildningarna).

Vi rekommenderar starkt att du använder **Windows Server 2019 Data Center** som:
- Windows 2019-kluster för växling vid fel är Azure medveten
- Vi har lagt till integrering och medvetenhet om underhåll av Azure-värd och bättre erfarenhet genom övervakning av Azure Schedule-händelser.
- Det går att använda det distribuerade nätverks namnet (det är standard alternativet). Det finns därför inget behov av att ha en dedikerad IP-adress för klustrets nätverks namn. Du behöver inte heller konfigurera den här IP-adressen på intern Azure-Load Balancer. 

## <a name="architecture"></a>Arkitektur

Både ERS1 (Queue Replication Server 1) och ERS2 (Queue Replication Server 2) stöds i multi-SID-konfiguration.  Det finns inte stöd för en blandning av ERS1 och ERS2 i samma kluster. 

1. I det första exemplet visas två SAP-sid: er, både med ERS1-arkitektur där:

   - SAP-SID1 distribueras på en delad disk med ERS1. ERS-instansen är installerad på den lokala värden och på den lokala enheten.
     SAP SID1 har en egen (virtuell) IP-adress (SID1 (A) SCS-IP1), som har kon figurer ATS på den interna Azure-belastningsutjämnaren.

   - SAP-SID2 distribueras på en delad disk med ERS1. ERS-instansen är installerad på den lokala värden och på den lokala enheten.
     SAP SID2 har en egen (virtuell) IP-adress (SID2 (A) SCS-IP2), som också har kon figurer ATS på den interna Azure Load Balancer.

![Hög tillgänglighet SAP ASCS/SCS-instans – två instanser med ERS1-konfiguration][sap-ha-guide-figure-6007]

2. I det andra exemplet visas två SAP-sid: er, både med ERS2-arkitektur där: 

   - SAP SID1 med ERS2, som också ingår i kluster och distribueras på den lokala enheten.  
     SAP SID1 har en egen (virtuell) IP-adress (SID1 (A) SCS-IP1), som har kon figurer ATS på den interna Azure-belastningsutjämnaren.
     SAP ERS2, som används av SAP SID1-systemet har sin egen (virtuella) IP-adress (SID1 ERS2 IP2), som är konfigurerad på den interna Azure-belastningsutjämnaren.

   - SAP SID2 med ERS2, som också ingår i kluster och distribueras på den lokala enheten.  
     SAP SID2 har en egen (virtuell) IP-adress (SID2 (A) SCS-IP3), som har kon figurer ATS på den interna Azure-belastningsutjämnaren.
     SAP ERS2, som används av SAP SID2-systemet har sin egen (virtuella) IP-adress (SID2 ERS2 IP4), som är konfigurerad på den interna Azure-belastningsutjämnaren.

   Här har vi totalt fyra virtuella IP-adresser:  
   - SID1 (A) SCS-IP1
   - SID2 ERS2-IP2
   - SID2 (A) SCS-IP3
   - SID2 ERS2-IP4

![Hög tillgänglighet SAP ASCS/SCS-instans – två instanser med ERS1 och ERS2-konfiguration][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Infrastruktur förberedelse

Vi installerar en ny SAP SID- **PR2**, förutom den **befintliga klustrade** SAP- **PR1** ASCS/SCS-instansen.  

### <a name="host-names-and-ip-addresses"></a>Värdnamn och IP-adresser

| Värd namns roll | Värdnamn | Statisk IP-adress | Tillgänglighetsuppsättning | Placerings grupp för närhet |
| --- | --- | --- |---| ---|
| första klusternoden ASCS/SCS-kluster |PR1-ASCs-10 |10.0.0.4 |PR1-ASCs-avset |PR1PPG |
| andra klusternoder ASCS/SCS-kluster |PR1-ASCs-11 |10.0.0.5 |PR1-ASCs-avset |PR1PPG |
| Kluster nätverks namn | pr1clust |10.0.0.42 (**endast** för Win 2016-kluster) | saknas | saknas |
| **SID1** Nätverks namn för ASCS-kluster | pr1-ascscl |10.0.0.43 | saknas | saknas |
| **SID1** ERS kluster nätverks namn (**endast** för ERS2) | pr1-erscl |10.0.0.44 | saknas | saknas |
| **SID2** Nätverks namn för ASCS-kluster | pr2-ascscl |10.0.0.45 | saknas | saknas |
| **SID2** ERS kluster nätverks namn (**endast** för ERS2) | pr1-erscl |10.0.0.46 | saknas | saknas |

### <a name="create-azure-internal-load-balancer"></a>Skapa intern Azure-belastningsutjämnare

SAP ASCS, SAP SCS och den nya SAP-ERS2 använder du virtuella värdnamn och virtuella IP-adresser. I Azure krävs en [belastningsutjämnare](../../../load-balancer/load-balancer-overview.md) för att använda en virtuell IP-adress. Vi rekommenderar starkt att du använder [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

Du måste lägga till konfigurationen till den befintliga belastningsutjämnaren för den andra SAP SID-ASCS/SCS/ERS instance **PR2**. Konfigurationen för de första SAP-SID- **PR1** bör redan finnas på plats.  

**En SCS-PR2 [instans nummer 02]**
- Konfiguration av klient del
    - Statisk ASCS/SCS IP- **10.0.0.45**
- Server dels konfiguration  
    Redan på plats – de virtuella datorerna har redan lagts till i backend-poolen, medan du konfigurerade för SAP SID- **PR1**
- Avsöknings port
    - Port 620 **nr** [**62002**] lämna standard alternativet för protokoll (TCP), intervall (5), tröskelvärde för fel (2)
- Belastnings Utjämnings regler
    - Om du använder Standard Load Balancer väljer du HA-portar
    - Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
        - 32 **nr** TCP [**3202**]
        - 36 **nr** TCP [**3602**]
        - 39 **nr** TCP [**3902**]
        - 81 **nr** TCP [**8102**]
        - 5 **nr** 13 TCP [**50213**]
        - 5 **nr** 14 TCP [**50214**]
        - 5 **nr** 16 TCP [**50216**]
        - Associera med **PR2** ASCS-frontend-IP, hälso avsökning och befintlig backend-pool.  

    - Kontrol lera att tids gränsen för inaktivitet (minuter) är inställd på max värdet 30 och att den flytande IP-adressen (direkt Server retur) är aktive rad.

**ERS2 PR2 [instance Number 12]** 

Eftersom ERS2 (Queue server 2) också är klustrad måste ERS2 virtuell IP-adress också konfigureras på Azure ILB förutom över SAP ASCS/SCS IP. Det här avsnittet gäller endast om du använder en arkitektur för Server 2 i Queue-replikering för **PR2**.  
- Ny klient dels konfiguration
    - Statisk SAP ERS2 IP- **10.0.0.46**

- Server dels konfiguration  
  De virtuella datorerna har redan lagts till i ILB-adresspoolen.  

- Ny avsöknings port
    - Port 621 **nr**  [**62112**] lämna standard alternativet för protokoll (TCP), intervall (5), tröskelvärde för fel (2)

- Nya regler för belastnings utjämning
    - Om du använder Standard Load Balancer väljer du HA-portar
    - Om du använder grundläggande Load Balancer skapa belastnings Utjämnings regler för följande portar
        - 32 **nr** TCP [**3212**]
        - 33 **nr** TCP [**3312**]
        - 5 **nr** 13 TCP [**51212**]
        - 5 **nr** 14 TCP [**51212**]
        - 5 **nr** 16 TCP [**51212**]
        - Associera med **PR2** ERS2-frontend-IP, hälso avsökning och befintlig backend-pool.  

    - Se till att tids gränsen för inaktivitet (minuter) är inställd på Max värde, t. ex. 30, och att flytande IP (direkt Server retur) är aktiverat.


### <a name="create-and-attach-second-azure-shared-disk"></a>Skapa och ansluta andra Azure-delade diskar

Kör det här kommandot på en av klusternoderna. Du måste justera värdena för resurs gruppen, Azure-regionen, SAPSID och så vidare.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formatera den delade disken med PowerShell
1. Hämta disk numret. Kör PowerShell-kommandona på en av noderna i klustret:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formatera disken. I det här exemplet är det disk nummer 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Kontrol lera att disken nu är synlig som en kluster disk.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Registrera disken i klustret.  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen

1. Skapa en DNS-post för det virtuella värd namnet för nya SAP ASCS/SCS-instansen i Windows DNS Manager.  
   IP-adressen som du tilldelar det virtuella värd namnet i DNS måste vara samma som den IP-adress som du tilldelade i Azure Load Balancer.  

   ![_Define DNS-posten för det virtuella SAP-ASCS/SCS-klustrets virtuella namn och IP-adress][sap-ha-guide-figure-6009]
 
   _Definiera DNS-posten för SAP ASCS/SCS-klustrets virtuella namn och IP-adress_

2. Om du använder SAP Queue server 2, som också är klustrad instans, måste du även reservera i DNS ett virtuellt värdnamn för ERS2. 
   IP-adressen som du tilldelar det virtuella värd namnet för ERS2 i DNS måste vara samma som den IP-adress som du tilldelade i Azure Load Balancer.  

   ![_Define DNS-posten för det virtuella SAP ERS2-klustrets namn och IP-adress][sap-ha-guide-figure-6010]
 
   _Definiera DNS-posten för det virtuella SAP ERS2-klustrets namn och IP-adress_

3. Om du vill definiera IP-adressen som är kopplad till det virtuella värd namnet väljer du **DNS Manager-**  >  **domän**.

   ![Nytt virtuellt namn och IP-adress för SAP ASCS/SCS och ERS2 kluster konfiguration][sap-ha-guide-figure-6011]

   _Nytt virtuellt namn och TCP/IP-adress för SAP ASCS/SCS och ERS2 kluster konfiguration_

## <a name="sap-installation"></a>SAP-installation 

### <a name="install-the-sap-first-cluster-node"></a>Installera den första SAP-klusternoden

Följ anvisningarna för att installera SAP. Se till att starta installations alternativet "första klusternod" och välj "Cluster Shared disk" som konfigurations alternativ.  
Välj den nyligen skapade delade disken.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Ändra SAP-profilen för ASCS/SCS-instansen

Om du kör Queue server 1 lägger du till SAP Profile-parametern `enque/encni/set_so_keepalive` enligt beskrivningen nedan. Profil parametern förhindrar anslutningar mellan SAP-arbetsprocesser och-servern från att stängas när de är inaktiva för länge. SAP-parametern krävs inte för ERS2. 

1. Lägg till den här profil parametern till instansen av SAP ASCS/SCS, om du använder ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Se till att `keepalive` OS-parametrarna är inställda enligt beskrivningen i SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)för både ERS1 och ERS2.   

2. Om du vill tillämpa ändringarna för parametern för SAP-profilen startar du om SAP ASCS/SCS-instansen.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Konfigurera avsöknings port på kluster resursen

Använd den interna belastningsutjämnaren för att göra så att hela kluster konfigurationen fungerar med Azure Load Balancer. Azures interna belastningsutjämnare distribuerar vanligt vis den inkommande arbets belastningen lika mellan deltagande virtuella datorer.

Detta fungerar dock inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiv och kan inte acceptera någon av arbets belastningarna. En avsöknings funktion hjälper när den interna Azure-belastningsutjämnaren identifierar vilken instans som är aktiv och endast är riktad mot den aktiva instansen.  

> [!IMPORTANT]
> I den här exempel konfigurationen är **ProbePort** inställd på 620 **nr**. För SAP ASCS-instans med Number **02** är 620 **02**.
> Du måste ändra konfigurationen så att den matchar dina SAP-instansnamn och SAP SID.

För att lägga till en avsöknings port kör den här PowerShell-modulen på en av de virtuella kluster datorerna:

- Vid SAP ASC/SCS-instans med instans nummer **02** 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Om du använder ERS2, med instans nummer **12**, som är klustrad. Det finns inget behov av att konfigurera avsöknings porten för ERS1, eftersom den inte är klustrad.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 Koden för funktionen `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` ser ut så här:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Fortsätt med SAP-installationen

1. Installera databas instansen genom att följa den process som beskrivs i installations guiden för SAP.  
2. Installera SAP på den andra klusternoden genom att följa stegen som beskrivs i installations guiden för SAP.  
3. Installera den primära SAP-instansen av Application Server (PAS) på den virtuella datorn som du har angivit som värd för PAS.   
   Följ processen som beskrivs i installations guiden för SAP. Det finns inga beroenden i Azure.
4. Installera ytterligare SAP-programservrar på de virtuella datorerna som är avsedda att vara värd för SAP Application Server-instanser.  
   Följ processen som beskrivs i installations guiden för SAP. Det finns inga beroenden i Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testa redundansväxling av SAP ASCS/SCS-instansen
För de utväxlade redundanstestningen-testerna antar vi att SAP ASCS är aktivt på nod A.  

1. Kontrol lera att SAP-systemet kan redundansväxla från nod A till nod B. I det här exemplet görs testet för SAPSID **PR2**.  
   Se till att varje SAPSID kan flyttas till den andra klusternoden.   
   Välj ett av dessa alternativ för att initiera en redundansväxling av SAP- \<SID\> klusterresursen från klusternod a till klusternod B:
    - Klusterhanteraren för växling vid fel  
    - PowerShell för redundanskluster

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Starta om klusternoden A i Windows gäst operativ system. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.  
3. Starta om klusternoden A från Azure Portal. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.  
4. Starta om klusternoden A med hjälp av Azure PowerShell. Detta initierar en automatisk redundansväxling av SAP- \<SID\> kluster gruppen från nod A till nod B.

## <a name="next-steps"></a>Nästa steg

* [Förbered Azure-infrastrukturen för SAP-HA med hjälp av ett Windows-redundanskluster och en delad disk för en SAP ASCS/SCS-instans][sap-high-availability-infrastructure-wsfc-shared-disk]
* [Installera SAP NetWeaver HA på ett Windows-redundanskluster och en delad disk för en SAP-ASCS/SCS-instans][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../availability.md