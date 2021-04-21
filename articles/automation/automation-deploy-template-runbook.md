---
title: Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell- runbook
description: Den här artikeln beskriver hur du distribuerar en Azure Resource Manager som lagras i Azure Storage från en PowerShell-runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 20dbf9f9bbf97ed0c24ea3a525c56c7cde2db428
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834848"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Distribuera en Azure Resource Manager i en PowerShell-runbook

Du kan skriva en [Azure Automation PowerShell-runbook](./learn/automation-tutorial-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Management-mall.](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) Med mallarna kan du använda Azure Automation för att automatisera distributionen av dina Azure-resurser. Du kan underhålla dina Resource Manager på en central, säker plats, till exempel Azure Storage.

I den här artikeln skapar vi en PowerShell-runbook som använder en Resource Manager-mall som lagras [i Azure Storage](../storage/common/storage-introduction.md) för att distribuera ett Azure Storage konto.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon ännu kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [registrera dig för ett kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Ett [Automation-konto för Azure](./automation-security-overview.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage konto](../storage/common/storage-account-create.md) där mallen Resource Manager ska lagras.
* Azure PowerShell installerat på en lokal dator. Se [Installera Azure PowerShell Module för](/powershell/azure/install-az-ps) information om hur du hämtar Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här exemplet använder vi en Resource Manager som distribuerar ett nytt Azure Storage konto.

Kopiera följande text i en textredigerare:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Spara filen lokalt som **enTemplateTest.jspå**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Spara Resource Manager i Azure Storage

Nu använder vi PowerShell för att skapa Azure Storage en filresurs och ladda **uppTemplateTest.jspå** filen. Anvisningar om hur du skapar en filresurs och laddar upp en fil i Azure Portal finns i Kom [igång med Azure File Storage i Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en filresurs och ladda upp Resource Manager till den filresursen.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Skapa PowerShell-runbookskriptet

Nu ska vi skapa ett **PowerShell-skript som hämtarTemplateTest.jspå** filen från Azure Storage och distribuerar mallen för att skapa ett nytt Azure Storage konto.

Klistra in följande text i en textredigerare:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Spara filen lokalt som **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera runbooken till ditt Azure Automation konto

Nu använder vi PowerShell för att importera runbooken till ditt Azure Automation konto och sedan publicera runbooken. Information om hur du importerar och publicerar en runbook i Azure Portal finns i [Hantera runbooks i Azure Automation](manage-runbooks.md).

Om du **DeployTemplate.ps1** till ditt Automation-konto som en PowerShell-runbook kör du följande PowerShell-kommandon:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starta runbook

Nu startar vi runbooken genom att anropa [cmdleten Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) Information om hur du startar en runbook i Azure Portal finns i [Starting a runbook in Azure Automation](./start-runbooks.md).

Kör följande kommandon i PowerShell-konsolen:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

När runbooken har körts kan du kontrollera dess status genom att hämta egenskapsvärdet för jobbobjektet `$job.Status` .

Runbooken hämtar Resource Manager och använder den för att distribuera ett nytt Azure Storage konto. Du kan se att det nya lagringskontot har skapats genom att köra följande kommando:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Resource Manager finns i [Azure Resource Manager översikt.](../azure-resource-manager/management/overview.md)
* Kom igång med Azure Storage i [Introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Om du vill hitta Azure Automation användbara runbooks kan du [gå till Använda runbooks och moduler i Azure Automation](automation-runbook-gallery.md).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation#automation).
