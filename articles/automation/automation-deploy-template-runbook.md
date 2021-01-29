---
title: Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell- runbook
description: Den här artikeln beskriver hur du distribuerar en Azure Resource Manager-mall som lagras i Azure Storage från en PowerShell-Runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: PowerShell, Runbook, JSON, Azure Automation
ms.openlocfilehash: d9b443d1840840d3d6202140da235589c73453cc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051404"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Distribuera en Azure Resource Manager-mall i en PowerShell-Runbook

Du kan skriva en [Azure Automation PowerShell-Runbook](./learn/automation-tutorial-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Management-mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Med mallarna kan du automatisera distributionen av dina Azure-resurser med hjälp av Azure Automation. Du kan underhålla Resource Manager-mallarna på en central, säker plats, till exempel Azure Storage.

I den här artikeln skapar vi en PowerShell-Runbook som använder en Resource Manager-mall som lagras i [Azure Storage](../storage/common/storage-introduction.md) för att distribuera ett nytt Azure Storage-konto.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har något än kan du [aktivera din prenumeration på MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](./automation-security-overview.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage konto](../storage/common/storage-account-create.md) där du vill lagra Resource Manager-mallen.
* Azure PowerShell installerad på en lokal dator. Se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps) för information om hur du hämtar Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här exemplet använder vi en Resource Manager-mall som distribuerar ett nytt Azure Storage-konto.

Kopiera följande text i en text redigerare:

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

Spara filen lokalt som **TemplateTest.jspå**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Spara Resource Manager-mallen i Azure Storage

Nu använder vi PowerShell för att skapa en Azure Storage fil resurs och ladda upp **TemplateTest.jspå** filen. Instruktioner för hur du skapar en fil resurs och laddar upp en fil i Azure Portal finns i [komma igång med Azure File Storage i Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en fil resurs och ladda upp Resource Manager-mallen till fil resursen.

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

## <a name="create-the-powershell-runbook-script"></a>Skapa PowerShell-skriptet för Runbook

Nu skapar vi ett PowerShell-skript som hämtar **TemplateTest.jspå** filen från Azure Storage och distribuerar mallen för att skapa ett nytt Azure Storage-konto.

I en text redigerare klistrar du in följande text:

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

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera runbooken i ditt Azure Automation-konto

Nu använder vi PowerShell för att importera runbooken till ditt Azure Automation-konto och sedan publicera runbooken. Information om hur du importerar och publicerar en Runbook i Azure Portal finns i [Hantera Runbooks i Azure Automation](manage-runbooks.md).

Kör följande PowerShell-kommandon om du vill importera **DeployTemplate.ps1** till ditt Automation-konto som en PowerShell-Runbook:

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

Nu startar du runbooken genom att anropa cmdleten [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . Information om hur du startar en Runbook i Azure Portal finns i [starta en Runbook i Azure Automation](./start-runbooks.md).

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

När runbooken har körts kan du kontrol lera dess status genom att hämta egenskap svärdet för jobbobjektet `$job.Status` .

Runbook hämtar Resource Manager-mallen och använder den för att distribuera ett nytt Azure Storage-konto. Du kan se att det nya lagrings kontot har skapats genom att köra följande kommando:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Resource Manager-mallar finns i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md).
* Information om hur du kommer igång med Azure Storage finns i [Introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Du hittar andra användbara Azure Automation runbooks [i använda Runbooks och moduler i Azure Automation](automation-runbook-gallery.md).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation#automation).
