---
title: Aktivera SQL-TDE med Azure Key Vault
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Lär dig hur du konfigurerar en Azure SQL Database och Azure Synapse Analytics för att börja använda transparent datakryptering (TDE) för kryptering i vila med PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 610f0a9692c18afbd7bb446959b09bac14d6f629
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507627"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell och Azure CLI: Aktivera transparent datakryptering med kundhanterad nyckel från Azure Key Vault
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln beskriver hur du använder en nyckel från Azure Key Vault för transparent datakryptering (TDE) i Azure SQL Database eller Azure Synapse Analytics. Om du vill veta mer om TDE med stöd för Azure Key Vault integration-Bring Your Own Key (BYOK) går du till [TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-overview.md).

> [!NOTE] 
> Azure SQL stöder nu användning av en RSA-nyckel som lagras i en hanterad HSM som TDE-skydd. Den här funktionen finns i **offentlig för hands version**. Azure Key Vault hanterad HSM är en fullständigt hanterad moln tjänst med hög tillgänglighet, en standard som är kompatibel med en enda klient, som gör att du kan skydda kryptografiska nycklar för dina moln program med hjälp av FIPS 140-2 nivå 3-verifierade HSM: er. Läs mer om [hanterade HSM: er](../../key-vault/managed-hsm/index.yml).

## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

- Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
- [Rekommenderas men valfritt] Ha en maskinvaru-säkerhetsmodul (HSM) eller lokal nyckel lagring för att skapa en lokal kopia av TDE skydds nyckel materialet.
- Du måste ha Azure PowerShell installerat och igång.
- Skapa en Azure Key Vault och nyckel som ska användas för TDE.
  - [Instruktioner för att använda en maskinvaru-säkerhetsmodul (HSM) och Key Vault](../../key-vault/keys/hsm-protected-keys.md)
    - Nyckel valvet måste ha följande egenskap för att kunna användas för TDE:
  - [mjukt borttagnings-](../../key-vault/general/soft-delete-overview.md) och rensnings skydd
- Nyckeln måste ha följande attribut för att kunna användas för TDE:
  - Inget förfallodatum
  - Inte aktiverat
  - Kan utföra *Get*-, *wrap*-och *unwrap Key* -åtgärder
- **(För hands version)** Om du vill använda en hanterad HSM-nyckel följer du anvisningarna för att [skapa och aktivera en hanterad HSM med Azure CLI](../../key-vault/managed-hsm/quick-create-cli.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Instruktioner för installation av Az-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). För vissa cmdlets, se [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

Mer information om Key Vault finns i [PowerShell-instruktioner från Key Vault](../../key-vault/secrets/quick-create-powershell.md) och [hur du använder Key Vault mjuk borttagning med PowerShell](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> PowerShell-modulen för Azure Resource Manager (RM) stöds fortfarande, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Tilldela en Azure Active Directory-identitet (Azure AD) till servern

Om du har en befintlig [Server](logical-servers.md)kan du använda följande för att lägga till en Azure Active Directory (Azure AD)-identitet på servern:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Om du skapar en server använder du cmdleten [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) med taggen tag identitet för att lägga till en Azure AD-identitet när servern skapas:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Bevilja Key Vault behörigheter till servern

Använd cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att ge servern åtkomst till nyckel valvet innan du använder en nyckel från den för TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Om du vill lägga till behörigheter till servern på en hanterad HSM lägger du till den lokala RBAC-rollen "hanterad HSM-krypto" på servern. Detta gör att servern kan utföra get, wrapping Key, upwrap Key Operations på nycklarna i den hanterade HSM.
[Instruktioner för etablering av Server åtkomst på hanterad HSM](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Lägg till Key Vault-nyckeln till servern och ange TDE-skyddet

- Använd cmdleten [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) för att hämta nyckel-ID: t från Key Vault
- Använd cmdleten [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) för att lägga till nyckeln från Key Vault på servern.
- Använd cmdleten [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) för att ange nyckeln som TDE-skydd för alla server resurser.
- Använd cmdleten [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) för att bekräfta att TDE-skyddet har kon figurer ATS som avsett.

> [!NOTE]
> **(För hands version)** För hanterade HSM-nycklar använder du AZ. SQL 2.11.1-versionen av PowerShell.

> [!NOTE]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.

> [!TIP]
> Ett exempel på en KeyId från Key Vault:<br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Ett exempel på KeyId från hanterad HSM:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Aktivera TDE

Använd cmdleten [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att aktivera TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Databasen eller informations lagret har nu TDE Aktiver ATS med en krypterings nyckel i Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrol lera krypterings tillstånd och krypterings aktivitet

Använd [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) för att hämta krypterings tillstånd och [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) för att kontrol lera krypterings förloppet för en databas eller ett informations lager.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill installera den version av Azure CLI som krävs (version 2,0 eller senare) och ansluta till din Azure-prenumeration, se [Installera och konfigurera Azures plattforms oberoende Command-Line gränssnitt 2,0](/cli/azure/install-azure-cli).

Mer information om Key Vault finns i [hantera Key Vault med cli 2,0](../../key-vault/general/manage-with-cli2.md) och [hur du använder Key Vault mjuk borttagning med CLI](../../key-vault/general/key-vault-recovery.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Tilldela en Azure AD-identitet till servern

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Behåll "principalID" från att skapa servern, det är det objekt-ID som används för att tilldela Key Vault-behörigheter i nästa steg

## <a name="grant-key-vault-permissions-to-your-server"></a>Bevilja Key Vault behörigheter till servern

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Behåll nyckel-URI: n eller keyID för den nya nyckeln för nästa steg, till exempel: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Lägg till Key Vault-nyckeln till servern och ange TDE-skyddet

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Den kombinerade längden för Key Vault-namnet och nyckel namnet får inte överskrida 94 tecken.

## <a name="turn-on-tde"></a>Aktivera TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Databasen eller informations lagret har nu TDE Aktiver ATS med en kundhanterad krypterings nyckel i Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrol lera krypterings tillstånd och krypterings aktivitet

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdletar

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Använd cmdleten [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) för att inaktivera TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Använd cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för att returnera listan över Key Vault nycklar som har lagts till på servern.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Använd [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) om du vill ta bort en Key Vault nyckel från servern.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Allmänna databas inställningar finns i [AZ SQL](/cli/azure/sql).

- För nyckel inställningar för valv, se [AZ SQL Server Key](/cli/azure/sql/server/key).

- TDE-inställningar finns i [AZ SQL Server TDE-Key](/cli/azure/sql/server/tde-key) och [AZ SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Felsökning

Kontrol lera följande om ett problem inträffar:

- Om det inte går att hitta nyckel valvet ser du till att du har rätt prenumeration.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Om den nya nyckeln inte kan läggas till på servern, eller om den nya nyckeln inte kan uppdateras som TDE-skydd, kontrollerar du följande:
   - Nyckeln ska inte ha ett utgångs datum
   - Nyckeln måste ha de *Get*-, *wrap*-och *unwrap* -nycklar som är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du roterar TDE-skyddet för en server för att uppfylla säkerhets kraven: [rotera Transparent datakryptering-skyddet med hjälp av PowerShell](transparent-data-encryption-byok-key-rotation.md).
- I händelse av en säkerhets risk lär du dig hur du tar bort ett potentiellt komprometterat TDE-skydd: [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-remove-tde-protector.md).
