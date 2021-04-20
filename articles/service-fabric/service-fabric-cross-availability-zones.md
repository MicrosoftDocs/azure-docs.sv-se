---
title: Distribuera ett kluster över Tillgänglighetszoner
description: Lär dig hur du skapar ett Azure Service Fabric-kluster i Tillgänglighetszoner.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727770"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuera ett Azure Service Fabric-kluster över Tillgänglighetszoner
Tillgänglighetszoner i Azure är ett erbjudande med hög tillgänglighet som skyddar dina program och data mot datacenterfel. En tillgänglighetszon är en unik fysisk plats med oberoende strömförsörjning, kylning och nätverk i en Azure-region.

Service Fabric stöder kluster som sträcker sig över Tillgänglighetszoner genom att distribuera nodtyper som är fästa på specifika zoner. Detta säkerställer hög tillgänglighet för dina program. Azure-tillgänglighetszoner är endast tillgängliga i utvalda regioner. Mer information finns i [Azure-tillgänglighetszoner Översikt.](../availability-zones/az-overview.md)

Exempelmallar är tillgängliga: [Service Fabric mellan tillgänglighetszoner](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Rekommenderad topologi för den primära nodtypen för Azure Service Fabric-kluster som sträcker sig över Tillgänglighetszoner
Ett Service Fabric kluster som distribueras över Tillgänglighetszoner säkerställer hög tillgänglighet för klustertillståndet. Om du vill Service Fabric ett kluster mellan zoner måste du skapa en primär nodtyp i varje tillgänglighetszon som stöds av regionen. Detta distribuerar start start startnoder jämnt över var och en av de primära nodtyperna.

Den rekommenderade topologin för den primära nodtypen kräver de resurser som beskrivs nedan:

* Klustrets tillförlitlighetsnivå inställd påString.
* Tre nodtyper markerade som primära.
    * Varje nodtyp ska mappas till en egen VM-skalningsuppsättning som finns i olika zoner.
    * Varje VM-skalningsuppsättning bör ha minst fem noder (silverbeständighet).
* En enskild offentlig IP-resurs med standard-SKU.
* En resurs Load Balancer standard-SKU.
* En NSG som refereras av undernätet där du distribuerar dina VM-skalningsuppsättningar.

>[!NOTE]
> Egenskapen för en enskild placeringsgrupp för VM-skalningsuppsättningen måste anges till true.

Diagram som visar arkitekturdiagram för Azure Service Fabric ![ tillgänglighetszon som visar arkitekturen för Azure Service Fabric tillgänglighetszon.][sf-architecture]

Exempelnodlista som illustrerar FD-/UD-format i en VM-skalningsuppsättning som sträcker sig över zoner

 ![Exempelnodlista som visar FD/UD-format i en VM-skalningsuppsättning som sträcker sig över zoner.][sf-multi-az-nodes]

**Distribution av tjänstrepliker** mellan zoner: När en tjänst distribueras på nodeTypes som sträcker sig över zoner placeras replikerna för att säkerställa att de hamnar i separata zoner. Detta säkerställs eftersom feldomänen på noderna som finns i var och en av dessa nodeTypes konfigureras med zoninformationen (dvs. FD = fd:/zone1/1 osv.). Exempel: för 5 repliker eller instanser av en tjänst är distributionen 2-2-1 och körningen försöker se till att fördelningen mellan tillgänglighetsplatser är lika.

**Konfiguration av användartjänstreplik:** Tillståndsful-användartjänster som distribuerats på nodtyperna i zonen Korstillgänglighet bör konfigureras med den här konfigurationen: replikantal med mål = 9, min = 5. Den här konfigurationen hjälper tjänsten att fungera även när en zon går ned eftersom 6 repliker fortfarande är upp i de andra två zonerna. En programuppgradering i ett sådant scenario går också igenom.

**Cluster ReliabilityLevel:** Detta definierar antalet startnoder i klustret och även replikstorleken för systemtjänsterna. Eftersom en zonkonfiguration för flera tillgänglighetszoner har ett högre antal noder, som är fördelade mellan zoner för att aktivera zonåter återhämtning, säkerställer ett högre tillförlitlighetsvärde att noden har fler startvärdesnoder och systemtjänstrepliker finns och är jämnt fördelade mellan zoner, så att klustret och systemtjänsterna inte implementeras vid ett zonfel. "ReliabilityLevel = Seed" ser till att det finns 9 startvärdesnoder fördelade på zoner i klustret med 3 kärnor i varje zon, vilket är det som rekommenderas för konfigurationen av zonen korstillgänglighet.

**Scenario med nedzon:** När en zon går ned visas alla noder i den zonen som nere. Tjänstrepliker på dessa noder kommer också att vara nere. Eftersom det finns repliker i de andra zonerna fortsätter tjänsten att svara med primära repliker som går över till de zoner som fungerar. Tjänsterna visas i varningstillstånd eftersom antalet målrepliker ännu inte har uppnåtts och eftersom antalet virtuella datorer fortfarande är större än den minsta målreplikstorleken. Därefter Service Fabric lastbalanserare repliker i arbetszonerna för att matcha antalet konfigurerade målrepliker. I det här läget visas tjänsterna som felfria. När zonen som var nere kommer tillbaka upp i belastningsutjämning kommer återigen att sprida alla tjänstrepliker jämnt över alla zoner.

## <a name="networking-requirements"></a>Nätverkskrav
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP och Load Balancer resurs
Om du vill aktivera zonegenskapen för en VM-skalningsuppsättningsresurs måste både lastbalanseraren och IP-resursen som vm-skalningsuppsättningen refererar till använda en standard-SKU.  Om du skapar en lastbalanserare eller IP-resurs utan SKU-egenskapen skapas en Basic-SKU som inte stöder Tillgänglighetszoner. En standard-SKU-lastbalanserare blockerar all trafik utifrån som standard. För att tillåta extern trafik måste en NSG distribueras till undernätet.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Det går inte att göra en ändring på plats av SKU på de offentliga IP- och lastbalanseringsresurserna. Om du migrerar från befintliga resurser som har en grundläggande SKU kan du läsa migreringsavsnittet i den här artikeln.

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regler för VM-skalningsuppsättning
Inkommande NAT-regler för lastbalanserare ska matcha NAT-poolerna från VM-skalningsuppsättningen. Varje VM-skalningsuppsättning måste ha en unik inkommande NAT-pool.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardregler för Load Balancer utgående trafik
Standard Load Balancer och offentlig IP-standard introducerar nya funktioner och olika beteenden för utgående anslutningar jämfört med att använda grundläggande SKU:er. Om du vill ha utgående anslutningar när du arbetar med standard-SKU:er måste du uttryckligen definiera den med offentliga IP-standardadresser eller offentliga standard-Load Balancer. Mer information finns i [Utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) och [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standardmallen refererar till en NSG som tillåter all utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för Service Fabric-hanteringsåtgärder. NSG-reglerna kan ändras för att uppfylla dina krav.

>[!NOTE]
> Alla Service Fabric kluster som använder en standard-SKU SLB måste se till att varje nodtyp har en regel som tillåter utgående trafik på port 443. Detta är nödvändigt för att slutföra klusterinstallationen och distribution utan en sådan regel kommer att misslyckas.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Aktivera zoner på en VM-skalningsuppsättning
Om du vill aktivera en zon måste du på en VM-skalningsuppsättning inkludera följande tre värden i resursen för VM-skalningsuppsättningen.

* Det första värdet är egenskapen **zones,** som anger vilken tillgänglighetszon som VM-skalningsuppsättningen ska distribueras till.
* Det andra värdet är egenskapen "singlePlacementGroup", som måste anges till true.
* Det tredje värdet är egenskapen "faultDomainOverride" i Service Fabric vm-skalningsuppsättningstillägget. Värdet för den här egenskapen ska endast innehålla den zon där vm-skalningsuppsättningen ska placeras. Exempel: "faultDomainOverride": "az1" Alla resurser för VM-skalningsuppsättningen måste placeras i samma region eftersom Azure Service Fabric-kluster inte har stöd för flera regioner.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Aktivera flera primära nodtyper i Service Fabric klusterresursen
Om du vill ange en eller flera nodtyper som primära i en klusterresurs anger du egenskapen "isPrimary" till "true". När du distribuerar Service Fabric kluster Tillgänglighetszoner bör du ha tre nodtyper i distinkta zoner.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrera till med Tillgänglighetszoner från ett kluster med hjälp av en Basic SKU-Load Balancer och en Grundläggande SKU-IP
Om du vill migrera ett kluster, som använde en Load Balancer och IP med en grundläggande SKU, måste du först skapa en helt ny Load Balancer- och IP-resurs med hjälp av standard-SKU:n. Det går inte att uppdatera dessa resurser på plats.

Den nya lb- och IP-adressen ska refereras till i de nya nodtyperna för flera tillgänglighetszoner som du vill använda. I exemplet ovan har tre nya resurser för VM-skalningsuppsättning lagts till i zonerna 1,2 och 3. Dessa VM-skalningsuppsättningar refererar till den nyligen skapade lb- och IP-adressen och markeras som primära nodtyper i Service Fabric klusterresursen.

För att komma igång måste du lägga till de nya resurserna i din Resource Manager mall. Dessa resurser är:
* En offentlig IP-resurs med standard-SKU.
* En Load Balancer resurs med standard-SKU.
* En NSG som refereras av undernätet där du distribuerar dina VM-skalningsuppsättningar.
* Tre nodtyper markerade som primära.
    * Varje nodtyp ska mappas till en egen VM-skalningsuppsättning som finns i olika zoner.
    * Varje VM-skalningsuppsättning bör ha minst fem noder (silverbeständighet).

Ett exempel på dessa resurser finns i [exempelmallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

När resurserna har distribuerats kan du börja inaktivera noderna i den primära nodtypen från det ursprungliga klustret. När noderna är inaktiverade migrerar systemtjänsterna till den nya primära nodtypen som hade distribuerats i steget ovan.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

När noderna är inaktiverade körs systemtjänsterna på den primära nodtypen, som är utspridd över zoner. Du kan sedan ta bort de inaktiverade noderna från klustret. När noderna har tagits bort kan du ta bort den ursprungliga IP Load Balancer och VM-skalningsuppsättningen.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Du bör sedan ta bort referenserna till dessa resurser från Resource Manager som du har distribuerat.

Det sista steget handlar om att uppdatera DNS-namnet och den offentliga IP-adressen.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>(Förhandsversion) Aktivera flera tillgänglighetszoner i en vm-skalningsuppsättning

Den tidigare nämnda lösningen använder en nodeType per AZ. Med följande lösning kan användarna distribuera 3 AZ:er i samma nodeType.

**Eftersom den här funktionen för närvarande är i förhandsversion stöds den inte för produktionsscenarier.**

Fullständig exempelmall finns [här.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)

![Arkitektur för Azure Service Fabric tillgänglighetszon][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Konfigurera zoner på en VM-skalningsuppsättning
Om du vill aktivera zoner på en VM-skalningsuppsättning måste du inkludera följande tre värden i resursen för VM-skalningsuppsättningen.

* Det första värdet är **zonegenskapen,** som anger Tillgänglighetszoner finns i VM-skalningsuppsättningen.
* Det andra värdet är egenskapen "singlePlacementGroup", som måste anges till true. **Skalningsuppsättningen sträcker sig över 3 AZ:er kan skala upp till 300 virtuella datorer även med "singlePlacementGroup = true".**
* Det tredje värdet är "zoneBalance", vilket garanterar strikt zonbalansering. Detta bör vara "true". Detta säkerställer att de virtuella datorernas distributioner mellan zoner inte är obalanserade, vilket säkerställer att när en av zonerna kraschar har de andra två zonerna tillräckligt med virtuella datorer för att säkerställa att klustret fortsätter att köras utan avbrott. Ett kluster med en obalanserad VM-distribution kanske inte klarar ett scenario med nedströmszon eftersom zonen kan ha majoriteten av de virtuella datorerna. Obalanserad distribution av virtuella datorer mellan zoner leder också till problem som rör tjänstplacering & uppdateringar av infrastrukturen fastnar.. Läs mer [om zoneBalancing](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing).
* Åsidosättningar av FaultDomain och UpgradeDomain måste inte konfigureras.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric ska ha minst en Primär nodeType. DurabilityLevel för primära nodeTypes ska vara Silver eller högre.**
> * AZ-skalningsuppsättningen för virtuella datorer ska konfigureras med minst 3 tillgänglighetszoner oavsett hållbarhetNivå.
> * AZ som sträcker sig över VM-skalningsuppsättning med Silver-hållbarhet (eller högre) bör ha minst 15 virtuella datorer.
> * AZ som sträcker sig över VM-skalningsuppsättning med bronsbeständighet bör ha minst 6 virtuella datorer.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Aktivera stöd för flera zoner i Service Fabric nodeType
Nodtypen Service Fabric vara aktiverad för att stödja flera tillgänglighetszoner.

* Det första värdet är **multipleAvailabilityZones** som ska anges till true för nodeType.
* Det andra värdet är **sfZonalUpgradeMode och** är valfritt. Den här egenskapen kan inte ändras om en nodtyp med flera AZ:er redan finns i klustret.
  Egenskapen styr den logiska grupperingen av virtuella datorer i uppgraderingsdomäner.
  **Om värdet är inställt på "Parallel":** Virtuella datorer under nodetype grupperas i NAD:er och zoninformationen ignoreras i 5 NA. Detta resulterar i UD0 i alla zoner för att uppgraderas samtidigt. Det här distributionsläget är snabbare för uppgraderingar, men rekommenderas inte eftersom det går emot riktlinjerna för SDP, där det står att uppdateringarna endast ska tillämpas en zon i taget.
  **Om värdet utelämnas eller anges till "Hierarkiskt":** Virtuella datorer grupperas för att återspegla zonfördelningen i upp till 15 NA. Var och en av de 3 zonerna har 5 NA. Detta säkerställer att uppdateringarna går zonvis och flyttas till nästa zon först efter att du har slutfört 5 NA inom den första zonen, långsamt över 15 UD:er (3 zoner, 5 NA), vilket är säkrare ur klustrets och användarprogrammets perspektiv.
  Den här egenskapen definierar endast uppgraderingsbeteendet för ServiceFabric-program och koduppgraderingar. De underliggande uppgraderingarna av VM-skalningsuppsättningen kommer fortfarande att vara parallella i alla AZ:er.
  Den här egenskapen påverkar inte UD-distributionen för nodtyper som inte har flera zoner aktiverade.
* Det tredje värdet är **vmssZonalUpgradeMode = Parallel**. Det här är *en* obligatorisk egenskap som ska konfigureras i klustret om en nodeType med flera AZ har lagts till. Den här egenskapen definierar uppgraderingsläget för uppdateringar av VM-skalningsuppsättningen, vilket sker parallellt i alla AZ:er samtidigt.
  Just nu kan den här egenskapen bara anges som parallell.
* Den Service Fabric klusterresursen apiVersion ska vara "2020-12-01-preview" eller högre.
* Klusterkodversionen ska vara "7.2.445" eller högre.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Offentliga IP- och Load Balancer resurser bör använda standard-SKU:n enligt beskrivningen tidigare i artikeln.
> * Egenskapen "multipleAvailabilityZones" på nodeType kan bara definieras när nodeType skapas och kan inte ändras senare. Befintliga nodeTypes kan därför inte konfigureras med den här egenskapen.
> * När "sfZonalUpgradeMode" utelämnas eller anges till "Hierarkisk" blir kluster- och programdistributionerna långsammare eftersom det finns fler uppgraderingsdomäner i klustret. Det är viktigt att justera tidsgränserna för uppgraderingsprincipen korrekt för att inkludera för uppgraderingstiden för 15 uppgraderingsdomäner. Uppgraderingsprincipen för både appen och klustret bör uppdateras för att säkerställa att distributionen inte överskrider tidsgränserna för Azure Resource Seriqe-distributionen på 12 timmar. Det innebär att distributionen inte får ta mer än 12 timmar för 15UDs, dvs. bör inte ta mer än 40 min/UD.
> * Ange cluster **reliabilityLevel = Script för** att se till att klustret klarar scenariot med en zon nedåt.

>[!NOTE]
> För bästa praxis rekommenderar vi att sfZonalUpgradeMode anges till Hierarkisk eller utelämnas. Distributionen följer zonfördelningen av virtuella datorer som påverkar en mindre mängd repliker och/eller instanser vilket gör dem säkrare.
> Använd sfZonalUpgradeMode inställt på Parallel om distributionshastighet är en prioritet eller bara tillståndslös arbetsbelastning körs på nodtypen med flera AZ:er. Detta leder till att UD-genomgången sker parallellt i alla AZ:er.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Migrering till nodtypen med flera Tillgänglighetszoner
För alla migreringsscenarier måste en ny nodeType läggas till som har flera tillgänglighetszoner som stöds. En befintlig nodeType kan inte migreras för att stödja flera zoner.
Artikeln här [innehåller detaljerade](./service-fabric-scale-up-primary-node-type.md) steg för att lägga till en ny nodeType och även lägga till de andra resurser som krävs för den nya nodeType som IP- och LB-resurserna. I samma artikel beskrivs nu även att dra tillbaka den befintliga nodeType efter att nodeType med flera tillgänglighetszoner har lagts till i klustret.

* Migrering från en nodeType som använder grundläggande LB- [](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) och IP-resurser: Detta beskrivs redan här för lösningen med en nodtyp per AZ. 
    För den nya nodtypen är den enda skillnaden att det bara finns 1 VM-skalningsuppsättning och 1 nodtyp för alla AZ:er i stället för 1 per AZ.
* Migrering från en nodeType som använder standard-SKU LB och IP-resurser med NSG: Följ samma procedur som beskrivs ovan med undantaget att det inte finns något behov av att lägga till nya resurser för lb, IP och NSG, och samma resurser kan återanvändas i den nya nodeType.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
