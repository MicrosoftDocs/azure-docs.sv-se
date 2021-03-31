---
title: Nätverks mönster för Azure Service Fabric
description: Beskriver vanliga nätverks mönster för Service Fabric och hur du skapar ett kluster med hjälp av funktioner i Azure-nätverk.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 20bd5e931307725016c3e2ad69dae91214b2caab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421475"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric nätverks mönster
Du kan integrera ditt Azure Service Fabric-kluster med andra funktioner i Azure-nätverk. I den här artikeln visar vi hur du skapar kluster som använder följande funktioner:

- [Befintligt virtuellt nätverk eller undernät](#existingvnet)
- [Statisk offentlig IP-adress](#staticpublicip)
- [Intern belastningsutjämnare](#internallb)
- [Intern och extern belastningsutjämnare](#internalexternallb)

Service Fabric körs i en standard skalnings uppsättning för virtuella datorer. Alla funktioner som du kan använda i en skalnings uppsättning för virtuella datorer kan du använda med ett Service Fabric-kluster. Nätverks avsnitten i Azure Resource Manager mallar för skalnings uppsättningar för virtuella datorer och Service Fabric är identiska. När du har distribuerat till ett befintligt virtuellt nätverk är det enkelt att använda andra nätverksfunktioner, t. ex. Azure ExpressRoute, Azure VPN Gateway, en nätverks säkerhets grupp och virtuell nätverks-peering.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Låta Service Fabric Resource Provider köra en fråga till klustret

Service Fabric är unikt för andra nätverksfunktioner i en aspekt. [Azure Portal](https://portal.azure.com) använder internt Service Fabric resurs leverantören för att anropa ett kluster för att få information om noder och program. Service Fabric Resource Provider kräver offentligt tillgänglig inkommande åtkomst till HTTP Gateway-porten (port 19080 som standard) på hanterings slut punkten. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) använder hanterings slut punkten för att hantera klustret. Den Service Fabric Resource providern använder också den här porten för att fråga efter information om klustret, för att visa i Azure Portal. 

Om Port 19080 inte går att komma åt från Service Fabric Resource Provider, visas ett meddelande som *noder som inte hittas* i portalen, och din nod och program lista visas som tom. Om du vill se klustret i Azure Portal måste belastningsutjämnaren exponera en offentlig IP-adress och nätverks säkerhets gruppen måste tillåta inkommande trafik på port 19080. Om din installation inte uppfyller dessa krav, visar Azure Portal inte klustrets status.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>Mallar

Alla Service Fabric mallar är i [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking). Du bör kunna distribuera mallarna med hjälp av följande PowerShell-kommandon. Om du distribuerar den befintliga Azure Virtual Network-mallen eller den statiska offentliga IP-mallen läser du först avsnittet [inledande konfiguration](#initialsetup) i den här artikeln.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Första konfigurationen

### <a name="existing-virtual-network"></a>Befintligt virtuellt nätverk

I följande exempel börjar vi med ett befintligt virtuellt nätverk med namnet ExistingRG-VNet, i resurs gruppen **ExistingRG** . Under nätet kallas default. Dessa standard resurser skapas när du använder Azure Portal för att skapa en virtuell standard dator (VM). Du kan skapa det virtuella nätverket och under nätet utan att skapa den virtuella datorn, men huvud målet med att lägga till ett kluster i ett befintligt virtuellt nätverk är att tillhandahålla nätverks anslutning till andra virtuella datorer. När du skapar den virtuella datorn får du ett användbart exempel på hur ett befintligt virtuellt nätverk vanligt vis används. Om ditt Service Fabric kluster bara använder en intern belastningsutjämnare, utan en offentlig IP-adress, kan du använda den virtuella datorn och dess offentliga IP-adress som en säker *hopp ruta*.

### <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

En statisk offentlig IP-adress är vanligt vis en dedikerad resurs som hanteras separat från den virtuella datorn eller de virtuella datorer som den är tilldelad till. Den är etablerad i en dedikerad nätverks resurs grupp (till skillnad från i själva Service Fabric själva kluster resurs gruppen). Skapa en statisk offentlig IP-adress med namnet staticIP1 i samma ExistingRG-resurs grupp, antingen i Azure Portal eller med hjälp av PowerShell:

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric mall

I exemplen i den här artikeln använder vi Service Fabric template.jspå. Du kan använda standard Portal guiden för att ladda ned mallen från portalen innan du skapar ett kluster. Du kan också använda en av [exempel mallarna](https://github.com/Azure-Samples/service-fabric-cluster-templates), t. ex. ett [säkert Service Fabric kluster med fem noder](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Befintligt virtuellt nätverk eller undernät

1. Ändra under näts parametern till namnet på det befintliga under nätet och Lägg sedan till två nya parametrar för att referera till det befintliga virtuella nätverket:

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

   Du kan också kommentera ut parametern med namnet "virtualNetworkName" så att du inte behöver ange det virtuella nätverks namnet två gånger på bladet kluster distribution i Azure Portal.

2. Kommentera ut `nicPrefixOverride` attributet av `Microsoft.Compute/virtualMachineScaleSets` , eftersom du använder ett befintligt undernät och du har inaktiverat den här variabeln i steg 1.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. Ändra `vnetID` variabeln så att den pekar mot det befintliga virtuella nätverket:

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Ta bort `Microsoft.Network/virtualNetworks` från dina resurser så att Azure inte skapar ett nytt virtuellt nätverk:

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
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
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. Kommentera det virtuella nätverket från `dependsOn` attributet för `Microsoft.Compute/virtualMachineScaleSets` , så du är inte beroende av att skapa ett nytt virtuellt nätverk:

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Efter distributionen bör det virtuella nätverket innehålla de nya virtuella datorerna med skalnings uppsättningen. Nodtypen för skalnings uppsättningen för den virtuella datorn bör visa det befintliga virtuella nätverket och under nätet. Du kan också använda Remote Desktop Protocol (RDP) för att få åtkomst till den virtuella datorn som redan finns i det virtuella nätverket och för att pinga de nya virtuella datorerna med skalnings uppsättningar:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Ett annat exempel finns i [en som inte är unik för Service Fabric](https://github.com/gbowerman/azure-myriad/tree/main/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Statisk offentlig IP-adress

1. Lägg till parametrar för namnet på befintlig statisk IP-adressresurs, namn och fullständigt kvalificerat domän namn (FQDN):

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Ta bort `dnsName` parametern. (Den statiska IP-adressen har redan en.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Lägg till en variabel som refererar till den befintliga statiska IP-adressen:

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser så att Azure inte skapar någon ny IP-adress:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Kommentera ut IP-adressen från `dependsOn` attributet för `Microsoft.Network/loadBalancers` , så du är inte beroende av att skapa en ny IP-adress:

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. I `Microsoft.Network/loadBalancers` resursen ändrar du `publicIPAddress` elementet för `frontendIPConfigurations` att referera till den befintliga statiska IP-adressen i stället för en nyligen skapad:

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. I `Microsoft.ServiceFabric/clusters` resursen ändrar `managementEndpoint` du till DNS-FQDN för den statiska IP-adressen. Om du använder ett säkert kluster ser du till att ändra *http://* till *https://*. (Observera att det här steget endast gäller för Service Fabric kluster. Hoppa över det här steget om du använder en skalnings uppsättning för virtuella datorer.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Efter distributionen kan du se att belastningsutjämnaren är kopplad till den offentliga statiska IP-adressen från den andra resurs gruppen. Slut punkten för Service Fabric klient anslutningen och [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) slut punkts punkt till DNS-FQDN för den statiska IP-adressen.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Intern belastningsutjämnare

Det här scenariot ersätter den externa belastningsutjämnaren i standard Service Fabric-mallen med en intern belastningsutjämnare. Se [tidigare i artikeln](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) för att få konsekvenser för Azure Portal och för Service Fabric resurs leverantör.

1. Ta bort `dnsName` parametern. (Det behövs inte.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Om du använder en statisk tilldelnings metod kan du också lägga till en statisk IP-adress parameter. Om du använder en dynamisk tilldelnings metod behöver du inte göra det här steget.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Ta bort `Microsoft.Network/publicIPAddresses` från dina resurser så att Azure inte skapar någon ny IP-adress:

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Ta bort IP-adressprefixet `dependsOn` för `Microsoft.Network/loadBalancers` , så du är inte beroende av att skapa en ny IP-adress. Lägg till attributet för det virtuella nätverket `dependsOn` eftersom belastningsutjämnaren nu är beroende av under nätet från det virtuella nätverket:

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Ändra belastnings Utjämnings `frontendIPConfigurations` inställningen från att använda en `publicIPAddress` , för att använda ett undernät och `privateIPAddress` . `privateIPAddress` använder en fördefinierad statisk intern IP-adress. Om du vill använda en dynamisk IP-adress tar du bort `privateIPAddress` elementet och ändrar sedan `privateIPAllocationMethod` till **dynamiskt**.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. I `Microsoft.ServiceFabric/clusters` resursen ändrar `managementEndpoint` du till att peka på den interna belastnings Utjämnings adressen. Om du använder ett säkert kluster ser du till att ändra *http://* till *https://*. (Observera att det här steget endast gäller för Service Fabric kluster. Hoppa över det här steget om du använder en skalnings uppsättning för virtuella datorer.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Efter distributionen använder belastningsutjämnaren den privata statiska 10.0.0.250-IP-adressen. Om du har en annan dator i samma virtuella nätverk kan du gå till den interna [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) slut punkten. Observera att den ansluter till en av noderna bakom belastningsutjämnaren.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Intern och extern belastningsutjämnare

I det här scenariot börjar du med den befintliga typen av externa belastningsutjämnare med en nod och lägger till en intern belastningsutjämnare för samma nodtyp. En backend-port som är ansluten till en backend-adresspool kan bara tilldelas en enda belastningsutjämnare. Välj vilken belastningsutjämnare som ska ha dina program portar och vilka belastningsutjämnare som ska hantera slut punkterna (portarna 19000 och 19080). Om du har slut på hanterings slut punkter i den interna belastningsutjämnaren bör du tänka på Service Fabric Resource Provider-begränsningar som beskrivs [ovan i artikeln](#allowing-the-service-fabric-resource-provider-to-query-your-cluster). I exemplet som vi använder finns hanterings slut punkterna kvar på den externa belastningsutjämnaren. Du kan också lägga till en port 80-programport och placera den på den interna belastningsutjämnaren.

I ett kluster med två noder-typ finns en nodtyp på den externa belastningsutjämnaren. Den andra nodtypen finns på den interna belastningsutjämnaren. Om du vill använda ett kluster med två noder, i portalen-skapade mall för två noder (som medföljer två belastningsutjämnare) växlar du den andra belastningsutjämnaren till en intern belastningsutjämnare. Mer information finns i avsnittet [intern belastnings utjämning](#internallb) .

1. Lägg till IP-parametern statisk intern belastningsutjämnare. (Mer information om hur du använder en dynamisk IP-adress finns i tidigare avsnitt i den här artikeln.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Lägg till en program port 80-parameter.

3. Om du vill lägga till interna versioner av befintliga nätverks variabler kopierar du och klistrar in dem och lägger till "-int" i namnet:

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Om du börjar med den Portal-genererade mallen som använder program port 80, lägger standard Portal mal len till AppPort1 (port 80) i den externa belastningsutjämnaren. I det här fallet tar du bort AppPort1 från den externa belastningsutjämnaren `loadBalancingRules` och avsökningar, så att du kan lägga till den i den interna belastningsutjämnaren:

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Lägg till en andra `Microsoft.Network/loadBalancers` resurs. Den ser ut ungefär som den interna belastningsutjämnaren som skapats i avsnittet [intern belastnings utjämning](#internallb) , men använder belastnings Utjämnings variablerna "-int" och implementerar bara program porten 80. Detta tar också bort `inboundNatPools` för att behålla RDP-slutpunkter på den offentliga belastningsutjämnaren. Om du vill använda RDP på den interna belastningsutjämnaren flyttar du `inboundNatPools` från den externa belastningsutjämnaren till den här interna belastningsutjämnaren:

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. I `networkProfile` för `Microsoft.Compute/virtualMachineScaleSets` resursen lägger du till den interna backend-adresspoolen:

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Distribuera mallen:

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Efter distributionen kan du se två belastningsutjämnare i resurs gruppen. Om du bläddrar i belastningsutjämnare kan du se den offentliga IP-adressen och hanterings slut punkter (portarna 19000 och 19080) som är kopplade till den offentliga IP-adressen. Du kan också se den statiska interna IP-adressen och program slut punkten (port 80) som har tilldelats till den interna belastningsutjämnaren. Båda belastnings utjämningarna använder samma pool för skalnings uppsättning för virtuella datorer.

## <a name="notes-for-production-workloads"></a>Kommentarer till produktions arbets belastningar

Ovanstående GitHub-mallar är utformade för att fungera med standard-SKU: n för Azure Standard Load Balancer (SLB), den grundläggande SKU: n. Detta SLB har inget service avtal för produktions arbets belastningar som standard-SKU: n ska användas. Mer information finns i [Översikt över Azure-standard Load Balancer](../load-balancer/load-balancer-overview.md). Alla Service Fabric kluster som använder standard-SKU: n för SLB måste se till att varje nodtyp har en regel som tillåter utgående trafik på port 443. Detta är nödvändigt för att slutföra kluster installationen och alla distributioner utan sådan regel kommer att Miss lyckas. I exemplet ovan i en "intern" belastningsutjämnare måste ytterligare en extern belastningsutjämnare läggas till i mallen med en regel som tillåter utgående trafik för port 443.

## <a name="next-steps"></a>Nästa steg
[Skapa ett kluster](service-fabric-cluster-creation-via-arm.md)

Efter distributionen kan du se två belastningsutjämnare i resurs gruppen. Om du bläddrar i belastningsutjämnare kan du se den offentliga IP-adressen och hanterings slut punkter (portarna 19000 och 19080) som är kopplade till den offentliga IP-adressen. Du kan också se den statiska interna IP-adressen och program slut punkten (port 80) som har tilldelats till den interna belastningsutjämnaren. Båda belastnings utjämningarna använder samma pool för skalnings uppsättning för virtuella datorer.
