---
title: Konfigurera Azure Active Directory-autentisering
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Lär dig hur du ansluter SQL Database, SQL Managed Instance och Azure Synapse Analytics med hjälp av Azure Active Directory-autentisering när du har konfigurerat Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c75364f2565611b6738996c082610229db0cb2a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762235"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Konfigurera och hantera Azure AD-autentisering med Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln visar hur du skapar och fyller i en Azure Active Directory-instans (Azure AD) och sedan använder Azure AD [med Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) [och Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt finns i [Azure Active Directory autentisering.](authentication-aad-overview.md)

## <a name="azure-ad-authentication-methods"></a>Azure AD-autentiseringsmetoder

Azure AD-autentisering stöder följande autentiseringsmetoder:

- Azure AD-identiteter endast i molnet
- Azure AD-hybrididentiteter som stöder:
  - Molnautentisering med två alternativ tillsammans med sömlös enkel inloggning (SSO)
    - Azure AD-autentisering med lösenordshashar
    - Direktautentisering i Azure AD
  - Federerad autentisering

Mer information om Azure AD-autentiseringsmetoder och vilken du bör välja finns i Välja rätt autentiseringsmetod för din [Azure Active Directory hybrididentitetslösning](../../active-directory/hybrid/choose-ad-authn.md).

Mer information om Azure AD-hybrididentiteter, konfiguration och synkronisering finns i:

- Autentisering med lösenordshashar [– Implementera synkronisering av lösenordshashar Azure AD Connect synkronisering](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Direktautentisering [– Azure Active Directory direktautentisering](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federerad autentisering – [distribuera Active Directory Federation Services (AD FS) i Azure och](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) Azure AD Connect och [federation](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Skapa och fylla i en Azure AD-instans

Skapa en Azure AD-instans och fyll den med användare och grupper. Azure AD kan vara den första Azure AD-hanterade domänen. Azure AD kan också vara en lokal Active Directory Domain Services som är federerad med Azure AD.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Add your own domain name to Azure AD](../../active-directory/fundamentals/add-custom-domain.md) (Lägga till dina egna domännamn i Azure AD), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft stöder nu federation med Windows Server Active Directory), [Administering your Azure AD directory](../../active-directory/fundamentals/active-directory-whatis.md) (Administrera Azure Active Directory), [Manage Azure AD using Windows PowerShell](/powershell/azure/) (Hantera Azure AD med Windows PowerShell) och [Hybrid Identity Required Ports and Protocols](../../active-directory/hybrid/reference-connect-ports.md) (Portar och protokoll som krävs för hybrididentiet).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associera eller lägg till en Azure-prenumeration till Azure Active Directory

1. Associera din Azure-prenumeration Azure Active Directory genom att göra katalogen till en betrodd katalog för Den Azure-prenumeration som är värd för databasen. Mer information finns i [Associera eller lägga till en Azure-prenumeration till din Azure Active Directory klientorganisation.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. Använd katalogväxeln i den virtuella Azure Portal att växla till den prenumeration som är associerad med domänen.

   > [!IMPORTANT]
   > Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-instans. Det innebär att den litar på den katalogen för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma katalog, men en prenumeration litar bara på en katalog. Den här förtroenderelationen mellan en prenumeration och en katalog skiljer sig från relationen mellan en prenumeration och alla andra resurser i Azure (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration går ut stoppas även åtkomsten till de resurser som är associerade med prenumerationen. Men katalogen finns kvar i Azure och du kan associera en annan prenumeration med katalogen och fortsätta hantera kataloganvändarna. Mer information om resurser finns i [Förstå resursåtkomst i Azure](../../active-directory/external-identities/add-users-administrator.md). Mer information om den här betrodda relationen finns [i Så här associerar eller lägger du till en Azure-prenumeration till Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-administratör med en server i SQL Database

Varje [server](logical-servers.md) i Azure (som är värd SQL Database eller Azure Synapse) börjar med ett enda serveradministratörskonto som är administratör för hela servern. Skapa ett andra administratörskonto som ett Azure AD-konto. Det här huvudnamnet skapas som en innesluten databasanvändare i serverns huvuddatabas. Administratörskonton är medlemmar i **db_owner** roll i varje användardatabas och anger varje användardatabas som **dbo-användare.** Mer information om administratörskonton finns i [Hantera databaser och inloggningar.](logins-create-manage.md)

När du Azure Active Directory med geo-replikering måste Azure Active Directory konfigureras för både den primära och den sekundära servern. Om en server inte har en Azure Active Directory kan Azure Active Directory inloggningar och användare får ett `Cannot connect` till server-fel.

> [!NOTE]
> Användare som inte är baserade på ett Azure AD-konto (inklusive serveradministratörskontot) kan inte skapa Azure AD-baserade användare, eftersom de inte har behörighet att verifiera föreslagna databasanvändare med Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Etablera Azure AD-administratör (SQL Managed Instance)

> [!IMPORTANT]
> Följ bara de här stegen om du etablerar en Azure SQL Managed Instance. Den här åtgärden kan bara köras av global administratör eller privilegierad rolladministratör i Azure AD.
>
> I **offentlig förhandsversion** kan du tilldela rollen **Katalogläsare** till en grupp i Azure AD. Gruppägare kan sedan lägga till den hanterade instansidentiteten som medlem i den här gruppen, vilket gör att du kan etablera en Azure AD-administratör för SQL Managed Instance. Mer information om den här funktionen finns i [Katalogläsarroll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md).

Din SQL Managed Instance behörighet att läsa Azure AD för att kunna utföra uppgifter, till exempel autentisering av användare via medlemskap i säkerhetsgrupper eller skapande av nya användare. För att detta ska fungera måste du ge SQL Managed Instance behörighet att läsa Azure AD. Du kan göra detta med hjälp Azure Portal eller PowerShell.

### <a name="azure-portal"></a>Azure Portal

Om du vill SQL Managed Instance Azure AD-läsbehörighet med hjälp av Azure Portal loggar du in som global administratör i Azure AD och följer dessa steg:

1. I [Azure Portal](https://portal.azure.com)väljer du anslutningen i det övre högra hörnet i en listrutan över möjliga aktiva kataloger.

2. Välj rätt Active Directory som standard-Azure AD.

   Det här steget länkar prenumerationen som är associerad med Active Directory till SQL Managed Instance och ser till att samma prenumeration används för både Azure AD-instansen och SQL Managed Instance.

3. Gå till den SQL Managed Instance du vill använda för Azure AD-integrering.

   ![Skärmbild av den Azure Portal som visar active directory-administratörssidan öppen för den valda hanterade SQL-instansen.](./media/authentication-aad-configure/aad.png)

4. Välj banderollen överst på Active Directory-administratörssidan och bevilja behörighet till den aktuella användaren.

    ![Skärmbild av dialogrutan för att bevilja behörigheter till en SQL-hanterad instans för åtkomst till Active Directory. Knappen Bevilja behörigheter är markerad.](./media/authentication-aad-configure/grant-permissions.png)

5. När åtgärden har lyckats visas följande meddelande i det övre högra hörnet:

    ![Skärmbild av ett meddelande som bekräftar att Active Directory-läsbehörigheterna har uppdaterats för den hanterade instansen.](./media/authentication-aad-configure/success.png)

6. Nu kan du välja din Azure AD-administratör för din SQL Managed Instance. För att göra det väljer du Ange administratörskommando på **sidan Active Directory-administratör.**

    ![Skärmbild som visar kommandot Ange administratör markerat på active directory-administratörssidan för den valda hanterade SQL-instansen.](./media/authentication-aad-configure/set-admin.png)

7. På azure AD-administratörssidan söker du efter en användare, väljer den användare eller grupp som ska vara administratör och väljer sedan **Välj.**

   På sidan Active Directory-administratör visas alla medlemmar och grupper i Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. Se listan över administratörer som stöds i [Funktioner och begränsningar i Azure AD.](authentication-aad-overview.md#azure-ad-features-and-limitations) Rollbaserad åtkomstkontroll i Azure (Azure RBAC) gäller endast för Azure Portal och sprids inte till SQL Database, SQL Managed Instance eller Azure Synapse.

    ![Lägg Azure Active Directory administratör](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Längst upp på sidan Active Directory-administratör väljer du **Spara**.

    ![Skärmbild av sidan Active Directory-administratör med knappen Spara på den översta raden bredvid knapparna Ange administratör och Ta bort administratör.](./media/authentication-aad-configure/save.png)

    Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan Active Directory-administratör.

När du har etablerat en Azure AD-administratör för SQL Managed Instance kan du börja skapa Azure AD-serverhuvudnamn (inloggningar) med [syntaxen CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Mer information finns i [SQL Managed Instance översikt.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)

> [!TIP]
> Om du senare vill ta bort en administratör väljer du Ta bort administratör överst på active directory-administratörssidan och väljer sedan **Spara.**

### <a name="powershell"></a>PowerShell

Om du vill SQL Managed Instance azure AD-läsbehörighet med hjälp av PowerShell kör du det här skriptet:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell för SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill köra PowerShell-cmdlets måste du ha Azure PowerShell installerat och igång. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Managed Instance, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot felkorrigeringar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Om du vill etablera en Azure AD-administratör kör du följande Azure PowerShell kommandon:

- Connect-AzAccount
- Select-AzSubscription

De cmdlets som används för att etablera och hantera Azure AD-administratör för SQL Managed Instance visas i följande tabell:

| Cmdlet-namn | Description |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Tillhandahåller en Azure AD-administratör för SQL Managed Instance i den aktuella prenumerationen. (Måste komma från den aktuella prenumerationen)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Tar bort en Azure AD-administratör för SQL Managed Instance i den aktuella prenumerationen. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Returnerar information om en Azure AD-administratör för SQL Managed Instance i den aktuella prenumerationen.|

Följande kommando hämtar information om en Azure AD-administratör för en SQL Managed Instance med namnet ManagedInstance01 som är associerad med en resursgrupp med namnet ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Följande kommando tillhandahåller en Azure AD-administratörsgrupp med namnet DBA:er för SQL Managed Instance med namnet ManagedInstance01. Den här servern är associerad med resursgruppen ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Följande kommando tar bort Azure AD-administratören för den SQL Managed Instance med namnet ManagedInstanceName01 som är associerad med resursgruppen ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan också etablera en Azure AD-administratör för SQL Managed Instance genom att anropa följande CLI-kommandon:

| Kommando | Beskrivning |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | Anger en Azure Active Directory administratör för SQL Managed Instance (måste vara från den aktuella prenumerationen). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | Tar bort Azure Active Directory administratör för SQL Managed Instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | Returnerar information om en Azure Active Directory administratör som för närvarande är konfigurerad för SQL Managed Instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | Uppdaterar Active Directory-administratören för SQL Managed Instance. |

Mer information om CLI-kommandon finns i [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Etablera Azure AD-administratör (SQL Database)

> [!IMPORTANT]
> Följ bara de här stegen om du etablerar en [server](logical-servers.md) för SQL Database eller Azure Synapse.

Följande två procedurer visar hur du etablerar en Azure Active Directory administratör för servern i Azure Portal och med hjälp av PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. På [Azure-portalen](https://portal.azure.com/) väljer du din anslutning i det övre högra hörnet för att visa en lista över möjliga Active Directories. Välj rätt Active Directory som standard-Azure AD. Det här steget länkar den prenumerations associerade Active Directory med servern och ser till att samma prenumeration används för både Azure AD och servern.

2. Sök efter och välj **SQL Server**.

    ![Sök efter och välj SQL-servrar](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Innan du väljer **SQL-servrar** på den  här sidan kan  du välja stjärnan bredvid namnet för att favoritera kategorin och lägga till **SQL-servrar** i det vänstra navigeringsfältet.

3. På **SQL Server** väljer du **Active Directory-administratör.**

4. På sidan **Active Directory-administratör** väljer du **Ange administratör.**

    ![KONFIGURERA Active Directory-administratör för SQL-servrar](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. på sidan **Lägg till administratör** söker du efter en användare, väljer den användare eller den grupp som ska vara administratör och väljer sedan **Välj**. (Active Directory-administratörssidan visar alla medlemmar och grupper för din Active Directory. Användare eller grupper som är nedtonade kan inte väljas eftersom de inte stöds som Azure AD-administratörer. (Se listan över administratörer som stöds i avsnittet Funktioner och begränsningar i **Azure AD** i [Använda Azure Active Directory-autentisering](authentication-aad-overview.md)för autentisering med SQL Database eller Azure Synapse .) Rollbaserad åtkomstkontroll i Azure (Azure RBAC) gäller endast för portalen och sprids inte till SQL Server.

    ![Välj Azure Active Directory administratör](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Längst upp på sidan **Active Directory-administratör** väljer du **Spara**.

    ![spara admin](./media/authentication-aad-configure/save-admin.png)

Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

   > [!NOTE]
   > När azure AD-administratören konfigureras kan det nya administratörsnamnet (användare eller grupp) inte redan finnas i den virtuella huvuddatabasen som en serverautentiseringsanvändare. Om det finns misslyckas Azure AD-administratörskonfigurationen. Den återställer skapande och anger att den administratören (namnet) redan finns. Eftersom en sådan serverautentiseringsanvändare inte är en del av Azure AD misslyckas alla försök att ansluta till servern med Hjälp av Azure AD-autentisering.

Om du senare vill ta bort en administratör väljer du Ta bort administratör överst på **Active Directory-administratörssidan** **och** väljer sedan **Spara.**

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell för SQL Database och Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill köra PowerShell-cmdlets måste du ha Azure PowerShell installerat och igång. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/). Om du vill etablera en Azure AD-administratör kör du följande Azure PowerShell kommandon:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets som används för att etablera och hantera Azure AD-SQL Database och Azure Synapse:

| Cmdlet-namn | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |En administratör Azure Active Directory servern som är värd för SQL Database eller Azure Synapse. (Måste komma från den aktuella prenumerationen) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Tar bort en Azure Active Directory administratör för servern som är värd för SQL Database eller Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Returnerar information om en Azure Active Directory administratör som för närvarande är konfigurerad för den server som är värd för SQL Database eller Azure Synapse. |

Använd PowerShell-kommandot get-help för att se mer information om vart och ett av dessa kommandon. Till exempel `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Följande skript tillhandahåller en Azure AD-administratörsgrupp **med namnet DBA_Group** (objekt-ID ) för demo_server-servern i en resursgrupp med namnet `40b79501-b343-44ed-9ce7-da4c8cc7353f` **Group-23**: 

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**Indataparametern DisplayName** accepterar antingen Azure AD-visningsnamnet eller användarens huvudnamn. Exempelvis ``DisplayName="John Smith"`` och ``DisplayName="johns@contoso.com"``. För Azure AD-grupper stöds endast Azure AD-visningsnamnet.

> [!NOTE]
> Kommandot Azure PowerShell hindrar ```Set-AzSqlServerActiveDirectoryAdministrator``` dig inte från att etablera Azure AD-administratörer för användare som inte stöds. En användare som inte stöds kan etableras, men kan inte ansluta till en databas.

I följande exempel används det valfria **ObjectID:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Azure AD **ObjectID** krävs när **DisplayName inte** är unikt. Om du vill **hämta värdena ObjectID** **och DisplayName** använder du Active Directory-avsnittet i den klassiska Azure-portalen och visar egenskaperna för en användare eller grupp.

I följande exempel returneras information om den aktuella Azure AD-administratören för servern:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

I följande exempel tas en Azure AD-administratör bort:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan etablera en Azure AD-administratör genom att anropa följande CLI-kommandon:

| Kommando | Beskrivning |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | En administratör Azure Active Directory servern som är värd för SQL Database eller Azure Synapse. (Måste komma från den aktuella prenumerationen) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | Tar bort en Azure Active Directory administratör för servern som är värd SQL Database eller Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | Returnerar information om en Azure Active Directory administratör som är konfigurerad för den server som är värd för SQL Database eller Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | Uppdaterar Active Directory-administratören för servern som är värd SQL Database eller Azure Synapse. |

Mer information om CLI-kommandon finns i [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Du kan också etablera en Azure Active Directory administratör med hjälp av REST-API:erna. Mer information finns i [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurera klientdatorerna

På alla klientdatorer som dina program eller användare ansluter till SQL Database eller Azure Synapse med hjälp av Azure AD-identiteter måste du installera följande programvara:

- .NET Framework 4.6 eller senare från [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory Authentication Library for SQL Server (*ADAL.DLL*). Nedan visas nedladdningslänkarna för att installera den senaste SSMS-, ODBC OLE DB- och OLE DB-drivrutinen som innehåller *ADAL.DLL-biblioteket.*
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC-drivrutin 17 för SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB Drivrutin 18 för SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Du kan uppfylla dessa krav genom att:

- Installation av den senaste versionen [av SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) eller [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) uppfyller kraven .NET Framework 4.6.
  - SSMS installerar x86-versionen av *ADAL.DLL*.
  - SSDT installerar amd64-versionen av *ADAL.DLL*.
  - Den senaste Visual Studio från [Visual Studio-nedladdningar](https://www.visualstudio.com/downloads/download-visual-studio-vs) uppfyller kravet .NET Framework 4.6, men installerar inte den amd64-version som *krävs förADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Skapa inneslutna användare som är mappade till Azure AD-identiteter

Eftersom SQL Managed Instance har stöd för Azure AD-serverhuvudnamn (inloggningar) krävs inte användning av inneslutna databasanvändare. Med Azure AD-serverhuvudkonton (inloggningar) kan du skapa inloggningar från Azure AD-användare, grupper eller program. Det innebär att du kan autentisera med din SQL Managed Instance med hjälp av Azure AD-serverinloggning i stället för en innesluten databasanvändare. Mer information finns i [SQL Managed Instance översikt.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration) Syntax för hur du skapar Azure AD-serverhuvudnamn (inloggningar) finns i [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Men om du Azure Active Directory autentisering med SQL Database och Azure Synapse du använda inneslutna databasanvändare baserat på en Azure AD-identitet. En innesluten databasanvändare har ingen inloggning i huvuddatabasen och mappar till en identitet i Azure AD som är associerad med databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information om användare av oberoende databas finns i avsnittet om [användare av oberoende databas – så gör du din databas portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable).

> [!NOTE]
> Databasanvändare (med undantag för administratörer) kan inte skapas med hjälp av Azure-portalen. Azure-roller sprids inte till databasen i SQL Database, SQL Managed Instance eller Azure Synapse. Azure-roller används för att hantera Azure-resurser och gäller inte för databasbehörigheter. Till exempel beviljar **SQL Server rollen** Deltagare inte åtkomst för att ansluta till databasen i SQL Database, SQL Managed Instance eller Azure Synapse. Åtkomstbehörigheten måste beviljas direkt i databasen med hjälp av Transact-SQL-instruktioner.

> [!WARNING]
> Specialtecken som kolon `:` eller et-tecken `&` när de ingår som användarnamn i T-SQL och instruktioner stöds `CREATE LOGIN` `CREATE USER` inte.

Om du vill skapa en Azure AD-baserad innesluten databasanvändare (förutom serveradministratören som äger databasen) ansluter du till databasen med en Azure AD-identitet som en användare med minst **behörigheten ALTER ANY USER.** Använd sedan följande Transact-SQL-syntax:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan vara användarens huvudnamn för en Azure AD-användare eller visningsnamnet för en Azure AD-grupp.

**Exempel:** Så här skapar du en innesluten databasanvändare som representerar en federerad eller hanterad Azure AD-domänanvändare:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Om du vill skapa en innesluten databasanvändare som representerar en Azure AD eller en federerad domängrupp anger du visningsnamnet för en säkerhetsgrupp:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Så här skapar du en innesluten databasanvändare som representerar ett program som ansluter med hjälp av en Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Det här kommandot kräver att SQL får åtkomst till Azure AD (den "externa providern") åt den inloggade användaren. Ibland uppstår omständigheter som gör att Azure AD returnerar ett undantag tillbaka till SQL. I dessa fall visas SQL-fel 33134, som ska innehålla det Azure AD-specifika felmeddelandet. I de flesta fall säger felet att åtkomsten nekas eller att användaren måste registrera sig i MFA för att få åtkomst till resursen, eller att åtkomsten mellan förstapartsprogram måste hanteras via förauktorisering. I de första två fallen orsakas problemet vanligtvis av principer för villkorsstyrd åtkomst som anges i användarens Azure AD-klientorganisation: de hindrar användaren från att komma åt den externa providern. Om du uppdaterar principerna för villkorsstyrd åtkomst för att tillåta åtkomst till programmet "00000002-0000-0000-c000-000000000000" (program-ID för Azure AD Graph API) bör problemet lösas. Om felet säger åtkomst mellan förstapartsprogram måste hanteras via förauktorisering beror problemet på att användaren är inloggad som ett huvudnamn för tjänsten. Kommandot bör lyckas om det körs av en användare i stället.

> [!TIP]
> Du kan inte skapa en användare direkt från en Azure Active Directory annan än den Azure Active Directory som är associerad med din Azure-prenumeration. Medlemmar i andra Active Directory-kataloger som är importerade användare i den associerade Active Directory (kallas även externa användare) kan dock läggas till i en Active Directory-grupp i klientorganisationens Active Directory. Genom att skapa en oberoende databasanvändare för den AD-gruppen kan användarna från den externa Active Directory få åtkomst till SQL Database.

Mer information om hur du skapar inneslutna databasanvändare baserat Azure Active Directory identiteter finns i [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Om du Azure Active Directory administratör för servern förhindras alla Azure AD-autentiseringsanvändare från att ansluta till servern. Om det behövs kan oanvändbara Azure AD-användare tas bort manuellt av en SQL Database administratör.

> [!NOTE]
> Om du får en **tidsgräns för anslutningen har upphört** att gälla kan du behöva ange `TransparentNetworkIPResolution` parametern för anslutningssträngen till false. Mer information finns i Problem [med timeout för anslutning .NET Framework 4.6.1 – TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

När du skapar en databasanvändare får användaren **behörigheten CONNECT** och kan ansluta till databasen som medlem i **rollen OFFENTLIG.** Inledningsvis är de enda behörigheter som är  tillgängliga för användaren alla behörigheter som beviljas till den offentliga rollen eller eventuella behörigheter som beviljas till alla Azure AD-grupper som de är medlemmar i. När du etablerar en Azure AD-baserad innesluten databasanvändare kan du ge användaren ytterligare behörigheter, på samma sätt som du beviljar behörighet till alla andra typer av användare. Bevilja vanligtvis behörigheter till databasroller och lägg till användare i roller. Mer information finns i [Grundläggande om databasmotorbehörighet.](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) Mer information om särskilda SQL Database finns i [Hantera databaser och inloggningar i Azure SQL Database](logins-create-manage.md).
Ett federerat domänanvändarkonto som importeras till en hanterad domän som en extern användare måste använda den hanterade domänidentiteten.

> [!NOTE]
> Azure AD-användare markeras i databasmetadata med typen E (EXTERNAL_USER) och för grupper med typen X (EXTERNAL_GROUPS). Mer information finns i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Ansluta till databasen med SSMS eller SSDT  

Bekräfta att Azure AD-administratören är korrekt konfigurerad genom att ansluta till **huvuddatabasen** med hjälp av Azure AD-administratörskontot.
Om du vill etablera en Azure AD-baserad innesluten databasanvändare (förutom den serveradministratör som äger databasen) ansluter du till databasen med en Azure AD-identitet som har åtkomst till databasen.

> [!IMPORTANT]
> Stöd för Azure Active Directory autentisering är tillgängligt [med SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) i Visual Studio 2015. Augusti 2016-versionen av SSMS innehåller också stöd för Active Directory Universal Authentication, som gör att administratörer kan kräva Multi-Factor Authentication med hjälp av ett telefonsamtal, SMS, smartkort med PIN-kod eller avisering via mobilapp.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Använda en Azure AD-identitet för att ansluta med SSMS eller SSDT

Följande procedurer visar hur du ansluter till SQL Database med en Azure AD-identitet med SQL Server Management Studio eller SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Använd den här metoden om du är inloggad i Windows med dina Azure Active Directory-autentiseringsuppgifter från en federerad domän eller en hanterad domän som har konfigurerats för sömlös enkel inloggning för direkt- och lösenordshasharautentisering. Mer information finns i [Azure Active Directory för sömlös enkel inloggning.](../../active-directory/hybrid/how-to-connect-sso.md)

1. Starta Management Studio eller dataverktyg och i dialogrutan Anslut till **server** (eller Anslut  till **databasmotor)** går du till rutan Autentisering och **väljer Azure Active Directory - Integrerad**. Inget lösenord krävs eller kan anges eftersom dina befintliga autentiseringsuppgifter visas för anslutningen.

   ![Välj AD-integrerad autentisering][11]

2. Välj knappen **Alternativ** och  skriv namnet på  den användardatabas som du vill ansluta till i rutan Anslut till databas på sidan Anslutningsegenskaper. Mer information finns i artikeln [Multi-factor Azure AD-autentisering](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) om skillnaderna mellan anslutningsegenskaperna för SSMS 17.x och 18.x.

   ![Välj databasnamnet][13]

### <a name="active-directory-password-authentication"></a>Active Directory-lösenordsautentisering

Använd den här metoden när du ansluter med ett Azure AD-huvudnamn med hjälp av den Hanterade Azure AD-domänen. Du kan också använda den för federerade konton utan åtkomst till domänen, till exempel när du arbetar via fjärråtkomst.

Använd den här metoden för att autentisera till databasen i SQL Database eller SQL Managed Instance med azure AD-molnbaserade identitetsanvändare eller de som använder Azure AD-hybrididentiteter. Den här metoden stöder användare som vill använda sina Windows-autentiseringsuppgifter, men deras lokala dator är inte ansluten till domänen (till exempel med fjärråtkomst). I det här fallet kan en Windows-användare ange sitt domänkonto och lösenord och autentisera till databasen i SQL Database, SQL Managed Instance eller Azure Synapse.

1. Starta Management Studio eller Dataverktyg och i dialogrutan Anslut till **server** (eller Anslut  till databasmotor) går du till rutan Autentisering och väljer Azure Active Directory - **Lösenord**.

2. I rutan **Användarnamn skriver** du Azure Active Directory användarnamn i formatet **användarnamn \@ domain.com**. Användarnamn måste vara ett konto från Azure Active Directory eller ett konto från en hanterad eller federerad domän med Azure Active Directory.

3. I rutan **Lösenord** anger du ditt användarlösenord för Azure Active Directory eller hanterat/federerat domänkonto.

    ![Välj AD-lösenordsautentisering][12]

4. Välj knappen **Alternativ** och  skriv namnet på  den användardatabas som du vill ansluta till i rutan Anslut till databas på sidan Anslutningsegenskaper. (Se bilden i föregående alternativ.)

### <a name="active-directory-interactive-authentication"></a>Interaktiv Active Directory-autentisering

Använd den här metoden för interaktiv autentisering med eller utan Multi-Factor Authentication (MFA), där lösenord begärs interaktivt. Den här metoden kan användas för att autentisera till databasen i SQL Database, SQL Managed Instance och Azure Synapse för azure AD-användare med endast molnbaserad identitet, eller de som använder Azure AD-hybrididentiteter.

Mer information finns i Använda [Multi-Factor Azure AD-autentisering med SQL Database och Azure Synapse (SSMS-stöd för MFA).](authentication-mfa-ssms-overview.md)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Använda en Azure AD-identitet för att ansluta från ett klientprogram

Följande procedurer visar hur du ansluter till en SQL Database med en Azure AD-identitet från ett klientprogram.

### <a name="active-directory-integrated-authentication"></a>Active Directory-integrerad autentisering

Om du vill använda integrerad Windows-autentisering måste domänens Active Directory vara federerat med Azure Active Directory eller vara en hanterad domän som är konfigurerad för sömlös enkel inloggning för direkt- eller lösenordshasharautentisering. Mer information finns i [Azure Active Directory för sömlös enkel inloggning.](../../active-directory/hybrid/how-to-connect-sso.md)

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) för integrerad Windows-autentisering stöds inte för sömlös enkel inloggning för direkt- och lösenordshasharautentisering.

Klientprogrammet (eller en tjänst) som ansluter till databasen måste köras på en domänansluten dator under en användares domänautentiseringsuppgifter.

Om du vill ansluta till en databas med integrerad autentisering och en Azure AD-identitet måste autentiseringsnyckelordet i databasanslutningssträngen anges till `Active Directory Integrated` . Följande C#-kodexempel använder ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Nyckelordet för `Integrated Security=True` anslutningssträngen stöds inte för att ansluta till Azure SQL Database. När du upprättar en ODBC-anslutning måste du ta bort blanksteg och ange "ActiveDirectoryIntegrated" för Autentisering.

### <a name="active-directory-password-authentication"></a>Active Directory-lösenordsautentisering

Om du vill ansluta till en databas med endast Azure AD-identitetsanvändarkonton, eller de som använder Azure AD-hybrididentiteter, måste autentiseringsnyckelordet vara inställt på `Active Directory Password` . Anslutningssträngen måste innehålla nyckelord och värden för användar-ID/UID och lösenord/PWD. I följande C#-kodexempel används ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Läs mer om Azure AD-autentiseringsmetoder med hjälp av de demokodexempel som finns på [GitHub-demonstrationen för Azure AD-autentisering.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)

## <a name="azure-ad-token"></a>Azure AD-token

Med den här autentiseringsmetoden kan tjänster på mellannivå hämta JSON-webbtoken [(JWT)](../../active-directory/develop/id-tokens.md) för att ansluta till databasen i SQL Database, SQL Managed Instance eller Azure Synapse genom att hämta en token från Azure AD. Den här metoden möjliggör olika programscenarier, inklusive tjänstidentiteter, tjänstens huvudnamn och program som använder certifikatbaserad autentisering. Du måste utföra fyra grundläggande steg för att använda Azure AD-tokenautentisering:

1. Registrera ditt program med Azure Active Directory och hämta klient-ID:t för din kod.
2. Skapa en databasanvändare som representerar programmet. (Slutfördes tidigare i steg 6.)
3. Skapa ett certifikat på klientdatorn som kör programmet.
4. Lägg till certifikatet som en nyckel för ditt program.

Exempel på anslutningssträng:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Mer information finns i [SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Information om hur du lägger till ett certifikat [finns i Kom igång med certifikatbaserad autentisering i Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Följande instruktioner ansluter med version 13.1 av sqlcmd, som är tillgänglig från [Download Center](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` med kommandot `-G` fungerar inte med systemidentiteter och kräver inloggning med användarens huvudnamn.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Felsöka Azure AD-autentisering

Vägledning om felsökning av problem med Azure AD-autentisering finns i följande blogg: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Nästa steg

- En översikt över inloggningar, användare, databasroller och behörigheter i SQL Database finns i [Inloggningar, användare, databasroller och användarkonton.](logins-create-manage.md)
- Mer information om huvudkonton finns i [Huvudkonton](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Mer information om databasroller finns [Databasroller](/sql/relational-databases/security/authentication-access/database-level-roles).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](firewall-configure.md).
- Information om hur du anger en Azure AD-gästanvändare som Azure AD-administratör finns i Skapa [Azure AD-gästanvändare och ange som Azure AD-administratör.](authentication-aad-guest-users.md)
- Information om hur du använder tjänstens huvudnamn med Azure SQL finns i [Skapa Azure AD-användare med Hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
