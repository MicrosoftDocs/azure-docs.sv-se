---
title: Autentisering för dedikerad SQL-pool (tidigare SQL DW)
description: Lär dig hur du autentiserar till dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics genom att använda Azure Active Directory (Azure AD) eller SQL Server autentisering.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 80bc9f6fc6af94ba2a5ade77cc1d53b3fc29f1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685352"
---
# <a name="authenticate-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Autentisera till dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

Lär dig hur du autentiserar till dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse med hjälp av Azure Active Directory (Azure AD) eller SQL Server autentisering.

Om du vill ansluta till en dedikerad SQL-pool (tidigare SQL DW) måste du skicka säkerhets uppgifter för autentisering. När du upprättar en anslutning konfigureras vissa anslutnings inställningar som en del av upprättandet av sessionen.  

Mer information om säkerhet och hur du aktiverar anslutningar till din dedikerade SQL-pool (tidigare SQL DW) finns i [skydda en databas dokumentation](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>SQL-autentisering

Om du vill ansluta till en dedikerad SQL-pool (tidigare SQL DW) måste du ange följande information:

* Fullständigt kvalificerat Server namn
* Ange SQL-autentisering
* Användarnamn
* Lösenord
* Standard databas (valfritt)

Som standard ansluter anslutningen till *huvud* databasen och inte till din användar databas. Om du vill ansluta till din användar databas kan du välja att göra något av följande:

* Ange standard databasen när du registrerar servern med SQL Server Object Explorer i SSDT, SSMS eller i din program anslutnings sträng. Inkludera till exempel parametern InitialCatalog för en ODBC-anslutning.
* Markera användar databasen innan du skapar en session i SSDT.

> [!NOTE]
> Transact-SQL-instruktionen **använder-databasen;** stöds inte för att ändra databasen för en anslutning. Vägledning för att ansluta till en SQL-pool med SSDT finns i artikeln [fråga med Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) autentisering är en mekanism för att ansluta till SQL-poolen med hjälp av identiteter i Azure Active Directory (Azure AD). Med Azure Active Directory autentisering kan du centralt hantera identiteter för databas användare och andra Microsoft-tjänster på en central plats. Hantering av centrala ID ger en enda plats för att hantera dedikerad SQL-pool (tidigare SQL DW) användare och fören klar behörighets hanteringen.

### <a name="benefits"></a>Fördelar

Azure Active Directory fördelarna är:

* Är ett alternativ till att SQL Server autentisering.
* Hjälper till att stoppa spridningen av användar identiteter mellan servrar.
* Lösenord kan roteras på ett och samma ställe
* Hantera databas behörigheter med hjälp av externa (Azure AD) grupper.
* Eliminerar lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Använder inneslutna databas användare för att autentisera identiteter på databas nivå.
* Stöder tokenbaserad autentisering för program som ansluter till SQL-poolen.
* Stöder Multi-Factor Authentication via Active Directory Universal Authentication för olika verktyg, inklusive [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och [SQL Server Data Tools](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> Azure Active Directory är fortfarande relativt nytt och har vissa begränsningar. För att säkerställa att Azure Active Directory passar bra för din miljö kan du läsa mer i [Azure AD-funktioner och begränsningar](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), särskilt ytterligare överväganden.

### <a name="configuration-steps"></a>Konfigurationssteg

Följ dessa steg om du vill konfigurera Azure Active Directory autentisering.

1. Skapa och fylla i en Azure Active Directory
2. Valfritt: associera eller ändra Active Directory som för närvarande är associerat med din Azure-prenumeration
3. Skapa en Azure Active Directory administratör för Azure-Synapse
4. Konfigurera klient datorerna
5. Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter
6. Ansluta till SQL-poolen med hjälp av Azure AD-identiteter

Azure Active Directory användare visas för närvarande inte i SSDT Object Explorer. Som en lösning kan du Visa användare i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="find-the-details"></a>Hitta informationen

* Stegen för att konfigurera och använda Azure Active Directory autentisering är nästan identiska för Azure SQL Database och Synapse SQL i Azure Synapse. Följ de detaljerade stegen i avsnittet [ansluta till SQL Database eller SQL-pool med Azure Active Directory autentisering](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Skapa anpassade databas roller och Lägg till användare i rollerna. Ge sedan detaljerade behörigheter till rollerna. Mer information finns i [komma igång med databas motor behörigheter](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Nästa steg

Information om hur du börjar fråga med Visual Studio och andra program finns i [fråga med Visual Studio](sql-data-warehouse-query-visual-studio.md).
