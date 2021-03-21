---
title: Snabb start – ange och hämta en hemlighet från Azure Key Vault
description: Snabbstart som visar hur du ställer in och hämtar en hemlighet från Azure Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 1443ab37beb28706227159c53d336384216d8387
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582467"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI

I den här snabb starten skapar du ett nyckel valv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault kan du läsa [översikten](../general/overview.md). Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har gjort det kommer du att lagra en hemlighet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabb starten kräver version 2.0.4 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

När du ska lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. Det här lösenordet kan användas av ett program. Lösenordet kallas **ExamplePassword** och vi lagrar värdet **hVFkk965BuUv** i det.

Använd kommandot Azure CLI [AZS Secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) nedan om du vill skapa en hemlighet i Key Vault som kallas **ExamplePassword** som ska lagra värdet **hVFkk965BuUv** :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Nu kan du referera till det här lösenordet som du lagt till i Azure Key Vault med hjälp av dess URI. Använd **"https://<ditt-Unique-nyckel-namn>. Vault.Azure.net/Secrets/ExamplePassword"** för att hämta den aktuella versionen.

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Nu har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en hemlighet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Lär dig hur du [lagrar flera linje hemligheter i Key Vault](multiline-secrets.md)
- Se referensen för [kommandon för Azure CLI-AZ](/cli/azure/keyvault)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
