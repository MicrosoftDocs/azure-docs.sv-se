---
title: Rotera TDE-skydd (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Lär dig hur du roterar transparent datakryptering (TDE) skydd för en server i Azure som används av Azure SQL Database och Azure Synapse Analytics med PowerShell och Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 37931aee24e2dbcab03bca400d58f236601c87e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321430"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Rotera transparent datakryptering-skyddet (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Den här artikeln beskriver nyckel rotation för en [Server](logical-servers.md) med hjälp av ett TDE-skydd från Azure Key Vault. Att rotera det logiska TDE-skyddet för en server innebär att byta till en ny asymmetrisk nyckel som skyddar databaserna på servern. Nyckel rotation är en online-åtgärd som bara tar några sekunder att slutföra, eftersom detta bara dekrypterar och krypterar om databasens data krypterings nyckel, inte hela databasen.

I den här guiden beskrivs två alternativ för att rotera TDE-skyddskomponenten på servern.

> [!NOTE]
> En pausad dedikerad SQL-pool i Azure Synapse Analytics måste återupptas innan nyckel rotationer.

> [!IMPORTANT]
> Ta inte bort tidigare versioner av nyckeln efter en omstart. När nycklar överförs är vissa data fortfarande krypterade med de tidigare nycklarna, till exempel äldre databas säkerhets kopior.

## <a name="prerequisites"></a>Förutsättningar

- Den här instruktions guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för Azure SQL Database-eller Azure Synapse-analys. Se [Transparent datakryptering med BYOK-stöd](transparent-data-encryption-byok-overview.md).
- Du måste ha Azure PowerShell installerat och igång.
- [Rekommenderas men valfritt] Skapa nyckel materialet för TDE-skyddet i en HSM-modul (Hardware Security Module) eller lokal nyckel lagring först och importera nyckel materialet till Azure Key Vault. Följ [instruktionerna för att använda en maskin varu säkerhetsmodul (HSM) och Key Vault](../../key-vault/general/overview.md) om du vill veta mer.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För vissa cmdlets, se [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell-modulen för Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om installation finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Manuell nyckel rotation

Manuell nyckel rotation använder följande kommandon för att lägga till en helt ny nyckel, som kan vara under ett nytt nyckel namn eller till och med ett annat nyckel valv. Med den här metoden kan du lägga till samma nyckel till olika nyckel valv för att stödja hög tillgänglighet och geo-Dr-scenarier.

> [!NOTE]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd cmdletarna [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)och [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [nyckeln AZ Key Vault Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)och [AZ SQL Server TDE-Key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Växla TDE-skydds läge

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skydd från Microsoft-hanterat till BYOK-läge.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att växla TDE-skyddet från BYOK-läge till Microsoft-hanterad.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

I följande exempel används [AZ SQL Server TDE-Key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- För att växla TDE-skydd från Microsoft-hanterat till BYOK-läge

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- För att växla TDE-skyddet från BYOK-läge till Microsoft-hanterat,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Nästa steg

- I händelse av en säkerhets risk lär du dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-remove-tde-protector.md).

- Kom igång med Azure Key Vault integration och Bring Your Own Key stöd för TDE: [Aktivera TDE med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-configure.md).