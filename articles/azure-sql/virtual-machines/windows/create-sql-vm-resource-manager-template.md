---
title: Skapa SQL Server VM med en ARM-mall
description: Lär dig hur du skapar en SQL Server på en virtuell Azure-dator med hjälp av en Azure Resource Manager-mall (ARM-mall).
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359650"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Snabb start: skapa SQL Server VM med en ARM-mall

Använd den här Azure Resource Manager mallen (ARM-mallen) om du vill distribuera en SQL Server på en virtuell Azure-dator (VM). 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

SQL Server VM ARM-mallen kräver följande:

- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) och/eller [PowerShell](/powershell/scripting/install/installing-powershell). 
- En förkonfigurerad [resurs grupp](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) med ett för berett [virtuellt nätverk](../../../virtual-network/quick-create-portal.md) och [undernät](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Fem Azure-resurser definieras i mallen: 

- [Microsoft. Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): skapar en offentlig IP-adress. 
- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): skapar en nätverks säkerhets grupp. 
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): konfigurerar nätverks gränssnittet. 
- [Microsoft. Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): skapar en virtuell dator i Azure. 
- [Microsoft. SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): registrerar den virtuella datorn med SQL IaaS agent-tillägget. 

Mer SQL Server på mallar för virtuella Azure-datorer finns i [galleriet snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en virtuell dator med den avsedda SQL Server-versionen installerad och har registrerats med SQL IaaS agent-tillägget. 

   [![Distribuera till Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Välj eller ange följande värden.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: den för beredda resurs gruppen för din SQL Server VM. 
    * **Region**: Välj en region.  Välj till exempel **USA, centrala**.
    * **Namn på virtuell dator**: Ange ett namn för SQL Server virtuell dator. 
    * **Storlek på virtuell dator**: Välj lämplig storlek för den virtuella datorn i list rutan.
    * **Befintligt Virtual Network namn**: Ange namnet på det för beredda virtuella nätverket för din SQL Server VM. 
    * **Befintlig VNet-resurs grupp**: Ange resurs gruppen där ditt virtuella nätverk har förberetts. 
    * **Befintligt under näts namn**: namnet på ditt för beredda undernät. 
    * **Bild erbjudande**: välj den SQL Server och Windows Server-avbildning som passar dina affärs behov bäst. 
    * **SQL SKU**: Välj den version av SQL Server SKU som passar dina affärs behov bäst. 
    * **Admin-användar namn**: användar namnet för den virtuella datorns administratör. 
    * **Administratörs lösen ord**: lösen ordet som används av administratörs kontot för den virtuella datorn. 
    * **Typ av lagrings arbets belastning**: den typ av lagring för arbets belastningen som bäst matchar ditt företag. 
    * **Antal SQL-data diskar**: antalet diskar SQL Server används för datafiler.  
    * **Data Sök väg**: sökvägen till SQL Server-datafilerna. 
    * **Antal diskar i SQL-loggen**: antalet diskar SQL Server används för loggfiler. 
    * **Logg Sök väg**: sökvägen till SQL Server loggfilerna. 
    * **Plats**: platsen för alla resurser, det här värdet ska vara standardvärdet `[resourceGroup().location]` . 

3. Välj **Granska + skapa**. När SQL Server VM har distribuerats får du ett meddelande.

Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure CLI för att kontrol lera distribuerade resurser. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: skapa och distribuera din första ARM-mall](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Andra sätt att distribuera en SQL Server VM finns i: 
- [Azure-portalen](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Mer information finns i [Översikt över SQL Server på virtuella Azure-datorer](sql-server-on-azure-vm-iaas-what-is-overview.md).