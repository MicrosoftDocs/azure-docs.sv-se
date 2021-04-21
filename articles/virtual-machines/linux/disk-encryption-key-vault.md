---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption på en virtuell Linux-dator.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 29f6f01ece0536d93ca18ca54379757adc120308
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818790"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i Kom [igång med Azure Key Vault](../../key-vault/general/overview.md) och Skydda ditt [nyckelvalv.](../../key-vault/general/security-features.md) 

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera den virtuella datorn. Se [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md) för mer information.

Att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption omfattar tre steg:

1. Skapa en resursgrupp om det behövs.
2. Skapa ett nyckelvalv. 
3. Ange avancerade åtkomstprinciper för nyckelvalvet.

De här stegen illustreras i följande snabbstarter:

- [Skapa och kryptera en virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md)

Du kan också, om du vill, generera eller importera en nyckelkrypteringsnyckel (KEK).

> [!Note]
> Stegen i den här artikeln automatiseras i [cli Azure Disk Encryption skriptet](https://github.com/ejarvi/ade-cli-getting-started) för förhandskrav [och Azure Disk Encryption PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och ansluta till Azure

Stegen i den här artikeln kan utföras med [Azure CLI,](/cli/azure/) [Azure PowerShell Az-modulen](/powershell/azure/)eller [Azure Portal](https://portal.azure.com). 

Även om portalen är tillgänglig via webbläsaren kräver Azure CLI och Azure PowerShell lokal installation. Se [Azure Disk Encryption för Linux: Installera verktyg](disk-encryption-linux.md#install-tools-and-connect-to-azure) för mer information.

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör det genom [att logga in med Azure CLI,](/cli/azure/authenticate-azure-cli)logga in med Azure [Powershell](/powershell/azure/authenticate-azureps)eller ange dina autentiseringsuppgifter för Azure Portal när du uppmanas till det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption CLI-skript för förhandskrav](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption PowerShell-skript som krävs](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Lär [Azure Disk Encryption scenarier på virtuella Linux-datorer](disk-encryption-linux.md)
- Lär dig hur [du felsöker Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Läs [Azure Disk Encryption exempelskript](disk-encryption-sample-scripts.md)
