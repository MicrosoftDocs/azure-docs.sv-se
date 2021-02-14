---
title: 'ML Studio (klassisk): Distribuera arbets ytor med Azure Resource Manager – Azure'
description: Så här distribuerar du en arbets yta för Azure Machine Learning Studio (klassisk) med Azure Resource Manager-mall
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520042"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Distribuera Azure Machine Learning Studio-arbetsytan (klassisk) med Azure Resource Manager

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Genom att använda en mall för Azure Resource Manager distribution sparar du tid genom att ge dig ett skalbart sätt att distribuera sammankopplade komponenter med en mekanism för validering och återförsök. Om du vill konfigurera Azure Machine Learning Studio (klassiska) arbets ytor, till exempel, måste du först konfigurera ett Azure Storage-konto och sedan distribuera din arbets yta. Föreställ dig att göra detta manuellt för hundratals arbets ytor. Ett enklare alternativ är att använda en Azure Resource Manager mall för att distribuera en Studio (klassisk) arbets yta och alla dess beroenden. Den här artikeln vägleder dig genom den här processen steg för steg. En bra översikt över Azure Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Steg för steg: skapa en Machine Learning-arbetsyta
Vi kommer att skapa en Azure-resurs grupp och sedan distribuera ett nytt Azure Storage-konto och en ny Azure Machine Learning Studio (klassisk) arbets yta med en Resource Manager-mall. När distributionen är klar kommer vi att skriva ut viktig information om arbets ytorna som skapades (primär nyckeln, workspaceID och URL: en till arbets ytan).

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager mall

En Machine Learning-arbetsyta kräver ett Azure Storage-konto för att lagra data uppsättningen som är länkad till den.
Följande mall använder namnet på resurs gruppen för att generera lagrings kontots namn och namnet på arbets ytan.  Det använder också lagrings konto namnet som en egenskap när arbets ytan skapas.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Spara den här mallen som mlworkspace.jspå fil under c:\Temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuera resurs gruppen baserat på mallen

* Öppna PowerShell
* Installera moduler för Azure Resource Manager och Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   De här stegen laddar ned och installerar de moduler som krävs för att slutföra de återstående stegen. Detta måste bara göras en gång i miljön där du kör PowerShell-kommandona.

* Autentisera till Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Det här steget måste upprepas för varje session. När du har autentiserat bör du Visa din prenumerations information.

![Azure-konto](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu när vi har åtkomst till Azure kan vi skapa resurs gruppen.

* Skapa en resursgrupp

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrol lera att resurs gruppen har kon figurer ATS korrekt. **ProvisioningState** bör vara "lyckades".
Resurs gruppens namn används av mallen för att generera namnet på lagrings kontot. Lagrings kontots namn måste vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.

![Resursgrupp](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Distribuera en ny Machine Learning-arbetsyta med hjälp av resurs grupps distributionen.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

När distributionen är klar är det enkelt att komma åt egenskaper för den arbets yta som du har distribuerat. Du kan till exempel komma åt token för primär nyckel.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Ett annat sätt att hämta token för en befintlig arbets yta är att använda kommandot Invoke-AzResourceAction. Du kan till exempel lista de primära och sekundära token för alla arbets ytor.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
När arbets ytan har tillhandahållits kan du också automatisera många Azure Machine Learning Studio (klassiska) aktiviteter med [PowerShell-modulen för Azure Machine Learning Studio (klassisk)](https://aka.ms/amlps).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).
* Ta en titt på [lagrings platsen för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates).
* Titta på den här videon om [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Se [referens hjälp för Resource Manager-mall](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->