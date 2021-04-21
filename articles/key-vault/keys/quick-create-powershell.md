---
title: Skapa och hämta attribut för en nyckel i Azure Key Vault – Azure PowerShell
description: Snabbstart som visar hur du ställer in och hämtar en nyckel från Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: ede01de815a65aede410fe539b6205f1f4e4fd68
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815458"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Snabbstart: Ange och hämta en nyckel från Azure Key Vault med Azure PowerShell

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure PowerShell. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [Översikt.](../general/overview.md) Azure PowerShell används för att skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det lagrar du en nyckel.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Lägga till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara vidta några ytterligare steg. Den här nyckeln kan användas av ett program. 

Skriv kommandona nedan för att skapa ett namn **som heter ExampleKey:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Du kan nu referera till den här nyckeln som du lade till Azure Key Vault med hjälp av dess URI. Använd **"https://<your-unique-keyvault-name>.vault.azure.net/keys/ExampleKey"** för att hämta den aktuella versionen. 

Så här visar du en tidigare lagrad nyckel:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Nu har du skapat en Key Vault, lagrat en nyckel och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
