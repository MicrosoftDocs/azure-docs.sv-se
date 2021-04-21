---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c9706176e537ec926a5335b54d667081773d76b1
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817367"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att kontrollera och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i Kom [igång med Azure Key Vault](../key-vault/general/overview.md) och Azure Key Vault [säkerhetsfunktioner.](../key-vault/general/security-features.md)

Att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption omfattar tre steg:

1. Skapa en resursgrupp om det behövs.
2. Skapa ett nyckelvalv. 
3. Ange avancerade åtkomstprinciper för Nyckelvalv.

De här stegen illustreras i följande snabbstarter:

Om du vill kan du också generera eller importera en nyckelkrypteringsnyckel (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Stegen i den här artikeln kan utföras med [Azure CLI,](/cli/azure/) [Azure PowerShell Az-modulen](/powershell/azure/)eller [Azure Portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör det genom att logga [in med Azure CLI,](/cli/azure/authenticate-azure-cli) [logga in med Azure PowerShell](/powershell/azure/authenticate-azureps)eller ange dina autentiseringsuppgifter till Azure Portal när du uppmanas att göra det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption översikt](disk-encryption-overview.md)
- [Kryptera en VM-skalningsuppsättningar med hjälp av Azure CLI](disk-encryption-cli.md)
- [Kryptera en VM-skalningsuppsättningar med hjälp av Azure PowerShell](disk-encryption-powershell.md)
