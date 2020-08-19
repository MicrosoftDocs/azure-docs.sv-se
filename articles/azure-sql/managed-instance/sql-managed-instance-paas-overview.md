---
title: Vad är en hanterad Azure SQL-instans?
description: Lär dig mer om hur Azure SQL Managed instance ger nära 100% kompatibilitet med den senaste SQL Server (Enterprise Edition) databas motor
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 08/14/2020
ms.openlocfilehash: 72d0745e5a885ddbc57a9a849a7537a40e0b1215
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590072"
---
# <a name="what-is-azure-sql-managed-instance"></a>Vad är en hanterad Azure SQL-instans?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den hanterade Azure SQL-instansen är den intelligenta, skalbara moln databas tjänsten som kombinerar det bredaste SQL Servers databas motorns kompatibilitet med alla fördelar med en fullständigt hanterad och Evergreen plattform som en tjänst. SQL-hanterad instans har nära 100% kompatibilitet med den senaste SQL Server (Enterprise Edition) databas motorn, som tillhandahåller en ursprunglig [virtuell nätverks implementering (VNet)](../../virtual-network/virtual-networks-overview.md) som behandlar vanliga säkerhets problem och en [affärs modell](https://azure.microsoft.com/pricing/details/sql-database/) som är fördelaktig för befintliga SQL Server kunder. SQL-hanterad instans gör att befintliga SQL Server kunder kan lyfta och byta sina lokala program till molnet med minimala program-och databas ändringar. Samtidigt bevarar SQL-hanterad instans alla PaaS-funktioner (automatisk uppdatering och versions uppdateringar, [automatiska säkerhets kopieringar](../database/automated-backups-overview.md), [hög tillgänglighet](../database/high-availability-sla.md)) som drastiskt minskar hanterings kostnader och TCO.

> [!IMPORTANT]
> En lista över regioner där SQL-hanterad instans är tillgänglig finns i [regioner som stöds](resource-limits.md#supported-regions).

Följande diagram beskriver viktiga funktioner i SQL-hanterad instans:

![Huvudfunktioner](./media/sql-managed-instance-paas-overview/key-features.png)

Den hanterade Azure SQL-instansen är utformad för kunder som vill migrera ett stort antal appar från en lokal eller IaaS, självbyggd eller ISV-baserad miljö till en helt hanterad PaaS-moln miljö med så liten migrering som möjligt. Med den helt automatiserade [tjänsten Azure Data Migration](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)kan kunder lyfta och byta sin befintliga SQL Server instans till SQL-hanterad instans, vilket ger kompatibilitet med SQL Server och fullständig isolering av kund instanser med inbyggt VNet-stöd.  Med Software Assurance kan du byta ut dina befintliga licenser för rabatterade priser på SQL-hanterad instans med hjälp av [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL-hanterad instans är det bästa migrerings målet i molnet för SQL Server instanser som kräver hög säkerhet och en omfattande programmerings yta.

## <a name="key-features-and-capabilities"></a>Viktiga funktioner och funktioner

SQL-hanterad instans kombinerar de bästa funktionerna som finns tillgängliga både i Azure SQL Database och i SQL Server databas motorn.

> [!IMPORTANT]
> SQL-hanterad instans körs med alla funktioner i den senaste versionen av SQL Server, inklusive online-åtgärder, automatiska plan korrigeringar och andra förbättringar i företags prestanda. En jämförelse av funktionerna som är tillgängliga förklaras i [funktions jämförelsen: Azure SQL-hanterad instans jämfört med SQL Server](../database/features-comparison.md).

| **PaaS-förmåner** | **Verksamhetskontinuitet** |
| --- | --- |
|Ingen maskin varu inköp och-hantering <br>Ingen hanterings kostnad för hantering av underliggande infrastruktur <br>Snabb etablering och tjänst skalning <br>Automatiserad uppdatering och versions uppgradering <br>Integrering med andra PaaS Data Services |SLA för 99,99% drift tid  <br>Inbyggd [hög tillgänglighet](../database/high-availability-sla.md) <br>Data som skyddas med [automatiserade säkerhets kopieringar](../database/automated-backups-overview.md) <br>Kvarhållning av kundens bevarande period för säkerhets kopiering <br>Användarinitierad [säkerhets kopieringar](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>Funktion [för återställning av databasens](../database/recovery-using-backups.md#point-in-time-restore) tidpunkt |
|**Säkerhet och efterlevnad** | **Hantering**|
|Isolerad miljö ([VNet-integrering](connectivity-architecture-overview.md), enskild klient tjänst, dedikerad beräkning och lagring) <br>[Transparent data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory (Azure AD)-autentisering](../database/authentication-aad-overview.md), stöd för enkel inloggning <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD server-Huvudkonton (inloggningar)</a>  <br>Följer standarderna för efterlevnad på samma sätt som Azure SQL Database <br>[SQL-granskning](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager-API för automatisering av tjänst etablering och skalning <br>Azure Portal funktioner för manuell tjänst etablering och skalning <br>Datamigreringstjänst

> [!IMPORTANT]
> En hanterad Azure SQL-instans har certifierats mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Compliance-erbjudanden](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), där du hittar den senaste listan över certifieringar för SQL Managed instance-kompatibilitet, som visas under **SQL Database**.

Huvud funktionerna i SQL-hanterad instans visas i följande tabell:

|Funktion | Beskrivning|
|---|---|
| SQL Server version/build | SQL Server databas motor (senaste stabila) |
| Hanterade automatiserade säkerhets kopieringar | Yes |
| Inbyggd instans och databas övervakning och mått | Yes |
| Automatisk uppdatering av program vara | Yes |
| De senaste databas motor funktionerna | Yes |
| Antal datafiler (rader) per databas | Flera |
| Antal loggfiler (logg) per databas | 1 |
| VNet – Azure Resource Manager distribution | Yes |
| VNet – klassisk distributions modell | No |
| Portal stöd | Yes|
| Inbyggd integrerings tjänst (SSIS) | No-SSIS är en del av [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Inbyggd Analysis Service (SSAS) | No-SSAS är separat [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Inbyggd repor ting service (SSRS) | Använd [Power BI sid färdiga rapporter](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) i stället för att vara värd för SSRS på en virtuell Azure-dator. SQL-hanterad instans kan inte köra SSRS som en tjänst, men kan vara värd för [SSRS-katalog databaser](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) för en rapport server som är installerad på en virtuell Azure-dator med hjälp av SQL Server autentisering. |
|||

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

Den [vCore-baserade inköps modellen](../database/service-tiers-vcore.md) för SQL-hanterad instans ger dig flexibilitet, kontroll, transparens och ett enkelt sätt att översätta lokala arbets belastnings krav till molnet. Med den här modellen kan du ändra beräkning, minne och lagring baserat på dina arbets belastnings behov. VCore-modellen är också kvalificerad för upp till 55 procent besparingar med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för SQL Server.

I vCore-modellen kan du välja mellan generationens maskin vara.

- **Gen4** logiska CPU: er baseras på Intel® E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD, fysiska kärnor, 7 GB RAM-minne per kärna och beräknings storlekar mellan 8 och 24 virtuella kärnor.
- **Gen5** logiska CPU: er baseras på Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) och Intel® 8272CL (överlappande sjö) 2,5 GHz-processorer, snabbt NVMe SSD, Hyper-threaded Logical Core och beräknings storlekar mellan 4 och 80 kärnor.

Hitta mer information om skillnaden mellan maskin varu generationer i [resurs gränser för SQL-hanterade instanser](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Tjänstnivåer

SQL-hanterad instans finns tillgänglig på två tjänst nivåer:

- **Generell användning**: utformad för program med vanliga prestanda-och I/O latens-krav.
- **Verksamhets kritisk**: utformad för program med låg I/O latens-krav och minimal påverkan på underliggande underhålls åtgärder på arbets belastningen.

Båda tjänst nivåerna garanterar 99,99% tillgänglighet och gör att du kan välja lagrings storlek och beräknings kapacitet separat. Mer information om arkitekturen för hög tillgänglighet för Azure SQL-hanterad instans finns i [hög tillgänglighet och hanterad Azure SQL-instans](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Generell användning tjänst nivå

I följande lista beskrivs viktiga egenskaper för Generell användning tjänst nivån:

- Utformad för de flesta affärs program med typiska prestanda krav
- Azure Blob Storage med höga prestanda (8 TB)
- Inbyggd [hög tillgänglighet](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) baserat på tillförlitlig Azure Blob Storage och [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Mer information finns i [lagrings skiktet på generell användning nivå](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) och [metod tips för lagring och överväganden för SQL-hanterad instans (generell användning)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Hitta mer information om skillnaden mellan tjänst nivåer i [resurs gränser för SQL-hanterade instanser](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Affärskritisk tjänst nivå

Affärskritisk tjänst nivån har skapats för program med höga I/O-krav. Den ger den högsta återhämtningen till problem med flera isolerade repliker.

I följande lista beskrivs viktiga egenskaper för Affärskritisk tjänst nivå:

- Utformad för företags program med högsta prestanda och HA krav
- Levereras med super-fast lokal SSD-lagring (upp till 1 TB på Gen4 och upp till 4 TB på Gen5)
- Inbyggd [hög tillgänglighet](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) baserat på [tillgänglighets grupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) och [Azure-Service Fabric](../../service-fabric/service-fabric-overview.md)
- Inbyggd ytterligare [skrivskyddad databas replik](../database/read-scale-out.md) som kan användas för rapportering och andra skrivskyddade arbets belastningar
- [Minnes intern OLTP](../in-memory-oltp-overview.md) som kan användas för arbets belastning med höga prestanda krav  

Hitta mer information om skillnaderna mellan tjänst nivåer i [resurs gränser för SQL-hanterade instanser](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Hanteringsåtgärder

Azure SQL-hanterad instans tillhandahåller hanterings åtgärder som du kan använda för att automatiskt distribuera nya hanterade instanser, uppdatera instans egenskaper och ta bort instanser när de inte längre behövs. Detaljerad förklaring av hanterings åtgärder finns på [översikts sidan hanterade instans hanterings åtgärder](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-hanterad instans levereras med avancerade säkerhetsfunktioner från Azure-plattformen och SQL Server databas motorn.

### <a name="security-isolation"></a>Säkerhetsisolering

SQL-hanterad instans ger ytterligare säkerhets isolering från andra klienter på Azure-plattformen. Säkerhets isolering innehåller:

- [Inbyggd implementering av virtuella nätverk](connectivity-architecture-overview.md) och anslutning till din lokala miljö med Azure ExpressRoute eller VPN gateway.
- I en standard distribution exponeras SQL-slutpunkten bara via en privat IP-adress, vilket ger säker anslutning från privata Azure-eller hybrid nätverk.
- En-klient med dedikerad underliggande infrastruktur (beräkning, lagring).

I följande diagram beskrivs olika anslutnings alternativ för dina program:

![Hög tillgänglighet](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Om du vill veta mer om VNet-integrering och nätverks princip tillämpning på under näts nivån, se [VNet-arkitektur för hanterade instanser](connectivity-architecture-overview.md) och [Anslut ditt program till en hanterad instans](connect-application-instance.md).

> [!IMPORTANT]
> Placera flera hanterade instanser i samma undernät, där de tillåts av dina säkerhets krav, eftersom det ger dig ytterligare förmåner. Att samplacera instanser i samma undernät fören klar avsevärt underhållet av nätverks infrastrukturen och minskar etablerings tiden för instansen, eftersom en lång etablerings tid är kopplad till kostnaden för att distribuera den första hanterade instansen i ett undernät.

### <a name="security-features"></a>Säkerhetsfunktioner

Azure SQL-hanterad instans innehåller en uppsättning avancerade säkerhetsfunktioner som kan användas för att skydda dina data.

- [SQL-hanterad instans granskning](auditing-configure.md) spårar databas händelser och skriver dem till en Gransknings logg fil som placerats på ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Data kryptering i motion-SQL-hanterad instans säkrar dina data genom att tillhandahålla kryptering för data i rörelse med hjälp av Transport Layer Security. Förutom Transport Layer Security ger SQL-hanterad instans skydd av känsliga data i flygning, i vila och under bearbetning av frågor med [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted erbjuder data säkerhet mot överträdelser som innebär stöld av kritiska data. Med Always Encrypted lagras exempelvis kreditkorts nummer krypterade i databasen alltid, även under frågekörning, vilket tillåter dekryptering vid användnings punkten av behörig personal eller program som behöver behandla dessa data.
- [Avancerat skydd](threat-detection-configure.md) kompletterar [granskning](auditing-configure.md) genom att tillhandahålla ett extra lager av säkerhets information som är inbyggt i tjänsten och som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Du får varningar om misstänkta aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst mönster. Aviseringar om avancerade hot skydd kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/). De ger information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet.  
- [Dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking) begränsar känslig data exponering genom att maskera den till icke-privilegierade användare. Dynamisk datamaskering bidrar till att förhindra obehörig åtkomst till känsliga data genom att göra det möjligt att ange hur mycket av känsliga data som ska visas med minimal påverkan på program nivån. Det är en principbaserad säkerhetsfunktion som döljer känsliga data i resultat uppsättningen för en fråga över angivna databas fält, medan data i databasen inte ändras.
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) (RLS) gör att du kan styra åtkomsten till rader i en databas tabell baserat på egenskaperna för användaren som kör en fråga (t. ex. efter grupp medlemskap eller körnings kontext). RLS fören klar utformningen och kodningen av säkerhet i ditt program. RLS låter dig implementera begränsningar för dataåtkomst för raden. Du kan till exempel se till att anställda endast kan komma åt de data rader som är relevanta för deras avdelning eller begränsa en data åtkomst till enbart relevanta data.
- [Transparent data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) KRYPTERAr SQL-hanterade instans data filer, vilket kallas att kryptera data i vila. TDE utför I/O-kryptering i real tid och dekryptering av data-och loggfiler. Krypteringen använder en databas krypterings nyckel (DEK), som lagras i databasens start post för tillgänglighet under återställningen. Du kan skydda alla dina databaser i en hanterad instans med transparent data kryptering. TDE är en beprövad krypterings-och rest-teknik i SQL Server som krävs av många kompatibla standarder för att skydda mot stöld av lagrings medier.

Migrering av en krypterad databas till SQL-hanterad instans stöds via Azure Database Migration Service eller intern återställning. Om du planerar att migrera en krypterad databas med intern återställning, är det ett obligatoriskt steg att migrera det befintliga TDE-certifikatet från SQL Server-instansen till SQL Managed instance. Mer information om alternativ för migrering finns i [SQL Server migrering till SQL-hanterad instans](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integrering

SQL-hanterad instans stöder traditionella SQL Server inloggningar och inloggningar i databas motorn som är integrerade med Azure AD. Azure AD server-Huvudkonton (inloggningar) (**offentlig för hands**version) är en Azure Cloud-version av lokala databas inloggningar som du använder i din lokala miljö. Med Azure AD server-Huvudkonton (inloggningar) kan du ange användare och grupper från din Azure AD-klient som en sann instans – begränsade huvud servrar, som kan utföra alla instanser på instans nivå, inklusive frågor över flera databaser inom samma hanterade instans.

En ny syntax introduceras för att skapa Azure AD server-huvudobjekt (inloggningar), **från extern provider**. Mer information om syntaxen finns i <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning</a>och granska artikeln [etablera en Azure Active Directory administratör för SQL-hanterad instans](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

SQL-hanterad instans gör att du centralt kan hantera identiteter för databas användare och andra Microsoft-tjänster med [Azure Active Directory-integrering](../database/authentication-aad-overview.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [Multi-Factor Authentication](../database/authentication-mfa-ssms-configure.md) för att öka säkerheten för data och program med stöd för en enkel inloggnings process.

### <a name="authentication"></a>Autentisering

SQL-hanterad instans-autentisering syftar på hur användare kan bevisa sin identitet när de ansluter till databasen. SQL-hanterad instans stöder två typer av autentisering:  

- **SQL-autentisering**:

  Den här autentiseringsmetoden använder ett användar namn och lösen ord.
- **Azure Active Directory autentisering**:

  Den här autentiseringsmetoden använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Auktorisering

Auktorisering syftar på vad en användare kan göra inom en databas i en Azure SQL-hanterad instans och styrs av ditt användar kontos databas roll medlemskap och behörigheter på objekt nivå. SQL-hanterad instans har samma Authorization-funktioner som SQL Server 2017.

## <a name="database-migration"></a>Databasmigrering

SQL-hanterad instans riktar sig mot användar scenarier med Mass databas migrering från lokala eller IaaS databas implementeringar. SQL-hanterad instans har stöd för flera databas migrerings alternativ:

### <a name="backup-and-restore"></a>Säkerhetskopiering och återställning  

Migreringsprocessen utnyttjar SQL-säkerhetskopieringar till Azure Blob Storage. Säkerhets kopior som lagras i en Azure Storage-BLOB kan återställas direkt till en hanterad instans med hjälp av [kommandot T-SQL REstore](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- En snabb start som visar hur du återställer Wide World-importörer – standard säkerhets kopierings filen för databasen finns i [återställa en säkerhets kopia till en hanterad instans](restore-sample-database-quickstart.md). Den här snabb starten visar att du måste ladda upp en säkerhets kopia till Azure Blob Storage och skydda den med en SAS-nyckel (signatur för delad åtkomst).
- Information om återställning från URL finns i [intern återställning från URL](migrate-to-instance-from-sql-server.md#native-restore-from-url).

> [!IMPORTANT]
> Säkerhets kopieringar från en hanterad instans kan bara återställas till en annan hanterad instans. De kan inte återställas till en SQL Server instans eller Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredje parter och SQL Server databaser till Azure SQL Database, Azure SQL-hanterad instans och SQL Server på virtuella Azure-datorer. Se [hur du migrerar din lokala databas till SQL-hanterad instans med hjälp av Database migration service](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

SQL-hanterad instans syftar till att leverera nära 100% Surface Area-kompatibilitet med den senaste SQL Server versionen genom en plan för stegvis publicering. För en funktion och jämförelse lista, se [jämförelse av SQL-hanterad instans](../database/features-comparison.md)och för en lista över t-SQL-skillnader i SQL-hanterad instans jämfört med SQL Server, se [SQL-hanterade instans t-SQL-skillnader från SQL Server](transact-sql-tsql-differences-sql-server.md).

SQL-hanterad instans har stöd för bakåtkompatibilitet till SQL Server 2008-databaser. Direkt migrering från SQL Server 2005-databas servrar stöds och kompatibilitetsnivån för migrerade SQL Server 2005-databaser uppdateras till SQL Server 2008.
  
Följande diagram visar kompatibiliteten för Surface Area i SQL-hanterad instans:  

![Migrering](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Viktiga skillnader mellan SQL Server lokal och SQL-hanterad instans

SQL-hanterade instans förmåner är alltid uppdaterade i molnet, vilket innebär att vissa funktioner i SQL Server kan vara föråldrade, dras tillbaka eller ha alternativ. Det finns särskilda fall när verktyg måste identifiera att en viss funktion fungerar på ett något annorlunda sätt eller att tjänsten körs i en miljö som du inte har kontroll över helt.

Några viktiga skillnader:

- Hög tillgänglighet är inbyggd och förkonfigurerad med teknik som liknar [Always on-tillgänglighetsgrupper](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Det finns bara automatiserade säkerhets kopieringar och återställning av tidpunkter. Kunder kan initiera `copy-only` säkerhets kopieringar som inte stör den automatiska säkerhets kopierings kedjan.
- Det finns inte stöd för att ange fullständiga fysiska sökvägar, så alla motsvarande scenarier måste stödjas på olika sätt: Restore DB stöder inte flytt, CREATE DB tillåter inte fysiska sökvägar, BULK INSERT bara fungerar med Azure-blobbar, osv.
- SQL-hanterad instans stöder [Azure AD-autentisering](../database/authentication-aad-overview.md) som ett moln alternativ till Windows-autentisering.
- SQL-hanterad instans hanterar automatiskt XTP fil grupper och filer för databaser som innehåller InMemory OLTP-objekt.
- SQL-hanterad instans stöder SQL Server Integration Services (SSIS) och kan vara värd för en SSIS-katalog (SSISDB) som lagrar SSIS-paket, men de körs på en hanterad Azure-SSIS Integration Runtime (IR) i Azure Data Factory. Se [skapa Azure-SSIS IR i Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Information om hur du jämför SSIS-funktionerna finns i [jämföra SQL Database till SQL-hanterad instans](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Administrations funktioner

SQL-hanterad instans gör det möjligt för system administratörer att ägna mindre tid åt administrativa uppgifter eftersom tjänsten antingen utför dem åt dig eller avsevärt fören klar dessa aktiviteter. Till exempel [installation och uppdatering av OS/RDBMS](../database/high-availability-sla.md), [dynamisk storleks ändring och konfiguration av instanser](../database/single-database-scale.md), [säkerhets kopieringar](../database/automated-backups-overview.md), [databasreplikering](replication-between-two-instances-configure-tutorial.md) (inklusive system databaser), [konfiguration av hög tillgänglighet](../database/high-availability-sla.md)och konfiguration av data strömmar för hälso-och [prestanda övervakning](../../azure-monitor/insights/azure-sql.md) .

Mer information finns i [en lista över funktioner som stöds och som inte stöds och som inte stöds av](../database/features-comparison.md)SQL, samt [T-SQL-skillnader mellan SQL-hanterad instans och SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identifiera en hanterad instans program mässigt

I följande tabell visas flera egenskaper, som är tillgängliga via Transact-SQL, som du kan använda för att identifiera att ditt program fungerar med SQL-hanterad instans och hämta viktiga egenskaper.

|Egenskap|Värde|Kommentar|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Värdet är samma som i SQL Database. Detta indikerar **inte** SQL-motor version 12 (SQL Server 2014). SQL-hanterad instans kör alltid den senaste säkra versionen av SQL-motorn, som är lika med eller högre än den senaste tillgängliga RTM-versionen av SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Värdet är samma som i SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Det här värdet identifierar en hanterad instans unikt.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Fullständigt instans-DNS-namn i följande format: `<instanceName>` . `<dnsPrefix>` . database.windows.net, där `<instanceName>` är namnet som tillhandahålls av kunden, medan `<dnsPrefix>` är automatiskt genererad del av namnet som garanterar unikt DNS-namn ("wcus17662feb9ce98", till exempel)|Exempel: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av SQL-hanterad instans databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL-hanterad instans med Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
- Information om priser finns i [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
