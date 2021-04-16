---
title: 'Snabbstart: Skapa en Data Science VM – Resource Manager mall'
titleSuffix: Azure Data Science Virtual Machine
description: I den här snabbstarten använder du en Azure Resource Manager för att snabbt distribuera en Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530653"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Snabbstart: Skapa en Ubuntu-Data Science Virtual Machine med hjälp av en ARM-mall

Den här snabbstarten visar hur du skapar en Ubuntu 18.04-Data Science Virtual Machine med hjälp av en Azure Resource Manager mall (ARM-mall). Data Science Virtual Machines molnbaserade virtuella datorer förinstallerade med en uppsättning ramverk och verktyg för datavetenskap och maskininlärning. När de distribueras på GPU-drivna beräkningsresurser konfigureras alla verktyg och bibliotek för att använda GPU:n.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/services/machine-learning/) innan du börjar.

* Om du vill använda CLI-kommandona i det här dokumentet **från din lokala** miljö behöver du Azure [CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Följande resurser definieras i mallen:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines:](/azure/templates/microsoft.compute/virtualmachines)Skapa en molnbaserad virtuell dator. I den här mallen konfigureras den virtuella datorn som en Data Science Virtual Machine som kör Ubuntu 18.04.

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill använda mallen från Azure CLI loggar du in och väljer din prenumeration (se [Logga in med Azure CLI).](/cli/azure/authenticate-azure-cli) Kör sedan:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

När du kör kommandot ovan anger du:

1. Namnet på den resursgrupp som du vill skapa som ska innehålla DSVM och associerade resurser.
1. Den Azure-plats där du vill göra distributionen.
1. Den autentiseringstyp som du vill använda (ange strängen `password` eller `sshPublicKey` ).
1. Inloggningsnamnet för administratörskontot (det här värdet kanske inte är `admin` ).
1. Värdet för lösenordet eller den offentliga SSH-nyckeln för kontot.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Så här ser du Data Science Virtual Machine:

1. Gå till [Azure Portal](https://portal.azure.com)
1. Logga in.
1. Välj den resursgrupp som du nyss skapade.

Du ser resursgruppens information:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Skärmbild av en grundläggande resursgrupp som innehåller en DSVM":::

Klicka på resursen Virtuell dator för att gå till dess informationssida. Här hittar du information om den virtuella datorn, inklusive anslutningsinformation.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill använda den här virtuella datorn tar du bort den. Eftersom DSVM är associerad med andra resurser, till exempel ett lagringskonto, vill du förmodligen ta bort hela resursgruppen som du skapade. Du kan ta bort resursgruppen med hjälp av portalen genom att klicka på **knappen Ta** bort och bekräfta. Eller så kan du ta bort resursgruppen från CLI med:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Data Science Virtual Machine från en ARM-mall.

> [!div class="nextstepaction"]
> [Exempelprogram & ML-genomgångar](dsvm-samples-and-walkthroughs.md)
