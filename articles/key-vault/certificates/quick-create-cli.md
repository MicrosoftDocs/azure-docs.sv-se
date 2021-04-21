---
title: Snabbstart – Konfigurera & visa Azure Key Vault certifikat med Azure CLI
description: Snabbstart som visar hur du ställer in och hämtar ett certifikat från Azure Key Vault med Hjälp av Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc3abe43c2cdf4d0ee58ac9aed570a2c507b0d7f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815278"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Snabbstart: Ange och hämta ett certifikat från en Azure Key Vault hjälp av Azure CLI

I den här snabbstarten skapar du ett nyckelvalv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [Översikt.](../general/overview.md) Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det lagrar du ett certifikat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabbstarten kräver version 2.0.4 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Lägga till ett certifikat i Key Vault

Om du vill lägga till ett certifikat i valvet behöver du bara vidta några ytterligare steg. Det här certifikatet kan användas av ett program. 

Skriv kommandona nedan för att skapa ett själv signerat certifikat med standardprincipen **ExampleCertificate** :

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Du kan nu referera till det här certifikatet som du har lagt till Azure Key Vault med hjälp av dess URI. Använd **"https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate"** för att hämta den aktuella versionen. 

Så här visar du tidigare lagrade certifikat:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Nu har du skapat en Key Vault, lagrat ett certifikat och hämtat det.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat Key Vault och lagrat ett certifikat i den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [Azure CLI az keyvault-kommandon](/cli/azure/keyvault)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
