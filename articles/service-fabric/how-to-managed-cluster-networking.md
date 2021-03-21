---
title: Konfigurera nätverks inställningar för Service Fabric hanterade kluster (för hands version)
description: Lär dig hur du konfigurerar Service Fabric hanterat kluster för NSG-regler, RDP-port åtkomst, belastnings Utjämnings regler med mera.
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: e17251523c0720665c4c6f5b7811304eebc9923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746992"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters-preview"></a>Konfigurera nätverks inställningar för Service Fabric hanterade kluster (för hands version)

Service Fabric hanterade kluster skapas med en standard nätverks konfiguration. Den här konfigurationen består av obligatoriska regler för viktiga kluster funktioner och några valfria regler som är avsedda att göra kund konfigurationen enklare.

Utöver standard konfigurationen för nätverk kan du ändra nätverks reglerna så att de uppfyller kraven för ditt scenario.

## <a name="nsg-rules-guidance"></a>Vägledning för NSG-regler

Tänk på följande när du skapar nya NSG-regler för ditt hanterade kluster.

* Service Fabric hanterade kluster reserverar prioritets intervallet 0 till 999 för NSG-regeln för viktiga funktioner. Du kan inte skapa anpassade NSG-regler med prioritets värde som är mindre än 1000.
* Service Fabric hanterade kluster reserverar prioritets intervallet 3001 till 4000 för att skapa valfria NSG-regler. De här reglerna läggs automatiskt till för att göra konfigurationer snabbt och enkelt. Du kan åsidosätta de här reglerna genom att lägga till anpassade NSG-regler i prioritets intervallet 1000 till 3000.
* Anpassade NSG-regler ska ha en prioritet inom intervallet 1000 till 3000.

## <a name="apply-nsg-rules"></a>Tillämpa NSG-regler

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

## <a name="rdp-ports"></a>RDP-portar

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

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>ClientConnection-och HttpGatewayConnection-portar

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>NSG-regel: SFMC_AllowServiceFabricGatewayToSFRP

En standard regel för NSG läggs till för att tillåta att Service Fabric resurs leverantören får åtkomst till klustrets clientConnectionPort och httpGatewayConnectionPort. Den här regeln tillåter åtkomst till portarna via tjänst tag gen "ServiceFabric".

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

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>NSG-regel: SFMC_AllowServiceFabricGatewayPorts

Det här är en valfri NSG-regel för att tillåta åtkomst till clientConnectionPort och httpGatewayPort från Internet. Med den här regeln kan kunder komma åt SFX, ansluta till klustret med hjälp av PowerShell och använda Service Fabric API-slutpunkter för klustret utanför. 

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

## <a name="load-balancer-ports"></a>Belastnings Utjämnings portar

Service Fabric hanterade kluster skapar en regel för NSG i standard prioritets intervallet för alla belastningsutjämnare (LB) som har kon figurer ATS under avsnittet "loadBalancingRules" under *ManagedCluster* Properties. Den här regeln öppnar LB-portar för inkommande trafik från Internet.  

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

## <a name="load-balancer-probes"></a>Belastnings Utjämnings avsökningar

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

## <a name="next-steps"></a>Nästa steg

[Service Fabric alternativ för hanterad kluster konfiguration](how-to-managed-cluster-configuration.md)

[Översikt över Service Fabric hanterade kluster](overview-managed-cluster.md)
