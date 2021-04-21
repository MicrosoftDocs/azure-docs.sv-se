---
title: säkerhetsöversikt
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig mer om säkerhet i Azure SQL Database och Azure SQL Managed Instance, inklusive hur den skiljer sig från SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: 084f9aae16cfbf495f05c90c8244b2b9b71cf624
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812992"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>En översikt över Azure SQL Database och SQL Managed Instance säkerhetsfunktioner
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln beskriver grunderna för att skydda datanivån för ett program [med hjälp av Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Säkerhetsstrategin som beskrivs följer den djupskyddsmetod som visas i bilden nedan och rör sig utifrån i:

![Diagram över djupskiktat skydd. Kunddata finns i lager för nätverkssäkerhet, åtkomsthantering samt hot- och informationsskydd.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Nätverkssäkerhet

Microsoft Azure SQL Database, SQL Managed Instance och Azure Synapse Analytics en relationsdatabastjänst för moln- och företagsprogram. För att skydda kunddata förhindrar brandväggar nätverksåtkomst till servern tills åtkomst uttryckligen beviljas baserat på IP-adress eller Trafikursprung för virtuellt Azure-nätverk.

### <a name="ip-firewall-rules"></a>IP-brandväggsregler

IP-brandväggsregler beviljar åtkomst till databaser baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i [Översikt över Azure SQL Database och Azure Synapse Analytics brandväggsregler](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk

[Tjänstslutpunkter för virtuellt](../../virtual-network/virtual-network-service-endpoints-overview.md) nätverk utökar anslutningen till det virtuella nätverket via Azure-stamnätet och gör det möjligt för Azure SQL Database att identifiera det virtuella nätverksundernät som trafiken kommer från. Använd SQL-tjänsttaggarna för att tillåta [](../../virtual-network/network-security-groups-overview.md) utgående trafik genom nätverkssäkerhetsgrupper för att tillåta att trafik når Azure SQL Database nätverk.

[Regler för virtuella](vnet-service-endpoint-rule-overview.md) nätverk gör Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.

> [!NOTE]
> Åtkomstkontroll med brandväggsregler *gäller inte* för **SQL Managed Instance**. Mer information om nätverkskonfigurationen som behövs finns i [Ansluta till en hanterad instans](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Åtkomsthantering

> [!IMPORTANT]
> Hantering av databaser och servrar i Azure styrs av portalanvändarkontots rolltilldelningar. Mer information om den här artikeln finns [i Rollbaserad åtkomstkontroll i Azure i Azure Portal](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Autentisering

Autentisering är processen att bevisa att användaren är den som han eller hon hävdar att han eller hon är. Azure SQL Database och SQL Managed Instance två typer av autentisering:

- **SQL-autentisering:**

    SQL-autentisering syftar på autentisering av en användare vid anslutning till Azure SQL Database eller Azure SQL Managed Instance med användarnamn och lösenord. En **serveradministratörsinloggning** med ett användarnamn och lösenord måste anges när servern skapas. Med dessa autentiseringsuppgifter kan **en serveradministratör** autentisera till valfri databas på den servern eller instansen som databasägare. Därefter kan ytterligare SQL-inloggningar och användare skapas av serveradministratören, vilket gör det möjligt för användare att ansluta med användarnamn och lösenord.

- **Azure Active Directory autentisering:**

    Azure Active Directory autentisering är en mekanism för [att ansluta till Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (Azure AD). Med Azure AD-autentisering kan administratörer centralt hantera identiteter och behörigheter för databasanvändare tillsammans med andra Azure-tjänster på en central plats. Detta inkluderar minimering av lösenordslagring och möjliggör centraliserade principer för lösenordsrotation.

     En serveradministratör med namnet **Active Directory-administratör måste** skapas för att använda Azure AD-autentisering med SQL Database. Mer information finns i [Ansluta till SQL Database med hjälp av Azure Active Directory autentisering](authentication-aad-overview.md). Azure AD-autentisering stöder både hanterade och federerade konton. De federerade kontona stöder Windows-användare och -grupper för en kunddomän som är federerad med Azure AD.

    Ytterligare alternativ för Azure AD-autentisering är [Active Directory Universal Authentication för SQL Server Management Studio-anslutningar,](authentication-mfa-ssms-overview.md) inklusive [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) och [villkorsstyrd åtkomst.](conditional-access-configure.md)

> [!IMPORTANT]
> Hantering av databaser och servrar i Azure styrs av ditt portalanvändarkontos rolltilldelningar. Mer information om den här artikeln finns i [Rollbaserad åtkomstkontroll i Azure i Azure Portal](../../role-based-access-control/overview.md). Åtkomstkontroll med brandväggsregler *gäller inte* för **SQL Managed Instance**. Mer information om [nätverkskonfigurationen som behövs finns i](../managed-instance/connect-application-instance.md) följande artikel om hur du ansluter till en hanterad instans.

## <a name="authorization"></a>Auktorisering

Auktorisering avser de behörigheter som tilldelats en användare i en databas i Azure SQL Database eller Azure SQL Managed Instance och avgör vad användaren får göra. Behörigheter styrs genom att [](/sql/relational-databases/security/authentication-access/database-level-roles) lägga till användarkonton till databasroller och tilldela behörigheter på databasnivå till dessa roller eller genom att ge användaren vissa [behörigheter på objektnivå.](/sql/relational-databases/security/permissions-database-engine) Mer information finns i [Inloggningar och användare](logins-create-manage.md)

Bästa praxis är att skapa anpassade roller när det behövs. Lägg till användare i rollen med den minsta behörighet som krävs för att göra sin jobbfunktion. Tilldela inte behörigheter direkt till användare. Serveradministratörskontot är medlem i den inbyggda db_owner, som har omfattande behörigheter och endast bör beviljas till få användare med administrativa uppgifter. För program använder du [EXECUTE AS för](/sql/t-sql/statements/execute-as-clause-transact-sql) att ange körningskontexten för den anropade modulen eller använda [programroller](/sql/relational-databases/security/authentication-access/application-roles) med begränsade behörigheter. Den här metoden säkerställer att programmet som ansluter till databasen har den minsta behörighet som krävs av programmet. Genom att följa dessa metodtips främjas även uppdelning av uppgifter.

### <a name="row-level-security"></a>Säkerhet på radnivå

Row-Level Security ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för den användare som kör en fråga (till exempel gruppmedlemskap eller körningskontext). Row-Level Security kan också användas för att implementera anpassade etikettbaserade säkerhetsbegrepp. Mer information finns i [Säkerhet på radnivå.](/sql/relational-databases/security/row-level-security)

![Diagram som visar Row-Level Security skyddar enskilda rader i en SQL-databas från åtkomst av användare via en klientapp.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Hotskydd

SQL Database och SQL Managed Instance kunddata genom att tillhandahålla funktioner för granskning och hotidentifiering.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL-granskning i Azure Monitor loggar och Event Hubs

SQL Database och SQL Managed Instance-granskning spårar databasaktiviteter och hjälper till att upprätthålla efterlevnad med säkerhetsstandarder genom att registrera databashändelser i en granskningslogg i ett kundägt Azure Storage-konto. Granskning gör att användare kan övervaka pågående databasaktiviteter, samt analysera och undersöka historiska aktiviteter för att identifiera potentiella hot eller misstänkt missbruk och säkerhetsöverträdelser. Mer information finns i Kom igång med [SQL Database Auditing](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Advanced Threat Protection analyserar dina loggar för att identifiera onormalt beteende och potentiellt skadliga försök att komma åt eller utnyttja databaser. Aviseringar skapas för misstänkta aktiviteter som SQL- injection, potentiell dataintrång och brute force-attacker eller för avvikelser i åtkomstmönster för att fånga behörighetseskaleringar och intrång i autentiseringsuppgifter. Aviseringar visas från  [Azure Security Center](https://azure.microsoft.com/services/security-center/), där information om misstänkta aktiviteter tillhandahålls och rekommendationer för ytterligare undersökning ges tillsammans med åtgärder för att minimera hotet. Advanced Threat Protection kan aktiveras per server mot en extra avgift. Mer information finns i [Kom igång med SQL Database Advanced Threat Protection](threat-detection-configure.md).

![Diagram som visar SQL Threat Detection som övervakar åtkomsten till SQL-databasen för en webbapp från en extern angripare och illvillig insider.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Informationsskydd och kryptering

### <a name="transport-layer-security-encryption-in-transit"></a>Transport Layer Security (kryptering under överföring)

SQL Database, SQL Managed Instance och Azure Synapse Analytics kunddata genom att kryptera data i rörelse [med Transport Layer Security (TLS).](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

SQL Database, SQL Managed Instance och Azure Synapse Analytics alltid fram kryptering (SSL/TLS) för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen **för Encrypt** eller **TrustServerCertificate** i anslutningssträngen.

Vi rekommenderar att du i anslutningssträngen som används av programmet anger en krypterad anslutning och inte litar _**på**_ servercertifikatet. Detta tvingar ditt program att verifiera servercertifikatet och förhindrar därför att programmet är sårbart för man i mittentypens attacker.

När du till exempel använder ADO.NET-drivrutinen sker detta via  **Encrypt=True** och **TrustServerCertificate=False**. Om du hämtar anslutningssträngen från Azure Portal har den rätt inställningar.

> [!IMPORTANT]
> Observera att vissa drivrutiner som inte kommer från Microsoft kanske inte använder TLS som standard eller förlitar sig på en äldre version av TLS (<1.2) för att fungera. I det här fallet kan du fortfarande ansluta till databasen på servern. Vi rekommenderar dock att du utvärderar säkerhetsriskerna med att tillåta att sådana drivrutiner och program ansluter till SQL Database, särskilt om du lagrar känsliga data.
>
> Mer information om TLS och anslutning finns i [TLS-överväganden](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>transparent datakryptering (kryptering i vila)

[Transparent datakryptering (TDE) för SQL Database, SQL Managed Instance](transparent-data-encryption-tde-overview.md) och Azure Synapse Analytics lägger till ett säkerhetslager för att skydda vilodata från obehörig eller offlineåtkomst till rådatafiler eller säkerhetskopior. Vanliga scenarier är stöld av datacenter eller oskyddad försäljning av maskinvara eller media, till exempel diskar och säkerhetskopieringsband.TDE krypterar hela databasen med hjälp av en AES-krypteringsalgoritm, som inte kräver att programutvecklare gör några ändringar i befintliga program.

I Azure krypteras alla nyligen skapade databaser som standard och databaskrypteringsnyckeln skyddas av ett inbyggt servercertifikat.  Certifikatunderhåll och -rotation hanteras av tjänsten och kräver inga indata från användaren. Kunder som föredrar att ta kontroll över krypteringsnycklarna kan hantera nycklarna i [Azure Key Vault](../../key-vault/general/security-features.md).

### <a name="key-management-with-azure-key-vault"></a>Nyckelhantering med Azure Key Vault

[Bring Your Own Key](transparent-data-encryption-byok-overview.md) (BYOK) stöd för [transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) gör att kunder kan bli ägare till nyckelhantering och rotering med [hjälp av Azure Key Vault](../../key-vault/general/security-features.md), Azures molnbaserade externa nyckelhanteringssystem. Om databasens åtkomst till nyckelvalvet återkallas kan en databas inte dekrypteras och läsas in i minnet. Azure Key Vault en central plattform för nyckelhantering, använder noggrant övervakade maskinvarusäkerhetsmoduler (HSM) och möjliggör uppdelning av uppgifter mellan hantering av nycklar och data för att uppfylla säkerhetskraven.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (kryptering används)

![Diagram som visar grunderna i Always Encrypted funktion. En SQL-databas med ett lås kan endast nås av en app som innehåller en nyckel.](./media/security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) är en funktion som har utformats för att skydda känsliga data som lagras i specifika databaskolumner från åtkomst (till exempel kreditkortsnummer, nationella identifieringsnummer eller data som behöver _känna_ till dem). Detta omfattar databasadministratörer eller andra privilegierade användare som har behörighet att komma åt databasen för att utföra hanteringsuppgifter, men som inte har något affärs behov av att komma åt specifika data i de krypterade kolumnerna. Data krypteras alltid, vilket innebär att krypterade data endast dekrypteras för bearbetning av klientprogram med åtkomst till krypteringsnyckeln. Krypteringsnyckeln exponeras aldrig för SQL Database eller SQL Managed Instance och kan lagras antingen i [Windows-certifikatarkivet](always-encrypted-certificate-store-configure.md) eller [i Azure Key Vault](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

![Diagram som visar dynamisk datamaskering. En företagsapp skickar data till en SQL-databas som maskerar data innan de skickas tillbaka till företagsappen.](./media/security-overview/azure-database-ddm.png)

Med dynamisk datamaskering begränsas exponeringen av känsliga data genom att de maskeras för icke-privilegierade användare. Dynamisk datamaskning identifierar automatiskt potentiellt känsliga data i Azure SQL Database och SQL Managed Instance och ger användbara rekommendationer för att maskera dessa fält, med minimal påverkan på programlagret. Det fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. Mer information finns i [Kom igång med att SQL Database och SQL Managed Instance dynamisk datamaskering.](dynamic-data-masking-overview.md)

## <a name="security-management"></a>Säkerhetshantering

### <a name="vulnerability-assessment"></a>Sårbarhetsbedömning

[Sårbarhetsbedömning](sql-vulnerability-assessment.md) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa till att åtgärda potentiella säkerhetsrisker i databasen med målet att proaktivt förbättra den övergripande databassäkerheten. Sårbarhetsbedömning (VA) är en del av Azure Defender för SQL,vilket är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Sårbarhetsbedömning kan nås och hanteras via den centrala Azure Defender för SQL-portalen.

### <a name="data-discovery-and-classification"></a>Dataidentifiering och -klassificering

Dataidentifiering och -klassificering (för närvarande i förhandsversion) innehåller avancerade funktioner som är inbyggda i Azure SQL Database och SQL Managed Instance för att identifiera, klassificera, märka och skydda känsliga data i dina databaser. Att identifiera och klassificera känsliga data (företag/ekonomi, sjukvård, personliga data osv.) kan spela en viktig roll i organisationens informationsskyddsstatistik. Tjänsten kan fungera som infrastruktur inom följande områden:

- Olika säkerhetsscenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Kontrollera åtkomst till och härda säkerheten för databaser som innehåller mycket känsliga data.
- Hjälp med att uppfylla standarder för datasekretess och efterlevnadsregler.

Mer information finns i [Kom igång med dataidentifiering och -klassificering.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Efterlevnad

Förutom ovanstående funktioner och funktioner som kan hjälpa ditt program att uppfylla olika säkerhetskrav, deltar Azure SQL Database även i regelbundna granskningar och har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure Säkerhetscenter](https://www.microsoft.com/trust-center/compliance/compliance-overview) där du hittar den senaste listan över SQL Database efterlevnadscertifieringar.

## <a name="next-steps"></a>Nästa steg

- En diskussion om hur du använder inloggningar, användarkonton, databasroller och behörigheter i SQL Database och SQL Managed Instance finns i [Hantera inloggningar och användarkonton](logins-create-manage.md).
- En diskussion om databasgranskning finns i [granska](../../azure-sql/database/auditing-overview.md).
- En diskussion om hotidentifiering finns i [hotidentifiering.](threat-detection-configure.md)