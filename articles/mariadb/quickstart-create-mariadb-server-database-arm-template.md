---
title: 'Snabbstart: Skapa en Azure DB for MariaDB – ARM-mall'
description: I den här snabbstartsartikeln lär du dig hur du skapar Azure Database for MariaDB server med hjälp av en Azure Resource Manager mall.
author: savjani
ms.author: pariks
ms.date: 05/14/2020
ms.topic: quickstart
ms.service: mariadb
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6bf7f4d30f2ad4f9e0181aed332e3f6cb9265ca0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531327"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Snabbstart: Använda en ARM-mall för att skapa en Azure Database for MariaDB server

Azure Database for MariaDB är en hanterad tjänst som du använder för att köra, hantera och skala Hög tillgängliga MariaDB-databaser i molnet. I den här snabbstarten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure Database for MariaDB-server i Azure Portal, PowerShell eller Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)
* Om du vill köra koden lokalt kan du [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)
* Om du vill köra koden lokalt, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Granska mallen

Du skapar en Azure Database for MariaDB-server med en definierad uppsättning beräknings- och lagringsresurser. Mer information finns [i Azure Database for MariaDB olika prisnivåer.](concepts-pricing-tiers.md) Du skapar servern i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

Mallen definierar fem Azure-resurser:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforServeraDB/servers**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforServeraDB/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforServeraDB/servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Fler Azure Database for MariaDB finns i [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure Database for MariaDB servermallen i Azure Portal:

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

På sidan **Azure Database for MariaDB med VNet:**

1. För **Resursgrupp** väljer du **Skapa ny,** anger ett namn för den nya resursgruppen och väljer **OK.**

2. Om du har skapat en ny resursgrupp väljer du **en** Plats för resursgruppen och den nya servern.

3. Ange ett **servernamn,** **administratörsinloggning** och **inloggningslösenord för administratör.**

    ![Distribuera Azure Database for MariaDB med VNet-fönstret, Azure-snabbstartsmallen Azure Portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Ändra de andra standardinställningarna om du vill:

    * **Prenumeration:** Den Azure-prenumeration som du vill använda för servern.
    * **SKU-kapacitet:** vCore-kapaciteten, som kan vara *2* (standard), *4*, *8*, *16,* *32* eller *64*.
    * **SKU-namn:** SKU-nivåprefixet, SKU-familjen och SKU-kapaciteten, som är sammankopplade med understreck, till exempel *B_Gen5_1,* *GP_Gen5_2* (standard) *eller MO_Gen5_32*.
    * **SKU-storlek MB:** lagringsstorleken, i megabyte, för Azure Database for MariaDB *(standard: 51200*).
    * **SKU-nivå:** distributionsnivån, till *exempel Basic,* *GeneralPurpose* (standard) eller *MemoryOptimized*.
    * **SKU-familj:** *Gen4* eller *Gen5* (standard), vilket anger maskinvarugenerering för serverdistribution.
    * **Mariadb-version:** den version av MariaDB-servern som ska distribueras, till exempel *10.2* eller *10.3* (standard).
    * **Kvarhållningsdagar** för säkerhetskopior: Önskad period för geo-redundant kvarhållning av säkerhetskopior, i dagar (standard *7*).
    * **Geo-redundant** *säkerhetskopiering: Aktiverad* *eller* inaktiverad (standard), beroende på kraven för geo-haveriberedskap (Geo-DR).
    * **Virtual Network namn:** namnet på det virtuella nätverket (standard *azure_mariadb_vnet*).
    * **Undernätsnamn:** namnet på undernätet *(standardnamnet azure_mariadb_subnet*).
    * **Virtual Network regelnamn:** namnet på den regel för virtuellt nätverk som tillåter undernätet (standard *AllowSubnet*).
    * **Vnet-adressprefix:** adressprefixet för det virtuella nätverket *(standard: 10.0.0.0/16*).
    * **Undernätsprefix:** adressprefixet för *undernätet (standard: 10.0.0.0/16*).

5. Läs villkoren och välj sedan Jag **godkänner villkoren som anges ovan.**

6. Välj **Köp**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Använd följande interaktiva kod för att skapa en Azure Database for MariaDB server med hjälp av mallen. Koden uppmanar dig att ange det nya servernamnet, namnet och platsen för en ny resursgrupp samt ett administratörskontonamn och lösenord.

Om du vill köra koden i Azure Cloud Shell du **Prova i** det övre hörnet av ett kodblock.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Använd följande interaktiva kod för att skapa en Azure Database for MariaDB server med hjälp av mallen. Koden uppmanar dig att ange det nya servernamnet, namnet och platsen för en ny resursgrupp samt ett administratörskontonamn och lösenord.

Om du vill köra koden i Azure Cloud Shell du **Prova i** det övre hörnet av ett kodblock.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din Azure Database for MariaDB server:

1. I listan [Azure Portal](https://portal.azure.com)du efter och väljer **Azure Database for MariaDB servrar**.

2. I databaslistan väljer du den nya servern. **Översiktssidan** för den nya Azure Database for MariaDB servern visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod för att visa information om din Azure Database for MariaDB server. Du måste ange namnet på den nya servern.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod för att visa information om din Azure Database for MariaDB server. Du måste ange namnet och resursgruppen för den nya servern.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen, vilket tar bort resurserna i resursgruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)du efter och väljer **Resursgrupper**.

2. I listan över resursgrupper väljer du namnet på din resursgrupp.

3. På sidan **Översikt** för resursgruppen väljer du Ta **bort resursgrupp.**

4. I bekräftelsedialogrutan skriver du namnet på resursgruppen och väljer sedan Ta **bort.**

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Nästa steg

En stegvis självstudiekurs som vägleder dig genom processen för att skapa en ARM-mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
