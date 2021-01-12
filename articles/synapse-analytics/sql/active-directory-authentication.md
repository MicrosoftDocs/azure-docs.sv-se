---
title: Azure Active Directory
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL Database, hanterad instans och Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 962e0e734abd73f5a66f9b9ee6067155dd839e5d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118293"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Använd Azure Active Directory autentisering för autentisering med Synapse SQL

Azure Active Directory autentisering är en mekanism som ansluter till [Azure Synapse Analytics](../overview-faq.md) med hjälp av identiteter i Azure Active Directory (Azure AD).

Med Azure AD-autentisering kan du centralt hantera användar identiteter som har åtkomst till Azure-Synapse för att förenkla behörighets hanteringen. Här är några av fördelarna:

- Det ger ett alternativ till vanlig användar namn och lösenordsautentisering.
- Hjälper till att stoppa spridningen av användar identiteter mellan servrar.
- Tillåter lösen ords rotation på en enda plats.
- Kunder kan hantera behörigheter med hjälp av externa (Azure AD) grupper.
- Det kan eliminera lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Azure AD stöder tokenbaserad autentisering för program som ansluter till Azure Synapse.
- Azure AD-autentisering stöder ADFS (Domain Federation) eller autentisering med intern användare/lösenordsautentisering för en lokal Azure Active Directory utan domän-synkronisering.
- Azure AD har stöd för anslutning från SQL Server Management Studio som använder Active Directory Universal Authentication, inklusive Multi-Factor Authentication (MFA).  MFA inkluderar stark autentisering med en rad enkla verifierings alternativ, till exempel telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande om mobilapp. Mer information finns i [SSMS-stöd för Azure AD MFA med SYNAPSE SQL](mfa-authentication.md).
- Azure AD har stöd för liknande anslutningar från SSDT (SQL Server Data Tools) som använder Active Directory Interactive Authentication. Läs mer i [Stöd för Azure Active Directory i SSDT (SQL Server Data Tools)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Konfigurations stegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory autentisering.

1. Skapa och fyll i Azure AD.
2. Skapa en Azure Active Directory identitet
3. Tilldela roll till skapande av Azure Active Directory identitet i Synapse-arbetsytan
4. Anslut till Synapse Studio med hjälp av Azure AD-identiteter.

## <a name="azure-ad-pass-through-in-azure-synapse-analytics"></a>Azure AD-vidarekoppling i Azure Synapse Analytics

Med Azure Synapse Analytics kan du komma åt data i data Lake med din Azure Active Directory identitet.

Genom att definiera åtkomst rättigheter för filer och data som respekteras i olika data motorer kan du förenkla dina data Lake-lösningar genom att ha en enda plats där behörigheterna definieras i stället för att behöva definiera dem på flera platser.

## <a name="trust-architecture"></a>Förtroende arkitektur

I följande diagram i hög grad sammanfattas lösnings arkitekturen för att använda Azure AD-autentisering med Synapse SQL. För att ge stöd åt Azure AD Native User Password anses bara moln delen och Azure AD/Synapse Synapse SQL. För att stödja federerad autentisering (eller användare/lösen ord för Windows-autentiseringsuppgifter) krävs kommunikation med ADFS-block. Pilarna visar kommunikations vägar.

![Azure AD auth-diagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

Följande diagram visar de Federations-, förtroende-och värd relationer som gör det möjligt för en klient att ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrodd av databasen. 

Kund 1 kan representera ett Azure Active Directory med interna användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning inklusive importerade användare. i det här exemplet kommer från en federerad Azure Active Directory med ADFS synkroniseras med Azure Active Directory. 

Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värd prenumerationen är kopplad till Azure AD. Samma prenumeration måste användas för att skapa SQL Server som är värd för Azure SQL Database eller dedikerad SQL-pool.

![prenumerations relation](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Administratörs struktur

När du använder Azure AD-autentisering finns det två administratörs konton för Synapse-SQL; den ursprungliga SQL Server administratören och Azure AD-administratören. Endast administratören som baseras på ett Azure AD-konto kan skapa den första Azure AD-inneslutna databas användaren i en användar databas. 

Inloggningen för Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett grupp konto kan den användas av alla grupp medlemmar, vilket möjliggör flera Azure AD-administratörer för Synapse SQL-instansen. 

Om du använder grupp kontot som administratör ökar hanterbarheten genom att du kan lägga till och ta bort grupp medlemmar centralt i Azure AD utan att ändra användare eller behörigheter i Azure Synapse Analytics-arbetsytan. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörs struktur](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare måste du ha `ALTER ANY USER` behörighet i-databasen. `ALTER ANY USER`Behörigheten kan beviljas till alla databas användare. `ALTER ANY USER`Behörigheten hålls också av Server administratörs kontona och databas användare med `CONTROL ON DATABASE` `ALTER ON DATABASE` behörigheten eller för databasen, och av medlemmar i `db_owner` databas rollen.

Om du vill skapa en innesluten databas användare i Synapse SQL måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första inneslutna databas användaren måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). 

Alla Azure AD-autentiseringar är bara möjliga om Azure AD-administratören har skapats för Synapse SQL. Om Azure Active Directory administratören har tagits bort från servern kan befintliga Azure Active Directory användare som skapats tidigare i Synapse SQL inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.
 
## <a name="azure-ad-features-and-limitations"></a>Funktioner och begränsningar i Azure AD

- Följande Azure AD-medlemmar kan tillhandahållas i Synapse SQL:

  - Ursprungliga medlemmar: en medlem som skapats i Azure AD i den hanterade domänen eller i en kund domän. Mer information finns i [lägga till ditt eget domän namn i Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Federerade domän medlemmar: en medlem som skapats i Azure AD med en federerad domän. Mer information finns i [Microsoft Azure stöder nu Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importerade medlemmar från andra Azure-annonser som är interna eller federerade domän medlemmar.
  - Active Directory grupper som skapats som säkerhets grupper.

- Azure AD-användare som är en del av en grupp som har `db_owner` Server rollen kan inte använda syntaxen **[skapa databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** mot Synapse SQL. Följande fel meddelande visas:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Ge `db_owner` rollen direkt till den enskilda Azure AD-användaren för att minimera problemet med att **skapa databasen med begränsade autentiseringsuppgifter** .

- Dessa system funktioner returnerar NULL-värden när de körs under Azure AD-huvud konton:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Anslut med hjälp av Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med hjälp av Azure AD-identiteter:

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med MFA
- Använda autentisering med program-token

Följande autentiseringsmetoder stöds för Azure AD server-Huvudkonton (inloggningar):

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med MFA

### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för Synapse SQL-pool när som helst.
  - Genom att lägga till Azure AD server-huvudobjekt (inloggningar) för Synapse SQL kan du skapa flera Azure AD server-huvudobjekt (inloggningar) som kan läggas till i `sysadmin` rollen.
- Endast en Azure AD-administratör för Synapse SQL kan ansluta till Synapse SQL med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera efterföljande Azure AD Database-användare.
- Vi rekommenderar att du ställer in tids gränsen för anslutningen på 30 sekunder.
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1 april 2016 eller senare) stöder Azure Active Directory autentisering. (Azure AD-autentisering stöds av **.NET Framework Data Provider för SQLServer**; minst version .NET Framework 4,6). De nyaste versionerna av dessa verktyg och data skikts program (DAC och. BACPAC) kan använda Azure AD-autentisering.
- Från och med version 15.0.1, [SQLCMD-verktyget](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) och BCP- [verktyget](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) stöds Active Directory interaktiv autentisering med MFA.
- SQL Server Data Tools för Visual Studio 2015 kräver minst april 2016-versionen av data verktyg (version 14.0.60311.1). Azure AD-användare visas för närvarande inte i SSDT Object Explorer. Som en lösning kan du Visa användare i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- [Microsoft JDBC-drivrutin 6,0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se även [Ange anslutnings egenskaper](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Nästa steg

- En översikt över åtkomst och kontroll i Synapse SQL finns i [SYNAPSE SQL Access Control](../security/synapse-workspace-access-control-overview.md).
- Mer information om huvudkonton finns i [Huvudkonton](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Mer information om databasroller finns [Databasroller](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

