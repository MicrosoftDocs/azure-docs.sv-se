---
title: Flytta interna Azure-Load Balancer till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager mall för att flytta interna Azure-Load Balancer från en Azure-region till en annan med hjälp av Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 2c89ad69207a51a92b56d268c685aa2be4118cf1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507593"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Flytta interna Azure-Load Balancer till en annan region med hjälp av PowerShell

Det finns olika scenarier där du vill flytta din befintliga interna belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en intern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en intern belastningsutjämnare till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta Azures interna belastningsutjämnare från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och det virtuella nätverket för en intern belastningsutjämnare.  Du kan sedan mellanlagra resursen i en annan region genom att exportera belastningsutjämnaren och virtuella nätverk till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Förutsättningar

- Se till att den interna belastningsutjämnaren i Azure är i den Azure-region som du vill flytta.

- Det går inte att flytta Azures interna belastningsutjämnare mellan regioner.  Du måste koppla den nya belastningsutjämnaren till resurser i mål regionen.

- Om du vill exportera en intern belastnings Utjämnings konfiguration och distribuera en mall för att skapa en intern belastningsutjämnare i en annan region behöver du rollen som nätverks deltagare eller högre.
   
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper, virtuella datorer och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa interna belastningsutjämnare i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att lägga till belastningsutjämnare för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den interna belastningsutjämnaren för flytt med hjälp av en Resource Manager-mall och flyttar den interna belastnings Utjämnings konfigurationen till mål regionen med Azure PowerShell.  Som en del av den här processen måste den virtuella belastnings utjämningens virtuella nätverks konfiguration tas med och måste utföras först innan den interna belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportera mallen för det virtuella nätverket och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Hämta resurs-ID för det virtuella nätverk som du vill flytta till mål regionen och placera det i en variabel med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera det virtuella käll nätverket till en. JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **DefaultValue** för det virtuella käll nätverks namnet till namnet på det virtuella mål nätverket, se till att namnet är i citat tecken:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Om du vill redigera mål regionen där det virtuella nätverket ska flyttas ändrar du egenskapen **location** under resurser:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Du kan också ändra andra parametrar i **\<resource-group-name> . JSON** -filen om du väljer och är valfria beroende på dina krav:

    * **Adress utrymme** – det virtuella nätverkets adress utrymme kan ändras innan du sparar genom att ändra avsnittet **Resources**  >  **addressSpace** och ändra egenskapen **addressPrefixes** i **\<resource-group-name> JSON** -filen:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Undernät** – under nätets namn och under nätets adress utrymme kan ändras eller läggas till genom att ändra avsnittet **undernät** i **\<resource-group-name> JSON** -filen. Namnet på under nätet kan ändras genom att ändra egenskapen **Name** . Adress utrymmet för under nätet kan ändras genom att ändra egenskapen **addressPrefix** i **\<resource-group-name> JSON** -filen:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         I **\<resource-group-name> JSON** -filen, för att ändra adressprefixet, måste det redige ras på två platser, avsnittet som anges ovan och i avsnittet **typ** nedan.  Ändra egenskapen **addressPrefix** så att den matchar det som beskrivs ovan:

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Spara **\<resource-group-name> . JSON** -filen.

10. Skapa en resurs grupp i mål regionen som det virtuella mål-VNET ska distribueras med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuera den redigerade **\<resource-group-name> . JSON** -filen till resurs gruppen som skapades i föregående steg med [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Om du vill kontrol lera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) och [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den interna belastnings Utjämnings mal len och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID för den interna belastningsutjämnare som du vill flytta till mål regionen och placera den i en variabel med [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera den interna käll konfigurationen för belastnings utjämning till en JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för den interna belastnings Utjämnings namnet ändrar du egenskapen **DefaultValue** för det interna namnet på den interna belastningsutjämnaren till namnet på den interna mål belastnings utjämningen, se till att namnet är inom citat tecken:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Om du vill redigera värdet för det virtuella mål nätverket som flyttades ovan måste du först skaffa resurs-ID och sedan kopiera och klistra in det i **\<resource-group-name> . JSON** -filen.  Hämta ID: t med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Skriv variabeln och tryck på RETUR för att Visa resurs-ID.  Markera ID-sökvägen och kopiera den till Urklipp:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  I **\<resource-group-name> JSON** -filen klistrar du in **resurs-ID: t** från variabeln i stället för **DefaultValue** i den andra parametern för det virtuella nätverks-ID: t, se till att du omger sökvägen i citat tecken:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Om du vill redigera mål regionen där den interna belastnings Utjämnings konfigurationen ska flyttas ändrar du egenskapen **location** under **resurser** i **\<resource-group-name> JSON** -filen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:
    
    * **SKU** – du kan ändra SKU: n för den interna belastningsutjämnaren i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen SKU-   >  **namn** i **\<resource-group-name> JSON** -filen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Mer information om skillnaderna mellan Basic-och standard SKU-belastningsutjämnare finns i [Översikt över Azure standard Load Balancer](./load-balancer-overview.md)

    * **Belastnings Utjämnings regler** – du kan lägga till eller ta bort belastnings Utjämnings regler i konfigurationen genom att lägga till eller ta bort poster i avsnittet **loadBalancingRules** i **\<resource-group-name> JSON** -filen:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Mer information om belastnings Utjämnings regler finns i [Vad är Azure Load Balancer?](./load-balancer-overview.md)

    * **Avsökningar** – du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i **\<resource-group-name> JSON** -filen:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Mer information om Azure Load Balancer hälso avsökningar finns i [Load Balancer hälso avsökningar](./load-balancer-custom-probe-overview.md)

    * **Ingående NAT-regler** – du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i **inboundNatRules** -avsnittet i **\<resource-group-name> JSON** -filen:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        För att slutföra tillägg eller borttagning av en inkommande NAT-regel måste regeln finnas eller tas bort som en **typ** egenskap i slutet av **\<resource-group-name> . JSON** -filen:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Mer information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](./load-balancer-overview.md)
    
13. Spara **\<resource-group-name> . JSON** -filen.
    
10. Skapa eller en resurs grupp i mål regionen för den interna mål belastnings utjämning som ska distribueras med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Den befintliga resurs gruppen ovan kan också återanvändas som en del av den här processen:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuera den redigerade **\<resource-group-name> . JSON** -filen till resurs gruppen som skapades i föregående steg med [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill kontrol lera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) och [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Ignorera 

Om du vill börja om eller ta bort det virtuella nätverket och belastningsutjämnaren i målet efter distributionen, tar du bort resurs gruppen som skapades i målet och det flyttade virtuella nätverket och belastningsutjämnaren tas bort.  Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av NSG tar du bort käll NSG eller resurs gruppen, använder [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) eller [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) och [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en intern Azure-belastningsutjämnare från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)