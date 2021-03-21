---
title: Ta bort TDE-skydd (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Lär dig hur du svarar på ett potentiellt komprometterat TDE-skydd för Azure SQL Database eller Azure Synapse Analytics med hjälp av TDE med en egen nyckel (BYOK) support.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 31298be4d50c7f562e2e2b9adbf889b165b197e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461860"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Ta bort ett transparent datakryptering-skydd (TDE) med PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


I det här avsnittet beskrivs hur du svarar på en potentiellt komprometterad TDE-skydd för Azure SQL Database eller Azure Synapse Analytics som använder TDE med Kundhanterade nycklar i Azure Key Vault-Bring Your Own Key (BYOK) support. Mer information om BYOK-stöd för TDE finns på [sidan Översikt](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> De procedurer som beskrivs i den här artikeln bör endast göras i extrema fall eller i test miljöer. Granska stegen noggrant, som att ta bort aktivt använda TDE-skydd från Azure Key Vault resulterar i att **databasen blir otillgänglig**.

Om en nyckel någonsin misstänks vara komprometterad, så att en tjänst eller användare har obehörig åtkomst till nyckeln, är det bäst att ta bort nyckeln.

Tänk på att när TDE-skyddet tas bort i Key Vault, i upp till 10 minuter, kommer alla krypterade databaser att börja neka alla anslutningar med motsvarande fel meddelande och ändra dess tillstånd till [otillgängligt](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector).

Den här instruktions guiden går över två metoder beroende på önskat resultat efter ett komprometterat incident svar:

- För att göra databaserna i Azure SQL Database/Azure Synapse Analytics **otillgängliga**.
- För att göra databaserna i Azure SQL Database/Azure Azure Synapse Analytics **otillgängliga**.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen
- Du måste ha Azure PowerShell installerat och igång.
- Den här instruktions guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL Database eller Azure Synapse. Se [Transparent datakryptering med BYOK support](transparent-data-encryption-byok-overview.md) för mer information.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För vissa cmdlets, se [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell-modulen för Azure Resource Manager (RM) stöds fortfarande men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om installation finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>Kontrol lera TDE-skyddskomponenten tumavtrycken

Följande steg beskriver hur du kontrollerar TDE-skydds tumavtrycken som fortfarande används av virtuella loggfiler (VLF) för en specifik databas.
Tumavtrycket för det aktuella TDE-skyddet för databasen och databas-ID: t kan hittas genom att köra:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Följande fråga returnerar VLFs och TDE-skyddet respektive tumavtrycken som används. Varje annat tumavtryck refererar till en annan nyckel i Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Du kan också använda PowerShell eller Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell-kommandot **Get-AzureRmSqlServerKeyVaultKey**   tillhandahåller TUMAVTRYCKET för det TDE-skydd som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort i akv. Endast nycklar som inte längre används av databasen kan tas bort på ett säkert sätt från Azure Key Vault.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell-kommandot **AZ SQL Server Key show**   tillhandahåller TUMAVTRYCKET för det TDE-skydd som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort i akv. Endast nycklar som inte längre används av databasen kan tas bort på ett säkert sätt från Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Se till att krypterade resurser är tillgängliga

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en [ny nyckel i Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Se till att den nya nyckeln skapas i ett separat nyckel valv från det potentiellt komprometterade TDE-skyddet, eftersom åtkomst kontroll har tillhandahållits på en valv nivå.

2. Lägg till den nya nyckeln till servern med cmdletarna [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) och [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) och uppdatera den som serverns nya TDE-skydd.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Se till att servern och alla repliker har uppdaterats till det nya TDE-skyddet med hjälp av [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) -cmdleten.

   > [!NOTE]
   > Det kan ta några minuter för det nya TDE-skyddet att spridas till alla databaser och sekundära databaser under servern.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Gör en [säkerhets kopia av den nya nyckeln](/powershell/module/az.keyvault/backup-azkeyvaultkey) i Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Ta bort den komprometterade nyckeln från Key Vault med cmdleten [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Så här återställer du en nyckel till Key Vault i framtiden med hjälp av cmdleten [restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

För kommando referensen, se [Azure CLI-nyckel valvet](/cli/azure/keyvault/key).

1. Skapa en [ny nyckel i Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Se till att den nya nyckeln skapas i ett separat nyckel valv från det potentiellt komprometterade TDE-skyddet, eftersom åtkomst kontroll har tillhandahållits på en valv nivå.

2. Lägg till den nya nyckeln på servern och uppdatera den som det nya TDE-skyddet för servern.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Kontrol lera att servern och alla repliker har uppdaterats till det nya TDE-skyddet.

   > [!NOTE]
   > Det kan ta några minuter för det nya TDE-skyddet att spridas till alla databaser och sekundära databaser under servern.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Gör en säkerhets kopia av den nya nyckeln i Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Ta bort den komprometterade nyckeln från Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. För att återställa en nyckel till Key Vault i framtiden.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Gör krypterade resurser otillgängliga

1. Ta bort databaserna som krypteras av den potentiellt komprometterade nyckeln.

   Databasen och loggfilerna säkerhets kopie ras automatiskt, så en återställning av databasen kan göras när som helst (så länge du anger nyckeln). Databaserna måste släppas innan det går att ta bort ett aktivt TDE-skydd för att förhindra data förlust på upp till 10 minuter från de senaste transaktionerna.

2. Säkerhetskopiera nyckel materialet i TDE-skyddskomponenten i Key Vault.
3. Ta bort den potentiellt komprometterade nyckeln från Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skyddet för en server för att uppfylla säkerhets kraven: [rotera Transparent datakryptering-skyddet med hjälp av PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Kom igång med Bring Your Own Key-stöd för TDE: [Aktivera TDE med din egen nyckel från Key Vault med hjälp av PowerShell](transparent-data-encryption-byok-configure.md)