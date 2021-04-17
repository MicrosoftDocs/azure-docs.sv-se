---
title: Distribuera endast tillståndslösa nodtyper i ett Service Fabric kluster
description: Lär dig hur du skapar och distribuerar tillståndslösa nodtyper i Azure Service Fabric kluster.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 68c617b6e9345910bfd913e61e227a8e6c401bbc
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576048"
---
# <a name="deploy-an-azure-service-fabric-cluster-with-stateless-only-node-types"></a>Distribuera ett Azure Service Fabric-kluster med tillståndslösa nodtyper
Service Fabric nodtyper har ett inbyggda antagande att tillståndsful-tjänster kan placeras på noderna vid en viss tidpunkt. Tillståndslösa nodtyper lättar på det här antagandet för en nodtyp, vilket gör att nodtypen kan använda andra funktioner, till exempel snabbare utskalningsåtgärder, stöd för automatiska OS-uppgraderingar på bronsbeständighet och utskalning till fler än 100 noder i en enda VM-skalningsuppsättning.

* Primära nodtyper kan inte konfigureras att vara tillståndslösa
* Tillståndslösa nodtyper stöds endast med bronsbeständighetsnivåer
* Tillståndslösa nodtyper stöds endast på Service Fabric Runtime version 7.1.409 eller senare.


Exempelmallar är tillgängliga: [Service Fabric mall för tillståndslösa nodtyper](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="enabling-stateless-node-types-in-service-fabric-cluster"></a>Aktivera tillståndslösa nodtyper i Service Fabric kluster
Om du vill ange en eller flera nodtyper som tillståndslösa i en klusterresurs anger du **egenskapen isStateless** till **true**. När du distribuerar Service Fabric-kluster med tillståndslösa nodtyper måste du komma ihåg att ha minst en primär nodtyp i klusterresursen.

* Den Service Fabric klusterresursen apiVersion ska vara "2020-12-01-preview" eller högre.

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

## <a name="configuring-virtual-machine-scale-set-for-stateless-node-types"></a>Konfigurera VM-skalningsuppsättning för tillståndslösa nodtyper
Om du vill aktivera tillståndslösa nodtyper bör du konfigurera den underliggande resursen för VM-skalningsuppsättningen på följande sätt:

* Värdet för  **egenskapen singlePlacementGroup,** som ska vara inställt på **false** om du behöver skala till fler än 100 virtuella datorer.
* Skalningsuppsättningens **upgradeMode ska** vara inställd på **Rullande**.
* Läget för löpande uppgradering kräver att programhälsotillägget eller hälsoavsökningar har konfigurerats. Konfigurera hälsoavsökning med standardkonfiguration för tillståndslösa nodtyper enligt förslaget nedan. När program har distribuerats till nodtypen kan du ändra tilläggsportarna för hälsoavsökning/hälsotillstånd för att övervaka programmets hälsa.

>[!NOTE]
> När du använder autoskalning med tillståndslösa nodtyper rensas inte nodtillståndet automatiskt efter nedskalningen. Vi rekommenderar att du rensar NodeState för noder med nedskalning [Service Fabric](https://github.com/Azure/service-fabric-autoscale-helper) autoskalningshjälp.

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

## <a name="configuring-stateless-node-types-with-multiple-availability-zones"></a>Konfigurera tillståndslösa nodtyper med flera Tillgänglighetszoner
Om du vill konfigurera tillståndslös nodtyp som sträcker sig över flera [tillgänglighetszoner](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set)följer du dokumentationen här, tillsammans med några av ändringarna på följande sätt:

* Ange **singlePlacementGroup:**  **false**  om flera placeringsgrupper måste vara aktiverade.
* Ange  **upgradeMode:** **Rullande och**   lägg till tillägget för programhälsa/hälsoavsökningar enligt ovan.
* Ange **platformFaultDomainCount:** **5 för** VM-skalningsuppsättning.

>[!NOTE]
> Oavsett vilka VMSSZonalUpgradeMode som konfigurerats i klustret sker uppdateringar av VM-skalningsuppsättningen alltid sekventiellt en tillgänglighetszon i taget för den tillståndslösa nodtypen som sträcker sig över flera zoner, eftersom den använder läget för löpande uppgradering.

Mer information finns i mallen för [att](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-2-NodeTypes-Windows-Stateless-CrossAZ-Secure) konfigurera tillståndslösa nodtyper med flera Tillgänglighetszoner

## <a name="networking-requirements"></a>Nätverkskrav
### <a name="public-ip-and-load-balancer-resource"></a>Offentlig IP-adress Load Balancer resurs
Om du vill aktivera skalning till fler än 100 virtuella datorer på en vm-skalningsuppsättningsresurs måste både lastbalanseraren och IP-resursen som vm-skalningsuppsättningen refererar till använda en standard-SKU.  Om du skapar en lastbalanserare eller IP-resurs utan SKU-egenskapen skapas en Basic-SKU som inte stöder skalning till fler än 100 virtuella datorer. En standard-SKU-lastbalanserare blockerar all trafik utifrån som standard. För att tillåta extern trafik måste en NSG distribueras till undernätet.

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
> Det går inte att göra en ändring på plats av SKU på de offentliga IP- och lastbalanseringsresurserna. 

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standardregler för SKU Load Balancer utgående trafik
Standard Load Balancer och standard offentlig IP introducerar nya funktioner och olika beteenden för utgående anslutningar jämfört med att använda grundläggande SKU:er. Om du vill ha utgående anslutning när du arbetar med standard-SKU:er måste du uttryckligen definiera den med offentliga IP-standardadresser eller offentliga standard-Load Balancer. Mer information finns i [Utgående anslutningar och](../load-balancer/load-balancer-outbound-connections.md) [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standardmallen refererar till en NSG som tillåter all utgående trafik som standard. Inkommande trafik är begränsad till de portar som krävs för Service Fabric hanteringsåtgärder. NSG-reglerna kan ändras för att uppfylla dina krav.

>[!NOTE]
> Alla Service Fabric kluster som använder en standard-SKU SLB måste se till att varje nodtyp har en regel som tillåter utgående trafik på port 443. Detta är nödvändigt för att slutföra klusterkonfigurationen, och distribution utan en sådan regel kommer att misslyckas.



## <a name="migrate-to-using-stateless-node-types-in-a-cluster"></a>Migrera till med tillståndslösa nodtyper i ett kluster
För alla migreringsscenarier måste en ny tillståndslös nodtyp läggas till. Befintlig nodtyp kan inte migreras till att vara tillståndslös.

Om du vill migrera ett kluster, som använde en Load Balancer och IP med en grundläggande SKU, måste du först skapa en helt ny Load Balancer- och IP-resurs med hjälp av standard-SKU:n. Det går inte att uppdatera dessa resurser på plats.

Den nya lb- och IP-adressen ska refereras till i de nya tillståndslösa nodtyper som du vill använda. I exemplet ovan läggs en ny VM-skalningsuppsättningsresurser till för att användas för tillståndslösa nodtyper. Skalningsuppsättningarna för virtuella datorer refererar till den nyligen skapade lb- och IP-adressen och markeras som tillståndslösa nodtyper i Service Fabric klusterresursen.

För att börja måste du lägga till de nya resurserna i din befintliga Resource Manager mall. Dessa resurser är:
* En offentlig IP-resurs med standard-SKU.
* En Load Balancer resurs med standard-SKU.
* En NSG som refereras av undernätet där du distribuerar dina VM-skalningsuppsättningar.

När resurserna har distribuerats kan du börja inaktivera noderna i nodtypen som du vill ta bort från det ursprungliga klustret.

## <a name="next-steps"></a>Nästa steg 
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Nodtyper och skaluppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)

