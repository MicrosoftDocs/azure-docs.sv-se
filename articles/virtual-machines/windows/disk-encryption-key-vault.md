---
title: Skapa och konfigurera ett nyckel valv för Azure Disk Encryption på en virtuell Windows-dator
description: Den här artikeln innehåller steg för att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption på en virtuell Windows-dator.
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c1fcd639eb55f61c215742d0eec7821e282010a2
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807522"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Skapa och konfigurera ett nyckel valv för Azure Disk Encryption

Azure Disk Encryption använder Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter.  Mer information om nyckel valv finns i [Kom igång med Azure Key Vault](../../key-vault/general/overview.md) och [skydda nyckel valvet](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md) .

Att skapa och konfigurera ett nyckel valv som ska användas med Azure Disk Encryption omfattar tre steg:

> [!Note]
> Du måste välja alternativet i princip inställningarna för Azure Key Vault åtkomst för att ge åtkomst till Azure Disk Encryption för volym kryptering. Om du har aktiverat brand väggen i nyckel valvet måste du gå till fliken nätverk i nyckel valvet och aktivera åtkomst till Microsoft-betrodda tjänster. 

1. Skapa en resurs grupp, om det behövs.
2. Skapar ett nyckel valv. 
3. Ställer in avancerade åtkomst principer för nyckel valvet.

Dessa steg illustreras i följande snabb starter:

- [Skapa och kryptera en virtuell Windows-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Windows-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md)

Du kan också, om du vill, skapa eller importera en nyckel krypterings nyckel (KEK).

> [!Note]
> Stegen i den här artikeln är automatiserade i [skripten för Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och [Azure Disk Encryption krav på PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installera verktyg och Anslut till Azure

Stegen i den här artikeln kan utföras med [Azure CLI](/cli/azure/), [Azure PowerShell AZ-modulen](/powershell/azure/)eller [Azure Portal](https://portal.azure.com).

Även om portalen är tillgänglig via webbläsaren kan Azure CLI och Azure PowerShell kräva lokal installation. Se [Azure Disk Encryption för Windows: installera verktyg](disk-encryption-windows.md#install-tools-and-connect-to-azure) för mer information.

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Innan du använder Azure CLI eller Azure PowerShell måste du först ansluta till din Azure-prenumeration. Du gör detta genom att [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli), [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps)eller ange dina autentiseringsuppgifter till Azure Portal när du uppmanas till det.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Lär dig [Azure Disk Encryption scenarier på virtuella Windows-datorer](disk-encryption-windows.md)
- Lär dig hur du [felsöker Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Läs [Azure Disk Encryption exempel skript](disk-encryption-sample-scripts.md)
