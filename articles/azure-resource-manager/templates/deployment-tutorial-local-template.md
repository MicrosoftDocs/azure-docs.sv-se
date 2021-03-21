---
title: Självstudie – distribuera en lokal Azure Resource Manager-mall
description: Lär dig hur du distribuerar en Azure Resource Manager-mall (ARM-mall) från den lokala datorn
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384239"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Självstudie: Distribuera en lokal ARM-mall

Lär dig hur du distribuerar en Azure Resource Manager-mall (ARM-mall) från den lokala datorn. Det tar ungefär **8 minuter** att slutföra.

Den här självstudien är den första i en serie. När du går igenom serien modularize du mallen genom att skapa en länkad mall. du lagrar den länkade mallen i ett lagrings konto och skyddar den länkade mallen med hjälp av SAS-token och du får lära dig hur du skapar en DevOps-pipeline för att distribuera mallar. Den här serien fokuserar på mall-distribution. Om du vill lära dig att utveckla mallar kan du läsa mer i [nybörjar självstudierna](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Hämta verktyg

Låt oss börja med att kontrol lera att du har de verktyg du behöver för att distribuera mallar.

### <a name="command-line-deployment"></a>Kommando rads distribution

Du behöver antingen Azure PowerShell eller Azure CLI för att distribuera mallen. Installations anvisningar finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)
- [Installera Azure CLI på macOS](/cli/azure/install-azure-cli-macos)

När du har installerat antingen Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Mer information finns i [Logga in-PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Redigerare (valfritt)

Mallar är JSON-filer. Om du vill granska/redigera mallar behöver du en lämplig JSON-redigerare. Vi rekommenderar Visual Studio Code med Resource Manager Tools-tillägget. Om du behöver installera dessa verktyg, se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Granska mall

Mallen distribuerar ett lagrings konto, en app service-plan och en webbapp. Om du är intresse rad av att skapa mallen kan du gå igenom [självstudier om snabb starts mallar](template-tutorial-quickstart-template.md). Det krävs dock inte för att slutföra den här kursen.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får inte innehålla siffror och gemener. Namnet måste vara unikt. I mallen är lagrings kontots namn det projekt namn där **Store** lagts till och projekt namnet måste innehålla mellan 3 och 11 tecken. Projekt namnet måste uppfylla kraven på lagrings kontots namn och innehålla färre än 11 tecken.

Spara en kopia av mallen på din lokala dator med _. JSON_ -tillägget, till exempel _azuredeploy.jspå_. Du distribuerar den här mallen senare i självstudien.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill börja arbeta med Azure PowerShell/Azure CLI för att distribuera en mall loggar du in med dina Azure-autentiseringsuppgifter.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Om du har flera Azure-prenumerationer väljer du den prenumeration som du vill använda. Ersätt `[SubscriptionID/SubscriptionName]` och hakparenteserna `[]` med din prenumerations information:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Skapa resursgrupp

När du distribuerar en mall anger du en resurs grupp som ska innehålla resurserna. Innan du kör distributionskommandot skapar du resursgruppen med antingen Azure CLI eller Azure PowerShell. Välj flikarna i följande kod avsnitt om du vill välja mellan Azure PowerShell och Azure CLI. CLI-exemplen i den här artikeln är skrivna för bash-gränssnittet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuera mallen

Använd ett eller båda distributions alternativ för att distribuera mallen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Mer information om hur du distribuerar mallar med hjälp av Azure PowerShell finns i [distribuera resurser med ARM-mallar och Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Mer information om hur du distribuerar mallar med hjälp av Azure CLI finns i [distribuera resurser med ARM-mallar och Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en lokal mall. I nästa självstudie separerar du mallen till en huvudfil och en länkad mall och lär dig hur du lagrar och skyddar den länkade mallen.

> [!div class="nextstepaction"]
> [Distribuera en länkad mall](./deployment-tutorial-linked-template.md)
