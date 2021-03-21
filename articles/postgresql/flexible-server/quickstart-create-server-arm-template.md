---
title: 'Snabb start: skapa en Azure DB för PostgresSQL flexibel Server-ARM-mall'
description: I den här snabb starten får du lära dig hur du skapar en Azure Database for PostgresSQL-flexibel server med ARM-mall.
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 2/11/2021
ms.openlocfilehash: 1f6abb62086bf92be8ae2fe50abbfa5300185fd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384273"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>Snabb start: Använd en ARM-mall för att skapa en Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Flexibel Server är en hanterad tjänst som du använder för att köra, hantera och skala hög tillgängliga PostgreSQL-databaser i molnet. Du kan använda en Azure Resource Manager mall (ARM-mall) för att etablera en PostgreSQL flexibel Server för att distribuera flera servrar eller flera databaser på en server.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure Resource Manager är Azures tjänst för distribution och hantering. Den ger dig ett hanteringslager där du kan skapa, uppdatera och ta bort resurser i ditt Azure-konto. Du kan använda hanteringsfunktioner som åtkomstkontroll, lås och taggar till att skydda och organisera dina resurser efter distributionen. Mer information om Azure Resource Manager mallar finns [malldistribution översikt](../../azure-resource-manager/templates/overview.md).

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Granska mallen

En Azure Database for PostgresSQL-Server är överordnad resurs för en eller flera databaser inom en region. Det tillhandahåller omfånget för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller och konfigurationer.

Skapa en _postgres-flexible-server-template.jspå_ filen och kopiera följande JSON-skript till den.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

Dessa resurser definieras i mallen:

- Microsoft. DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>Distribuera mallen

Välj **testa** från följande PowerShell-kodblock för att öppna Azure Cloud Shell.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Följ de här stegen för att kontrol lera om servern har skapats i Azure.

# <a name="azure-portal"></a>[Azure-portalen](#tab/portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure Database for PostgreSQL flexibla servrar (för hands version)**.
1. I listan databas väljer du den nya servern för att se **översikts** sidan för att hantera servern.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Du måste ange namnet på den nya servern om du vill visa information om din Azure Database for PostgreSQL flexibla Server.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Du måste ange namnet och resurs gruppen för den nya servern om du vill visa information om din Azure Database for PostgreSQL flexibla Server.

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---


## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resurs gruppen, servern och den enkla databasen om du vill gå vidare till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

Så här tar du bort resursgruppen:

# <a name="portal"></a>[Portal](#tab/azure-portal)

I [portalen](https://portal.azure.com)väljer du den resurs grupp som du vill ta bort.

1. Välj **Ta bort resursgrupp**.
1. Bekräfta borttagningen genom att skriva namnet på resurs gruppen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med dumpa och Återställ](../howto-migrate-using-dump-and-restore.md)
