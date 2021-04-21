---
title: Azure Active Directory tjänstens huvudnamn med Azure SQL
description: Azure AD-program (tjänstens huvudnamn) stöder skapande av Azure AD-användare i Azure SQL Database, Azure SQL Managed Instance och Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775429"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory tjänstens huvudnamn med Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Stöd för Azure Active Directory -användare (Azure AD) i Azure SQL Database (SQL DB) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) för Azure AD-program (tjänstens huvudnamn) är för närvarande i **offentlig förhandsversion.**

> [!NOTE]
> Den här funktionen stöds redan för SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Stöd för tjänstens huvudnamn (Azure AD-program)

Den här artikeln gäller för program som är integrerade med Azure AD och som är en del av Azure AD-registreringen. Dessa program behöver ofta autentiserings- och auktoriseringsåtkomst till Azure SQL utföra olika uppgifter. Den här funktionen **i offentlig förhandsversion** gör nu att tjänstens huvudnamn kan skapa Azure AD-användare i SQL Database och Azure Synapse. Det fanns en begränsning som förhindrar att Azure AD-objekt skapas för Azure AD-program som har tagits bort.

När ett Azure AD-program registreras med Azure Portal eller ett PowerShell-kommando skapas två objekt i Azure AD-klientorganisationen:

- Ett programobjekt
- Ett objekt för tjänstens huvudnamn

Mer information om Azure AD-program finns i Program- och tjänsthuvudnamnsobjekt [i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) och Skapa ett huvudnamn för [Azure-tjänsten med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

SQL Database, Azure Synapse och SQL Managed Instance stöd för följande Azure AD-objekt:

- Azure AD-användare (hanterade, federerade och gäst)
- Azure AD-grupper (hanterade och federerade)
- Azure AD-program 

T-SQL-kommandot `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` för ett Azure AD-program stöds nu för SQL Database och Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funktioner för att skapa Azure AD-användare med hjälp av tjänstens huvudnamn

Stöd för den här funktionen är användbart i Azure AD-programautomatiseringsprocesser där Azure AD-objekt skapas och underhålls i SQL Database och Azure Synapse utan mänsklig interaktion. Tjänstens huvudnamn kan vara en Azure AD-administratör för den logiska SQL-servern som en del av en grupp eller en enskild användare. Programmet kan automatisera skapandet av Azure AD-SQL Database Azure Synapse när det körs som systemadministratör och kräver inte några ytterligare SQL-behörigheter. På så sätt kan du automatisera skapandet av en databasanvändare. Den här funktionen stöds också för system tilldelad hanterad identitet och användar tilldelad hanterad identitet. Mer information finns i Vad [är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Aktivera tjänstens huvudnamn för att skapa Azure AD-användare

Om du vill aktivera skapandet av Ett Azure AD-SQL Database Azure Synapse för ett Azure AD-program krävs följande inställningar:

1. Tilldela serveridentiteten. Den tilldelade serveridentiteten representerar den hanterade tjänstidentiteten (MSI). För närvarande har serveridentiteten för Azure SQL inte stöd för användarstyrd identitet (UMI).
    - För en ny Azure SQL logisk server kör du följande PowerShell-kommando:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Mer information finns i kommandot [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver)

    - För befintliga Azure SQL logiska servrar kör du följande kommando:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Mer information finns i kommandot [Set-AzSqlServer.](/powershell/module/az.sql/set-azsqlserver)

    - Kontrollera om serveridentiteten har tilldelats servern genom att köra Get-AzSqlServer kommando.

    > [!NOTE]
    > Serveridentitet kan också tilldelas med hjälp av CLI-kommandon. Mer information finns i [az sql server create och](/cli/azure/sql/server#az_sql_server_create) az sql server [update](/cli/azure/sql/server#az_sql_server_update).

2. Ge Azure [**AD-katalogläsarna**](../../active-directory/roles/permissions-reference.md#directory-readers) behörighet till serveridentiteten som skapats eller tilldelats till servern.
    - Om du vill bevilja den här behörigheten följer du beskrivningen som SQL Managed Instance som finns i följande artikel: [Etablera Azure AD-administratör (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Den Azure AD-användare som beviljar den här behörigheten måste vara en del av rollen Global administratör **för** Azure AD eller **Privilegierade roller.**

> [!IMPORTANT]
> Steg 1 och 2 måste köras i ovanstående ordning. Skapa eller tilldela först serveridentiteten, följt av att ge [**behörigheten Katalogläsare.**](../../active-directory/roles/permissions-reference.md#directory-readers) Om du utelämnar något av de här stegen eller båda kommer det att orsaka ett körningsfel när ett Azure AD-objekt skapas i Azure SQL för ett Azure AD-program.
>
> Om du använder tjänstens huvudnamn för att ange eller ta bort azure AD-administratören måste programmet också ha behörigheten [Directory.Read.All](/graph/permissions-reference#application-permissions-18) Application API i Azure AD. Mer information om behörigheter som krävs för att ange en [Azure AD-administratör](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)och stegvisa instruktioner för att skapa en Azure AD-användare för ett Azure AD-program finns i Självstudie: Skapa Azure AD-användare med Hjälp av [Azure AD-program.](authentication-aad-service-principal-tutorial.md)
>
> I **offentlig förhandsversion** kan du tilldela rollen **Katalogläsare** till en grupp i Azure AD. Gruppägare kan sedan lägga till den hanterade identiteten som medlem i den här gruppen, vilket skulle kringgå behovet av att en **global administratör** eller privilegierad **rolladministratör** beviljar rollen **Katalogläsare.** Mer information om den här funktionen finns i [Katalogläsarroll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Felsökning och begränsningar för offentlig förhandsversion

- När du skapar Azure AD-objekt i Azure SQL för ett Azure AD-program utan att aktivera serveridentitet och bevilja katalogläsarbehörighet, misslyckas åtgärden med följande möjliga fel.  Exempelfelet nedan är för en PowerShell-kommandokörning för att skapa en SQL Database i artikeln Självstudie: Skapa Azure AD-användare med Hjälp av `myapp` [Azure AD-program.](authentication-aad-service-principal-tutorial.md)
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - För ovanstående fel följer du stegen för att tilldela en identitet till [den logiska Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) och tilldela katalogläsare behörighet till den logiska [SQL-serveridentiteten](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > Felmeddelandena som anges ovan kommer att ändras före funktionens GA för att tydligt identifiera det saknade konfigurationskravet för Azure AD-programstöd.
- Att ange Azure AD-programmet som Azure AD-administratör för SQL Managed Instance stöds endast med hjälp av CLI-kommandot och PowerShell-kommando med [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) eller senare. Mer information finns i kommandona [az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) och [Set-AzSqlInstanceActiveDirectoryAdministrator.](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 
    - Om du vill använda Azure Portal för SQL Managed Instance ange Azure AD-administratör är en möjlig lösning att skapa en Azure AD-grupp. Lägg sedan till tjänstens huvudnamn (Azure AD-program) i den här gruppen och ange den här gruppen som Azure AD-administratör för SQL Managed Instance.
    - Det går att ange tjänstens huvudnamn (Azure AD-program) som Azure AD-administratör för SQL Database och Azure Synapse med hjälp av kommandona Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse) [och CLI.](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)
- Användning av ett Azure AD-program med tjänstens huvudnamn från en annan Azure AD-klientorganisation misslyckas vid åtkomst till SQL Database eller SQL Managed Instance som skapats i en annan klientorganisation. Ett huvudnamn för tjänsten som tilldelats det här programmet måste komma från samma klientorganisation som den logiska SQL-servern eller den hanterade instansen.
- [Az.Sql 2.9.0-modulen](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) eller senare behövs när du använder PowerShell för att konfigurera ett enskilt Azure AD-program som Azure AD-administratör för Azure SQL. Se till att du har uppgraderat till den senaste modulen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skapa Azure AD-användare med hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md)
