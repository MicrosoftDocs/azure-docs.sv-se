---
title: Snabbstart – Skapa en Azure Key Vault med Azure CLI
description: Snabbstart som visar hur du skapar en Azure Key Vault med hjälp av Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815134"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Snabbstart: Skapa ett nyckelvalv med hjälp av Azure CLI

Azure Key Vault är en molntjänst som tillhandahåller ett säkert arkiv [för nycklar,](../keys/index.yml) [hemligheter](../secrets/index.yml)och [certifikat](../certificates/index.yml). Mer information om Key Vault finns i [Om Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckelvalv finns [i Om nycklar, hemligheter och certifikat.](about-keys-secrets-certificates.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabbstarten kräver version 2.0.4 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du Key Vault och tog bort den. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](overview.md)
- Granska Azure Key Vault [säkerhetsöversikten](security-features.md)
- Se referensen för [Azure CLI az keyvault-kommandon](/cli/azure/keyvault)

