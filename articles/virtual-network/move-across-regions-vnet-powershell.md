---
title: Flytta ett virtuellt Azure-nätverk till en annan Azure-region med hjälp av Azure PowerShell
description: Flytta ett virtuellt Azure-nätverk från en Azure-region till en annan med hjälp av en Resource Manager-mall och Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934923"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Flytta ett virtuellt Azure-nätverk till en annan region med hjälp av Azure PowerShell

Det finns olika scenarier för att flytta ett befintligt virtuellt Azure-nätverk från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet som ditt befintliga virtuella nätverk. Eller så kanske du vill flytta ett virtuellt produktions nätverk till en annan region som en del av Disaster Recovery-planeringen.

Du kan använda en Azure Resource Manager-mall för att slutföra flyttningen av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen. Mer information om Resource Manager-mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Förutsättningar

- Se till att ditt virtuella nätverk finns i den Azure-region som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region måste du ha rollen som nätverks deltagare eller högre.

- Peering för virtuella nätverk återskapas inte, och de kommer inte att fungera om de fortfarande finns i mallen. Innan du exporterar mallen måste du ta bort alla virtuella nätverks-peer-datorer. Du kan sedan återupprätta dem efter det att det virtuella nätverket har flyttats.
    
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser.

- Kontrol lera att din Azure-prenumeration låter dig skapa virtuella nätverk i mål regionen. Kontakta supporten om du vill aktivera den begärda kvoten.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till virtuella nätverk för den här processen. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Förbered för flytt
I det här avsnittet förbereder du det virtuella nätverket för flytten med hjälp av en Resource Manager-mall. Sedan flyttar du det virtuella nätverket till mål regionen genom att använda Azure PowerShell kommandon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här exporterar du det virtuella nätverket och distribuerar det virtuella mål nätverket med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ sedan anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Hämta resurs-ID för det virtuella nätverk som du vill flytta till mål regionen och placera det sedan i en variabel med hjälp av [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportera det virtuella käll nätverket till en. JSON-fil i den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Den hämtade filen har samma namn som resurs gruppen som resursen exporterades från. Leta upp *\<resource-group-name> JSON* -filen som du exporterade med kommandot och öppna den i din redigerare:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **DefaultValue** för det virtuella käll nätverks namnet till namnet på det virtuella mål nätverket. Var noga med att skriva namnet inom citat tecken.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Om du vill redigera mål regionen där det virtuella nätverket ska flyttas ändrar du egenskapen **location** under resurser:

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
  
1. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Valfritt Du kan också ändra andra parametrar i *\<resource-group-name> . JSON* -filen, beroende på dina krav:

    * **Adress utrymme**: innan du sparar filen kan du ändra adress utrymmet för det virtuella nätverket genom att ändra avsnittet **Resources**  >  **addressSpace** och ändra egenskapen **addressPrefixes** :

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

    * **Undernät**: du kan ändra eller lägga till i under nätets namn och under nätets adress utrymme genom att ändra filens **undernät** . Du kan ändra namnet på under nätet genom att ändra egenskapen **namn** . Och du kan ändra adress utrymmet för under nätet genom att ändra egenskapen **addressPrefix** :

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

        Om du vill ändra adressprefixet redigerar du filen på två platser: i koden i föregående avsnitt och i avsnittet **typ** i följande kod. Ändra egenskapen **addressPrefix** i följande kod så att den matchar egenskapen **addressPrefix** i koden i föregående avsnitt.

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

1. Spara *\<resource-group-name> . JSON* -filen.

1. Skapa en resurs grupp i mål regionen för det virtuella mål nätverket som ska distribueras med hjälp av [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Distribuera den redigerade *\<resource-group-name> . JSON* -filen till resurs gruppen som du skapade i föregående steg genom att använda [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. För att verifiera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) och [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Ta bort det virtuella nätverket eller resurs gruppen 

När du har distribuerat det virtuella nätverket, för att starta eller ta bort det virtuella nätverket i mål regionen, tar du bort resurs gruppen som du skapade i mål regionen och det flyttade virtuella nätverket tas bort. 

Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Rensa

Gör något av följande om du vill spara ändringarna och slutföra den virtuella nätverks flyttningen:

* Ta bort resurs gruppen med hjälp av [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Ta bort det virtuella käll nätverket med hjälp av [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork):  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett virtuellt nätverk från en region till en annan med hjälp av PowerShell och rensade de onödiga käll resurserna. Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:

- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Flytta virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)
