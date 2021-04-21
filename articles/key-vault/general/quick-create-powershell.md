---
title: Snabbstart – Skapa en Azure Key Vault med Azure PowerShell
description: Snabbstart som visar hur du skapar en Azure Key Vault med Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cc6d9ca2621a56242d7472a088e55651f5502c9c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814810"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Snabbstart: Skapa ett nyckelvalv med PowerShell

Azure Key Vault är en molntjänst som tillhandahåller ett säkert arkiv [för nycklar,](../keys/index.yml) [hemligheter](../secrets/index.yml)och [certifikat](../certificates/index.yml). Mer information om Key Vault finns i [Om Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckelvalv finns [i Om nycklar, hemligheter och certifikat.](about-keys-secrets-certificates.md)

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

I den här snabbstarten skapar du ett nyckelvalv med [Azure PowerShell](/powershell/azure/). Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Key Vault med Azure PowerShell. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](overview.md)
- Se referensen för [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/)
- Granska Azure Key Vault [säkerhetsöversikten](security-features.md)

