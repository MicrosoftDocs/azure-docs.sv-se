---
title: 'Azure Resource Manager: skapa en enskild databas'
description: Skapa en enskild databas i Azure SQL Database med en Azure Resource Manager-mall.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96187093"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>Snabb start: skapa en enda databas i Azure SQL Database med en ARM-mall

Att skapa en [enskild databas](single-database-overview.md) är det snabbaste och enklaste alternativet för att skapa en databas i Azure SQL Database. Den här snabb starten visar hur du skapar en enskild databas med en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Granska mallen

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [köpmodellerna](purchasing-models.md). När du skapar en enskild databas definierar du också en [Server](logical-servers.md) för att hantera den och placerar den i [Azure-resurs gruppen](../../active-directory-b2c/overview.md) i en angiven region.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-sql-database/).

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

Dessa resurser definieras i mallen:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/Servers/databaser**](/azure/templates/microsoft.sql/servers/databases)

Fler Azure SQL Database mal sampel finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

Välj **testa** från följande PowerShell-kodblock för att öppna Azure Cloud Shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>Verifiera distributionen

Information om hur du frågar databasen finns i [fråga databasen](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resurs gruppen, servern och den enkla databasen om du vill gå vidare till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

Så här tar du bort resursgruppen:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

- Skapa en brandväggsregel på servernivå för att ansluta till den enkla databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](connect-query-content-reference-guide.md) databasen med hjälp av flera olika verktyg och språk.
  - [Ansluta och köra frågor med SQL Server Management Studio](connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- Information om hur du skapar en enskild databas med Azure CLI finns i [Azure CLI-exempel](az-cli-script-samples-content-guide.md).
- Om du vill skapa en enskild databas med hjälp av Azure PowerShell, se [Azure PowerShell exempel](powershell-script-content-guide.md).
- Information om hur du skapar ARM-mallar finns i [skapa din första mall](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).