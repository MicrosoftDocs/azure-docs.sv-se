---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 3d8cd9891329e86ce47dac6d8d44af529c104b61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386640"
---
Azure Disk Encryption kan aktiveras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/powershell/azure/new-azureps-module-az). Om du vill göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0 är ett](/cli/azure) kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att flexibelt fråga efter data, stödja långvariga åtgärder som icke-blockerande processer och göra skript enkelt. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Om [du vill logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot az [login.](/cli/azure/reference-index#az-login)

```azurecli
az login
```

Om du vill välja en klientorganisation att logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en specifik, hämtar du din prenumerationslista med [az account list](/cli/azure/account#az-account-list) och anger med az account [set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2.0.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Modulen [Azure PowerShell az innehåller](/powershell/azure/new-azureps-module-az) en uppsättning cmdlets som använder Azure Resource Manager för att hantera dina Azure-resurser. [](../articles/azure-resource-manager/management/overview.md) Du kan använda det i webbläsaren [med Azure Cloud Shell](../articles/cloud-shell/overview.md), eller så kan du installera det på den lokala datorn med hjälp av anvisningarna i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). 

Om du redan har installerat det lokalt kontrollerar du att du använder den senaste versionen Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen [Azure PowerShell versionen](https://github.com/Azure/azure-powershell/releases).

Om [du vill logga in på ditt Azure-Azure PowerShell](/powershell/azure/authenticate-azureps)använder du cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att visa dem, följt av cmdleten [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Om du [kör cmdleten Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifieras att rätt prenumeration har valts.

Kontrollera att Azure Disk Encryption cmdlets är installerade med hjälp av [cmdleten Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).