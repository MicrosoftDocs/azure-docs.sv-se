---
title: Flytta externa Azure-Load Balancer till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager mall för att flytta externa Azure-Load Balancer från en Azure-region till en annan med hjälp av Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 94cb46536bcf029a9e71a7238772ccc7b186b1dc
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500282"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Flytta externa Azure-Load Balancer till en annan region med Azure PowerShell

Det finns olika scenarier där du vill flytta din befintliga externa belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en extern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en extern belastningsutjämnare till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta externa Azure-belastningsutjämnare från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera den befintliga konfigurationen och den offentliga IP-adressen till en extern belastningsutjämnare.  Du kan sedan mellanlagra resursen i en annan region genom att exportera belastningsutjämnaren och offentliga IP-adresser till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Förutsättningar

- Se till att den externa Azure-belastningsutjämnaren finns i den Azure-region som du vill flytta.

- Det går inte att flytta externa Azure-belastningsutjämnare mellan regioner.  Du måste koppla den nya belastningsutjämnaren till resurser i mål regionen.

- Om du vill exportera en extern belastnings Utjämnings konfiguration och distribuera en mall för att skapa en extern belastningsutjämnare i en annan region behöver du rollen som nätverks deltagare eller högre.
   
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper, offentliga IP-adresser och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa externa belastningsutjämnare i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att lägga till belastningsutjämnare för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den externa belastningsutjämnaren för flytt med hjälp av en Resource Manager-mall och flyttar den externa belastnings Utjämnings konfigurationen till mål regionen med Azure PowerShell.  Som en del av den här processen måste den offentliga IP-konfigurationen för den externa belastningsutjämnaren inkluderas och måste först utföras innan den externa belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportera den offentliga IP-mallen och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Hämta resurs-ID för den offentliga IP-adress som du vill flytta till mål regionen och placera den i en variabel med hjälp av [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera den offentliga käll-IP-adressen till en. JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det offentliga IP-namnet ändrar du egenskapen **DefaultValue** för det offentliga IP-namnet för källan till namnet på din publika IP-adress, se till att namnet är i citat tecken:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Om du vill redigera mål regionen där den offentliga IP-adressen ska flyttas ändrar du egenskapen **location** under resurser:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **SKU** – du kan ändra SKU: n för den offentliga IP-adressen i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen **SKU**-  >  **namn** i **\<resource-group-name> JSON** -filen:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Mer information om skillnaderna mellan grundläggande och standardiserade SKU: er för offentliga IP-adresser finns i [skapa, ändra eller ta bort en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md).

    * **Metod för offentlig IP-tilldelning** och **tids gräns för inaktivitet** – du kan ändra båda alternativen i mallen genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller **statisk** till **dynamisk**. Tids gränsen för inaktivitet kan ändras genom att ändra egenskapen **idleTimeoutInMinutes** till önskad mängd.  Standardvärdet är **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Mer information om fördelnings metoder och tids gräns värden för inaktivitet finns i [skapa, ändra eller ta bort en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md).


9. Spara **\<resource-group-name> . JSON** -filen.

10. Skapa en resurs grupp i mål regionen för den offentliga mål-IP-adressen som ska distribueras med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuera den redigerade **\<resource-group-name> . JSON** -filen till resurs gruppen som skapades i föregående steg med [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill kontrol lera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) och [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den externa belastnings Utjämnings mal len och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID för den externa belastningsutjämnare som du vill flytta till mål regionen och placera den i en variabel med [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer):

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera den externa käll konfigurationen för belastnings utjämning till en JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för den externa belastnings Utjämnings namnet ändrar du egenskaps- **defaultvalueen** för det externa namnet på den externa belastningsutjämnaren till namnet på den externa mål belastnings utjämningen, se till att namnet är inom citat tecken:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Om du vill redigera värdet för den offentliga mål-IP-adress som flyttades ovan måste du först hämta resurs-ID: t och sedan kopiera och klistra in det i **\<resource-group-name> . JSON** -filen.  Hämta ID: t med [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Skriv variabeln och tryck på RETUR för att Visa resurs-ID.  Markera ID-sökvägen och kopiera den till Urklipp:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  I **\<resource-group-name> JSON** -filen klistrar du in **resurs-ID: t** från variabeln i stället för **DefaultValue** i den andra parametern för det offentliga IP-ID: t, se till att du omger sökvägen i citat tecken:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Om du har konfigurerat utgående NAT och utgående regler för belastningsutjämnaren finns en tredje post i den här filen för det externa ID: t för den utgående offentliga IP-adressen.  Upprepa stegen ovan i **mål regionen** för att hämta ID: t för den utgående offentliga iP-adressen och klistra in posten i **\<resource-group-name> . JSON** -filen:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Om du vill redigera mål regionen där den externa belastnings Utjämnings konfigurationen ska flyttas ändrar du egenskapen **location** under **resurser** i **\<resource-group-name> JSON** -filen:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **SKU** – du kan ändra SKU: n för den externa belastningsutjämnaren i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen SKU-   >  **namn** i **\<resource-group-name> JSON** -filen:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **Utgående regler** – du kan lägga till eller ta bort utgående regler i konfigurationen genom att redigera egenskapen **outboundRules** i **\<resource-group-name> JSON** -filen:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Mer information om utgående regler finns i [Load Balancer utgående regler](./load-balancer-outbound-connections.md#outboundrules)

13. Spara **\<resource-group-name> . JSON** -filen.
    
10. Skapa eller en resurs grupp i mål regionen för den externa mål belastnings utjämning som ska distribueras med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Den befintliga resurs gruppen ovan kan också återanvändas som en del av den här processen:
    
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

Om du efter distributionen vill börja om eller ta bort den offentliga IP-adressen och belastningsutjämnaren i målet, tar du bort resurs gruppen som skapades i målet och den flyttade offentliga IP-adressen och belastningsutjämnaren kommer att tas bort.  Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av NSG tar du bort käll NSG eller resurs gruppen, använder [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) eller [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) och [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat en Azure-nätverks säkerhets grupp från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)