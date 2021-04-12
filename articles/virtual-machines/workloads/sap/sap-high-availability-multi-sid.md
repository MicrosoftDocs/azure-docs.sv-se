---
title: Skapa SAP multi-SID-konfiguration med hög tillgänglighet i Azure
description: Lär dig hur du skapar en hög tillgänglig SAP NetWeaver-konfiguration med flera SID på virtuella Windows-datorer.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 079ac273f7b2aa91b61ad2cdbcfe62cd989bb2f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675268"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Skapa en hög tillgänglig SAP NetWeaver multi-SID-konfiguration

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

I september 2016 släppte Microsoft en funktion där du kan hantera flera virtuella IP-adresser med hjälp av en intern Azure-belastningsutjämnare. Den här funktionen finns redan i den externa Azure-belastningsutjämnaren.

Om du har en SAP-distribution kan du använda en intern belastningsutjämnare för att skapa en Windows-kluster konfiguration för SAP ASCS/SCS, enligt beskrivningen i [hand boken för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide].

Den här artikeln fokuserar på hur du flyttar från en enda ASCS/SCS-installation till en SAP multi-SID-konfiguration genom att installera ytterligare SAP ASCS/SCS-klustrade instanser i ett befintligt WSFC-kluster (Windows Server Failover Clustering). När den här processen har slutförts har du konfigurerat ett SAP multi-SID-kluster.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
Du har redan konfigurerat ett WSFC-kluster som används för en SAP ASCS/SCS-instans, enligt beskrivningen i [hand boken för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide] och som visas i det här diagrammet.

![Hög tillgänglighet SAP ASCS/SCS-instans][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Mål arkitektur

Målet är att installera flera SAP ABAP-ASCS eller SAP Java SCS-klustrade instanser i samma WSFC-kluster som visas här:

![Flera SAP ASCS-/SCS-klustrade instanser i Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Det finns en gräns för antalet privata klient dels-IP: er för varje Azure Internal belastningsutjämnare.
>
>Det maximala antalet SAP ASCS/SCS-instanser i ett WSFC-kluster är lika med det maximala antalet privata klient dels-IP-adresser för varje Azure intern belastningsutjämnare.
>

Mer information om gränser för belastnings utjämning finns i "privat klientens IP-adress per belastningsutjämnare" i [nätverks gränser: Azure Resource Manager][networking-limits-azure-resource-manager].

Det fullständiga landskapet med två SAP-system med hög tillgänglighet skulle se ut så här:

![SAP med hög tillgänglighet för multi-SID-installation med två SAP-system-sid][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Installationen måste uppfylla följande villkor:
> - SAP ASCS/SCS-instanserna måste dela samma WSFC-kluster.
> - Varje DBMS-SID måste ha ett eget dedikerat WSFC-kluster.
> - SAP-programservrar som tillhör ett SAP-system-SID måste ha egna dedikerade virtuella datorer.


## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen
För att förbereda infrastrukturen kan du installera ytterligare en SAP ASCS/SCS-instans med följande parametrar:

| Parameternamn | Värde |
| --- | --- |
| SAP-ASCS/SCS-SID |PR1 – lb-ASCs |
| Intern belastningsutjämnare för SAP-DBMS | PR5 |
| Namn på SAP-virtuell värd | PR5 – SAP-cl |
| IP-adress för SAP ASCS/SCS virtuell värd (ytterligare Azure Load Balancer IP-adress) | 10.0.0.50 |
| Instans nummer för SAP ASCS/SCS | 50 |
| ILB avsöknings port för ytterligare SAP ASCS/SCS-instans | 62350 |

> [!NOTE]
> För SAP ASCS/SCS-kluster instanser kräver varje IP-adress en unik avsöknings port. Om en IP-adress i en intern belastningsutjämnare i Azure exempelvis använder avsöknings port 62300, kan ingen annan IP-adress på belastningsutjämnaren använda avsöknings port 62300.
>
>I vårt syfte, eftersom avsöknings port 62300 redan är reserverad, använder vi avsöknings port 62350.

Du kan installera ytterligare SAP ASCS/SCS-instanser i det befintliga WSFC-klustret med två noder:

| Rollen virtuell dator | Värddator namn för virtuell dator | Statisk IP-adress |
| --- | --- | --- |
| första klusternoden för ASCS/SCS-instans |PR1-ASCs-0 |10.0.0.10 |
| andra klusternoden för ASCS/SCS-instans |PR1-ASCs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Skapa ett virtuellt värdnamn för den klustrade SAP ASCS/SCS-instansen på DNS-servern

Du kan skapa en DNS-post för det virtuella värd namnet för ASCS/SCS-instansen med hjälp av följande parametrar:

| Nytt SAP ASCS/SCS Virtual Host-namn | Tillhör ande IP-adress |
| --- | --- |
|PR5 – SAP-cl |10.0.0.50 |

Det nya värd namnet och IP-adressen visas i DNS-hanteraren, som visas på följande skärm bild:

![DNS-hanterarens lista har markerat den definierade DNS-posten för det nya SAP ASCS/SCS-klustrets virtuella namn och TCP/IP-adress][sap-ha-guide-figure-6004]

Proceduren för att skapa en DNS-post beskrivs också i detalj i huvud [guiden för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide-9.1.1].

> [!NOTE]
> Den nya IP-adressen som du tilldelar det virtuella värd namnet för den ytterligare ASCS/SCS-instansen måste vara samma som den nya IP-adressen som du tilldelade till SAP Azure Load Balancer.
>
>I vårt scenario är IP-adressen 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Lägga till en IP-adress till en befintlig Azure-intern belastningsutjämnare med hjälp av PowerShell

Om du vill skapa fler än en SAP ASCS/SCS-instans i samma WSFC-kluster, använder du PowerShell för att lägga till en IP-adress i en befintlig intern belastningsutjämnare i Azure. Varje IP-adress kräver egna regler för belastnings utjämning, avsöknings port, frontend IP-pool och backend-pool.

Följande skript lägger till en ny IP-adress till en befintlig belastningsutjämnare. Uppdatera PowerShell-variablerna för din miljö. Skriptet skapar alla nödvändiga regler för belastnings utjämning för alla SAP ASCS/SCS-portar.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
När skriptet har körts visas resultaten i Azure Portal, som du ser i följande skärm bild:

![Ny IP-adresspool på klient sidan i Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lägg till diskar i kluster datorer och konfigurera SIOS-kluster delnings disk

Du måste lägga till en ny kluster resurs disk för varje ytterligare SAP ASCS/SCS-instans. För Windows Server 2012 R2 är WSFC-klustrets resurs disk som används för närvarande SIOS DataKeeper-programlösningen.

Gör följande:
1. Lägg till ytterligare en disk eller diskar av samma storlek (som du måste Stripa) till var och en av klusternoderna och formatera dem.
2. Konfigurera Storage Replication med SIOS DataKeeper.

Den här proceduren förutsätter att du redan har installerat SIOS-DataKeeper på WSFC-klustrets datorer. Om du har installerat den måste du nu konfigurera replikering mellan datorerna. Processen beskrivs i detalj i huvud [hand boken för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide-8.12.3.3].  

![DataKeeper synkron spegling för den nya SAP-ASCS/SCS-resursen][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Distribuera virtuella datorer för SAP-program servrar och DBMS-kluster

För att slutföra förberedelsen av infrastrukturen för det andra SAP-systemet gör du följande:

1. Distribuera dedikerade virtuella datorer för SAP-program servrar och placera dem i sin egen dedikerade tillgänglighets grupp.
2. Distribuera dedikerade virtuella datorer för DBMS-kluster och placera dem i sin egen dedikerade tillgänglighets grupp.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Installera det andra SAP SID2 NetWeaver-systemet

Den fullständiga processen för att installera ett andra SAP SID2-system beskrivs i huvud [guiden för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide-9].

Den övergripande proceduren är följande:

1. [Installera den första SAP-klusternoden][sap-ha-guide-9.1.2].  
 I det här steget installerar du SAP med en ASCS/SCS-instans med hög tillgänglighet på den **befintliga WSFC-klusternoden 1**.

2. [Ändra SAP-profilen för ASCS/SCS-instansen][sap-ha-guide-9.1.3].

3. [Konfigurera en avsöknings port][sap-ha-guide-9.1.4].  
 I det här steget konfigurerar du en SAP-kluster resurs SAP-SID2-IP-avsöknings port med hjälp av PowerShell. Kör den här konfigurationen på en av SAP ASCS/SCS-klusternoderna.

4. [Installera databas instansen] [SAP-ha-guide-9,2].  
 I det här steget installerar du DBMS på ett dedikerat WSFC-kluster.

5. [Installera den andra klusternoden] [SAP-ha-guide-9,3].  
 I det här steget installerar du SAP med en ASCS/SCS-instans med hög tillgänglighet på den befintliga WSFC-klusternoden 2.

6. Öppna Windows brand Väggs portar för SAP ASCS/SCS-instansen och ProbePort.  
 På båda klusternoder som används för SAP ASCS/SCS-instanser öppnar du alla Windows-brandväggens portar som används av SAP ASCS/SCS. Dessa portar visas i [hand boken för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide-8.8].  
 Öppna även avsöknings porten för den interna Azure-belastningsutjämnaren, som är 62350 i vårt scenario.

7. [Ändra starttyp för SAP ers Windows-][sap-ha-guide-9.4]tjänstinstans.

8. [Installera den primära SAP-programservern][sap-ha-guide-9.5] på den nya dedikerade virtuella datorn.

9. [Installera den ytterligare SAP-programservern][sap-ha-guide-9.6] på den nya dedikerade virtuella datorn.

10. [Testa replikeringen av SAP ASCS/SCS-instansen och SIOS-replikeringen][sap-ha-guide-10].

## <a name="next-steps"></a>Nästa steg

- [Nätverks gränser: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Flera VIP-adresser för Azure Load Balancer][load-balancer-multivip-overview]
- [Guide för SAP-NetWeaver med hög tillgänglighet på virtuella Windows-datorer][sap-ha-guide]
