---
title: Flytta Azures nätverks säkerhets grupp (NSG) till en annan Azure-region med hjälp av Azure PowerShell
description: Använd Azure Resource Manager mall för att flytta Azures nätverks säkerhets grupp från en Azure-region till en annan med hjälp av Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934989"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Flytta Azures nätverks säkerhets grupp (NSG) till en annan region med hjälp av Azure PowerShell

Det finns olika scenarier där du vill flytta befintliga NSG: er från en region till en annan. Du kanske till exempel vill skapa en NSG med samma konfigurations-och säkerhets regler för testning. Du kanske också vill flytta en NSG till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta Azure-säkerhetsgrupper från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera befintliga konfigurations-och säkerhets regler för en NSG.  Du kan sedan mellanlagra resursen i en annan region genom att exportera NSG till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att Azure-nätverks säkerhets gruppen finns i den Azure-region som du vill flytta.

- Det går inte att flytta Azures nätverks säkerhets grupper mellan regioner.  Du måste associera den nya NSG till resurser i mål regionen.

- Om du vill exportera en NSG-konfiguration och distribuera en mall för att skapa en NSG i en annan region behöver du rollen som nätverks deltagare eller högre.
   
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, offentliga IP-adresser och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa NSG: er i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till NSG: er för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder nätverks säkerhets gruppen för konfigurationen och säkerhets regeln flyttas med hjälp av en Resource Manager-mall och flyttar NSG-konfigurationen och säkerhets reglerna till mål regionen med hjälp av Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID för den NSG som du vill flytta till mål regionen och placera den i en variabel med hjälp av [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera käll-NSG till en. JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot med namnet **\<resource-group-name> . JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för NSG-namnet ändrar du egenskapen **DefaultValue** för käll NSG namn till namnet på din mål-NSG, se till att namnet är i citat tecken:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Om du vill redigera mål regionen där NSG-konfigurationen och säkerhets reglerna ska flyttas ändrar du egenskapen **location** under **resurser**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Du kan också ändra andra parametrar i **\<resource-group-name> . JSON** om du väljer och är valfria beroende på dina krav:

    * **Säkerhets regler** – du kan redigera vilka regler som distribueras till mål-NSG genom att lägga till eller ta bort regler i **securityRules** -avsnittet i **\<resource-group-name> JSON** -filen:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Om du vill slutföra tillägg eller borttagning av reglerna i mål-NSG måste du också redigera anpassade regel typer i slutet av **\<resource-group-name> . JSON** -filen i formatet i exemplet nedan:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Spara **\<resource-group-name> . JSON** -filen.

10. Skapa en resurs grupp i mål regionen som mål-NSG ska distribueras med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuera den redigerade **\<resource-group-name> . JSON** -filen till resurs gruppen som skapades i föregående steg med [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill kontrol lera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) och [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Ignorera 

När distributionen är klar, om du vill börja om eller ta bort NSG i målet, tar du bort resurs gruppen som skapades i målet och den flyttade NSG tas bort.  Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av NSG tar du bort käll NSG eller resurs gruppen, använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) eller [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat en Azure-nätverks säkerhets grupp från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)
