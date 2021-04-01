---
title: Snabb start – skapa en Azure Key Vault med Azure PowerShell
description: Snabb start visar hur du skapar en Azure Key Vault med hjälp av Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070225"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Snabb start: skapa ett nyckel valv med PowerShell

Azure Key Vault är en moln tjänst som tillhandahåller en säker lagring för [nycklar](../keys/index.yml), [hemligheter](../secrets/index.yml)och [certifikat](../certificates/index.yml). Mer information om Key Vault finns i [About Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckel valv finns i [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

I den här snabb starten skapar du ett nyckel valv med [Azure PowerShell](/powershell/azure/). Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

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

I den här snabb starten skapade du en Key Vault med hjälp av Azure PowerShell. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](overview.md)
- Se referensen för [Azure PowerShell Key Vault-cmdletar](/powershell/module/az.keyvault/)
- Granska [Azure Key Vault säkerhets översikt](security-overview.md)

