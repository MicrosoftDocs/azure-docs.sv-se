---
title: Ta bort TDE-skydd (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Lär dig hur du svarar på ett potentiellt komprometterat TDE-skydd för Azure SQL Database eller Azure Synapse Analytics med TDE med stöd för Bring Your Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: f98dcdd9c1a479703c82c01b4fd240507ea355de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784477"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Ta bort transparent datakryptering skydd (TDE) med PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Det här avsnittet beskriver hur du svarar på ett potentiellt komprometterat TDE-skydd för Azure SQL Database eller Azure Synapse Analytics som använder TDE med kund hanterade nycklar i byok-stöd (Azure Key Vault – Bring Your Own Key). Mer information om BYOK-stöd för TDE finns på [översiktssidan.](transparent-data-encryption-byok-overview.md)

> [!CAUTION]
> De procedurer som beskrivs i den här artikeln bör endast utföras i extrema fall eller i testmiljöer. Granska stegen noggrant eftersom om du tar bort aktivt använda TDE-skydd från Azure Key Vault leder det till **att databasen blir otillgänglig.**

Om en nyckel någonsin misstänks vara komprometterad, så att en tjänst eller användare har haft obehörig åtkomst till nyckeln, är det bäst att ta bort nyckeln.

Tänk på att när TDE-skyddet har tagits bort i Key Vault kommer alla krypterade databaser inom 10 minuter att börja neka alla anslutningar med motsvarande felmeddelande och ändra dess tillstånd till [Otillgänglig](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector).

Den här guiden går igenom två metoder beroende på önskat resultat efter en komprometterad incidentlösning:

- För att göra databaserna i Azure SQL Database/Azure Synapse Analytics **otillgängliga**.
- För att göra databaserna i Azure SQL Database/Azure Azure Synapse Analytics **otillgängliga**.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen
- Du måste ha Azure PowerShell installerat och igång.
- Den här guiden förutsätter att du redan använder en nyckel från Azure Key Vault som TDE-skydd för en Azure SQL Database eller Azure Synapse. Mer [transparent datakryptering finns i transparent datakryptering byok-stöd.](transparent-data-encryption-byok-overview.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). Specifika cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är till för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Information om installation finns [i Installera Azure CLI.](/cli/azure/install-azure-cli)

* * *

## <a name="check-tde-protector-thumbprints"></a>Kontrollera tumavtryck för TDE-skydd

Följande steg beskriver hur du kontrollerar tumavtrycken för TDE-skydd som fortfarande används av VLF (Virtual Log Files) för en viss databas.
Tumavtrycket för det aktuella TDE-skyddet för databasen och databas-ID:t kan hittas genom att köra:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Följande fråga returnerar de VLAN och TDE-skydd respektive tumavtryck som används. Varje tumavtryck refererar till olika nycklar i Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Du kan också använda PowerShell eller Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell-kommandot **Get-AzureRmSqlServerKeyVaultKey** tillhandahåller tumavtrycket för det TDE-skydd som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort i   AKV. Endast nycklar som inte längre används av databasen kan tas bort från Azure Key Vault.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell-kommandot **az sql server key show** innehåller tumavtrycket för TDE-skyddet som används i frågan, så att du kan se vilka nycklar som ska behållas och vilka nycklar som ska tas bort   i AKV. Endast nycklar som inte längre används av databasen kan tas bort från Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Håll krypterade resurser tillgängliga

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en [ny nyckel i Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Se till att den nya nyckeln skapas i ett separat nyckelvalv från det potentiellt komprometterade TDE-skyddet, eftersom åtkomstkontroll etableras på valvnivå.

2. Lägg till den nya nyckeln på servern med cmdletarna [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) och [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) och uppdatera den som serverns nya TDE-skydd.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Kontrollera att servern och alla repliker har uppdaterats till det nya TDE-skyddet med cmdleten [Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Det kan ta några minuter för det nya TDE-skyddet att spridas till alla databaser och sekundära databaser under servern.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Gör en [säkerhetskopia av den nya nyckeln](/powershell/module/az.keyvault/backup-azkeyvaultkey) i Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Ta bort den komprometterade nyckeln Key Vault hjälp av [cmdleten Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Så här återställer du en nyckel Key Vault i framtiden med hjälp av cmdleten [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kommandoreferens finns i [Azure CLI-nyckelvalvet](/cli/azure/keyvault/key).

1. Skapa en [ny nyckel i Key Vault](/cli/azure/keyvault/key#az_keyvault_key_create). Se till att den här nya nyckeln skapas i ett separat nyckelvalv från det potentiellt komprometterade TDE-skyddet, eftersom åtkomstkontroll etableras på valvnivå.

2. Lägg till den nya nyckeln på servern och uppdatera den som det nya TDE-skyddet för servern.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Kontrollera att servern och alla repliker har uppdaterats till det nya TDE-skyddet.

   > [!NOTE]
   > Det kan ta några minuter för det nya TDE-skyddet att spridas till alla databaser och sekundära databaser under servern.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Gör en säkerhetskopia av den nya nyckeln i Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Ta bort den komprometterade nyckeln från Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Återställa en nyckel till Key Vault i framtiden.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Göra krypterade resurser otillgängliga

1. Ta bort de databaser som krypteras av den potentiellt komprometterade nyckeln.

   Databasen och loggfilerna säkerhetskopieras automatiskt, så en återställning till en tidpunkt av databasen kan göras när som helst (så länge du anger nyckeln). Databaserna måste tas bort innan ett aktivt TDE-skydd tas bort för att förhindra potentiell dataförlust på upp till 10 minuter av de senaste transaktionerna.

2. Back up the key material of the TDE protector in Key Vault.
3. Ta bort den potentiellt komprometterade nyckeln från Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skyddet för en server för att uppfylla säkerhetskraven: Rotera [transparent datakryptering skydd med PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Kom igång med Bring Your Own Key för TDE: [Aktivera TDE med](transparent-data-encryption-byok-configure.md) din egen nyckel från Key Vault powershell