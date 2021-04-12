---
title: Skapa Azure AD-användare som använder tjänstens huvud namn
description: Den här självstudien vägleder dig genom att skapa en Azure AD-användare med ett Azure AD-program (tjänst huvud namn) i Azure SQL Database och Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 13e049d3e7e0c87bd0a214a92491e10d652a3619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380618"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Självstudie: Skapa Azure AD-användare med hjälp av Azure AD-program

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Den här artikeln är i **offentlig för hands version**. Mer information finns i [Azure Active Directory tjänstens huvud namn med Azure SQL](authentication-aad-service-principal.md). Den här artikeln använder Azure SQL Database för att demonstrera de nödvändiga själv studie stegen, men kan användas på samma sätt i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Den här artikeln vägleder dig genom processen att skapa Azure AD-användare i Azure SQL Database, med hjälp av Azure-tjänstens huvud namn (Azure AD-program). Den här funktionen finns redan i Azure SQL-hanterad instans, men presenteras nu i Azure SQL Database och Azure Synapse Analytics. För att stödja det här scenariot måste en Azure AD-identitet genereras och tilldelas till den logiska Azure SQL-servern.

Mer information om Azure AD-autentisering för Azure SQL finns i artikeln [använda Azure Active Directory autentisering](authentication-aad-overview.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> - Tilldela en identitet till den logiska Azure SQL-servern
> - Tilldela katalog läsare behörighet till den logiska SQL-serverns identitet
> - Skapa ett huvud namn för tjänsten (ett Azure AD-program) i Azure AD
> - Skapa en tjänst huvud användare i Azure SQL Database
> - Skapa en annan Azure AD-användare i SQL Database med hjälp av en Azure AD-tjänstens huvud namn användare

## <a name="prerequisites"></a>Förutsättningar

- En befintlig [Azure SQL Database](single-database-create-quickstart.md) -eller [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) -distribution. Vi förutsätter att du har en fungerande SQL Database för den här självstudien.
- Åtkomst till ett redan befintligt Azure Active Directory.
- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) -modulen eller senare krävs när du använder PowerShell för att konfigurera ett enskilt Azure AD-program som Azure AD-administratör för Azure SQL. Se till att du har uppgraderat till den senaste modulen.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Tilldela en identitet till den logiska Azure SQL-servern

1. Anslut till din Azure Active Directory. Du måste hitta klient-ID: t. Du hittar detta genom att gå till [Azure Portal](https://portal.azure.com)och gå till din **Azure Active Directory** -resurs. I **översikts** fönstret bör du se ditt **klient-ID**. Kör följande PowerShell-kommando:

    - Ersätt `<TenantId>` med ditt **klient-ID**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Spela in `TenantId` för framtida användning i den här självstudien.

1. Skapa och tilldela en Azure AD-identitet till den logiska Azure SQL-servern. Kör följande PowerShell-kommando:

    - Ersätt `<resource group>` och `<server name>` med dina resurser. Om Server namnet är `myserver.database.windows.net` , ersätter du `<server name>` med `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Mer information finns i [set-AzSqlServer-](/powershell/module/az.sql/set-azsqlserver) kommandot.

    > [!IMPORTANT]
    > Om en Azure AD-identitet har kon figurer ATS för den logiska Azure SQL-servern, måste [**katalog läsar**](../../active-directory/roles/permissions-reference.md#directory-readers) behörigheten beviljas till identiteten. Vi går igenom det här steget i följande avsnitt. Hoppa **inte** över det här steget eftersom Azure AD-autentisering upphör att fungera.

    - Om du använde kommandot [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) med parametern för att `AssignIdentity` skapa en ny SQL Server tidigare, måste du köra kommandot [set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) efteråt som ett separat kommando för att aktivera den här egenskapen i Azure-infrastrukturen.

1. Kontrol lera att Server identiteten har tilldelats. Kör följande PowerShell-kommando:

    - Ersätt `<resource group>` och `<server name>` med dina resurser. Om Server namnet är `myserver.database.windows.net` , ersätter du `<server name>` med `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Dina utdata bör visa dig `PrincipalId` , `Type` och `TenantId` . Den identitet som tilldelats är `PrincipalId` .

1. Du kan också kontrol lera identiteten genom att gå till [Azure Portal](https://portal.azure.com).

    - Gå till **företags program** under **Azure Active Directory** -resursen. Ange namnet på din logiska SQL-Server. Du kommer att se att den har ett **objekt-ID** kopplat till resursen.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="objekt-ID":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Tilldela katalog läsare behörighet till den logiska SQL-serverns identitet

Om du vill tillåta att den Azure AD-tilldelade identiteten fungerar korrekt för Azure SQL, måste Azure AD- `Directory Readers` behörighet beviljas till Server identiteten.

För att bevilja den här nödvändiga behörigheten kör du följande skript.

> [!NOTE] 
> Det här skriptet måste utföras av en Azure AD `Global Administrator` eller en `Privileged Roles Administrator` .
>
> I **offentlig för hands version** kan du tilldela `Directory Readers` rollen till en grupp i Azure AD. Grupp ägarna kan sedan lägga till den hanterade identiteten som en medlem i den här gruppen, vilket skulle kringgå behovet av en `Global Administrator` eller `Privileged Roles Administrator` att bevilja `Directory Readers` rollen. Mer information om den här funktionen finns i [katalog läsare roll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md).

- Ersätt `<TenantId>` med din `TenantId` tidigare insamlade version.
- Ersätt `<server name>` med namnet på den logiska SQL-servern. Om Server namnet är `myserver.database.windows.net` , ersätter du `<server name>` med `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Utdata från ovanstående skript anger om katalog läsar behörighet har beviljats till identiteten. Du kan köra skriptet igen om du är osäker på om behörigheten har beviljats.

En liknande metod för hur du ställer in **katalog läsar** behörighet för SQL-hanterad instans finns i [etablera Azure AD admin (SQL-hanterad instans)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Skapa ett huvud namn för tjänsten (ett Azure AD-program) i Azure AD

1. Följ anvisningarna här för att [Registrera din app och ange behörigheter](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Se till att lägga till **program behörigheter** samt de **delegerade behörigheterna**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Skärm bild som visar Appregistreringar sidan för Azure Active Directory. En app med visnings namnet AppSP är markerad.":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="API – behörigheter":::

2. Du måste också skapa en klient hemlighet för att logga in. Följ anvisningarna här för att [Ladda upp ett certifikat eller skapa en hemlighet för inloggning](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Registrera följande från din program registrering. Den bör vara tillgänglig i **översikts** fönstret:
    - **Program-ID**
    - **Klient-ID** – det ska vara samma som innan

I den här självstudien kommer vi att använda *AppSP* som vårt huvud huvud tjänst huvud konto och *MyApp* som andra tjänst huvud användare som skapas i Azure SQL med *AppSP*. Du måste skapa två program, *AppSP* och *MyApp*.

Mer information om hur du skapar ett Azure AD-program finns i artikeln [så här gör du: använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="permissions-required-to-set-or-unset-the-azure-ad-admin"></a>Behörigheter som krävs för att ange eller ta bort Azure AD-administratören

För att tjänstens huvud namn ska kunna ange eller ta bort en Azure AD-administratör för Azure SQL krävs ytterligare API-behörighet. [Katalogen. Read. all](/graph/permissions-reference#application-permissions-18) Application API-behörighet måste läggas till i ditt program i Azure AD.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Directory. Reader. alla behörigheter i Azure AD":::

Tjänstens huvud namn måste också ha rollen [**SQL Server Contributor**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) för SQL Database eller rollen [**SQL-hanterad instans deltagare**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) för SQL-hanterad instans.

> [!NOTE]
> Även om Azure AD Graph API är inaktuellt är **katalogen. Reader. all** behörighet gäller fortfarande för den här självstudien. Microsoft Graph-API: t gäller inte för den här självstudien.

## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Skapa tjänstens huvud namn användare i Azure SQL Database

När du har skapat ett huvud namn för tjänsten i Azure AD skapar du användaren i SQL Database. Du måste ansluta till din SQL Database med en giltig inloggning med behörigheter för att skapa användare i databasen.

1. Skapa användar *AppSP* i SQL Database med hjälp av följande T-SQL-kommando:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Bevilja `db_owner` behörighet till *AppSP*, vilket gör att användaren kan skapa andra Azure AD-användare i databasen.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Mer information finns i [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Alternativt `ALTER ANY USER` kan behörigheten beviljas i stället för att ge `db_owner` rollen. Detta gör att tjänstens huvud namn kan lägga till andra Azure AD-användare.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > Inställningen ovan krävs inte när *AppSP* har angetts som en Azure AD-administratör för-servern. Om du vill ange tjänstens huvud namn som AD-administratör för den logiska SQL-servern kan du använda kommandona Azure Portal, PowerShell eller Azure CLI. Mer information finns i [etablera Azure AD-administratör (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Skapa en Azure AD-användare i SQL Database med hjälp av ett Azure AD-tjänstens huvud namn

> [!IMPORTANT]
> Tjänstens huvud namn som används för att logga in på SQL Database måste ha en klient hemlighet. Om det inte finns något följer du steg 2 i [skapa ett tjänst objekt (ett Azure AD-program) i Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Den här klient hemligheten måste läggas till som en indataparameter i skriptet nedan.

1. Använd följande skript för att skapa en tjänst huvud användare i Azure AD- *tjänsten med hjälp av* tjänstens huvud namn *AppSP*.

    - Ersätt `<TenantId>` med din `TenantId` tidigare insamlade version.
    - Ersätt `<ClientId>` med din `ClientId` tidigare insamlade version.
    - Ersätt `<ClientSecret>` med din klient hemlighet som skapades tidigare.
    - Ersätt `<server name>` med namnet på den logiska SQL-servern. Om Server namnet är `myserver.database.windows.net` , ersätter du `<server name>` med `myserver` .
    - Ersätt `<database name>` med ditt SQL Database namn.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret
    # AppSP is part of an Azure AD admin for the Azure SQL server below
    
    # Download latest  MSAL  - https://www.powershellgallery.com/packages/MSAL.PS
    Import-Module MSAL.PS
    
    $tenantId = "<TenantId>"   # tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   # AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   # Client secret for AppSP 
    $scopes = "https://database.windows.net/.default" # The end-point
    
    $result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -ClientSecret (ConvertTo-SecureString $clientSecret -AsPlainText -Force) -TenantId $tenantId -Scopes $scopes
    
    $Tok = $result.AccessToken
    #Write-host "token"
    $Tok
      
    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    $DatabaseName = "<database name>"     # Azure SQL database name
    
    Write-Host "Create SQL connection string"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()
    ``` 

    Du kan också använda kod exemplet i bloggen, [autentisering av Azure AD-tjänstens huvud namn till SQL DB-kod exemplet](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Ändra skriptet för att köra en DDL-instruktion `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . Samma skript kan användas för att skapa en vanlig Azure AD-användare en grupp i SQL Database.

    
2. Kontrol lera *om användaren finns* i databasen genom att köra följande kommando:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Du bör se utdata som liknar följande:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory tjänstens huvud namn med Azure SQL](authentication-aad-service-principal.md)
- [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Använda hanterade identiteter för App Service och Azure Functions](../../app-service/overview-managed-identity.md)
- [Azure AD Service Principal-autentisering till SQL DB-kod exempel](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Objekt för program och tjänstens huvudnamn i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Skapa tjänstens huvudnamn för Azure med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
- [Katalog läsar roll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md)