---
title: 'Självstudie: komma igång med Always Encrypted med säker enclaves i Azure SQL Database'
description: I den här självstudien får du lära dig hur du skapar en grundläggande miljö för Always Encrypted med säker enclaves i Azure SQL Database och hur du krypterar data på plats och utfärdar omfattande konfidentiella frågor mot krypterade kolumner med SQL Server Management Studio (SSMS).
keywords: kryptera data, SQL-kryptering, databas kryptering, känsliga data, Always Encrypted, säker enclaves, SGX, attestering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120954"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Självstudie: komma igång med Always Encrypted med säker enclaves i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted med säker enclaves för Azure SQL Database finns för närvarande i **offentlig för hands version**.

I den här självstudien lär du dig att komma igång med [Always Encrypted med säker enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) i Azure SQL Database. Då visas följande:

> [!div class="checklist"]
> - Hur du skapar en miljö för att testa och utvärdera Always Encrypted med säkra enclaves.
> - Hur du krypterar data på plats och utfärdar omfattande konfidentiella frågor mot krypterade kolumner med SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien kräver Azure PowerShell-och [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>PowerShell-krav

Se [Översikt över Azure PowerShell](/powershell/azure) för information om hur du installerar och kör Azure PowerShell. 

Lägsta version av AZ-moduler som krävs för att stödja attesterings åtgärder:

- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. attestering 0.1.8

Kör kommandot nedan för att kontrol lera den installerade versionen av alla AZ-moduler:

```powershell
Get-InstalledModule
```

Om versionerna inte matchar minimi kravet kör du `Update-Module` kommandot.

PowerShell-galleriet har föråldrade Transport Layer Security (TLS) version 1,0 och 1,1. TLS 1,2 eller en senare version rekommenderas. Följande fel kan uppstå om du använder en TLS-version som är lägre än 1,2:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Om du vill fortsätta att interagera med PowerShell-galleriet kör du följande kommando innan du Install-Module kommandon

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Krav för SSMS

Information om hur du hämtar SSMS finns i [hämta SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) .

Den nödvändiga lägsta versionen av SSMS är 18,8.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Steg 1: skapa och konfigurera en server och en databas för DC-serien

I det här steget ska du skapa en ny Azure SQL Database logisk server och en ny databas med hjälp av maskin varu generering i DC-serien, som krävs för att Always Encrypted med säker enclaves. Mer information finns i [DC-serien](service-tiers-vcore.md#dc-series).

1. Öppna en PowerShell-konsol och importera den nödvändiga versionen av AZ.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Logga in på Azure. Om det behövs [växlar du till den prenumeration](/powershell/azure/manage-subscriptions-azureps) som du använder för den här självstudien.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. Skapa en ny resursgrupp. 

  > [!IMPORTANT]
  > Du måste skapa en resurs grupp i en region (plats) som stöder både maskin varu generering i DC-serien och Microsoft Azure attestering. En lista över regioner som stöder DC-serien finns i [tillgänglighet för DC-serien](service-tiers-vcore.md#dc-series-1). [Här](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) är den regionala tillgängligheten för Microsoft Azure attestering.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Skapa en logisk Azure SQL-Server. När du uppmanas till det anger du Server administratörens namn och lösen ord. Se till att du kommer ihåg administratörens namn och lösen ordet – du behöver dem senare för att kunna ansluta till servern.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. Skapa en server brand Väggs regel som tillåter åtkomst från det angivna IP-intervallet.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. Tilldela en hanterad system identitet till servern. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. Skapa en databas för DC-serien.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. Hämta och spara informationen om servern och databasen. Du behöver den här informationen, samt administratörs namnet och lösen ordet från steg 4 i det här avsnittet i senare avsnitt.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>Steg 2: Konfigurera en provider för attestering 

I det här steget ska du skapa och konfigurera en attesterings leverantör i Microsoft Azure attestering. Detta krävs för att intyga de säkra enklaven som databasen använder.

1. Kopiera nedanstående princip för attestering och spara principen i en textfil (txt). Information om principen nedan finns i [skapa och konfigurera en attesterings leverantör](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importera den nödvändiga versionen av `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. Skapa en attesterings leverantör. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. Konfigurera din attesterings princip.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Ge din Azure SQL-Server åtkomst till din attesterings leverantör. I det här steget använder du objekt-ID: t för den hanterade tjänst identitet som du tilldelade till servern tidigare.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. Hämta den attesterings-URL som pekar på en princip för attestering som du har konfigurerat för SGX-enklaven. Spara URL: en eftersom du behöver den senare.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  URL: en för attestering bör se ut så här: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Steg 3: Fyll i databasen

I det här steget ska du skapa en tabell och fylla den med data som du senare ska kryptera och fråga.

1. Öppna SSMS och Anslut till **ContosoHR** -databasen i den logiska Azure SQL-servern som du skapade **utan att** Always Encrypted aktive rad i databas anslutningen.
    1. I dialog rutan **Anslut till Server** anger du det fullständigt kvalificerade namnet på servern (till exempel *myserver123.Database.Windows.net*) och anger administratörs användar namnet och lösen ordet du angav när du skapade servern.
    2. Klicka på **alternativ >>** och välj fliken **anslutnings egenskaper** . Se till att välja **ContosoHR** -databasen (inte standard huvud databasen). 
    3. Välj fliken **Always Encrypted** .
    4. Kontrol lera att kryss rutan **aktivera Always Encrypted (kolumn kryptering)** **inte** är markerad.

        ![Anslut utan Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Klicka på **Anslut**.

2. Skapa en ny tabell med namnet **anställda**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Lägg till några medarbetar poster i tabellen **anställda** .

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Steg 4: etablera enklaven-aktiverade nycklar

I det här steget ska du skapa en kolumn huvud nyckel och en kolumn krypterings nyckel som tillåter enklaven beräkningar.

1. Med SSMS-instansen från föregående steg expanderar du databasen i **Object Explorer** och navigerar till **säkerhets**  >  **Always Encrypted nycklar**.
1. Etablera en ny enklaven kolumn huvud nyckel:
    1. Högerklicka på **Always Encrypted nycklar** och välj **ny kolumn huvud nyckel...**.
    2. Välj kolumnens huvud nyckel namn: **CMK1**.
    3. Se till att du väljer antingen **Windows certifikat arkiv (aktuell användare eller lokal dator)** eller **Azure Key Vault**.
    4. Välj **Tillåt enklaven-beräkningar**.
    5. Om du har valt Azure Key Vault loggar du in på Azure och väljer nyckel valvet. Mer information om hur du skapar ett nyckel valv för Always Encrypted finns i [Hantera dina nyckel valv från Azure Portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Välj ditt certifikat eller din nyckel värde nyckel för Azure om den redan finns, eller klicka på knappen **generera certifikat** för att skapa en ny.
    7. Välj **OK**.

        ![Tillåt enklaven-beräkningar](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Skapa en ny enklaven-aktiverad kolumn krypterings nyckel:

    1. Högerklicka på **Always Encrypted nycklar** och välj **ny kolumn krypterings nyckel**.
    2. Ange ett namn för den nya kolumn krypterings nyckeln: **CEK1**.
    3. I list rutan **kolumn huvud nyckel** väljer du den kolumn huvud nyckel som du skapade i föregående steg.
    4. Välj **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Steg 5: kryptera vissa kolumner på plats

I det här steget ska du kryptera data som lagras i kolumnerna **SSN** och **lön** i enklaven på Server sidan och sedan testa en urvals fråga på data.

1. Öppna en ny SSMS-instans och Anslut till databasen **med** Always Encrypted aktive rad för databas anslutningen.
    1. Starta en ny instans av SSMS.
    2. I dialog rutan **Anslut till Server** anger du det fullständigt kvalificerade namnet på servern (till exempel *myserver123.Database.Windows.net*) och anger administratörs användar namnet och lösen ordet du angav när du skapade servern.
    3. Klicka på **alternativ >>** och välj fliken **anslutnings egenskaper** . Se till att välja **ContosoHR** -databasen (inte standard huvud databasen). 
    4. Välj fliken **Always Encrypted** .
    5. Kontrol lera att kryss rutan **aktivera Always Encrypted (kolumn kryptering)** är markerad.
    6. Ange din enklaven-attesterings-URL som du har skaffat genom att följa stegen i [steg 2: Konfigurera en attesterings leverantör](#step-2-configure-an-attestation-provider). Se skärm bilden nedan.

        ![Anslut med attestering](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Välj **Anslut**.
    8. Om du uppmanas att aktivera Parameterisering för Always Encrypted frågor väljer du **Aktivera**.



1. Använd samma SSMS-instans (med Always Encrypted aktive rad), öppna ett nytt frågefönster och kryptera kolumnerna **SSN** och **lön** genom att köra nedanstående instruktioner.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Observera att den ALTER DATABASE-begränsade konfigurationen RENSAr PROCEDURE_CACHE-instruktionen för att rensa fråge planens cacheminne för databasen i skriptet ovan. När du har ändrat tabellen måste du rensa planerna för alla batchar och lagrade procedurer som har åtkomst till tabellen för att uppdatera parametrarnas krypterings information. 

1. För att kontrol lera att kolumnerna **SSN** och **lön** nu är krypterade, öppna ett nytt frågefönster i SSMS-instansen **utan** Always Encrypted aktiverat för databas anslutningen och kör instruktionen nedan. Frågefönstret ska returnera krypterade värden i kolumnerna **SSN** och **lön** . Om du kör samma fråga med SSMS-instansen med Always Encrypted aktive rad bör du se dekrypterade data.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Steg 6: köra omfattande frågor mot krypterade kolumner

Du kan köra omfattande frågor mot de krypterade kolumnerna. En del bearbetning av frågor utförs i enklaven på Server sidan. 

1. I SSMS-instansen **med** Always Encrypted aktive rad kontrollerar du att parameterisering för Always Encrypted också är aktiverat.
    1. Välj **verktyg** på huvud menyn i SSMS.
    2. Välj **alternativ...**.
    3. Navigera till **köra**  >  **SQL Server**  >  **Advanced**.
    4. Se till att **Aktivera Parameterisering för Always Encrypted** är markerat.
    5. Välj **OK**.
2. Öppna ett nytt frågefönster, klistra in i nedanstående fråga och kör. Frågan ska returnera värden för oformaterad text och rader som uppfyller de angivna Sök kriterierna.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Försök med samma fråga igen i SSMS-instansen som inte har Always Encrypted aktive rad. Ett fel ska inträffa.
 
## <a name="next-steps"></a>Nästa steg

När du har slutfört den här självstudien kan du gå till någon av följande Självstudier:
- [Självstudie: utveckla ett .NET-program med hjälp av Always Encrypted med säker enclaves](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Självstudie: utveckla ett .NET Framework program med Always Encrypted med säker enclaves](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Självstudie: skapa och använda index i enklaven-aktiverade kolumner med slumpmässig kryptering](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Se även

- [Konfigurera och använda Always Encrypted med säker enclaves](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
