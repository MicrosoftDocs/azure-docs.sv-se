---
title: Azure Active Directory tjänstens huvud namn med Azure SQL
description: Azure AD-program (tjänstens huvud namn) stöder generering av Azure AD-användare i Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: d8268ebf89bed6b67919e77576118343b58edb6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516630"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory tjänstens huvud namn med Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Stöd för skapande av Azure Active Directory (Azure AD) av användare i Azure SQL Database (SQL DB) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) för Azure AD-program (tjänstens huvud namn) för närvarande finns i en **offentlig för hands version**.

> [!NOTE]
> Den här funktionen stöds redan för SQL-hanterad instans.

## <a name="service-principal-azure-ad-applications-support"></a>Stöd för tjänstens huvud namn (Azure AD-program)

Den här artikeln gäller för program som är integrerade med Azure AD och ingår i registrering av Azure AD. Dessa program behöver ofta autentisering och åtkomst till Azure SQL för att utföra olika uppgifter. Den här funktionen i för **hands versionen** tillåter nu att tjänstens huvud namn skapar Azure AD-användare i SQL Database och Azure-Synapse. En begränsning förhindrar att Azure AD-objekt skapas på uppdrag av Azure AD-program som har tagits bort.

När ett Azure AD-program registreras med Azure Portal eller ett PowerShell-kommando skapas två objekt i Azure AD-klienten:

- Ett programobjekt
- Ett objekt för tjänstens huvudnamn

Mer information om Azure AD-program finns [i program-och tjänst huvud objekt i Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) och [skapa ett Azure-tjänstens huvud namn med Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0).

SQL Database, Azure-Synapse och SQL-hanterad instans stöder följande Azure AD-objekt:

- Azure AD-användare (hanterade, federerade och gäst)
- Azure AD-grupper (hanterade och federerade)
- Azure AD-program 

T-SQL-kommandot för `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` ett Azure AD-programs räkning stöds nu för SQL Database och Azure-Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funktioner för att skapa Azure AD-användare med hjälp av tjänstens huvud namn

Att stödja den här funktionen är användbart i Azure AD Application Automation-processer där Azure AD-objekt skapas och underhålls i SQL Database och Azure Synapse utan mänsklig interaktion. Tjänstens huvud namn kan vara en Azure AD-administratör för den logiska SQL-servern, som en del av en grupp eller en enskild användare. Programmet kan automatisera genereringen av Azure AD-objekt i SQL Database och Azure-Synapse när den körs som system administratör och kräver inga ytterligare SQL-privilegier. Detta gör det möjligt att skapa en fullständig automatisering av en databas användare. Den här funktionen stöds också för systemtilldelad hanterad identitet och användare som tilldelats hanterad identitet. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Aktivera tjänstens huvud namn för att skapa Azure AD-användare

Om du vill aktivera en Azure AD-objekts skapande i SQL Database och Azure-Synapse för ett Azure AD-program, krävs följande inställningar:

1. Tilldela server identiteten
    - För en ny logisk Azure SQL-Server kör du följande PowerShell-kommando:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Mer information finns i kommandot [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) .

    - För befintliga Azure SQL logiska servrar kör du följande kommando:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Mer information finns i [set-AzSqlServer-](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) kommandot.

    - Om du vill kontrol lera om Server identiteten har tilldelats till servern kör du kommandot Get-AzSqlServer.

    > [!NOTE]
    > Server identiteten kan också tilldelas med CLI-kommandon. Mer information finns i [AZ SQL Server Create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) och [AZ SQL Server Update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update).

2. Ge Azure AD- [**katalogen läsar**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) behörighet till den server identitet som skapats eller tilldelats servern.
    - Om du vill bevilja behörigheten följer du beskrivningen som används för SQL-hanterad instans som finns i följande artikel: [etablera Azure AD admin (SQL-hanterad instans)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Den Azure AD-användare som beviljar behörigheten måste vara en del av administratörs rollen Azure AD **Global Administrator** eller **Privileged roles** .

> [!IMPORTANT]
> Steg 1 och 2 måste köras i ovanstående ordning. Börja med att skapa eller tilldela server identiteten och följ genom att ge [**katalog läsar**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) behörighet. Om du utelämnar något av dessa steg, eller båda kommer att orsaka ett körnings fel under en Azure AD-objekts skapande i Azure SQL för ett Azure AD-program. Stegvisa instruktioner för att skapa en Azure AD-användare på uppdrag av ett Azure AD-program finns i [Självstudier: Skapa Azure AD-användare med hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md).
>
> I **offentlig för hands version**kan du tilldela rollen **katalog läsare** till en grupp i Azure AD. Grupp ägarna kan sedan lägga till den hanterade identiteten som en medlem i den här gruppen, vilket skulle kringgå behovet av att en **Global administratör** eller **privilegierad roll administratör** ska bevilja rollen som **katalog läsare** . Mer information om den här funktionen finns i [katalog läsare roll i Azure Active Directory för Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Fel sökning och begränsningar för offentlig för hands version

- När du skapar Azure AD-objekt i Azure SQL på uppdrag av ett Azure AD-program utan att aktivera server identitet och beviljande av **katalog läsar** behörighet, Miss känner åtgärden med följande möjliga fel. Exemplet nedan är för att köra en PowerShell-kommando körning för att skapa en SQL Database användare `myapp` i artikeln [Självstudier: Skapa Azure AD-användare med hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - För ovanstående fel följer du stegen för att [tilldela en identitet till den logiska Azure SQL-servern](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) och [tilldela katalog läsar behörighet till den logiska SQL-serverns identitet](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > De fel meddelanden som anges ovan ändras innan funktionen GA identifierar det saknade installations kravet för stöd för Azure AD-program.
- Att ställa in Azure AD-programmet som en Azure AD-administratör för SQL-hanterad instans stöds bara med CLI-kommandot och PowerShell-kommandot med [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) eller senare. Mer information finns i [AZ SQL mi AD-admin Create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) och [set-AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) commands. 
    - Om du vill använda Azure Portal för SQL-hanterad instans för att ange Azure AD-administratören, är det en möjlig lösning att skapa en Azure AD-grupp. Lägg sedan till tjänstens huvud namn (Azure AD-program) i den här gruppen och ange den här gruppen som en Azure AD-administratör för SQL-hanterad instans.
    - Att ange tjänstens huvud namn (Azure AD-program) som Azure AD-administratör för SQL Database och Azure-Synapse stöds med hjälp av kommandona Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)och [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) .
- Det går inte att använda ett Azure AD-program med tjänstens huvud namn från en annan Azure AD-klient vid åtkomst till SQL Database eller SQL-hanterad instans som skapats i en annan klient. Ett tjänst objekt som är tilldelat till det här programmet måste vara från samma klient organisation som den logiska SQL-servern eller en hanterad instans.
- [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) -modulen eller senare krävs när du använder PowerShell för att konfigurera ett enskilt Azure AD-program som Azure AD-administratör för Azure SQL. Se till att du har uppgraderat till den senaste modulen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skapa Azure AD-användare med hjälp av Azure AD-program](authentication-aad-service-principal-tutorial.md)


