---
title: Migrera TDE-certifikat – hanterad instans
description: Migrera ett certifikat som skyddar databas krypterings nyckeln för en databas med transparent datakryptering till Azure SQL-hanterad instans
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: c465da3d5d812ea7e811cbe59318122700c6e786
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97824664"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrera ett certifikat för en TDE-skyddad databas till en Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

När du migrerar en databas som skyddas av [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) till en Azure SQL-hanterad instans med hjälp av det interna återställnings alternativet måste motsvarande certifikat från SQL Server-instansen migreras innan databasen återställs. Den här artikeln vägleder dig genom processen för manuell migrering av certifikatet till den Azure SQL-hanterade instansen:

> [!div class="checklist"]
>
> * Exportera certifikatet till en personlig information Exchange-fil (. pfx)
> * Extrahera certifikatet från en fil till en Base-64-sträng
> * Ladda upp den med en PowerShell-cmdlet

Ett alternativt alternativ som använder en fullständigt hanterad tjänst för sömlös migrering av både en TDE-skyddad databas och ett motsvarande certifikat finns i [så här migrerar du din lokala databas till Azure SQL-hanterad instans med hjälp av Azure Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Ett migrerat certifikat används endast för återställning av den TDE-skyddade databasen. Snart när återställningen är färdig ersätts det migrerade certifikatet av ett annat skydd, antingen ett tjänst hanterat certifikat eller en asymmetrisk nyckel från nyckel valvet, beroende på vilken typ av TDE som du har angett på instansen.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här artikeln:

* [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx)-kommandoradsverktyget installerat på den lokala servern eller en annan dator med åtkomst till det certifikat som exporterats som en fil. Verktyget Pvk2Pfx är en del av [Enterprise Windows Driver Kit](/windows-hardware/drivers/download-the-wdk), en fristående kommando rads miljö.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) version 5.0 eller senare installerat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se till att du har följande:

* Azure PowerShell-modulen [installeras och uppdateras](/powershell/azure/install-az-ps).
* [AZ. SQL-modul](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL-hanterad instans, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRM-modulerna är i stort sett identiska.

Kör följande kommandon i PowerShell för att installera/uppdatera modulen:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Exportera TDE-certifikatet till en PFX-fil

Certifikatet kan exporteras direkt från käll SQL Servers instansen eller från certifikat arkivet om det finns där.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Exportera certifikatet från käll SQL Servers instansen

Använd följande steg för att exportera certifikatet med SQL Server Management Studio och konvertera det till. PFX-format. De generiska namnen *TDE_Cert* och *full_path* används för certifikat-och fil namn och sökvägar genom stegen. De ska ersättas med de faktiska namnen.

1. Öppna ett nytt frågefönster i SSMS och Anslut till käll SQL Servers instansen.

1. Använd följande skript för att visa en lista över TDE-skyddade databaser och hämta namnet på certifikatet som skyddar krypteringen av databasen som ska migreras:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lista över TDE-certifikat](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. Kör följande skript för att exportera certifikatet till ett par filer (.cer och .pvk) med informationen för den offentliga nyckeln och den privata nyckeln:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Säkerhetskopiera TDE-certifikat](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Använd PowerShell-konsolen för att kopiera certifikat information från ett par med nyligen skapade filer till en. pfx-fil med hjälp av Pvk2Pfx-verktyget:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Exportera certifikatet från ett certifikat Arkiv

Om certifikatet sparas i den SQL Server lokala datorns certifikat Arkiv, kan det exporteras med följande steg:

1. Öppna PowerShell-konsolen och kör följande kommando för att öppna snapin-modulen certifikat i Microsoft Management Console:

   ```cmd
   certlm
   ```

2. I MMC-snapin-modulen certifikat expanderar du sökvägen personliga > certifikat för att se listan över certifikat.

3. Högerklicka på certifikatet och klicka på **Exportera**.

4. Följ guiden för att exportera certifikatet och den privata nyckeln till ett. PFX-format.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Överför certifikatet till den Azure SQL-hanterade instansen med hjälp av en Azure PowerShell-cmdlet

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Börja med förberedelsestegen i PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. När alla förberedelse steg har utförts kör du följande kommandon för att ladda upp det Base-64-kodade certifikatet till den hanterade mål instansen:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du måste först [Konfigurera ett Azure Key Vault](../../key-vault/general/manage-with-cli2.md) med din *. pfx* -fil.

1. Börja med förberedelsestegen i PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. När alla förberedelse steg har utförts kör du följande kommandon för att ladda upp det grundläggande 64-kodade certifikatet till den hanterade mål instansen:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certifikatet är nu tillgängligt för den angivna hanterade instansen och säkerhets kopieringen av motsvarande TDE-skyddade databas kan återställas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du migrerar ett certifikat som skyddar krypterings nyckeln för en databas med transparent datakryptering, från den lokala eller IaaS SQL Server-instansen till en Azure SQL-hanterad instans.

Se [återställa en databas säkerhets kopia till en hanterad Azure SQL-instans](restore-sample-database-quickstart.md) för att lära dig hur du återställer en databas säkerhets kopia till en Azure SQL-hanterad instans.