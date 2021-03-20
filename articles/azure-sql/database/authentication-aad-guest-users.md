---
title: Skapa gäst användare i Azure AD
description: Skapa Azure AD-gäst användare och ange dem som Azure AD-administratör utan att använda Azure AD-grupper i Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675060"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Skapa Azure AD-gästanvändare och ange som Azure AD-administratör

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Den här artikeln är i **offentlig för hands version**.

Gäst användare i Azure Active Directory (Azure AD) är användare som har importer ATS till den aktuella Azure AD från andra Azure Active Directory-kataloger eller utanför den. Gäst användare kan till exempel inkludera användare från andra Azure Active-kataloger eller från konton som *\@ Outlook.com*, *\@ hotmail.com*, *\@ Live.com* eller *\@ gmail.com*. Den här artikeln visar hur du skapar en Azure AD-gäst användare och anger att användaren är en Azure AD-administratör för den logiska Azure SQL-servern, utan att gäst användaren behöver ingå i en grupp i Azure AD.

## <a name="feature-description"></a>Funktionsbeskrivning

Den här funktionen lyfter den aktuella begränsningen som endast tillåter gäst användare att ansluta till Azure SQL Database, SQL-hanterad instans eller Azure Synapse Analytics när de är medlemmar i en grupp som skapats i Azure AD. Gruppen måste mappas till en användare manuellt med instruktionen [create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) i en specifik databas. När en databas användare har skapats för den Azure AD-grupp som innehåller gäst användaren, kan gäst användaren logga in på databasen med hjälp av Azure Active Directory med MFA-autentisering. Som en del av den här **offentliga för hands versionen** kan gäst användare skapas och ansluta direkt till SQL Database, SQL-hanterad instans eller Azure-Synapse utan kravet på att lägga till dem i en Azure AD-grupp först och sedan skapa en databas användare för den Azure AD-gruppen.

Som en del av den här funktionen har du också möjlighet att ställa in Azure AD-gäst användaren direkt som en AD-administratör för den logiska Azure SQL-servern. De befintliga funktionerna där gäst användaren kan vara en del av en Azure AD-grupp och gruppen kan sedan anges som Azure AD-administratör för den logiska Azure SQL-servern påverkas inte. Gäst användare i databasen som ingår i en Azure AD-grupp påverkas inte heller av den här ändringen.

Mer information om befintligt stöd för gäst användare som använder Azure AD-grupper finns i [använda Multi-factor Azure Active Directory-autentisering](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Förutsättning

- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) -modulen eller senare krävs när du använder PowerShell för att ange en gäst användare som Azure AD-administratör för den logiska Azure SQL-servern.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Skapa databas användare för Azure AD-gäst användare 

Följ dessa steg om du vill skapa en databas användare med hjälp av en Azure AD-gäst användare.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Skapa gäst användare i SQL Database och Azure-Synapse

1. Se till att gäst användaren (till exempel `user1@gmail.com` ) redan har lagts till i din Azure AD och att en Azure AD-administratör har angetts för databas servern. Det krävs en Azure AD-administratör för att Azure Active Directory autentisering.

1. Anslut till SQL-databasen som Azure AD-administratör eller en Azure AD-användare med tillräckliga SQL-behörigheter för att skapa användare och kör kommandot nedan på databasen där gäst användaren behöver läggas till:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Det bör nu vara en databas användare som skapats för gäst användaren `user1@gmail.com` .

1. Kör kommandot nedan för att kontrol lera att databas användaren har skapats:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Koppla från och logga in i databasen som gäst användare som `user1@gmail.com` använder [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) med autentiseringsmetoden **Azure Active Directory-Universal med MFA**. Mer information finns i [använda Multi-factor Azure Active Directory-autentisering](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Skapa gäst användare i SQL-hanterad instans

> [!NOTE]
> SQL-hanterad instans stöder inloggningar för Azure AD-användare, samt Azure AD-inneslutna databas användare. Stegen nedan visar hur du skapar en inloggning och användare för en Azure AD-gäst användare i SQL-hanterad instans. Du kan också välja att skapa en [innesluten databas användare](/sql/relational-databases/security/contained-database-users-making-your-database-portable) i SQL-hanterad instans med hjälp av metoden i avsnittet [skapa gäst användare i SQL Database och Azure-Synapse](#create-guest-user-in-sql-database-and-azure-synapse) .

1. Se till att gäst användaren (till exempel `user1@gmail.com` ) redan har lagts till i din Azure AD och att en Azure AD-administratör har ställts in för SQL-hanterad instans Server. Det krävs en Azure AD-administratör för att Azure Active Directory autentisering.

1. Anslut till SQL-hanterad instans server som Azure AD-administratör eller Azure AD-användare med tillräckliga SQL-behörigheter för att skapa användare och kör följande kommando i `master` databasen för att skapa en inloggning för gäst användaren:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Nu bör det finnas en inloggning som skapats för gäst användaren `user1@gmail.com` i `master` databasen.

1. Kör kommandot nedan för att kontrol lera att inloggningen har skapats:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Kör kommandot nedan på databasen där gäst användaren behöver läggas till: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Det bör nu vara en databas användare som skapats för gäst användaren `user1@gmail.com` .

1. Koppla från och logga in i databasen som gäst användare som `user1@gmail.com` använder [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) med autentiseringsmetoden **Azure Active Directory-Universal med MFA**. Mer information finns i [använda Multi-factor Azure Active Directory-autentisering](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Ange en gäst användare som en Azure AD-administratör

Följ dessa steg om du vill ange en Azure AD-gäst användare som Azure AD-administratör för den logiska SQL-servern.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Ange Azure AD-administratör för SQL Database och Azure-Synapse

1. Se till att gäst användaren (till exempel `user1@gmail.com` ) redan har lagts till i Azure AD.

1. Kör följande PowerShell-kommando för att lägga till gäst användaren som Azure AD-administratör för din logiska Azure SQL-Server:

    - Ersätt `<ResourceGroupName>` med namnet på din Azure-resurs grupp som innehåller den logiska Azure SQL-servern.
    - Ersätt `<ServerName>` med namnet på den logiska Azure SQL-servern. Om Server namnet är `myserver.database.windows.net` , ersätter du `<Server Name>` med `myserver` .
    - Ersätt `<DisplayNameOfGuestUser>` med ditt gäst användar namn.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Du kan också använda Azure CLI-kommandot [AZ SQL Server AD-admin](/cli/azure/sql/server/ad-admin) för att ange gäst användaren som en Azure AD-administratör för din logiska Azure SQL-Server.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Ange Azure AD-administratör för SQL-hanterad instans

1. Se till att gäst användaren (till exempel `user1@gmail.com` ) redan har lagts till i Azure AD.

1. Gå till [Azure Portal](https://portal.azure.com)och gå till din **Azure Active Directory** -resurs. Gå till fönstret **användare** under **Hantera**. Välj gäst användare och registrera `Object ID` . 

1. Kör följande PowerShell-kommando för att lägga till gäst användaren som Azure AD-administratör för din SQL-hanterade instans:

    - Ersätt `<ResourceGroupName>` med namnet på din Azure-resurs grupp som innehåller SQL-hanterad instans.
    - Ersätt `<ManagedInstanceName>` med ditt SQL-hanterade instans namn.
    - Ersätt `<DisplayNameOfGuestUser>` med ditt gäst användar namn.
    - Ersätt `<AADObjectIDOfGuestUser>` med den `Object ID` insamlade tidigare.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Du kan också använda Azure CLI-kommandot [AZ SQL mi AD-admin](/cli/azure/sql/mi/ad-admin) för att ange gäst användaren som en Azure AD-administratör för din SQL-hanterade instans.

## <a name="limitations"></a>Begränsningar

Det finns en begränsning på Azure Portal som förhindrar att du väljer en Azure AD-gäst användare som Azure AD-administratör för SQL-hanterad instans. För gäst konton utanför Azure AD, till exempel *\@ Outlook.com*, *\@ hotmail.com*, *\@ Live.com* eller *\@ gmail.com*, visar AD admin-väljaren dessa konton, men de är nedtonade och kan inte väljas. Använd de PowerShell- [eller CLI-kommandon](#setting-a-guest-user-as-an-azure-ad-admin) som visas ovan för att ange Azure AD-administratören. Alternativt kan en Azure AD-grupp som innehåller gäst användaren anges som Azure AD-administratör för SQL-hanterad instans.

Den här funktionen aktive ras för SQL-hanterad instans innan den här funktionen blir allmänt tillgänglig.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera och hantera Azure AD-autentisering med Azure SQL](authentication-aad-configure.md)
- [Använda Multi-Factor Azure Active Directory-autentisering](authentication-mfa-ssms-overview.md)
- [Skapa användare (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)