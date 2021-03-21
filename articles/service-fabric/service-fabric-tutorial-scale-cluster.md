---
title: Skala ett Service Fabric-kluster i Azure
description: I den här självstudien får du lära dig hur du skalar ett Service Fabric kluster i Azure ut och i och hur du rensar överblivna resurser.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cdc7ba8d6c83ae72ffb8f1afae3954b3a46dc6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788035"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Självstudie: Skala ut ett Service Fabric-kluster i Azure

Den här självstudien är del tre i en serie och visar hur du skalar ditt befintliga kluster och i. När du är klar kommer du att veta hur du skalar ditt kluster och hur du rensar överblivna resurser.  Mer information om hur du skalar ett kluster som körs i Azure finns i [skalnings Service Fabric kluster](service-fabric-cluster-scaling.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala in)
> * Lägga till och ta bort nodtyper (skala ut och skala in)
> * Öka Node-resurser (skala upp)

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * Skala in eller ut ett kluster
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure PowerShell](/powershell/azure/install-az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure

## <a name="important-considerations-and-guidelines"></a>Viktiga överväganden och rikt linjer

Vill du ändra program arbets belastningar med tiden? de befintliga tjänsterna behöver mer (eller färre) resurser?  [Lägg till eller ta bort noder](#add-nodes-to-or-remove-nodes-from-a-node-type) från en nodtyp för att öka eller minska kluster resurserna.

Behöver du lägga till fler än 100 noder i klustret?  En enskild Service Fabric nodtyp/skalnings uppsättning får inte innehålla fler än 100 noder/VM: ar.  Om du vill skala ett kluster bortom 100 noder [lägger du till ytterligare nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type).

Har ditt program flera tjänster och behöver de vara offentliga eller Internet riktade?  Typiska program innehåller en frontend Gateway-tjänst som tar emot indata från en klient och en eller flera backend-tjänster som kommunicerar med klient dels tjänsterna. I det här fallet rekommenderar vi att du [lägger till minst två nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) till klustret.  

Har dina tjänster olika infrastruktur behov, till exempel större RAM-minne eller högre CPU-cykler? Ditt program innehåller till exempel en frontend-tjänst och en backend-tjänst. Frontend-tjänsten kan köras på mindre virtuella datorer (VM-storlekar som D2) som har portar öppna för Internet. Server dels tjänsten är dock en beräknings intensiv och måste köras på större virtuella datorer (med VM-storlekar som D4, D6, D15) som inte är Internet-riktade. I det här fallet rekommenderar vi att du [lägger till två eller flera nodtyper](#add-nodes-to-or-remove-nodes-from-a-node-type) i klustret. Detta gör att varje nodtyp kan ha distinkta egenskaper som Internet anslutning eller storlek på virtuell dator. Antalet virtuella datorer kan också skalas oberoende av varandra.

När du skalar ett Azure-kluster bör du ha följande rikt linjer i åtanke:

* En enskild Service Fabric nodtyp/skalnings uppsättning får inte innehålla fler än 100 noder/VM: ar.  Om du vill skala ett kluster bortom 100 noder lägger du till ytterligare nodtyper.
* Primära nodtyper som kör produktions arbets belastningar bör ha en [hållbarhets nivå][durability] på guld eller silver och har alltid fem eller fler noder.
* Icke-primära nodtyper som kör tillstånds känsliga produktions arbets belastningar bör alltid ha fem eller fler noder.
* Icke-primära nodtyper som kör tillstånds lösa produktions arbets belastningar bör alltid ha två eller flera noder.
* Alla nodtyper för [hållbarhets nivån][durability] guld eller silver bör alltid ha fem eller fler noder.
* Om du skalar i (tar bort noder från) en typ av primär nod, bör du aldrig minska antalet instanser till mindre än vad [Tillförlitlighets nivån][reliability] kräver.

Mer information finns i [kluster kapacitets vägledning](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Exportera mallen för resursgruppen

När du har skapat ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) och konfigurerat resurs gruppen, exporterar du Resource Manager-mallen för resurs gruppen. Genom att exportera mallen kan du automatisera framtida distributioner av klustret och dess resurser, eftersom mallen innehåller all fullständig infrastruktur.  Mer information om hur du exporterar mallar finns i [hantera Azure Resource Manager resurs grupper med hjälp av Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md).

1. I [Azure Portal](https://portal.azure.com)går du till resurs gruppen som innehåller klustret (**sfclustertutorialgroup** om du följer den här självstudien). 

2. I det vänstra fönstret väljer du **distributioner** eller så väljer du länken under **distributioner**. 

3. Välj den senaste lyckade distributionen i listan.

4. I den vänstra rutan väljer du **mall** och väljer sedan **Ladda ned** för att exportera mallen som en zip-fil.  Spara mallen och parametrarna på den lokala datorn.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Lägga till noder i eller ta bort noder från en nodtyp

Att skala in och ut eller vågrät skalning ändrar antalet noder i klustret. När du skalar in eller ut lägger du till fler virtuella dator instanser i skalnings uppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parameter fil](#export-the-template-for-the-resource-group) från resurs gruppen för den senaste distributionen.  Öppna filen *parameters.js* .  Om du har distribuerat klustret med hjälp av [exempel mal len][template] i den här självstudien finns det tre olika nodtyper i klustret och tre parametrar som anger antalet noder för varje nodtyp: *nt0InstanceCount*, *nt1InstanceCount* och *nt2InstanceCount*.  Parametern *nt1InstanceCount* anger till exempel antalet instanser för den andra nodtypen och anger antalet virtuella datorer i den associerade skalnings uppsättningen för den virtuella datorn.

Så genom att uppdatera värdet för *nt1InstanceCount* ändrar du antalet noder i den andra nodtypen.  Kom ihåg att du inte kan skala upp en nodtyp till fler än 100 noder.  Icke-primära nodtyper som kör tillstånds känsliga produktions arbets belastningar bör alltid ha fem eller fler noder. Icke-primära nodtyper som kör tillstånds lösa produktions arbets belastningar bör alltid ha två eller flera noder.

Om du skalar i och tar bort noder från, [måste du][durability] [ta bort de nodernas status manuellt](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  För silver-och Gold-hållbarhets nivån görs de här stegen automatiskt av plattformen.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringarna i *template.js* och *parameters.jspå* filer.  Kör följande kommando för att distribuera den uppdaterade mallen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Eller följande Azure CLI-kommando:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Lägg till en nodtyp i klustret

Varje nodtyp som definieras i ett Service Fabric kluster som körs i Azure har kon figurer ATS som en [separat skalnings uppsättning för virtuella datorer](service-fabric-cluster-nodetypes.md). Varje nodtyp kan sedan hanteras separat. Du kan skala upp eller ned varje nodtyp oberoende av varandra, ha olika uppsättningar portar öppna och använda olika kapacitets mått. Du kan också självständigt ändra OS-SKU: n som körs på varje klusternod, men Observera att du inte kan ha en blandning av Windows och Linux som körs i exempel klustret. En enskild nodtyp/skalnings uppsättning får inte innehålla fler än 100 noder.  Du kan skala ett kluster vågrätt till över 100 noder genom att lägga till ytterligare nodtyper/skalnings uppsättningar. Du kan skala klustret när som helst, även när arbets belastningar körs på klustret.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parameter fil](#export-the-template-for-the-resource-group) från resurs gruppen för den senaste distributionen.  Öppna filen *parameters.js* .  Om du har distribuerat klustret med hjälp av [exempel mal len][template] i den här självstudien finns det tre olika nodtyper i klustret.  I det här avsnittet lägger du till en fjärde nodtyp genom att uppdatera och distribuera en Resource Manager-mall. 

Förutom den nya nodtypen lägger du också till den associerade skalnings uppsättningen för virtuella datorer (som körs i ett separat undernät i det virtuella nätverket) och nätverks säkerhets gruppen.  Du kan välja att lägga till nya eller befintliga offentliga IP-adresser och resurser för Azure Load Balancer för den nya skalnings uppsättningen.  Den nya nodtypen har en [hållbarhets nivå][durability] för silver och storlek på "Standard_D2_V2".

Lägg till följande nya parametrar i filen *template.js* :
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Lägg till följande nya variabler i filen *template.js* :
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

I *template.jspå* fil lägger du till ett nytt undernät i den virtuella nätverks resursen:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Lägg till nya offentliga IP-adresser och belastnings Utjämnings resurser i filen *template.js* :
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Lägg till en ny nätverks säkerhets grupp och resurser för skalnings uppsättningar för virtuella datorer i filen *template.js* .  Egenskapen NodeTypeRef i Service Fabric tilläggs egenskaperna för den virtuella datorns skal uppsättning mappar den angivna nodtypen till skalnings uppsättningen.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Uppdatera kluster resursen i *template.jspå* filen och Lägg till en ny nodtyp:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Lägg till följande nya parametrar och värden i filen *parameters.js* :
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringarna i *template.js* och *parameters.jspå* filer.  Kör följande kommando för att distribuera den uppdaterade mallen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Eller följande Azure CLI-kommando:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Ta bort en nodtyp från klustret
När du har skapat ett Service Fabric-kluster kan du skala ett kluster vågrätt genom att ta bort en nodtyp (skalnings uppsättning för virtuell dator) och alla dess noder. Du kan skala klustret när som helst, även när arbets belastningar körs på klustret. När klustret skalas, skalas programmen automatiskt.

> [!WARNING]
> Att använda Remove-AzServiceFabricNodeType för att ta bort en nodtyp från ett produktions kluster bör inte användas regelbundet. Det är ett farligt kommando eftersom det tar bort den virtuella datorns skalnings uppsättnings resurs bakom nodtypen. 

Om du vill ta bort nodtypen kör du cmdleten [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) .  Nodtypen måste vara silver eller guld [hållbarhets nivå][durability]  cmdleten tar bort den skalnings uppsättning som är associerad med nodtypen och tar lite tid att slutföra.  Kör sedan cmdleten [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) på var och en av noderna som ska tas bort, vilket tar bort nodens tillstånd och tar bort noderna från klustret. Om det finns tjänster på noderna flyttas tjänsterna först till en annan nod. Om kluster hanteraren inte kan hitta en nod för repliken/tjänsten är åtgärden försenad/blockerad.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Öka nodens resurser 
När du har skapat ett Service Fabric-kluster kan du skala en klusternod lodrätt (ändra resurserna för noderna) eller uppgradera operativ systemet för de virtuella datorernas nodtyper genom att ersätta den ursprungliga nodtypen med en ny nodtyp (med uppdaterad VM SKU eller OS-avbildning). Mer information finns i [skala upp en typ av Azure-Service Fabric Node](service-fabric-scale-up-primary-node-type.md).

> [!IMPORTANT]
> Försök aldrig att ändra på plats för VM SKU eller OS-avbildning, vilket är en farlig åtgärd och som inte stöds.

Om detta inte är möjligt kan du skapa ett nytt kluster och [återställa program tillstånd](service-fabric-reliable-services-backup-restore.md) (om det är tillämpligt) från det gamla klustret. Du behöver inte återställa någon system tjänst status. de återskapas när du distribuerar dina program till det nya klustret. Om du precis har kört tillstånds lösa program i klustret, så kan du distribuera dina program till det nya klustret, men du har inget att återställa.

### <a name="update-the-template"></a>Uppdatera mallen

[Exportera en mall och parameter fil](#export-the-template-for-the-resource-group) från resurs gruppen för den senaste distributionen.  Öppna filen *parameters.js* .  Om du har distribuerat klustret med hjälp av [exempel mal len][template] i den här självstudien finns det tre olika nodtyper i klustret.  

Storleken på de virtuella datorerna i den andra nodtypen anges i parametern *vmNodeType1Size* .  Ändra värdet för parametern *vmNodeType1Size* från Standard_D2_V2 till [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md), vilket dubblerar resurserna för varje VM-instans.

VM-SKU: n för alla tre nodtyper anges i parametern *vmImageSku* .  Återigen bör det vara försiktig med att ändra VM-SKU: n för en nodtyp och rekommenderas inte för den primära nodtypen.

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen
Spara ändringarna i *template.js* och *parameters.jspå* filer.  Kör följande kommando för att distribuera den uppdaterade mallen:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Eller följande Azure CLI-kommando:
```azurecli
az deployment group create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till och ta bort noder (skala ut och skala in)
> * Lägga till och ta bort nodtyper (skala ut och skala in)
> * Öka Node-resurser (skala upp)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
