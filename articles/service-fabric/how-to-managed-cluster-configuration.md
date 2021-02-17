---
title: Konfigurera Service Fabric hanterat kluster (för hands version)
description: Lär dig hur du konfigurerar Service Fabric hanterat kluster för automatiska OS-uppgraderingar, NSG-regler och mycket annat.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 90ba5057f06cf8841e278b8d921d812286459c49
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642582"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Konfigurations alternativ för Service Fabric hanterat kluster (för hands version)

Förutom att välja det [Service Fabric hanterade kluster-SKU: n](overview-managed-cluster.md#service-fabric-managed-cluster-skus) när du skapar klustret, finns det ett antal andra sätt att konfigurera det. I den aktuella för hands versionen kan du:

* Lägg till ett [tillägg för skalnings uppsättning för virtuell dator](how-to-managed-cluster-vmss-extension.md) till en nodtyp
* Aktivera [automatiska OS-uppgraderingar](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades) för noderna
* Aktivera [kryptering av operativ system och data diskar](how-to-enable-managed-cluster-disk-encryption.md) på noderna
* Om [nätverkskonfigurationer](how-to-managed-cluster-configuration.md#networking-configurations)
* Konfigurera [hanterad identitet](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) på olika nodtyper

## <a name="networking-configurations"></a>Nätverkskonfigurationer

Service Fabric hanterade kluster skapas med en standard nätverks konfiguration. Den här konfigurationen består av obligatoriska regler för viktiga kluster funktioner och några valfria regler som är avsedda att göra kund konfigurationen enklare.

Utöver standard konfigurationen för nätverk kan du ändra nätverks reglerna så att de uppfyller kraven för ditt scenario. 

### <a name="nsg-rules-guidance"></a>Vägledning för NSG-regler

* Service Fabric hanterade kluster reserverar prioritets intervallet 0 till 999 för NSG-regeln för viktiga funktioner. Du kan inte skapa anpassade NSG-regler med prioritets värde som är mindre än 1000. 
* Service Fabric hanterade kluster reserverar prioritets intervallet 3001 till 4000 för att skapa valfria NSG-regler. De här reglerna läggs automatiskt till för att göra konfigurationer snabbt och enkelt. Du kan åsidosätta de här reglerna genom att lägga till anpassade NSG-regler i prioritets intervallet 1000 till 3000. 
* Anpassade NSG-regler ska ha en prioritet inom intervallet 1000 till 3000. 


### <a name="apply-nsg-rules"></a>Tillämpa NSG-regler

Med klassisk (icke-hanterad) Service Fabric kluster, måste du deklarera och hantera en separat *Microsoft. Network/networkSecurityGroups* -resurs för att kunna [tillämpa NSG-regler (Network Security Group) i klustret](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Med Service Fabric hanterade kluster kan du tilldela NSG-regler direkt i kluster resursen för distributions mal len.

Använd egenskapen [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) för din *Microsoft. ServiceFabric/managedclusters* -resurs (version `2021-01-01-preview` eller senare) för att tilldela NSG-regler. Exempel:

```json
            "apiVersion": "2021-01-01-preview",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound" 
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

### <a name="rdp-ports"></a>RDP-portar 

Service Fabric hanterade kluster tillåter inte åtkomst till RDP-portarna som standard. Du kan öppna RDP-portar på Internet genom att ange följande egenskap på en Service Fabric hanterad kluster resurs. 

```json
"allowRDPAccess": true 
```

När allowRDPAccess-egenskapen har angetts till True kommer följande NSG-regel att läggas till i kluster distributionen.  

```json
{
    "name": "SFMC_AllowRdpPort", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

### <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection-och HttpGatewayConnection-portar 

**NSG-regel: SFMC_AllowServiceFabricGatewayToSFRP** En standard regel för NSG läggs till för att tillåta att Service Fabric resurs leverantören får åtkomst till klustrets clientConnectionPort och httpGatewayConnectionPort. Den här regeln tillåter åtkomst till portarna via tjänst tag gen "ServiceFabric".

>[!NOTE]
>Den här regeln läggs alltid till och kan inte åsidosättas. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayToSFRP", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.", 
        "protocol": "TCP", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "ServiceFabric", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 500, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

**NSG-regel: SFMC_AllowServiceFabricGatewayPorts** Det här är en valfri NSG-regel för att tillåta åtkomst till clientConnectionPort och httpGatewayPort från Internet. Med den här regeln kan kunder komma åt SFX, ansluta till klustret med hjälp av PowerShell och använda Service Fabric API-slutpunkter för klustret utanför. 

>[!NOTE]
>Den här regeln kommer inte att läggas till om det finns en anpassad regel med samma åtkomst-, riktnings-och protokoll värden för samma port. Du kan åsidosätta den här regeln med anpassade NSG-regler. 

```json
{ 
    "name": "SFMC_AllowServiceFabricGatewayPorts", 
    "type": "Microsoft.Network/networkSecurityGroups/securityRules", 
    "properties": { 
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.", 
        "protocol": "tcp", 
        "sourcePortRange": "*", 
        "sourceAddressPrefix": "*", 
        "destinationAddressPrefix": "VirtualNetwork", 
        "access": "Allow", 
        "priority": 3001, 
        "direction": "Inbound", 
        "sourcePortRanges": [], 
        "destinationPortRanges": [ 
            "19000", 
            "19080" 
        ] 
    } 
}
```

### <a name="load-balancer-ports"></a>Belastnings Utjämnings portar 

Service Fabric hanterade kluster skapar en regel för NSG i standard prioritets intervallet för alla LB-portar som kon figurer ATS under avsnittet "loadBalancingRules" under ManagedCluster Properties. Den här regeln öppnar LB-portar för inkommande trafik från Internet.  

>[!NOTE]
>Den här regeln läggs till i det valfria prioritets området och kan åsidosättas genom att lägga till anpassade NSG-regler. 

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*", 
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

### <a name="load-balancer-probes"></a>Belastnings Utjämnings avsökningar

Service Fabric hanterade kluster skapar automatiskt belastnings Utjämnings avsökningar för Fabric Gateway-portar samt alla portar som kon figurer ATS i avsnittet "loadBalancingRules" i hanterade kluster egenskaper.

```json
{ 
  "value": [ 
    { 
        "name": "FabricTcpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19000, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>"
                } 
            ] 
        }, 
        "type": "Microsoft.Network/loadBalancers/probes" 
    }, 
    { 
        "name": "FabricHttpGateway", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 19080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
                { 
                    "id": "<>" 
                } 
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes" 
    },
    {
        "name": "probe1_tcp_8080", 
        "properties": { 
            "provisioningState": "Succeeded", 
            "protocol": "Tcp", 
            "port": 8080, 
            "intervalInSeconds": 5, 
            "numberOfProbes": 2, 
            "loadBalancingRules": [ 
            { 
                "id": "<>" 
            } 
        ] 
      }, 
      "type": "Microsoft.Network/loadBalancers/probes" 
    } 
  ] 
} 
```

## <a name="enable-automatic-os-image-upgrades"></a>Aktivera automatiska uppgradering av operativ system avbildningar

Du kan välja att aktivera automatisk uppgradering av operativ system avbildningar till de virtuella datorerna som kör dina hanterade klusternoder. Även om de virtuella datorernas skalnings uppsättnings resurser hanteras för din räkning med Service Fabric hanterade kluster, är det du som väljer att aktivera automatiska uppgradering av operativ system avbildningar för dina klusternoder. Precis som med de [klassiska Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) -klustren uppgraderas inte hanterade klusternoder som standard för att förhindra oavsiktliga avbrott i klustret.

Så här aktiverar du automatiska OS-uppgraderingar:

* Använd `2021-01-01-preview` (eller senare) versionen av *Microsoft. ServiceFabric/Managedclusters* och *Microsoft. ServiceFabric/managedclusters/nodetypes* resurser
* Ange egenskapen `enableAutoOSUpgrade` för klustret till *Sant*
* Ange resurs egenskapen för kluster nodeTypes `vmImageVersion` till *senaste*

Exempel:

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

När den är aktive rad börjar Service Fabric fråga och spåra operativ system avbildnings versioner i det hanterade klustret. Om det finns en ny operativ system version, uppgraderas klusternodernas typer (Virtual Machine Scale set), en i taget. Service Fabric körnings uppgraderingar utförs först efter det att du har bekräftat att uppgraderingar av OS-avbildningar för klusternoder pågår.

Om uppgraderingen Miss lyckas försöker Service Fabric igen efter 24 timmar, för högst tre återförsök. Precis som klassiska (ohanterade) Service Fabric-uppgraderingar kan felaktiga appar eller noder blockera uppgraderingen av operativ systemet.

Mer information om avbildnings uppgraderingar finns i [automatisk uppgradering av operativ system avbildningar med skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md).

## <a name="next-steps"></a>Nästa steg

[Länka till exempel mall]

[Översikt över hanterat kluster]
