---
title: Skapa och hämta attribut för en nyckel i Azure Key Vault – Azure CLI
description: Snabbstart som visar hur du ställer in och hämtar en nyckel från Azure Key Vault med Hjälp av Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: f5281c02f68598b1cf121a6ab8a574f0694f5ea2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813460"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Snabbstart: Ange och hämta en nyckel från en Azure Key Vault hjälp av Azure CLI

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [Översikt.](../general/overview.md) Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det lagrar du en nyckel.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabbstarten kräver version 2.0.4 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Lägga till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara vidta några ytterligare steg. Den här nyckeln kan användas av ett program. 

Skriv kommandona nedan för att skapa en nyckel med **namnet ExampleKey:**

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Du kan nu referera till den här nyckeln som du lade till Azure Key Vault med hjälp av dess URI. Använd **"https://<your-unique-keyvault-name>.vault.azure.net/keys/ExampleKey"** för att hämta den aktuella versionen. 

Så här visar du en tidigare lagrad nyckel:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Nu har du skapat en Key Vault, lagrat en nyckel och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat en nyckel i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure CLI az keyvault-kommandon](/cli/azure/keyvault)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
