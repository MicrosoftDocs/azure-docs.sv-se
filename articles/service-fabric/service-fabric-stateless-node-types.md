---
title: Distribuera tillstånds lösa – endast nodtyper i ett Service Fabric kluster
description: Lär dig hur du skapar och distribuerar tillstånds lösa nodtyper i Azure Service Fabric-kluster.
author: peterpogorski
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: pepogors
ms.openlocfilehash: 74680f7b56ad98851e2839b53c1f9e92b6c6c23a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030022"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Distribuera ett Azure Service Fabric-kluster med enbart tillstånds lösa nodtyper
Service Fabric Node-typer levereras med ett förutsättnings antagande som vid en viss tidpunkt kan tillstånds känsliga tjänster placeras på noderna. Tillstånds lösa nodtyper sänker detta antagande för en nodtyp, vilket innebär att nodtypen kan använda andra funktioner, till exempel snabbare skalnings åtgärder, stöd för automatiska operativ system uppgraderingar på brons-hållbarhet och skalbarhet till fler än 100 noder i en enda skalnings uppsättning för virtuella datorer.

* Det går inte att konfigurera primära nodtyper till tillstånds lösa
* Tillstånds lösa nodtyper stöds bara med brons hållbarhets nivåer
* Tillstånds lösa nodtyper stöds endast på Service Fabric runtime-version 7.1.409 eller senare.


Exempel på mallar är tillgängliga: [Service Fabric mall för tillstånds löst Node types](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Aktivera tillstånds lösa nodtyper i Service Fabric kluster
Om du vill ange en eller flera nodtyper som tillstånds lösa i en kluster resurs anger du egenskapen **isStateless** till **True**. När du distribuerar ett Service Fabric kluster med tillstånds lösa nodtyper måste du komma ihåg att ha minst en primär nodtyp i kluster resursen.

* Service Fabric kluster resursens API version ska vara "2020-12-01-för hands version" eller högre.

```json
{
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
        "isStateless": false, // Primary Node Types cannot be stateless
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Bronze",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": false,
        "isStateless": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    }    
    ],
}
```

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Konfigurera skalnings uppsättning för tillstånds lösa noder för virtuella datorer
Om du vill aktivera tillstånds lösa nodtyper bör du konfigurera den underliggande resursen för skalnings uppsättningar för virtuella datorer på följande sätt:

* Egenskapen Value  **singlePlacementGroup** , som ska anges till **false** om du behöver skala till fler än 100 virtuella datorer.
* Skalnings uppsättningens **upgradeMode** ska vara inställd på **rullande**.
* Läget för löpande uppgradering kräver att ett program hälso tillägg eller hälso avsökning har kon figurer ATS. Konfigurera hälso avsökningen med standard konfiguration för tillstånds lösa nodtyper enligt rekommendationerna nedan. När program har distribuerats till nodtypen kan hälso avsökningen/hälso tilläggets portar ändras för att övervaka program hälsan.

>[!NOTE]
> När funktionen för automatisk skalning används med tillstånds lösa nodetypes, rensas inte nodens tillstånd automatiskt. För att rensa NodeState i noderna under autoskalning bör du använda [Service Fabric autoskalning-hjälpen](https://github.com/Azure/service-fabric-autoscale-helper) .

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Rolling",
          "automaticOSUpgradePolicy": {
            "enableAutomaticOSUpgrade": true
          }
        },
        "platformFaultDomainCount": 5
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
            "durabilityLevel": "Bronze",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
        },
        "typeHandlerVersion": "1.0"
    }
    },
    {
        "type": "extensions",
        "name": "HealthExtension",
        "properties": {
            "publisher": "Microsoft.ManagedServices",
            "type": "ApplicationHealthWindows",
            "autoUpgradeMinorVersion": true,
            "typeHandlerVersion": "1.0",
            "settings": {
            "protocol": "tcp",
            "port": "19000"
            }
            }
        },
    ]
}
```

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Konfigurera tillstånds lösa nodtyper med flera Tillgänglighetszoner
Om du vill konfigurera tillstånds lös NodeType som sträcker sig över flera tillgänglighets zoner följer du dokumentationen [här](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set), tillsammans med några ändringar enligt följande:

* Ange **singlePlacementGroup** :  **false**  om flera placerings grupper måste vara aktiverade.
* Ange  **upgradeMode** : **rullande**   och lägga till tillägg för program hälso tillägg/hälso avsökningar som anges ovan.
* Ange **platformFaultDomainCount** : **5** för skalnings uppsättning för virtuell dator.

>[!NOTE]
> Oavsett vilka VMSSZonalUpgradeMode som kon figurer ATS i klustret sker uppdateringar av skalnings uppsättningar för virtuella datorer alltid i turordning en tillgänglighets zon i taget för den tillstånds lösa NodeType som sträcker sig över flera zoner, eftersom den använder rullande uppgraderings läge.

För referens tittar du på [mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) för att konfigurera tillstånds lösa nodtyper med flera Tillgänglighetszoner

## <a name="networking-requirements"></a>Nätverkskrav
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP-adress och Load Balancer resurs
Om du vill aktivera skalning till fler än 100 virtuella datorer på en resurs för skalnings uppsättning för virtuella datorer måste den belastningsutjämnare och IP-resurs som refereras av den virtuella datorns skalnings uppsättning både använda en *standard* -SKU. Om du skapar en belastningsutjämnare eller en IP-resurs utan SKU-egenskapen skapas en grundläggande SKU, som inte stöder skalning till fler än 100 virtuella datorer. En standard-SKU-belastningsutjämnare blockerar all trafik från utsidan som standard. Om du vill tillåta yttre trafik måste en NSG distribueras till under nätet.

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
> Det går inte att göra en förändring på plats av SKU: n på den offentliga IP-adressen och belastnings Utjämnings resurserna. 

### <a name="virtual-machine-scale-set-nat-rules"></a>NAT-regler för skalnings uppsättning för virtuell dator
Belastnings utjämningens inkommande NAT-regler ska matcha NAT-poolerna från den virtuella datorns skal uppsättning. Varje skalnings uppsättning för virtuella datorer måste ha en unik inkommande NAT-pool.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard-SKU Load Balancer utgående regler
Standard Load Balancer och standard offentlig IP introducerar nya funktioner och olika beteenden för utgående anslutning jämfört med att använda Basic SKU: er. Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer. Mer information finns i [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) och [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standard mal len refererar till en NSG som tillåter all utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för Service Fabric hanterings åtgärder. NSG-reglerna kan ändras för att uppfylla dina krav.

>[!NOTE]
> Alla Service Fabric kluster som använder en standard-SKU SLB måste se till att varje nodtyp har en regel som tillåter utgående trafik på port 443. Detta är nödvändigt för att slutföra kluster installationen och alla distributioner utan sådan regel kommer att Miss lyckas.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrera till att använda tillstånds lösa nodtyper i ett kluster
För alla migreringsåtgärder måste en ny tillstånds lös nodtyp läggas till. Det går inte att migrera den befintliga nodtypen till endast tillstånds lös läge.

Om du vill migrera ett kluster som använde en Load Balancer och en IP-adress med en grundläggande SKU måste du först skapa en helt ny Load Balancer och IP-resurs med standard-SKU: n. Det går inte att uppdatera resurserna på plats.

Den nya LB och IP ska refereras till i de nya tillstånds lösa nodtyper som du vill använda. I exemplet ovan läggs en ny virtuell dators skalnings uppsättnings resurser till som ska användas för tillstånds lösa nodtyper. De här referenserna för den virtuella datorns skalnings uppsättningar innehåller den nyligen skapade LB och IP och har marker ATS som tillstånds lösa nodtyper i Service Fabric kluster resursen.

För att börja måste du lägga till de nya resurserna i din befintliga Resource Manager-mall. Dessa resurser omfattar:
* En offentlig IP-resurs med standard-SKU.
* En Load Balancer resurs med standard-SKU.
* En NSG som refereras till av under nätet som du distribuerar dina skalnings uppsättningar för virtuella datorer i.

När resurserna har distribuerats kan du börja inaktivera noderna i nodtypen som du vill ta bort från det ursprungliga klustret.

## <a name="next-steps"></a>Nästa steg 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nodtyper och skaluppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)

