---
title: Azure Active Directory-autentisering
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med Azure SQL Database, Azure SQL-hanterad instans och Synapse SQL i Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454302"
---
# <a name="use-azure-active-directory-authentication"></a>Använd Azure Active Directory autentisering

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory (Azure AD)-autentisering är en mekanism för att ansluta till [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md)och [Synapse SQL i Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure AD.

> [!NOTE]
> Den här artikeln gäller för Azure SQL Database, SQL-hanterad instans och Azure Synapse Analytics.

Med Azure AD-autentisering kan du centralt hantera identiteterna för databasanvändare och andra Microsoft-tjänster på en central plats. Central ID-hantering ger en enda plats för hantering av databasanvändare och förenklar behörighetshanteringen. Här är några av fördelarna:

- Det är ett alternativ till att SQL Server autentisering.
- Det hjälper till att stoppa spridningen av användar identiteter mellan servrar.
- Det tillåter lösen ords rotation på en enda plats.
- Kunder kan hantera databas behörigheter med hjälp av externa (Azure AD) grupper.
- Det kan eliminera lagring av lösen ord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Azure AD-autentisering använder inneslutna databas användare för att autentisera identiteter på databas nivå.
- Azure AD stöder tokenbaserad autentisering för program som ansluter till SQL Database-och SQL-hanterad instans.
- Azure AD-autentisering stöder:
  - Endast Azure AD-moln identiteter.
  - Azure AD Hybrid identiteter som stöder:
    - Molnbaserad autentisering med två alternativ tillsammans med sömlös enkel inloggning (SSO) **genom strömnings** -och hash-autentisering för **lösen ord** .
    - Federerad autentisering.
  - Mer information om autentiseringsmetoder för Azure AD och vilken av dem som ska väljas finns i följande artikel:
    - [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal Authentication, som innehåller Multi-Factor Authentication. Multi-Factor Authentication innehåller stark autentisering med en rad enkla verifierings alternativ – telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande om mobilapp. Mer information finns i [SSMS-stöd för Azure AD Multi-Factor Authentication med Azure SQL Database, SQL-hanterad instans och Azure Synapse](authentication-mfa-ssms-overview.md)

- Azure AD har stöd för liknande anslutningar från SSDT (SQL Server Data Tools) som använder Active Directory Interactive Authentication. Mer information finns i [Azure Active Directory support i SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Det går inte att ansluta till en SQL Server-instans som körs på en virtuell Azure-dator (VM) med ett Azure Active Directory konto. Använd ett Active Directory-domänkonto i stället.  

Konfigurations stegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory autentisering.

1. Skapa och fyll i Azure AD.
2. Valfritt: associera eller ändra Active Directory som för närvarande är associerat med din Azure-prenumeration.
3. Skapa en Azure Active Directory administratör.
4. Konfigurera klient datorerna.
5. Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter.
6. Anslut till databasen med hjälp av Azure AD-identiteter.

> [!NOTE]
> Information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure SQL Database, SQL-hanterad instans och Synapse SQL i Azure Synapse Analytics finns i [Konfigurera Azure AD med Azure SQL Database](authentication-aad-configure.md).

## <a name="trust-architecture"></a>Förtroende arkitektur

- Endast moln delen i Azure AD, SQL Database, SQL-hanterad instans och Azure-Synapse betraktas som stöd för Azure AD-interna användar lösen ord.
- Använd Azure Active Directory autentiseringsuppgifter från en federerad eller hanterad domän som har kon figurer ATS för sömlös enkel inloggning för direkt inloggning och hash-autentisering för lösen ord, om du vill använda autentiseringsuppgifter för enkel inloggning i Windows (eller användare/lösen ord för Windows-autentiseringsuppgifter). Mer information finns i [Azure Active Directory sömlös enkel inloggning](../../active-directory/hybrid/how-to-connect-sso.md).
- För att stödja federerad autentisering (eller användare/lösen ord för Windows-autentiseringsuppgifter) krävs kommunikation med ADFS-block.

Mer information om Hybrid identiteter för Azure Active Directory, installation och synkronisering finns i följande artiklar:

- Password hash-autentisering – [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Direktautentisering – [Azure Active Directory direktautentisering](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federerad autentisering – [distribuera Active Directory Federation Services (AD FS) i Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) och [Azure AD Connect och Federation](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Ett exempel på en federerad autentisering med ADFS-infrastruktur (eller användare/lösen ord för Windows-autentiseringsuppgifter) finns i diagrammet nedan. Pilarna visar kommunikations vägar.

![AAD-auth-diagram][1]

Följande diagram visar de Federations-, förtroende-och värd relationer som gör det möjligt för en klient att ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrodd av databasen. Kund 1 kan representera ett Azure Active Directory med interna användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning, inklusive importerade användare, i det här exemplet kommer från en federerad Azure Active Directory med ADFS som synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värd prenumerationen är kopplad till Azure AD. Samma prenumeration måste användas för att skapa Azure SQL Database, SQL-hanterad instans eller Azure Synapse-resurser.

![prenumerations relation][2]

## <a name="administrator-structure"></a>Administratörs struktur

När du använder Azure AD-autentisering finns det två administratörs konton: den ursprungliga Azure SQL Database administratören och Azure AD-administratören. Samma koncept gäller för Azure-Synapse. Endast administratören som baseras på ett Azure AD-konto kan skapa den första Azure AD-inneslutna databas användaren i en användar databas. Inloggningen för Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett grupp konto kan den användas av alla grupp medlemmar, vilket möjliggör flera Azure AD-administratörer för-servern. Om du använder grupp kontot som administratör ökar hanterbarheten genom att du kan lägga till och ta bort grupp medlemmar centralt i Azure AD utan att ändra användare eller behörigheter i SQL Database eller Azure-Synapse. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörs struktur][3]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare måste du ha `ALTER ANY USER` behörighet i-databasen. `ALTER ANY USER`Behörigheten kan beviljas till alla databas användare. `ALTER ANY USER`Behörigheten hålls också av Server administratörs kontona och databas användare med `CONTROL ON DATABASE` `ALTER ON DATABASE` behörigheten eller för databasen, och av medlemmar i `db_owner` databas rollen.

Om du vill skapa en innesluten databas användare i Azure SQL Database, SQL-hanterad instans eller Azure-Synapse, måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första inneslutna databas användaren måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). Detta visas i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure-Synapse](authentication-aad-configure.md). Azure AD-autentisering är bara möjligt om Azure AD-administratören har skapats för Azure SQL Database, SQL-hanterad instans eller Azure-Synapse. Om Azure Active Directory administratören har tagits bort från servern kan befintliga Azure Active Directory användare som skapats tidigare i SQL Server inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Funktioner och begränsningar i Azure AD

- Följande Azure AD-medlemmar kan tillhandahållas för Azure SQL Database:

  - Ursprungliga medlemmar: en medlem som skapats i Azure AD i den hanterade domänen eller i en kund domän. Mer information finns i [lägga till ditt eget domän namn i Azure AD](../../active-directory/fundamentals/add-custom-domain.md).
  - Medlemmar i en Active Directory domän som är federerad med Azure Active Directory på en hanterad domän som kon figurer ATS för sömlös enkel inloggning med hash-autentisering direkt eller lösenordsautentisering. Mer information finns i [Microsoft Azure stöder nu Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) och [Azure Active Directory sömlös enkel inloggning](../../active-directory/hybrid/how-to-connect-sso.md).
  - Importerade medlemmar från andra Azure AD-medlemmar som är interna eller federerade domän medlemmar.
  - Active Directory grupper som skapats som säkerhets grupper.

- Azure AD-användare som är en del av en grupp som har `db_owner` Server rollen kan inte använda syntaxen **[skapa databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** mot Azure SQL Database och Azure-Synapse. Följande fel meddelande visas:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Ge `db_owner` rollen direkt till den enskilda Azure AD-användaren för att minimera problemet med att **skapa databasen med begränsade autentiseringsuppgifter** .

- Dessa system funktioner returnerar NULL-värden när de körs under Azure AD-huvud konton:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL-hanterad instans

- Azure AD server-Huvudkonton (inloggningar) och användare stöds för [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md).
- Att ställa in Azure AD server-Huvudkonton (inloggningar) som är mappade till en Azure AD-grupp som databas ägare stöds inte i [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md).
  - En förlängning av detta är att när en grupp läggs till som en del av `dbcreator` Server rollen kan användare från den här gruppen ansluta till SQL-hanterad instans och skapa nya databaser, men kommer inte att kunna komma åt databasen. Detta beror på att den nya databas ägaren är SA och inte Azure AD-användaren. Det här problemet visar inte om den enskilda användaren läggs till i `dbcreator` Server rollen.
- SQL Agent Management och jobb körning stöds för Azure AD server-Huvudkonton (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- Granskning av alla satser som rör Azure AD server-Huvudkonton (inloggningar) och autentiserings händelser stöds.
- Dedikerad administratörs anslutning för Azure AD server-Huvudkonton (inloggningar) som är medlemmar i Server rollen sysadmin stöds.
  - Stöds via SQLCMD-verktyget och SQL Server Management Studio.
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker-och DB-e-post kan konfigureras med hjälp av ett Azure AD server-huvudobjekt (inloggning).

## <a name="connect-by-using-azure-ad-identities"></a>Anslut med hjälp av Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med hjälp av Azure AD-identiteter:

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med Multi-Factor Authentication
- Använda autentisering med program-token

Följande autentiseringsmetoder stöds för Azure AD server-Huvudkonton (inloggningar):

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med Multi-Factor Authentication

### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för en server i SQL Database eller Azure-Synapse när som helst.
  - Genom att lägga till Azure AD server-huvudobjekt (inloggningar) för SQL-hanterad instans kan du skapa flera Azure AD server-huvudobjekt (inloggningar) som kan läggas till i `sysadmin` rollen.
- Endast en Azure AD-administratör för-servern kan ansluta till servern eller den hanterade instansen från ett Azure Active Directory konto. Active Directory-administratören kan konfigurera efterföljande Azure AD Database-användare.
- Vi rekommenderar att du ställer in tids gränsen för anslutningen på 30 sekunder.
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1 april 2016 eller senare) stöder Azure Active Directory autentisering. (Azure AD-autentisering stöds av **.NET Framework Data Provider för SQLServer**; minst version .NET Framework 4,6). Därför kan de senaste versionerna av dessa verktyg och data skikts program (DAC och BACPAC) använda Azure AD-autentisering.
- Från och med version 15.0.1, [SQLCMD-verktyget](/sql/tools/sqlcmd-utility) och BCP- [verktyget](/sql/tools/bcp-utility) stöds Active Directory interaktiv autentisering med Multi-Factor Authentication.
- SQL Server Data Tools för Visual Studio 2015 kräver minst april 2016-versionen av data verktyg (version 14.0.60311.1). Azure AD-användare visas för närvarande inte i SSDT Object Explorer. Som en lösning kan du Visa användare i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).
- [Microsoft JDBC-drivrutin 6,0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se även [Ange anslutnings egenskaper](/sql/connect/jdbc/setting-the-connection-properties).
- PolyBase kan inte autentiseras med Azure AD-autentisering.
- Azure AD-autentisering stöds för Azure SQL Database och Azure-Synapse med hjälp av bladet Azure Portal **Importera databas** och **Exportera databas** . Import och export med Azure AD-autentisering stöds också från ett PowerShell-kommando.
- Azure AD-autentisering stöds för SQL Database, SQL-hanterad instans och Azure-Synapse med hjälp av CLI. Mer information finns i [Konfigurera och hantera Azure AD-autentisering med SQL Database eller Azure-Synapse](authentication-aad-configure.md) och [SQL Server-AZ SQL Server](/cli/azure/sql/server).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar och fyller i en Azure AD-instans och sedan konfigurerar den med Azure SQL Database, SQL-hanterad instans eller Azure-Synapse finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database, SQL-hanterad instans eller Azure-Synapse](authentication-aad-configure.md).
- En själv studie kurs om hur du använder Azure AD server-huvudobjekt (inloggningar) med SQL-hanterad instans finns i [Azure AD server-Huvudkonton (inloggningar) med SQL-hanterad instans](../managed-instance/aad-security-configure-tutorial.md)
- En översikt över inloggningar, användare, databas roller och behörigheter i SQL Database finns i [inloggningar, användare, databas roller och behörigheter](logins-create-manage.md).
- Mer information om huvudkonton finns i [Huvudkonton](/sql/relational-databases/security/authentication-access/principals-database-engine).
- Mer information om databasroller finns [Databasroller](/sql/relational-databases/security/authentication-access/database-level-roles).
- En syntax för att skapa Azure AD server-huvudobjekt (inloggningar) för SQL-hanterad instans finns i  [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](firewall-configure.md).

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
