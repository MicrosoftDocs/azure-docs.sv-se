---
title: Flytta Azures offentliga IP-konfiguration till en annan Azure-region med hjälp av Azure PowerShell
description: Använd Azure Resource Manager mall för att flytta den offentliga Azure-IP-konfigurationen från en Azure-region till en annan med hjälp av Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938695"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Flytta Azures offentliga IP-konfiguration till en annan region med hjälp av Azure PowerShell

Det finns olika scenarier där du vill flytta dina befintliga offentliga Azure-IP-konfigurationer från en region till en annan. Du kanske till exempel vill skapa en offentlig IP-adress med samma konfiguration och SKU för testning. Du kanske också vill flytta en offentlig IP-konfiguration till en annan region som en del av Disaster Recovery-planeringen.

**Offentliga Azure-IP-adresser är regions information och kan inte flyttas från en region till en annan.** Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen av en offentlig IP-adress.  Du kan sedan mellanlagra resursen i en annan region genom att exportera den offentliga IP-adressen till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Förutsättningar

- Se till att den offentliga Azure-IP-adressen finns i den Azure-region som du vill flytta från.

- Det går inte att flytta offentliga Azure-IP-adresser mellan regioner.  Du måste associera den nya offentliga IP-adressen till resurser i mål regionen.

- Om du vill exportera en offentlig IP-konfiguration och distribuera en mall för att skapa en offentlig IP-adress i en annan region behöver du rollen som nätverks deltagare eller högre.
   
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa offentliga IP-adresser i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till offentliga IP-adresser för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den offentliga IP-adressen för konfigurations flyttning med en Resource Manager-mall och flyttar den offentliga IP-konfigurationen till mål regionen med Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID för den offentliga IP-adress som du vill flytta till mål regionen och placera den i en variabel med hjälp av [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera det virtuella käll nätverket till en. JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

         Mer information om skillnaderna mellan grundläggande och standardiserade SKU: er för offentliga IP-adresser finns i [skapa, ändra eller ta bort en offentlig IP-adress](./virtual-network-public-ip-address.md).

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

        Mer information om fördelnings metoder och tids gräns värden för inaktivitet finns i [skapa, ändra eller ta bort en offentlig IP-adress](./virtual-network-public-ip-address.md).


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
## <a name="discard"></a>Ignorera 

När distributionen är klar, om du vill börja om eller ta bort den offentliga IP-adressen i målet, tar du bort resurs gruppen som skapades i målet och den flyttade offentliga IP-adressen tas bort.  Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av det virtuella nätverket tar du bort det virtuella käll nätverket eller resurs gruppen, använder [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) eller [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat en offentlig Azure-IP-adress från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)
