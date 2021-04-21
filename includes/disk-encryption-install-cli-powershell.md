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
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776802"
---
Azure Disk Encryption kan aktiveras och hanteras via [Azure CLI och](/cli/azure) [Azure PowerShell](/powershell/azure/new-azureps-module-az). Om du vill göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0 är ett](/cli/azure) kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att flexibelt fråga efter data, stödja långvariga åtgärder som icke-blockerande processer och göra skript enkelt. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Om [du vill logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot az [login.](/cli/azure/reference-index#az_login)

```azurecli
az login
```

Om du vill välja en klientorganisation att logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en specifik, hämtar du din prenumerationslista med [az account list](/cli/azure/account#az_account_list) och anger med az account [set](/cli/azure/account#az_account_set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2.0.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Modulen [Azure PowerShell az innehåller](/powershell/azure/new-azureps-module-az) en uppsättning cmdlets som använder Azure Resource Manager för att hantera dina Azure-resurser. [](../articles/azure-resource-manager/management/overview.md) Du kan använda det i webbläsaren [med Azure Cloud Shell](../articles/cloud-shell/overview.md), eller så kan du installera det på den lokala datorn med hjälp av [anvisningarna](/powershell/azure/install-az-ps)i Installera Azure PowerShell modulen . 

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

Kontrollera att Azure Disk Encryption cmdlet:ar är installerade med [cmdleten Get-command:](/powershell/module/microsoft.powershell.core/get-command)
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).