---
title: Lagra en intern hemlighet i Azure Key Vault
description: Självstudie som visar hur du ställer in flera linje hemligheter från Azure Key Vault med Azure CLI och PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610292"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Lagra en intern hemlighet i Azure Key Vault

Snabb starten för [Azure CLI](quick-create-cli.md) och [Azure PowerShell-snabb](quick-create-powershell.md) starten demonstrerar hur du lagrar en enskild rad hemlighet.   Du kan också använda Key Vault för att lagra en multi-line Secret, till exempel en JSON-fil eller en RSA-privat nyckel.

Det går inte att skicka flera rad hemligheter till kommandot Azure CLI [AZ Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) eller cmdleten Azure PowerShell [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) via kommando raden. I stället måste du först lagra multi-line Secret som en textfil. 

Du kan till exempel skapa en textfil med namnet "secretfile.txt" som innehåller följande rader:

```bash
This is my
multi-line
secret
```

Du kan sedan skicka den här filen till kommandot Azure CLI-AZ för att [skapa en nyckel valv](/cli/azure/keyvault/secret#az_keyvault_secret_set) med hjälp av `--file` parametern.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Med Azure PowerShell måste du först läsa filen med cmdleten [Get-Content](/powershell/module/microsoft.powershell.management/get-content) och sedan konvertera den till en säker sträng med hjälp av [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Slutligen lagrar du hemligheten med cmdleten [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

I båda fallen kan du Visa den lagrade hemligheten med hjälp av kommandot Azure CLI [AZ-valv för hemliga](/cli/azure/keyvault/secret#az_keyvault_secret_show) kommandon eller cmdleten Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) .

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Hemligheten kommer att returneras med newlines Embedded:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Nästa steg

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se snabb starten för [Azure CLI](quick-create-cli.md)
- Se [kommandona för Azure CLI AZ-valv](/cli/azure/keyvault)
- Se snabb starten för [Azure PowerShell](quick-create-powershell.md)
- Se [cmdletarna för Azure PowerShell AZ. nyckel valv](/powershell/module/az.keyvault#key-vault)
