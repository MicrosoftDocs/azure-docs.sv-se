---
title: Katalog läsar roll i Azure Active Directory för Azure SQL
description: Azure AD-program (tjänstens huvud namn) stöder generering av Azure AD-användare i Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: a517a4b14a64be2b9bf12270eef3d08e14d8f3c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556329"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Katalog läsar roll i Azure Active Directory för Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Den här funktionen i den här artikeln finns i **offentlig för hands version**.

Azure Active Directory (Azure AD) har introducerat [med moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)](../../active-directory/users-groups-roles/roles-groups-concept.md). Detta gör att Azure AD-roller kan tilldelas till grupper.

När du aktiverar en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) för Azure SQL Database, Azure SQL-hanterad instans eller Azure Synapse Analytics, måste rollen Azure AD [**Directory Reader**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) tilldelas identiteten för att tillåta Läs åtkomst till [Azure AD-Graph API](../../active-directory/develop/active-directory-graph-api.md). Den hanterade identiteten för SQL Database och Azure-Synapse kallas för Server identiteten. Den hanterade identiteten för SQL-hanterad instans kallas för den hanterade instans identiteten och tilldelas automatiskt när instansen skapas. Mer information om hur du tilldelar en server identitet till SQL Database eller Azure-Synapse finns i [Aktivera tjänstens huvud namn för att skapa Azure AD-användare](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Rollen **katalog läsare** är nödvändig för att:

- Skapa Azure AD-inloggningar för SQL-hanterad instans
- Personifiera Azure AD-användare i Azure SQL
- Migrera SQL Server användare som använder Windows-autentisering till SQL-hanterad instans med Azure AD-autentisering (med kommandot [Alter User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Ändra Azure AD-administratören för SQL-hanterad instans
- Tillåt [tjänst huvud namn (program)](authentication-aad-service-principal.md) för att skapa Azure AD-användare i Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Tilldela rollen katalog läsare

För att tilldela rollen [**katalog läsare**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) till en identitet krävs en användare med administratörs behörighet som [Global administratör](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) eller [privilegie rad roll](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) . Användare som ofta hanterar eller distribuerar SQL Database, SQL-hanterad instans eller Azure-Synapse kanske inte har åtkomst till dessa privilegierade roller. Detta kan ofta orsaka komplikationer för användare som skapar oplanerade Azure SQL-resurser, eller behöver hjälp från privilegierade roll medlemmar som ofta är otillgängliga i stora organisationer.

För SQL-hanterad instans måste rollen **katalog läsare** tilldelas till en hanterad instans identitet innan du kan [Konfigurera en Azure AD-administratör för den hanterade instansen](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Det krävs inte att tilldela rollen **katalog läsare** till Server identiteten för SQL Database eller Azure-Synapse när du konfigurerar en Azure AD-administratör för den logiska servern. Men om du vill aktivera en Azure AD-objekts skapande i SQL Database eller Azure-Synapse för ett Azure AD-program, krävs rollen **katalog läsare** . Om rollen inte är tilldelad till den logiska SQL-serverns identitet kommer det inte att gå att skapa Azure AD-användare i Azure SQL. Mer information finns i [Azure Active Directory tjänstens huvud namn med Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Bevilja katalog läsare rollen till en Azure AD-grupp

För närvarande kan du skapa en Azure AD-grupp och tilldela [**katalog läsar**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) behörighet till gruppen för närvarande [i en](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) **offentlig för hands version**. [Privileged Role Administrator](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) Detta ger åtkomst till Azure AD-Graph API för medlemmar i den här gruppen. Dessutom tillåts Azure AD-användare som är ägare av den här gruppen att tilldela nya medlemmar för den här gruppen, inklusive identiteter för logiska Azure SQL-servrar.

Den här lösningen kräver fortfarande en användare med hög behörighet (global administratör eller privilegie rad roll administratör) för att skapa en grupp och tilldela användare en gång, men Azure AD-gruppägare kommer att kunna tilldela ytterligare medlemmar som går framåt. Detta eliminerar behovet av att involvera en användare med hög behörighet i framtiden för att konfigurera alla SQL-databaser, SQL-hanterade instanser eller Azure Synapse-servrar i sin Azure AD-klient.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: tilldela katalog läsare rollen till en Azure AD-grupp och hantera roll tilldelningar](authentication-aad-directory-readers-role-tutorial.md)
