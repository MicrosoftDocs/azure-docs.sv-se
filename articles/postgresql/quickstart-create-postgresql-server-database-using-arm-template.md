---
title: 'Snabb start: skapa en Azure-databas för PostgreSQL-ARM-mall'
description: I den här snabb starten får du lära dig hur du skapar en Azure Database for PostgreSQL enskild server med hjälp av en Azure Resource Manager mall.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: fb9f12b3b31f1049cd4d9306294783e514331229
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382199"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Snabb start: Använd en ARM-mall för att skapa en Azure Database for PostgreSQL-enskild server

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa en Azure Database for PostgreSQL-enskild server i Azure Portal, PowerShell eller Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt kan du köra [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Granska mallen

Du skapar en Azure Database for PostgreSQL-server med en konfigurerad uppsättning beräknings-och lagrings resurser. Mer information finns i [pris nivåer i Azure Database for PostgreSQL-enskild server](concepts-pricing-tiers.md). Du skapar servern i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

Mallen definierar fem Azure-resurser:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/undernät**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforPostgreSQL/servers**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft. DBforPostgreSQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft. DBforPostgreSQL/servers/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Fler Azure Database for PostgreSQL mal sampel finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure Database for PostgreSQL Server mal len i Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

På sidan **distribuera Azure Database for PostgreSQL med VNet** :

1. För **resurs grupp** väljer du **Skapa ny**, anger ett namn för den nya resurs gruppen och väljer **OK**.

2. Om du har skapat en ny resurs grupp väljer du en **plats** för resurs gruppen och den nya servern.

3. Ange ett **Server namn**, **Administratörs inloggning** och **lösen ord för Administratörs inloggning**.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Distribuera Azure Database for PostgreSQL med VNet-fönstret, Azure snabb starts mal len Azure Portal":::

4. Ändra de andra standardinställningarna om du vill:

    * **Prenumeration**: den Azure-prenumeration som du vill använda för servern.
    * **SKU-kapacitet**: vCore-kapaciteten, som kan *vara 2* (standard), *4*, *8*, *16*, *32* eller *64*.
    * **SKU-namn**: SKU-nivåns prefix, SKU-serien och SKU-kapaciteten som ingår i under streck, till exempel *B_Gen5_1*, *GP_Gen5_2* (standard) eller *MO_Gen5_32*.
    * **SKU-storlek MB**: lagrings utrymmet i megabyte för Azure Database for postgresql server (standard *51200*).
    * **SKU-nivå**: distributions nivån, till *exempel Basic*, *generalpurpose* (standard) eller *MemoryOptimized*.
    * **SKU-familj**: *Gen4* eller *Gen5* (standard), vilket indikerar maskin varu generering för Server distribution.
    * **Postgresql-version**: den version av postgresql-servern som ska distribueras, till exempel *9,5*, *9,6*, *10* eller *11* (standard).
    * **Kvarhållning av säkerhets kopierings dagar**: den önskade perioden för kvarhållning av Geo-redundant säkerhets kopia i dagar (standard *7*).
    * **Geo-redundant säkerhets kopiering**: *aktive rad* eller *inaktive* rad (standard), beroende på krav för geo-haveri beredskap (geo-Dr).
    * **Virtual Network namn**: namnet på det virtuella nätverket (standard *azure_postgresql_vnet*).
    * **Under näts namn**: namnet på under nätet (standard *azure_postgresql_subnet*).
    * **Virtual Network regel namn**: namnet på den virtuella nätverks regel som tillåter under nätet (standard *AllowSubnet*).
    * **VNet-** adressprefix: adressprefixet för det virtuella nätverket (standard *10.0.0.0/16*).
    * **Undernätsprefixet**: adressprefixet för under nätet (standard *10.0.0.0/16*).

5. Läs de allmänna villkoren och välj sedan **Jag accepterar villkoren som anges ovan**.

6. Välj **Köp**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Använd följande interaktiva kod för att skapa en ny Azure Database for PostgreSQL server med hjälp av mallen. I koden uppmanas du att ange det nya Server namnet, namnet och platsen för en ny resurs grupp och ett administratörs konto namn och lösen ord.

Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Använd följande interaktiva kod för att skapa en ny Azure Database for PostgreSQL server med hjälp av mallen. I koden uppmanas du att ange det nya Server namnet, namnet och platsen för en ny resurs grupp och ett administratörs konto namn och lösen ord.

Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din nya Azure Database for PostgreSQL Server:

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure Database for PostgreSQL servrar**.

2. I listan databas väljer du den nya servern. **Översikts** sidan för din nya Azure Database for postgresql-server visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod om du vill visa information om din Azure Database for PostgreSQL-Server. Du måste ange namnet på den nya servern.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod om du vill visa information om din Azure Database for PostgreSQL-Server. Du måste ange namnet och resurs gruppen för den nya servern.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Exportera ARM-mall från portalen
Du kan [Exportera en arm-mall](../azure-resource-manager/templates/export-template-portal.md) från Azure Portal. Det finns två sätt att exportera en mall:

- [Exportera från resurs grupp eller resurs](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Med det här alternativet skapas en ny mall från befintliga resurser. Den exporterade mallen är en "ögonblicks bild" av resurs gruppens aktuella tillstånd. Du kan exportera en hel resurs grupp eller vissa resurser inom den resurs gruppen.
- [Exportera före distribution eller från historik](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Med det här alternativet hämtas en exakt kopia av en mall som används för distribution.

När du exporterar mallen visas i ```"properties":{ }```  avsnittet i postgresql-serverns resurs att du ser att ```administratorLogin``` och ```administratorLoginPassword``` inte tas med av säkerhets skäl. Du **måste** lägga till de här parametrarna i mallen innan du distribuerar mallen, annars Miss kommer mallen.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```



## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper**.

2. I listan resurs grupp väljer du namnet på din resurs grupp.

3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.

4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

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

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
