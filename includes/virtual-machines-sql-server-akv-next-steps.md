---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557999"
---
## <a name="next-steps"></a>Nästa steg

När du har aktiverat Azure Key Vault-integrering kan du aktivera SQL Server kryptering på den virtuella SQL-datorn. Först måste du skapa en asymmetrisk nyckel i ditt nyckel valv och en symmetrisk nyckel i SQL Server på den virtuella datorn. Sedan kommer du att kunna köra T-SQL-uttryck för att aktivera kryptering för dina databaser och säkerhets kopior.

Det finns flera typer av kryptering som du kan dra nytta av:

* [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Krypterade säkerhets kopior](/sql/relational-databases/backup-restore/backup-encryption)
* [Kryptering på kolumn nivå (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

Följande Transact-SQL-skript innehåller exempel för var och en av dessa områden.

### <a name="prerequisites-for-examples"></a>Krav för exempel

Varje exempel baseras på de två förutsättningarna: en asymmetrisk nyckel från ditt nyckel valv som kallas **CONTOSO_KEY** och en autentiseringsuppgift som skapats av AKV-integrations funktionen som kallas **Azure_EKM_cred**. Följande Transact-SQL-kommandon ställer in dessa krav för att köra exemplen.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

1. Skapa en SQL Server inloggning som ska användas av databas motorn för TDE och Lägg sedan till autentiseringsuppgiften till den.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Skapa den databas krypterings nyckel som ska användas för TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Krypterade säkerhets kopior

1. Skapa en SQL Server inloggning som ska användas av databas motorn för kryptering av säkerhets kopior och Lägg till autentiseringsuppgiften till den.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Säkerhetskopiera databasen som anger kryptering med den asymmetriska nyckel som lagras i nyckel valvet.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Kryptering på kolumn nivå (CLE)

Det här skriptet skapar en symmetrisk nyckel som skyddas av den asymmetriska nyckeln i nyckel valvet och använder sedan den symmetriska nyckeln för att kryptera data i databasen.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Ytterligare resurser

Mer information om hur du använder dessa krypterings funktioner finns i [använda EKM med SQL Server krypterings funktioner](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM).

Observera att stegen i den här artikeln förutsätter att du redan har SQL Server som körs på en virtuell Azure-dator. Om inte, se [etablera en SQL Server virtuell dator i Azure](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md). Andra rikt linjer för att köra SQL Server på virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).