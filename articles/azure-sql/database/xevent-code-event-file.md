---
title: Fil kod för XEvent-händelse
description: Tillhandahåller PowerShell och Transact-SQL för kod exempel i två faser som visar händelse fil målet i en utökad händelse på Azure SQL Database. Azure Storage är en obligatorisk del av det här scenariot.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96501234"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Mål kod för händelse filen för utökade händelser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill ha ett fullständigt kod exempel för ett robust sätt att samla in och rapportera information om en utökad händelse.

I Microsoft SQL Server används [händelse fil målet](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) för att lagra händelse resultat i en lokal hård disk fil. Men sådana filer är inte tillgängliga för Azure SQL Database. Vi använder istället tjänsten Azure Storage för att stödja Event File-målet.

I det här avsnittet presenteras ett kod exempel med två faser:

- PowerShell, för att skapa en Azure Storage-behållare i molnet.
- Transact-SQL:
  - För att tilldela Azure Storage container till ett händelse fil mål.
  - För att skapa och starta händelsesessionen och så vidare.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

- Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Alla databaser du kan skapa en tabell i.
  
  - Du kan också [skapa en **AdventureWorksLT** demonstrations databas](single-database-create-quickstart.md) på bara några minuter.

- SQL Server Management Studio (ssms.exe), helst den senaste månatliga uppdaterings versionen.
  Du kan hämta de senaste ssms.exe från:
  
  - Avsnitt med rubriken [hämta SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  - [En direkt länk till nedladdningen.](https://go.microsoft.com/fwlink/?linkid=616025)

- Du måste ha installerat [Azure PowerShell-modulerna](https://go.microsoft.com/?linkid=9811175) .

  - Modulerna innehåller kommandon som- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fas 1: PowerShell-kod för Azure Storage container

Den här PowerShell är fas 1 i kod exemplet med två faser.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare körning och är rerunnable.

1. Klistra in PowerShell-skriptet i en enkel text redigerare, till exempel Notepad.exe, och Spara skriptet som en fil med fil namns tillägget **. ps1**.
2. Starta PowerShell ISE som administratör.
3. I prompten skriver du<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>och tryck sedan på RETUR.
4. Öppna din **. ps1** -fil i PowerShell ISE. Kör skriptet.
5. Skriptet startar först ett nytt fönster där du loggar in på Azure.

   - Om du kör skriptet igen utan att avbryta din session, har du det praktiska alternativet att kommentera ut kommandot **Add-AzureAccount** .

![PowerShell ISE, med Azure-modulen installerad, redo att köra skript.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>PowerShell-kod

Det här PowerShell-skriptet förutsätter att du redan har installerat AZ-modulen. Mer information finns i [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Anteckna de få namngivna värden som PowerShell-skriptet skriver ut när det avslutas. Du måste redigera dessa värden i Transact-SQL-skriptet som följer som fas 2.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> I föregående PowerShell-kod exempel är SQL Extended Events inte kompatibla med ADLS Gen2 lagrings konton.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fas 2: Transact-SQL-kod som använder Azure Storage container

- I fas 1 av det här kod exemplet körde du ett PowerShell-skript för att skapa en Azure Storage-behållare.
- Nästa i fas 2 måste följande Transact-SQL-skript använda behållaren.

Skriptet börjar med kommandon för att rensa efter en eventuell tidigare körning och är rerunnable.

PowerShell-skriptet skrev ut några namngivna värden när det avslutades. Du måste redigera Transact-SQL-skriptet för att använda dessa värden. Se hur du kan göra det i Transact-SQL-skriptet för **att hitta redigerings** punkterna.

1. Öppna SQL Server Management Studio (ssms.exe).
2. Anslut till databasen i Azure SQL Database.
3. Klicka för att öppna ett nytt frågefönster.
4. Klistra in följande Transact-SQL-skript i frågefönstret.
5. Sök varje **gång** i skriptet och gör lämpliga ändringar.
6. Spara och kör sedan skriptet.

> [!WARNING]
> SAS-nyckelvärdet som genereras av föregående PowerShell-skript kan börja med en? (frågetecken). När du använder SAS-nyckeln i följande T-SQL-skript måste du *ta bort det inledande "?"*. Annars kan dina insatser blockeras av säkerhet.

### <a name="transact-sql-code"></a>Transact-SQL-kod

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Om målet inte kan ansluta när du kör måste du stoppa och starta om händelsesessionen:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Utdata

När Transact-SQL-skriptet är klart klickar du på en cell under **event_data_XML** kolumn rubriken. Ett **\<event>** element visas som visar en Update-instruktion.

Här är ett- **\<event>** element som genererades under testningen:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Föregående Transact-SQL-skript använde följande systemfunktion för att läsa event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

En förklaring av avancerade alternativ för visning av data från utökade händelser finns på:

- [Avancerad visning av mål data från utökade händelser](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konvertera kod exemplet som ska köras på SQL Server

Anta att du vill köra föregående Transact-SQL-exempel på Microsoft SQL Server.

- För enkelhetens skull vill du helt ersätta användningen av Azure Storage containern med en enkel fil, till exempel *C:\myeventdata.xel*. Filen skrivs till den lokala hård disken på den dator som är värd för SQL Server.
- Du behöver inte någon typ av Transact-SQL-uttryck för att **skapa huvud nyckel** och **skapa autentiseringsuppgifter**.
- I instruktionen **Skapa event-session** , i **dess Add Target** -sats, ersätter du det http-värde som tilldelats **filename =** med en fullständig Sök vägs sträng som *C:\myfile.xel*.
  
  - Inget Azure Storage konto behöver tas med.

## <a name="more-information"></a>Mer information

Mer information om konton och behållare i Azure Storage-tjänsten finns i:

- [Använda Blob Storage från .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Namnge och referera till behållare, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [Arbeta med rot behållaren](/rest/api/storageservices/Working-with-the-Root-Container)
- [Lektion 1: skapa en lagrad åtkomst princip och en signatur för delad åtkomst på en Azure-behållare](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [Lektion 2: skapa en SQL Server autentiseringsuppgift med hjälp av signaturen för delad åtkomst](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Utökade händelser för Microsoft SQL Server](/sql/relational-databases/extended-events/extended-events)