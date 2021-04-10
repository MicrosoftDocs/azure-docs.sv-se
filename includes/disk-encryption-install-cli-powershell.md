---
title: ta med fil
description: ta med fil
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 64db4de6628fcd8f3cf160bb2bde1b577219cb10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729855"
---
Azure Disk Encryption kan aktive ras och hanteras via [Azure CLI](/cli/azure) och [Azure PowerShell](/powershell/azure/new-azureps-module-az). Om du vill göra det måste du installera verktygen lokalt och ansluta till din Azure-prenumeration.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2,0](/cli/azure) är ett kommando rads verktyg för att hantera Azure-resurser. CLI är utformat för flexibel frågedata, stöder långvariga åtgärder som icke-blockerande processer och gör skript enkla. Du kan installera det lokalt genom att följa stegen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill [Logga in på ditt Azure-konto med Azure CLI](/cli/azure/authenticate-azure-cli)använder du kommandot [AZ login](/cli/azure/reference-index#az-login) .

```azurecli
az login
```

Om du vill välja en klient som ska logga in under använder du:
    
```azurecli
az login --tenant <tenant>
```

Om du har flera prenumerationer och vill ange en speciell, hämtar du din prenumerations lista med [AZ Account List](/cli/azure/account#az-account-list) och anger med [AZ Account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Mer information finns i [Kom igång med Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Modulen Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) innehåller en uppsättning cmdletar som använder [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) -modellen för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../articles/cloud-shell/overview.md), eller så kan du installera den på din lokala dator med hjälp av anvisningarna i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps). 

Om du redan har installerat det lokalt kontrollerar du att du använder den senaste versionen av Azure PowerShell SDK-versionen för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)-versionen.

Om du vill [Logga in på ditt Azure-konto med Azure PowerShell](/powershell/azure/authenticate-azureps)använder du cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```powershell
Connect-AzAccount
```

Om du har flera prenumerationer och vill ange en använder du cmdleten [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) för att lista dem, följt av cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Genom att köra cmdleten [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifierar du att rätt prenumeration har valts.

Om du vill bekräfta att Azure Disk Encryption-cmdletarna är installerade använder du cmdleten [Get-Command](/powershell/module/microsoft.powershell.core/get-command) :
     
```powershell
Get-command *diskencryption*
```
Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).