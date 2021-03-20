---
title: Hantera efter migrering
titleSuffix: Azure SQL Database
description: Lär dig hur du hanterar dina databaser med en och flera databaser efter migrering till Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446118"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Ny DBA i molnet – Hantera Azure SQL Database efter migrering
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Att flytta från den traditionella självhanterade, självkontrollerade miljön till en PaaS-miljö kan vara lite förbelastad i första hand. Som en app-utvecklare eller en DBA vill du känna till kärn funktionerna i plattformen som hjälper dig att hålla ditt program tillgängligt, utföra, säkert och flexibelt – alltid. Den här artikeln syftar på att göra exakt det. Artikeln kortfattat organiserar resurser och ger dig en vägledning om hur du bäst använder viktiga funktioner i Azure SQL Database med enkla databaser och grupperade databaser för att hantera och hålla ditt program effektivt och uppnå optimala resultat i molnet. Typisk mål grupp för den här artikeln är de som:

- Utvärderar migrering av sina program till Azure SQL Database – att du ska använda dem.
- Håller på att migrera sina program – ett pågående migrerings scenario.
- Nyligen har slutfört migreringen till Azure SQL Database – nytt DBA i molnet.

I den här artikeln beskrivs några av kärn egenskaperna för Azure SQL Database som en plattform som du lätt kan använda när du arbetar med enkla databaser och databaser i pooler i elastiska pooler. De är följande:

- Övervaka databaser med Azure-portalen
- Affärskontinuitet och haveriberedskap (BCDR)
- Säkerhet och efterlevnad
- Övervakning och underhåll av intelligent databas
- Dataförflyttning

## <a name="monitor-databases-using-the-azure-portal"></a>Övervaka databaser med Azure-portalen

I [Azure Portal](https://portal.azure.com/)kan du övervaka en enskild Databass användning genom att välja din databas och klicka på **övervaknings** diagrammet. Det öppnar **Mått**-fönstret, vilket du kan ändra genom att klicka på **Redigera diagram**-knappen. Lägg till följande mått:

- CPU-procent
- DTU-procent
- Data IO-procent
- Databasstorlek i procent

När du har lagt till dessa mått kan du fortsätta att visa dem i **övervaknings** diagrammet med mer information i **mått** fönstret. Alla fyra mätvärdena visar ett snittvärde för utnyttjandeprocent i förhållande till din databas **DTU:er**. Se den [DTU-baserade inköps modellen](service-tiers-dtu.md) och [vCore inköps modell](service-tiers-vcore.md) artiklar för mer information om tjänst nivåer.  

![Tjänstnivå-övervakning av databasprestanda.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Du kan också konfigurera aviseringar på prestandamåtten. Klicka på knappen **Lägg till avisering** i **mått**-fönstret. Följ guiden för att konfigurera aviseringen. Du har möjlighet att avisera om måttet överskrider ett visst tröskelvärde eller om måttet faller under ett visst tröskelvärde.

Om du exempelvis förväntar dig att arbetsbelastningen på din databas kommer att öka, kan du välja att konfigurera en e-postavisering när din databas kommer upp i 80 % för något av prestandamåtten. Du kan använda detta som en tidig varning för att ta reda på när du kanske måste växla till nästa högsta beräknings storlek.

Prestanda måtten kan också hjälpa dig att avgöra om du kan nedgradera till en lägre beräknings storlek. Anta att du använder en Standard S2-databas och alla prestandamått visar att databasen i snitt inte använder mer än 10 % vid något tillfälle. Det är då troligt att databasen skulle fungera bra i Standard S1. Du bör dock vara medveten om arbets belastningar som har högre eller fluktuerat innan du fattar beslut om att flytta till en lägre beräknings storlek.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Affärskontinuitet och haveriberedskap (BCDR)

Med affärs kontinuitet och haveri beredskap kan du fortsätta din verksamhet, som vanligt, i händelse av en katastrof. Haveriet kan vara en händelse på databas nivå (till exempel att någon av misstag släpper en viktig tabell) eller en data center nivå händelse (regional Catastrophe, till exempel en tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Hur gör jag för att skapa och hantera säkerhets kopior på SQL Database

Du skapar inga säkerhets kopior på Azure SQL Database och det beror på att du inte behöver. SQL Database säkerhetskopierar automatiskt databaser åt dig, så att du inte längre behöver bekymra dig om schemaläggning, insamling och hantering av säkerhets kopior. Plattformen tar en fullständig säkerhets kopia varje vecka, differentiell säkerhets kopiering med några timmar och en logg säkerhets kopia var femte minut för att säkerställa att haveri beredskap är effektiv och data förlusten är minimal. Den första fullständiga säkerhets kopieringen sker så snart du skapar en databas. Dessa säkerhets kopior är tillgängliga för dig under en viss period med namnet "kvarhållningsperiod" och varierar beroende på vilken tjänst nivå du väljer. SQL Database ger dig möjlighet att återställa till vilken tidpunkt som helst inom denna kvarhållningsperiod med hjälp av återställning av tidpunkt [(PITR)](recovery-using-backups.md#point-in-time-restore).

|Tjänstenivå|Kvarhållningsperiod i dagar|
|---|:---:|
|Grundläggande|7|
|Standard|35|
|Premium|35|
|||

Dessutom kan du med hjälp av funktionen för [långsiktig kvarhållning (brv)](long-term-retention-overview.md) Spara till dina säkerhets kopior under en längre tid, i upp till 10 år, och återställa data från dessa säkerhets kopior när som helst inom den perioden. Dessutom lagras databas säkerhets kopiorna i Geo-replikerad lagring för att garantera återhämtning från regionala Catastrophe. Du kan också återställa de här säkerhets kopiorna i valfri Azure-region vid alla tidpunkter inom kvarhållningsperioden. Se [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Hur gör jag för att garantera affärs kontinuitet i händelse av en katastrof eller regional Catastrophe på data center nivå

Eftersom dina databas säkerhets kopior lagras i Geo-replikerad lagring, så att du kan återställa säkerhets kopian till en annan Azure-region i händelse av en regional katastrof. Detta kallas geo-Restore. Återställnings punkt målet är vanligt vis < 1 timme och ERT (uppskattat återställnings tid – några minuter till timmar).

För verksamhets kritiska databaser Azure SQL Database erbjudanden, aktiv geo-replikering. Vad detta innebär i princip är att den skapar en geo-replikerad sekundär kopia av den ursprungliga databasen i en annan region. Om din databas till exempel inlednings vis finns i Azure västra USA-regionen och du vill ha regional katastrof återhämtning. Du skulle skapa en aktiv geo-replik av databasen i västra USA för att säga USA, östra. När Calamity går på västra USA kan du växla över till regionen USA, östra. Att konfigurera dem i en grupp för automatisk redundans är ännu bättre eftersom detta säkerställer att databasen automatiskt växlar över till den sekundära i östra USA i händelse av en katastrof. Återställningen för detta är < 5 sekunder och ERT < 30 sekunder.

Om en grupp för automatisk redundans inte har kon figurer ATS måste programmet aktivt övervaka för en katastrof och initiera en redundansväxling till den sekundära. Du kan skapa upp till 4 sådana aktiva geo-replikeringar i olika Azure-regioner. Den får ännu bättre. Du kan också komma åt dessa sekundära aktiva geo-repliker för skrivskyddad åtkomst. Detta är särskilt användbart för att minska svars tiden för ett geo-distribuerat program scenario.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Hur ändras planen för haveri beredskap från lokal plats till SQL Database

I sammanfattningen kräver SQL Server-installationen att du aktivt kan hantera din tillgänglighet genom att använda funktioner som redundanskluster, databas spegling, databasreplikering eller logg överföring samt underhålla och hantera säkerhets kopior för att säkerställa affärs kontinuiteten. Med SQL Database hanterar plattformen dessa åt dig, så att du kan fokusera på att utveckla och optimera databas programmet och inte bekymra dig om haveri hantering så mycket. Du kan ställa in säkerhets kopierings-och haveri beredskaps planer konfigurerade och arbeta med bara några klick på Azure Portal (eller några kommandon med hjälp av PowerShell-API: er).

Mer information om haveri beredskap finns i: [Azure SQL Database disaster recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Säkerhet och efterlevnad

SQL Database tar säkerhet och sekretess mycket allvarligt. Säkerhet i SQL Database är tillgängligt på databas nivå och på plattforms nivå och är bäst att förstå när du kategoriseras i flera lager. På varje lager får du kontroll över och ger optimal säkerhet för ditt program. Lagren är:

- Identitets & autentisering ([SQL-autentisering och Azure Active Directory [Azure AD]-autentisering](logins-create-manage.md)).
- Övervaknings aktivitet ([granskning](../../azure-sql/database/auditing-overview.md) och [hot identifiering](threat-detection-configure.md)).
- Skydda faktiska data ([Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) och [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Kontrol lera åtkomsten till känsliga och privilegierade data ([säkerhet på radnivå](/sql/relational-databases/security/row-level-security) och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) erbjuder centraliserad säkerhets hantering över arbets belastningar som körs i Azure, lokalt och i andra moln. Du kan se om viktiga SQL Database skydd, till exempel [granskning](../../azure-sql/database/auditing-overview.md) och [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , har kon figurer ATS på alla resurser och skapa principer baserat på dina egna krav.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Vilka metoder för användarautentisering som erbjuds i SQL Database

Det finns två autentiseringsmetoder som erbjuds i SQL Database:

- [Azure Active Directory-autentisering](authentication-aad-overview.md)
- [SQL-autentisering](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Den traditionella Windows-autentiseringen stöds inte. Azure Active Directory (Azure AD) är en centraliserad tjänst för identitets-och åtkomst hantering. Med det här kan du enkelt tillhandahålla enkel inloggning (SSO) till all personal i din organisation. Det innebär att autentiseringsuppgifterna delas mellan alla Azure-tjänster för enklare autentisering. 

Azure AD har stöd för [Azure ad Multi-Factor Authentication](authentication-mfa-ssms-overview.md) och med [några klick](../../active-directory/hybrid/how-to-connect-install-express.md) kan Azure AD integreras med Windows Server Active Directory. SQL-autentisering fungerar på samma sätt som du har använt den tidigare. Du anger ett användar namn/lösen ord och du kan autentisera användare till valfri databas på en specifik server. Detta gör det också möjligt SQL Database och Azure Synapse Analytics att erbjuda Multi-Factor Authentication-och gäst användar konton i en Azure AD-domän. Om du redan har ett Active Directory lokalt kan du federera katalogen med Azure Active Directory för att utöka din katalog till Azure.

|**Om du...**|**SQL Database/Azure Synapse Analytics**|
|---|---|
|Föredra att inte använda Azure Active Directory (Azure AD) i Azure|Använd [SQL-autentisering](security-overview.md)|
|Använda AD på SQL Server lokalt|[Federera AD med Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)och Använd Azure AD-autentisering. Med det här alternativet kan du använda enkel inloggning.|
|Du måste framtvinga Multi-Factor Authentication|Kräv Multi-Factor Authentication som en princip via [Microsoft villkorlig åtkomst](conditional-access-configure.md)och Använd [Azure AD Universal-autentisering med Multi-Factor Authentication stöd](authentication-mfa-ssms-overview.md).|
|Ha gäst konton från Microsoft-konton (live.com, outlook.com) eller andra domäner (gmail.com)|Använd [Azure AD Universal Authentication](authentication-mfa-ssms-overview.md) i SQL Database/Data Warehouse, som utnyttjar [Azure AD B2B-samarbete](../../active-directory/external-identities/what-is-b2b.md).|
|Är inloggad i Windows med dina autentiseringsuppgifter för Azure AD från en federerad domän|Använd [Azure AD-integrerad autentisering](authentication-aad-configure.md).|
|Är inloggade i Windows med autentiseringsuppgifter från en domän som inte är federerad med Azure|Använd [Azure AD-integrerad autentisering](authentication-aad-configure.md).|
|Ha tjänster på mellan nivå som måste ansluta till SQL Database eller Azure Synapse Analytics|Använd [Azure AD-integrerad autentisering](authentication-aad-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Hur gör jag för att begränsa eller kontrol lera anslutnings åtkomst till min databas

Det finns flera metoder som du kan använda för att uppnå optimal anslutnings organisation för ditt program.

- Brandväggsregler
- VNet-tjänstslutpunkter
- Reserverade ip-adresser

#### <a name="firewall"></a>Brandvägg

En brand vägg förhindrar åtkomst till servern från en extern entitet genom att endast tillåta att vissa entiteter har åtkomst till servern. Som standard tillåts inte alla anslutningar till databaser inuti servern, förutom (optionally7) anslutningar som kommer från andra Azure-tjänster. Med en brand Väggs regel kan du bara öppna åtkomst till servern till entiteter (till exempel en utvecklare) som du godkänner genom att tillåta datorns IP-adress genom brand väggen. Du kan också ange ett intervall med IP-adresser som du vill ge åtkomst till servern. Till exempel kan utvecklarens dator-IP-adresser i din organisation läggas till samtidigt genom att ange ett intervall på sidan brand Väggs inställningar.

Du kan skapa brand Väggs regler på server nivå eller på databas nivå. Regler för IP-brandvägg på server nivå kan antingen skapas med hjälp av Azure Portal eller med SSMS. Mer information om hur du ställer in en brand Väggs regel på server nivå och databas nivå finns i: [skapa IP-brandvägg i SQL Database](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Tjänstslutpunkter

Som standard är din databas konfigurerad för att "ge Azure-tjänster åtkomst till servern", vilket innebär att alla virtuella datorer i Azure kan försöka ansluta till din databas. Dessa försök behöver fortfarande autentiseras. Men om du inte vill att databasen ska vara tillgänglig för alla Azure-IP-adresser kan du inaktivera alternativet "Tillåt Azure-tjänster för åtkomst till servern". Dessutom kan du konfigurera [VNet-tjänstens slut punkter](vnet-service-endpoint-rule-overview.md).

Med tjänst slut punkter (SE) kan du bara exponera dina kritiska Azure-resurser för ditt privata virtuella nätverk i Azure. Genom att göra det, eliminerar du i princip den offentliga åtkomsten till dina resurser. Trafiken mellan ditt virtuella nätverk i Azure ligger kvar på Azures stamnät nätverk. Utan SE ska du Hämta paket routning för Tvingad tunnel trafik. Ditt virtuella nätverk tvingar Internet trafiken till din organisation och Azure-tjänstetrafiken att gå över samma väg. Med tjänst slut punkter kan du optimera detta eftersom paketen flödar direkt från ditt virtuella nätverk till tjänsten i Azure stamnät nätverket.

![VNet-tjänstslutpunkter](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Reserverade ip-adresser

Ett annat alternativ är att etablera [reserverade IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) -adresser för dina virtuella datorer och lägga till de angivna IP-adresserna för virtuella datorer i inställningarna för Server brand väggen. Genom att tilldela reserverade IP-adresser sparar du problem med att uppdatera brand Väggs reglerna med ändring av IP-adresser.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Vilken port ansluter jag till SQL Database på

Port 1433. SQL Database kommunicerar via den här porten. Om du vill ansluta inifrån ett företags nätverk måste du lägga till en utgående regel i brand Väggs inställningarna för din organisation. Undvik att exponera port 1433 utanför Azure-gränser som en rikt linje.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Hur kan jag övervaka och reglera aktiviteter på min server och databas i SQL Database

#### <a name="sql-database-auditing"></a>SQL Database Auditing

Med SQL Database kan du aktivera granskning för att spåra databas händelser. [SQL Database granskning](../../azure-sql/database/auditing-overview.md) registrerar databas händelser och skriver dem i en Gransknings logg fil i ditt Azure Storage-konto. Granskning är särskilt användbart om du vill få insikt i potentiella säkerhets-och princip överträdelser, upprätthålla efterlevnaden av efterlevnad. Det gör att du kan definiera och konfigurera vissa kategorier av händelser som du tror behöver granskning och utifrån att du kan hämta förkonfigurerade rapporter och en instrument panel för att få en översikt över händelser som inträffar i databasen. Du kan tillämpa dessa gransknings principer antingen på databas nivå eller på server nivå. En guide om hur du aktiverar granskning för servern/databasen finns i [aktivera SQL Database granskning](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Hotidentifiering

Med [hot identifiering](threat-detection-configure.md)får du möjlighet att agera på säkerhets-eller princip överträdelser som identifieras av granskningen mycket enkelt. Du behöver inte vara en säkerhets expert för att lösa potentiella hot eller överträdelser i systemet. Hot identifiering har också några inbyggda funktioner som identifiering av SQL-injektering. SQL-inmatning är ett försök att ändra eller kompromettera data och ett enhetligt sätt att angripa ett databas program i allmänhet. Hot identifieringen kör flera uppsättningar algoritmer som identifierar potentiella sårbarheter och SQL-injektering-attacker, samt avvikande åtkomst mönster för databaser (till exempel åtkomst från en ovanlig plats eller en okänd huvud administratör). Säkerhets ansvariga eller andra angivna administratörer får ett e-postmeddelande om ett hot har identifierats i databasen. Varje meddelande innehåller information om den misstänkta aktiviteten och rekommendationer om hur du kan undersöka och minimera hotet ytterligare. Information om hur du aktiverar hot identifiering finns i: [Aktivera hot identifiering](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Hur gör jag för att skydda mina data i allmänhet på SQL Database

Kryptering ger en kraftfull mekanism för att skydda och skydda känsliga data från inkräktare. Dina krypterade data används inte för inkräktare utan krypterings nyckeln. Därmed lägger den till ett extra skydds lager ovanpå de befintliga säkerhets lagren som skapats i SQL Database. Det finns två aspekter av att skydda dina data i SQL Database:

- Dina data som finns i vila i data-och loggfilerna
- Dina data som är i flygning

I SQL Database är som standard dina data i vila i data-och loggfilerna på underlag rings systemet fullständigt och alltid krypterade via [Transparent datakryptering [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Dina säkerhets kopior krypteras också. Med TDE finns det inga ändringar som krävs på din program sida som har åtkomst till dessa data. Kryptering och dekryptering sker transparent; Därför är namnet.
För att skydda känsliga data i flygning och i vila tillhandahåller SQL Database en funktion som kallas [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE är en form av kryptering på klient sidan som krypterar känsliga kolumner i databasen (så att de finns i chiffertexten till databas administratörer och obehöriga användare). Servern tar emot krypterade data att börja med. Nyckeln för Always Encrypted lagras också på klient sidan så att endast auktoriserade klienter kan dekryptera känsliga kolumner. Server-och data administratörer kan inte se känsliga data eftersom krypterings nycklarna lagras på klienten. AE krypterar känsliga kolumner i tabellen från slut punkt till slut punkt från obehöriga klienter till den fysiska disken. AE stöder likhets jämförelser idag, så databas administratörer kan fortsätta att fråga krypterade kolumner som en del av deras SQL-kommandon. Always Encrypted kan användas med en rad olika alternativ för nyckel lagring, till exempel [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), Windows certifikat Arkiv och lokala säkerhetsmoduler för maskin vara.

|**Kännetecken**|**Alltid krypterad**|**Transparent datakryptering**|
|---|---|---|
|**Krypterings omfång**|Slut punkt till slut punkt|Vilande data|
|**Servern kan komma åt känsliga data**|Inga|Ja, eftersom krypteringen är för vilande data|
|**Tillåtna T-SQL-åtgärder**|Likhets jämförelse|Alla ytor i T-SQL är tillgängligt|
|**App-ändringar krävs för att använda funktionen**|Minimal|Mycket minimal|
|**Krypterings precision**|Kolumn nivå|databasnivå|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Hur kan jag begränsa åtkomsten till känsliga data i min databas

Varje program har en viss bit av känsliga data i databasen som behöver skyddas från att vara synlig för alla. Vissa personer inom organisationen behöver se dessa data, men andra bör inte kunna visa dessa data. Ett exempel är löner för anställda. En chef behöver till gång till löne informationen för sina direkta rapporter men de enskilda grupp medlemmarna får inte till gång till löne informationen för sina kollegor. Ett annat scenario är data utvecklare som kan interagera med känsliga data under utvecklings stadier eller testning, till exempel SSNs av kunder. Den här informationen behöver inte exponeras för utvecklaren. I sådana fall måste känsliga data antingen maskeras eller inte visas alls. SQL Database erbjuder två sådana metoder för att förhindra att obehöriga användare kan visa känsliga data:

[Dynamisk datamaskering](dynamic-data-masking-overview.md) är en data mask funktion som gör att du kan begränsa känslig data exponering genom att maskera den till icke-privilegierade användare i program lagret. Du definierar en masknings regel som kan skapa ett masknings mönster (till exempel för att bara visa de sista fyra siffrorna i ett nationellt ID SSN: XXX-XX-0000 och markera det mesta av det som XS) och identifiera vilka användare som ska uteslutas från masknings regeln. Maskeringen sker direkt och det finns olika masknings funktioner som är tillgängliga för olika data kategorier. Med dynamisk datamaskering kan du automatiskt identifiera känsliga data i databasen och koppla dem till den.

[Säkerhet på radnivå](/sql/relational-databases/security/row-level-security) gör att du kan kontrol lera åtkomst på rad nivå. Detta innebär att vissa rader i en databas tabell baserat på den användare som kör frågan (grupp medlemskap eller körnings kontext) är dolda. Åtkomst begränsningen görs på databas nivån i stället för på en program nivå för att förenkla din program logik. Du börjar med att skapa ett filter-predikat, filtrera bort rader som inte visas och säkerhets principen nästa definierar vem som har åtkomst till dessa rader. Slutanvändaren kör sedan sin fråga och, beroende på användarens behörighet, visar de antingen de begränsade raderna eller kan inte se dem alls.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Hur gör jag för att hantera krypterings nycklar i molnet

Det finns nyckel hanterings alternativ för både Always Encrypted (kryptering på klient sidan) och transparent datakryptering (kryptering i vila). Vi rekommenderar att du regelbundet roterar krypterings nycklar. Rotations frekvensen bör överensstämma med både din interna organisations bestämmelser och krav på efterlevnad.

#### <a name="transparent-data-encryption-tde"></a>Transparent datakryptering (TDE)

Det finns en hierarki med två nycklar i TDE – data i varje användar databas krypteras av en symmetrisk AES-256-databas – unik databas krypterings nyckel (DEK), vilket i sin tur krypteras av en server-unik asymmetrisk RSA 2048-huvudnyckel. Huvud nyckeln kan hanteras antingen:

- Automatiskt av plattforms SQL Database.
- Eller genom att använda [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) som nyckel arkiv.

Som standard hanteras huvud nyckeln för transparent datakryptering av SQL Databases tjänsten för enkelhetens skull. Om din organisation vill ha kontroll över huvud nyckeln, finns det ett alternativ att använda Azure Key Vault] (Always-Encrypted-Azure-Key-Valve-configure.md) som nyckel arkiv. Genom att använda Azure Key Vault antar din organisation kontroll över nyckel etablering, rotation och behörighets kontroller. [Rotation eller växling av typen för en TDE huvud nyckel](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) är snabb, eftersom den bara krypterar om Dek. För organisationer med separering av roller mellan säkerhets-och data hantering kan en säkerhets administratör tillhandahålla nyckel materialet för huvud nyckeln TDE i Azure Key Vault och ange en Azure Key Vault nyckel identifierare till databas administratören som ska användas för kryptering i vila på en server. Key Vault är utformad så att Microsoft inte kan se eller extrahera några krypterings nycklar. Du får också en centraliserad hantering av nycklar för din organisation.

#### <a name="always-encrypted"></a>Always Encrypted

Det finns också en [hierarki med två nycklar](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) i Always Encrypted – en kolumn med känsliga data krypteras med en AES 256-kolumn krypterings nyckel (CEK), som i sin tur krypteras av en kolumn huvud nyckel (CMK). De angivna klient driv rutinerna för Always Encrypted har inga begränsningar för längden på CMKs. Det krypterade värdet för CEK lagras i databasen och CMK lagras i ett betrott nyckel lager, t. ex. Windows certifikat Arkiv, Azure Key Vault eller en modul för maskin varu säkerhet.

- Både [CEK och CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) kan roteras.
- CEK rotation är en storlek på data åtgärd och kan vara tids krävande beroende på storleken på de tabeller som innehåller de krypterade kolumnerna. Därför är det klokt att planera CEK rotationer.
- CMK rotation påverkar dock inte databasens prestanda och kan göras med avgränsade roller.

Följande diagram visar nyckel Arkiv alternativen för kolumn huvud nycklar i Always Encrypted

![Always Encrypted CMK Store providers](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Hur kan jag optimera och skydda trafiken mellan min organisation och SQL Database

Nätverks trafiken mellan din organisation och SQL Database skulle i allmänhet dirigeras via det offentliga nätverket. Men om du väljer att optimera den här sökvägen och göra den säkrare, kan du titta på Azure ExpressRoute. ExpressRoute låter dig i princip utöka företagets nätverk till Azure-plattformen via en privat anslutning. På så sätt går du inte över det offentliga Internet. Du får också högre säkerhets-, Tillförlitlighets-och Dirigerings optimering som översätter mindre nätverks fördröjningar och snabbare hastigheter än vad som normalt skulle uppstå via det offentliga Internet. Om du planerar att överföra ett betydande data segment mellan din organisation och Azure kan du använda ExpressRoute för att ge kostnads besparingar. Du kan välja mellan tre olika anslutnings modeller för anslutningen från din organisation till Azure:

- [Cloud Exchange Co-location](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Alla-till-alla](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Punkt-till-punkt](../../expressroute/expressroute-connectivity-models.md#Ethernet)

Med ExpressRoute kan du också överföra upp till 2 x bandbredden som du köper utan extra kostnad. Det är också möjligt att konfigurera anslutning mellan regioner med ExpressRoute. En lista över ExpressRoute-anslutnings leverantörer finns i: [ExpressRoute-partner och peering-platser](../../expressroute/expressroute-locations.md). I följande artiklar beskrivs Express Route i detalj:

- [Introduktion till Express Route](../../expressroute/expressroute-introduction.md)
- [Förutsättningar](../../expressroute/expressroute-prerequisites.md)
- [Arbetsflöden](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Är SQL Database kompatibel med alla myndighets krav och hur hjälper det till med min egen organisations efterlevnad

SQL Database är kompatibel med ett antal regler som compliancies. Om du vill visa den senaste uppsättningen compliancies som har uppfyllts av SQL Database kan du gå till [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) och öka detalj nivån på compliancies som är viktiga för din organisation för att se om SQL Database ingår i de kompatibla Azure-tjänsterna. Det är viktigt att Observera att även om SQL Database kan certifieras som en kompatibel tjänst, bidrar den till efterlevnaden av organisationens tjänst men garanterar inte automatiskt den.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Intelligent databas övervakning och underhåll efter migrering

När du har migrerat databasen till SQL Database ska du övervaka databasen (till exempel kontrol lera hur resursutnyttjande är som eller DBCC-kontroller) och utföra regelbundet underhåll (till exempel återskapa eller omorganisera index, statistik osv.). Lyckligt vis är SQL Database intelligent i den mening att den använder historiska trender och inspelade mått och statistik för att proaktivt hjälpa dig att övervaka och underhålla databasen, så att programmet körs optimalt alltid. I vissa fall kan Azure SQL Database automatiskt utföra underhålls aktiviteter beroende på konfigurations konfigurationen. Det finns tre aspekter för att övervaka din databas i SQL Database:

- Prestanda övervakning och optimering.
- Säkerhets optimering.
- Kostnads optimering.

### <a name="performance-monitoring-and-optimization"></a>Prestanda övervakning och optimering

Med frågornas prestanda insikter kan du få skräddarsydda rekommendationer för din databas belastning så att dina program kan fortsätta att köras på en optimal nivå – alltid. Du kan också ställa in det så att dessa rekommendationer tillämpas automatiskt och du inte behöver bry dig om underhålls aktiviteter. Med SQL Database Advisor kan du automatiskt implementera index rekommendationer baserat på din arbets belastning – detta kallas automatisk justering. Rekommendationerna utvecklas när din program arbets belastning ändras för att ge dig de mest relevanta förslagen. Du får också möjlighet att manuellt granska dessa rekommendationer och tillämpa dem på egen hand.  

### <a name="security-optimization"></a>Säkerhets optimering

SQL Database tillhandahåller åtgärds bara säkerhets rekommendationer som hjälper dig att skydda dina data och hot identifiering för att identifiera och undersöka misstänkta databas aktiviteter som kan utgöra en potentiell tråd till databasen. [Sårbarhets bedömning](sql-vulnerability-assessment.md) är en databas genomsöknings-och rapporterings tjänst som gör att du kan övervaka säkerhets status för dina databaser i stor skala och identifiera säkerhets risker och avvikelser från en säkerhets bas linje som definierats av dig. Efter varje genomsökning tillhandahålls en anpassad lista över åtgärds bara steg och reparations skript, samt en utvärderings rapport som kan användas för att uppfylla kraven för efterlevnad.

Med Azure Security Center identifierar du säkerhets rekommendationerna i tavlan och tillämpar dem med ett enda klick.

### <a name="cost-optimization"></a>Kostnadsoptimering

Azure SQL-plattformen analyserar användnings historiken över databaserna på en server för att utvärdera och rekommendera alternativ för kostnads optimering åt dig. Den här analysen tar vanligt vis en Fortnight för att analysera och utveckla rekommendationer som kan åtgärdas. Elastisk pool är ett sådant alternativ. Rekommendationen visas i portalen som en banderoll:

![rekommendationer för elastisk pool](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Du kan också visa den här analysen i avsnittet "Advisor":

![rekommendationer för elastisk pool – Advisor](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Hur gör jag för att övervaka prestanda-och resursutnyttjande i SQL Database

I SQL Database kan du utnyttja intelligenta insikter på plattformen för att övervaka prestanda och justera dem på motsvarande sätt. Du kan övervaka prestanda-och resursutnyttjande i SQL Database med följande metoder:

#### <a name="azure-portal"></a>Azure Portal

Azure Portal visar en Databass användning genom att välja databasen och klicka på diagrammet i fönstret Översikt. Du kan ändra diagrammet för att visa flera mått, inklusive processor procent, DTU-procent, data-IO-procent, sessioner procent och databas storlek i procent.

![Övervaknings diagram](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Övervakar CHART2](./media/manage-data-after-migrating-to-database/chart.png)

I det här diagrammet kan du också konfigurera aviseringar per resurs. Med de här aviseringarna kan du svara på resurs villkor med ett e-postmeddelande, skriva till en HTTPS/HTTP-slutpunkt eller utföra en åtgärd. Mer information finns i [skapa aviseringar](alerts-insights-configure-portal.md).

#### <a name="dynamic-management-views"></a>Dynamiska hanteringsvyer

Du kan köra en fråga [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamisk hanterings vy för att returnera statistik historik för resurs förbrukning från den senaste timmen och vyn [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) system katalog för att returnera historik under de senaste 14 dagarna.

#### <a name="query-performance-insight"></a>Query Performance Insight

Med [query Performance Insight](query-performance-insight-use.md) kan du Visa en historik över de mest resurs krävande frågorna och tids krävande frågor för en speciell databas. Du kan snabbt identifiera de vanligaste frågorna genom resursutnyttjande, varaktighet och frekvens för körning. Du kan spåra frågor och identifiera regression. Den här funktionen kräver att [frågearkivet](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) aktive ras och aktive ras för databasen.

![Query Performance Insight](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL-analys (för hands version) i Azure Monitor loggar

Med [Azure Monitor loggar](../../azure-monitor/insights/azure-sql.md) kan du samla in och visualisera viktiga Azure SQL Database prestanda mått, stöd för upp till 150 000 databaser och 5 000 ELASTISKa SQL-pooler per arbets yta. Du kan använda den för att övervaka och ta emot meddelanden. Du kan övervaka SQL Database och Elastic pool-mått över flera Azure-prenumerationer och elastiska pooler och kan användas för att identifiera problem på varje skikt i en programs tack.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Jag är märker prestanda problem: Hur skiljer sig SQL Database fel söknings metoden från SQL Server

En större del av de fel söknings tekniker som du skulle använda för att diagnostisera problem med frågor och databas prestanda är desamma. När alla samma databas motor har samma befogenheter som molnet. Men plattforms Azure SQL Database har skapat i "Intelligence". Det kan hjälpa dig att felsöka och diagnostisera prestanda problem ännu enklare. Den kan också utföra några av dessa korrigerande åtgärder för din räkning och i vissa fall åtgärda dem proaktivt automatiskt.

Din metod för fel sökning av prestanda problem kan avsevärt utnyttjas med hjälp av intelligenta funktioner som [query Performance Insight (QPI)](query-performance-insight-use.md) och [Database Advisor](database-advisor-implement-performance-recommendations.md) i samband med varandra, och därför skiljer sig skillnaden i metodiken – du behöver inte längre utföra det manuella arbetet med att ta bort den grundläggande information som kan hjälpa dig att felsöka problemet. Plattformen fungerar hårt för dig. Ett exempel på detta är QPI. Med QPI kan du öka detalj nivån till frågans nivå och titta på de historiska trenderna och räkna ut när exakt frågan försämrat. I Database Advisor får du rekommendationer om saker som kan hjälpa dig att förbättra den övergripande prestandan i generella, t. ex. index som saknas, släppa index, parametrar för dina frågor osv.

Med prestanda fel sökning är det viktigt att identifiera om det bara är programmet eller databasen som säkerhetskopierar, vilket påverkar programmets prestanda. Ofta beror prestanda problemet på program nivån. Det kan vara arkitekturen eller data åtkomst mönstret. Anta till exempel att du har ett chatt-program som är känsligt för nätverks fördröjning. I det här fallet är ditt program lidande eftersom det skulle finnas många korta begär Anden som kommer fram och tillbaka ("samtal") mellan programmet och servern och i ett överbelastat nätverk, så tur och retur lägger upp snabbt. För att förbättra prestandan i det här fallet kan du använda [batch-frågor](performance-guidance.md#batch-queries). Genom att använda batchar får du en fantastisk funktion eftersom dina begär Anden bearbetas i en batch. Därför hjälper du att minska tiden för roundtrip-svar och förbättra programmets prestanda.

Om du ser en försämring i databasens övergripande prestanda kan du också övervaka [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) och [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiska hanterings vyer för att förstå CPU-, IO-och minnes förbrukning. Dina prestanda kan påverkas eftersom databasen är har av resurser. Det kan vara att du kan behöva ändra beräknings storlek och/eller tjänste nivå baserat på de växande och förminskande arbets belastnings kraven.

En omfattande uppsättning rekommendationer för att justera prestanda problem finns i: [finjustera din databas](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Hur gör jag för att se till att jag använder rätt tjänst nivå och beräknings storlek

SQL Database erbjuder olika tjänst nivåer Basic, standard och Premium. Varje tjänst nivå får du en garanterad förutsägbar prestanda som är kopplad till tjänst nivån. Beroende på din arbets belastning kan du ha mellanstora aktiviteter där resursutnyttjande kan träffa taket för den aktuella beräknings storlek som du befinner dig i. I sådana fall är det bra att först börja med att utvärdera om någon justering kan hjälpa (till exempel att lägga till eller ändra ett index osv.). Om du fortfarande stöter på gräns problem bör du överväga att flytta till en högre tjänst nivå eller en beräknings storlek.

|**Tjänstenivå**|**Vanliga scenarier för användnings fall**|
|---|---|
|**Basic**|Program med fåtal-användare och en databas som inte har höga samtidighets-, skalnings-och prestanda krav. |
|**Standard**|Program med avsevärda samtidighets-, skalnings-och prestanda krav i kombination med låg till medelhög IO-krav. |
|**Premium**|Program med många samtidiga användare, höga processor/minne och höga IO-krav. Hög samtidighet, högt data flöde och svars tids känsliga appar kan utnyttja Premium nivån. |
|||

För att se till att du har rätt beräknings storlek kan du övervaka din fråga och databas resursförbrukning genom att använda ett av ovanstående sätt i "Hur gör jag för att övervaka prestanda-och resursutnyttjande i SQL Database". Om du upptäcker att dina frågor/databaser körs konsekvent på processor/minne osv. kan du skala upp till en högre beräknings storlek. På samma sätt verkar du inte använda resurserna lika mycket, om du är medveten om att även under dina arbets belastningar. Överväg att skala ned från den aktuella beräknings storleken.

Om du har ett SaaS app-mönster eller ett scenario för databas konsolidering bör du överväga att använda en elastisk pool för kostnads optimering. Elastisk pool är ett bra sätt att uppnå databas konsolidering och kostnads optimering. Läs mer om hur du hanterar flera databaser med elastisk pool i: [hantera pooler och databaser](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Hur ofta behöver jag köra databas integritets kontroller för min databas

SQL Database använder vissa smarta tekniker som gör det möjligt för IT att hantera vissa klasser av data som skadas automatiskt och utan data förlust. Dessa tekniker är inbyggda i tjänsten och utnyttjas av tjänsten när det behövs. Med jämna mellanrum testas dina databas säkerhets kopieringar i tjänsten genom att återställa dem och köra DBCC CHECKDB på den. Om det finns problem SQL Database proaktiva adresser. [Automatisk sid reparation](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) utnyttjas för att åtgärda sidor som är skadade eller som har problem med data integriteten. Databas sidorna verifieras alltid med standardinställningen för kontroll summa som verifierar sidans integritet. SQL Database övervakar och granskar data integriteten för databasen och, om det uppstår problem, adresser dem med högst prioritet. Förutom dessa kan du välja att eventuellt köra dina egna integritets kontroller.  Mer information finns [i data integritet i SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Data förflyttning efter migrering

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Hur gör jag för att exportera och importera data som BACPAC-filer från SQL Database med hjälp av Azure Portal

- **Exportera**: du kan exportera databasen i Azure SQL Database som en BACPAC-fil från Azure Portal

   ![databas export](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Importera**: du kan också importera data som en BACPAC-fil till databasen i Azure SQL Database att använda Azure Portal.

   ![databas import](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Hur gör jag för att synkronisera data mellan SQL Database och SQL Server

Det finns flera sätt att åstadkomma detta:

- **[Datasynkronisering](sql-data-sync-data-sql-server-sql-database.md)** – med den här funktionen kan du synkronisera data bi-riktning mellan flera SQL Server-databaser och SQL Database. Om du vill synkronisera med SQL Server databaser måste du installera och konfigurera Sync-agenten på en lokal dator eller en virtuell dator och öppna den utgående TCP-porten 1433.
- **[Transaktions replikering](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – med transaktions replikering kan du synkronisera data från en SQL Server databas till Azure SQL Database med SQL Server-instansen är utgivaren och Azure SQL Database som prenumerant. För närvarande stöds endast den här installationen. Mer information om hur du migrerar data från en SQL Server-databas till Azure SQL med minimal stillestånds tid finns i: [använda Transaction Replication](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Nästa steg

Läs mer om [SQL Database](sql-database-paas-overview.md).