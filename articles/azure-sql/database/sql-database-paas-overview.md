---
title: Vad är tjänsten Azure SQL Database?
description: 'Få en introduktion till SQL Database: teknisk information och funktioner i Microsoft Relations databas hanterings system (RDBMS) i molnet.'
keywords: introduktion till sql, sql-introduktion, vad är sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: c6b1129ca4a63e9adc8bb679dce53ff728456f42
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601839"
---
# <a name="what-is-azure-sql-database"></a>Vad är Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database är en fullständigt hanterad databasmotor som är en PaaS-tjänst (plattform som en tjänst) som hanterar de flesta av databashanteringsfunktionerna, till exempel uppgradering, korrigering, säkerhetskopiering och övervakning utan inblandning av användaren. Azure SQL Database körs alltid på den senaste stabila versionen av SQL Server-databasmotorn och ett korrigerat operativsystem med 99,99 % tillgänglighet. PaaS-funktioner som är inbyggda i Azure SQL Database gör att du kan fokusera på de domänspecifika databasadministrations- och optimeringsaktiviteter som är viktiga för ditt företag.

Med Azure SQL Database kan du skapa ett lager för datalagring med hög tillgänglighet och höga prestanda för program och lösningar i Azure. SQL Database kan vara det rätta valet för en mängd moderna moln program eftersom det gör att du kan bearbeta både Relations data och [icke-relationella strukturer](../multi-model-features.md), till exempel grafer, JSON, spatial och XML.

Azure SQL Database baseras på den senaste stabila versionen av [Microsoft SQL Server databas motorn](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). Du kan använda avancerade funktioner för frågekörning, till exempel [högpresterande minnes teknologier](../in-memory-oltp-overview.md) och [intelligent bearbetning av frågor](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). I själva verket lanseras de nyaste funktionerna i SQL Server först till SQL Database och sedan till SQL Server. Du får de senaste SQL Server-funktionerna utan kostnad för uppdatering eller uppgradering, testat för miljontals databaser. 

Med SQL Database kan du enkelt definiera och skala prestanda inom två olika inköps modeller: en [vCore-baserad inköps modell](service-tiers-vcore.md) och en [DTU-baserad inköps modell](service-tiers-dtu.md). SQL Database är en fullständigt hanterad tjänst som har inbyggd hög tillgänglighet, säkerhetskopiering och andra vanliga underhållsåtgärder. Microsoft hanterar alla korrigeringar och uppdateringar av SQL-och operativ system koden. Du behöver inte hantera den underliggande infrastrukturen.

Om du inte har använt Azure SQL Database kan du titta närmare på *Azure SQL Database översikts* video från vår djupgående [Azure SQL-video serie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Hur kan vi göra Azure SQL bättre? [Ta undersökningen](https://aka.ms/AzureSQLSurvey).

## <a name="deployment-models"></a>Distributionsmodeller

Azure SQL Database tillhandahåller följande distributions alternativ för en databas:

- En [enskild databas](single-database-overview.md) representerar en fullständigt hanterad, isolerad databas. Du kan använda det här alternativet om du har moderna moln program och mikrotjänster som behöver en enda tillförlitlig data källa. En enda databas liknar en [innesluten databas](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) i [SQL Server databas motorn](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- [Elastisk pool](elastic-pool-overview.md) är en samling av [enskilda databaser](single-database-overview.md) med en delad uppsättning resurser, till exempel processor eller minne. Enskilda databaser kan flyttas till och från en elastisk pool.

> [!IMPORTANT]
> Om du vill förstå funktions skillnaderna mellan SQL Database och SQL Server, och skillnaderna mellan olika Azure SQL Database alternativ, se [SQL Database funktioner](features-comparison.md).

SQL Database levererar förutsägbara prestanda med flera resurs typer, tjänst nivåer och beräknings storlekar. Den ger dynamisk skalbarhet utan drift avbrott, inbyggd intelligent optimering, global skalbarhet och tillgänglighet och avancerade säkerhets alternativ. Med de här funktionerna kan du fokusera på snabb app-utveckling och påskynda din tid till marknad, i stället för att hantera virtuella datorer och infrastruktur. SQL Database är för närvarande i 38-datacenter runtom i världen, så att du kan köra databasen i ett Data Center nära dig.

## <a name="scalable-performance-and-pools"></a>Skalbar prestanda och pooler

Du kan definiera mängden tilldelade resurser. 
- Med enkla databaser är varje databas isolerad från andra och är portabel. Var och en har sin egen garanterade mängd beräknings-, minnes-och lagrings resurser. Mängden resurser som är tilldelade till databasen är dedikerad till databasen och delas inte med andra databaser i Azure. Du kan skala upp och ned [enkla databas resurser](single-database-scale.md) dynamiskt. Alternativet enkel databas ger olika data bearbetnings-, minnes-och lagrings resurser för olika behov. Du kan till exempel hämta 1 till 80 virtuella kärnor eller 32 GB till 4 TB. Med den [storskaliga tjänst nivån](service-tier-hyperscale.md) för enskilda databaser kan du skala till 100 TB, med snabba säkerhets kopierings-och återställnings funktioner.
- Med elastiska pooler kan du tilldela resurser som delas av alla databaser i poolen. Du kan skapa en ny databas eller flytta befintliga enkla databaser till en resurspool för att maximera användningen av resurser och spara pengar. Med det här alternativet får du också möjlighet att dynamiskt [skala elastiska pool resurser](elastic-pool-scale.md) upp och ned.

Du kan bygga din första app på en liten, enkel databas till en låg kostnad per månad i tjänst nivån för generell användning. Du kan sedan ändra dess tjänst nivå manuellt eller program mässigt när som helst till den verksamhets kritiska tjänst nivån, för att uppfylla behoven hos din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser som du behöver när du behöver dem.

*Dynamisk skalbarhet* skiljer sig från *autoskalning*. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott. Alternativet enkel databas stöder manuell dynamisk skalbarhet, men inte autoskalning. Om du vill ha en mer automatisk upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov. Ett annat alternativ är att använda skript som kan hjälpa dig att automatisera skalbarheten för en enskild databas. Ett exempel finns i [använda PowerShell för att övervaka och skala en enskild databas](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Köpmodeller

SQL Database erbjuder följande inköps modeller:
- Med den [vCore-baserade inköps modellen](service-tiers-vcore.md) kan du välja antalet virtuella kärnor, mängden minne och lagrings hastigheten. Med den vCore-baserade inköps modellen kan du också använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) för att få kostnads besparingar. Mer information om Azure Hybrid-förmån finns i avsnittet Vanliga frågor och svar längre fram i den här artikeln.
- Den [DTU-baserade inköps modellen](service-tiers-dtu.md) erbjuder en blandning av beräknings-, minnes-och i/O-resurser i tre tjänst nivåer för att stödja ljus till tung databas arbets belastningar. Beräknings storlekar inom varje nivå ger en annan blandning av dessa resurser, där du kan lägga till ytterligare lagrings resurser.
- Den [serverbaserade modellen](serverless-tier-overview.md) skalar automatiskt beräkning baserat på arbets belastnings behov och fakturerar för mängden data bearbetning som används per sekund. Server lös beräknings nivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och återupptar automatiskt databaser när aktiviteten returnerar.

### <a name="service-tiers"></a>Tjänstnivåer

Azure SQL Database erbjuder tre tjänst nivåer som är utformade för olika typer av program:
- [Generell användning/standard](service-tier-general-purpose.md) service nivå som är avsedd för vanliga arbets belastningar. Det erbjuder budgetorienterade balanserade beräknings-och lagrings alternativ.
- [Affärskritisk/Premium](service-tier-business-critical.md) Service Tier som är utformad för OLTP-program med hög transaktions hastighet och I/O för lägsta svars tid. Den ger den högsta återhämtningen till problem genom att använda flera isolerade repliker.
- [Storskalig Service Tier](service-tier-hyperscale.md) som är utformad för en mycket stor OLTP-databas och möjligheten att skala lagrings utrymme och skala på ett smidigt och smidigt.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet

För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Oförutsägbara användnings mönster kan göra det svårt att hantera kostnader och din affärs modell. [Elastiska pooler](elastic-pool-overview.md) är utformade för att lösa detta problem. Du allokerar prestanda resurser till en pool i stället för en enskild databas. Du betalar för poolens samlade prestanda resurser snarare än för enkel databas prestanda.

   ![Bild som visar elastiska pooler i Basic-, standard-och Premium-versioner](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Med elastiska pooler behöver du inte fokusera på att slå upp och ned prestanda för att utföra resurser. Pooldatabaserna förbrukar den elastiska poolens prestandaresurser efter behov. Databaser i pooler förbrukar, men överskrider inte begränsningarna i poolen, så dina kostnader är förutsägbara även om den enskilda databas användningen inte är det.

Du kan [lägga till och ta bort databaser i poolen](elastic-pool-overview.md)och skala din app från en fåtal databaser till tusentals, allt inom en budget som du styr. Du kan också kontrol lera minimi-och Max resurserna som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla pool resurser och att varje databas i poolen har en garanterad minimal mängd resurser. Mer information om design mönster för SaaS-program (program vara som en tjänst) som använder elastiska pooler finns i [design mönster för SaaS-program med flera innehavare med SQL Database](saas-tenancy-app-design-patterns.md).

Skript kan hjälpa dig med att övervaka och skala elastiska pooler. Ett exempel finns i [använda PowerShell för att övervaka och skala en elastisk pool i Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Blanda enkla databaser med pooldatabaser

Du kan blanda enskilda databaser med elastiska pooler och ändra tjänst nivåerna för enskilda databaser och elastiska pooler för att anpassa sig till din situation. Du kan också kombinera och matcha andra Azure-tjänster med SQL Database för att möta dina unika design behov för appar, öka kostnaderna och resurs effektiviteten och låsa upp nya affärs möjligheter.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Omfattande övervakning och aviseringsfunktioner

Azure SQL Database tillhandahåller avancerade övervaknings-och fel söknings funktioner som hjälper dig att få djupare insikt i arbets Belastningens egenskaper. Följande funktioner och verktyg är:
 - De inbyggda övervaknings funktioner som tillhandahålls av den senaste versionen av SQL Server databas motorn. De gör att du kan hitta prestanda insikter i real tid. 
 - PaaS övervaknings funktioner som tillhandahålls av Azure och som hjälper dig att övervaka och felsöka ett stort antal databas instanser.

[Query Store](/sql/relational-databases/performance/best-practice-with-the-query-store), en inbyggd SQL Server övervaknings funktion, registrerar prestanda för dina frågor i real tid och gör det möjligt att identifiera potentiella prestanda problem och de främsta resurs förbrukarna. Automatisk justering och rekommendationer ger råd om frågor med försämrat prestanda och saknade eller duplicerade index. Med automatisk justering i SQL Database kan du antingen manuellt tillämpa skript som kan åtgärda problemen eller låta SQL Database tillämpa korrigeringen. SQL Database kan också testa och kontrol lera att korrigeringen ger viss förmån och behålla eller återställa ändringen beroende på resultatet. Förutom fråge Arkiv och automatiska justerings funktioner kan du använda standard [DMV: er och XEvent](monitoring-with-dmvs.md) för att övervaka arbets Belastningens prestanda.

Azure innehåller [inbyggda verktyg för prestanda övervakning](performance-guidance.md) och [avisering](alerts-insights-configure-portal.md) , kombinerat med prestanda klassificeringar som gör att du kan övervaka status för tusentals databaser. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ned baserat på dina aktuella eller planerade prestanda behov. Dessutom kan SQL Database [generera mått och resurs loggar](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) för enklare övervakning. Du kan konfigurera SQL-databasen för att lagra resursanvändning, personal och sessioner och anslutning till en av dessa Azure-resurser:

- **Azure Storage**: för att arkivera stora mängder telemetri för ett litet pris.
- **Azure Event Hubs**: för att integrera SQL Database telemetri med din anpassade övervaknings lösning eller aktiva pipeliner.
- **Azure Monitor loggar**: för en inbyggd övervaknings lösning med rapporter, aviseringar och mildrande funktioner.

![Diagram över Azures övervaknings arkitektur](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Azure SQL Database gör det möjligt för ditt företag att fortsätta arbeta under avbrott. I en traditionell SQL Servers miljö har du normalt minst två datorer som är lokalt konfigurerade. De här datorerna har exakta, synkront underhåll, kopior av data för att skydda mot ett haveri på en enskild dator eller komponent. Den här miljön ger hög tillgänglighet, men den skyddar inte mot en natur haveri förstör ditt data Center.

Haveri beredskap förutsätter att en oåterkallelig händelse är geografiskt lokaliserad för att ha en annan dator eller uppsättning datorer med en kopia av dina data långt bort. I SQL Server kan du använda Always on-tillgänglighetsgrupper som körs i asynkront läge för att få den här funktionen. Användare vill ofta inte vänta på att replikeringen ska ske innan den genomför en transaktion, så det är möjligt att förlora data när du gör oplanerad redundans.

Databaser i tjänst nivåerna Premium och Affärskritisk har redan ett [liknande sätt](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) som synkroniseringen av en tillgänglighets grupp. Databaser med lägre tjänst nivåer ger redundans genom att använda en [annan men likvärdig mekanism](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability). Inbyggd logik skyddar mot ett enskilt dator haveri. Funktionen aktiv geo-replikering ger dig möjlighet att skydda mot katastrofer där en hel region förstörs.

Azure-tillgänglighetszoner försöker skydda mot avbrott i en enda data centers byggnad inom en enda region. Det hjälper dig att skydda mot förlust av ström eller nätverk till en byggnad. I SQL Database placerar du de olika replikerna i olika tillgänglighets zoner (olika byggnader, effektivt).

Service avtalet [(SLA)](https://azure.microsoft.com/support/legal/sla/) för Azure, som drivs av ett globalt nätverk av Microsoft-hanterade data Center, hjälper till att hålla appen igång 24/7. Azure-plattformen hanterar fullständigt alla databaser och garanterar ingen data förlust och en hög procent andel data tillgänglighet. Azure hanterar automatiskt korrigering, säkerhets kopiering, replikering, fel identifiering, underliggande potentiell maskin vara, program vara eller nätverks fel, distribution av fel korrigeringar, redundans, databas uppgraderingar och andra underhålls aktiviteter. Standardtillgänglighet uppnås genom en uppdelning av beräknings och lagringslager. Premium-tillgänglighet uppnås genom att integrera beräkning och lagring på en enda nod för prestanda och sedan implementera teknik som liknar Always on-tillgänglighetsgrupper. En fullständig beskrivning av funktionerna för hög tillgänglighet i Azure SQL Database finns [SQL Database tillgänglighet](high-availability-sla.md). 

Dessutom ger SQL Database inbyggd [affärs kontinuitet och globala skalbarhets](business-continuity-high-availability-disaster-recover-hadr-overview.md) funktioner. Dessa omfattar:

- [Automatisk säkerhets kopiering](automated-backups-overview.md):

  SQL Database automatiskt utför fullständiga, differentiella och transaktions logg säkerhets kopior av databaser så att du kan återställa till vilken tidpunkt som helst. För enskilda databaser och databaser i pooler kan du konfigurera SQL Database att lagra fullständiga säkerhets kopior av databasen till Azure Storage för långsiktig kvarhållning av säkerhets kopior. För hanterade instanser kan du även utföra säkerhets kopieringar för långsiktig kvarhållning av säkerhets kopior.

- [Återställning av](recovery-using-backups.md)tidpunkt:

  Alla SQL Database distributions alternativ stöder återställning till vilken tidpunkt som helst inom den automatiska kvarhållningsperioden för säkerhets kopiering för alla databaser.
- [Aktiv geo-replikering](active-geo-replication-overview.md):

  Med alternativen för databas och pooler med flera databaser kan du konfigurera upp till fyra läsbara sekundära databaser i antingen samma eller globalt distribuerade Azure-datacenter. Om du till exempel har ett SaaS-program med en katalog databas som har en stor mängd samtidiga skrivskyddade transaktioner, använder du aktiv geo-replikering för att aktivera global Läs skalning. Detta tar bort Flask halsar på den primära som beror på Läs arbets belastningar. För hanterade instanser använder du grupper för automatisk redundans.
- [Grupper för automatisk redundans](auto-failover-group-overview.md):

  Med alla SQL Database distributions alternativ kan du använda grupper för växling vid fel för att aktivera hög tillgänglighet och belastnings utjämning i global skala. Detta inkluderar transparent geo-replikering och redundans av stora mängder databaser, elastiska pooler och hanterade instanser. Med växlings grupper kan du skapa globalt distribuerade SaaS-program med minimal administration. Detta lämnar all komplex övervakning, Routning och redundanskluster till SQL Database.
- [Zoner-redundanta databaser](high-availability-sla.md):

  Med SQL Database kan du etablera Premium-eller Affärskritisk-databaser eller elastiska pooler över flera tillgänglighets zoner. Eftersom dessa databaser och elastiska pooler har flera redundanta repliker för hög tillgänglighet ger repliker i flera tillgänglighets zoner högre återhämtning. Detta inkluderar möjligheten att återställa automatiskt från data centers skalnings fel utan data förlust.

## <a name="built-in-intelligence"></a>Inbyggd intelligens

Med SQL Database får du inbyggd information som hjälper dig att dramatiskt minska kostnaderna för att köra och hantera databaser och som maximerar programmets prestanda och säkerhet. Genom att köra miljon tals kund arbets belastningar dygnet runt, SQL Database samla in och bearbeta en enorm mängd telemetridata, samtidigt som kunden är helt respekterad. Olika algoritmer utvärderar kontinuerligt telemetri-data så att tjänsten kan lära sig och anpassa sig till ditt program.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisk prestandaövervakning och justering

SQL Database ger detaljerad inblick i frågor som du behöver övervaka. SQL Database lär dig mer om dina databas mönster och gör det möjligt att anpassa ditt databas schema till din arbets belastning. SQL-databasen innehåller [rekommendationer för prestandajustering](database-advisor-implement-performance-recommendations.md) där du kan granska justeringsåtgärder och använda dem.

Att ständigt övervaka en databas är dock en svår och omständlig uppgift, särskilt när du hanterar många databaser. [Intelligent Insights](intelligent-insights-overview.md) gör jobbet åt dig genom att automatiskt övervaka SQL Database prestanda i hög skala. Det informerar om prestanda försämrings problem, den identifierar rotor saken för varje problem och ger rekommendationer om prestanda förbättring när det är möjligt.

Att hantera ett stort antal databaser kan vara omöjligt att effektivisera även med alla tillgängliga verktyg och rapporter som SQL Database och Azure tillhandahåller. I stället för att övervaka och justera databasen manuellt, kan du delegera vissa av övervaknings-och justerings åtgärderna till SQL Database med hjälp av [Automatisk justering](automatic-tuning-overview.md). SQL Database använder automatiskt rekommendationer, tester och verifierar var och en av dess justerings åtgärder för att säkerställa att prestandan förbättras. På så sätt kan SQL Database automatiskt anpassas efter din arbets belastning på ett kontrollerat och säkert sätt. Automatisk justering innebär att databasens prestanda övervakas noga och jämförs före och efter varje justerings åtgärd. Om prestandan inte förbättras återställs justerings åtgärden.

Många av våra partner som kör [SaaS för flera klient](saas-tenancy-app-design-patterns.md) organisationer ovanpå SQL Database förlitar sig på automatisk prestanda justering för att säkerställa att deras program alltid har stabila och förutsägbara prestanda. För dessa minskar funktionen risken för en prestandaincident mitt i natten. Eftersom en del av deras kund Base också använder SQL Server, använder de samma indexerings rekommendationer som tillhandahålls av SQL Database för att hjälpa sina SQL Server kunder.

Det finns två automatiska justerings aspekter [i SQL Database](automatic-tuning-overview.md):

- **Automatisk indexhantering**: Identifierar index som bör läggas till i din databas och index som ska tas bort.
- **Automatisk plan korrigering**: identifierar problematiska planer och åtgärdar prestanda problem i SQL-plan.

### <a name="adaptive-query-processing"></a>Anpassningsbar frågebearbetning

Du kan använda [anpassad bearbetning av frågor](/sql/relational-databases/performance/intelligent-query-processing), inklusive överlagrad körning av tabell värdes funktioner med flera instruktioner, återkoppling av minnes bidrag i batch-läge och anpassningsbara kopplingar i batch-läge. Var och en av dessa funktioner för anpassad bearbetning av frågor gäller liknande "lär och anpassa"-tekniker, vilket hjälper ytterligare att lösa prestanda problem som är relaterade till tidigare indrags bara frågor om problemlösning.

## <a name="advanced-security-and-compliance"></a>Avancerad säkerhet och efterlevnad

SQL-databasen innehåller en uppsättning [inbyggda funktioner för säkerhet och efterlevnad](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) för att uppfylla olika krav för säkerhet och regelefterlevnad i programmet.

> [!IMPORTANT]
> Microsoft har certifierat Azure SQL Database (alla distributions alternativ) mot ett antal efterlevnads standarder. Mer information finns i [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.

### <a name="advance-threat-protection"></a>Skydd mot Avancerat skydd

Azure Defender för SQL är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Den innehåller funktioner för att hantera dina databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen. Det ger en enda plats för att aktivera och hantera dessa funktioner.

- [Sårbarhets bedömning](sql-vulnerability-assessment.md):

  Den här tjänsten kan upptäcka, spåra och hjälpa dig att åtgärda eventuella sårbarheter i databasen. Den ger inblick i ditt säkerhetstillstånd och inkluderar lämpliga åtgärder för att lösa säkerhetsproblem och förbättra databasens skydd.
- [Hot identifiering](threat-detection-configure.md):

  Den här funktionen identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databasen. Den övervakar kontinuerligt databasen efter misstänkta aktiviteter och ger omedelbara säkerhetsaviseringar om potentiella säkerhetsproblem, SQL-inmatningsattacker samt avvikande åtkomstmönster i databasen. Skydd mot hot ger detaljerad information om den misstänkta aktiviteten och rekommenderar åtgärder för att undersöka och minska risken.

### <a name="auditing-for-compliance-and-security"></a>Granskning för efterlevnad och säkerhet

[Granskning](../../azure-sql/database/auditing-overview.md) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

### <a name="data-encryption"></a>Datakryptering

SQL Database hjälper till att skydda dina data med hjälp av kryptering. För data i rörelse används [Transport Layer Security](https://support.microsoft.com/kb/3135244). För data i vila används [transparent data kryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). För data som används används [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Dataidentifiering och -klassificering

[Data identifiering och klassificering](data-discovery-and-classification-overview.md) innehåller funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera, märka och skydda känsliga data i dina databaser. Den ger insyn i databas klassificerings tillstånd och spårar åtkomsten till känsliga data i databasen och utanför dess gränser.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integrering och multifaktorautentisering

Med SQL Database kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster med [Azure Active Directory-integrering](authentication-aad-overview.md). Den här funktionen förenklar hanteringen av behörighet och ger ökad säkerhet. Azure Active Directory stöder [Multi-Factor Authentication](authentication-mfa-ssms-overview.md) för att öka säkerheten för data och program, samtidigt som den har stöd för en enda inloggnings process.

## <a name="easy-to-use-tools"></a>Användarvänliga verktyg

SQL Database gör byggandet och underhållet av appar enklare och mer produktivt. Med SQL Database kan du fokusera på det du gör väst: bygga bra appar. Du kan hantera och utveckla i SQL Database med hjälp av verktyg och färdigheter som du redan har.

|Verktyg|Beskrivning|
|:---|:---|
|[Azure-portalen](https://portal.azure.com/)|Ett webbaserat program för att hantera alla Azure-tjänster.|
|[Azure Data Studio](/sql/azure-data-studio/)|Ett databas verktyg över plattformar som körs på Windows, macOS och Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Ett kostnads fritt, nedladdnings Bart klient program för att hantera alla SQL-infrastrukturer, från SQL Server till SQL Database.|
|[SQL Server Data Tools i Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)|Ett kostnads fritt, nedladdnings Bart klient program för att utveckla SQL Server Relations databaser, databaser i Azure SQL Database, Integration Services-paket, Analysis Services data modeller och repor ting Services-rapporter.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|En kostnads fri, nedladdnings bar kod redigerare med öppen källkod för Windows, macOS och Linux. Det stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för frågor Microsoft SQL Server, Azure SQL Database och Azure Azure Synapse Analytics.|

SQL Database har stöd för att skapa program med python, Java, Node.js, PHP, ruby och .NET på macOS, Linux och Windows. SQL Database stöder samma [anslutningsbibliotek](connect-query-content-reference-guide.md#libraries) som SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Vanliga frågor och svar om SQL Database

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan jag styra när uppdaterings stopp inträffar?

Nej. Effekten av korrigeringen är vanligt vis inte märkbart om du [använder logik för omprövning](develop-overview.md#resiliency) i din app. Mer information finns i [Planera för underhålls händelser i Azure i Azure SQL Database](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Tala med teknikteamet för SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Ställ frågor om databas administration.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Ställ frågor om utveckling.
- [Sidan Microsoft Q&en fråga](/answers/topics/azure-synapse-analytics.html): Ställ tekniska frågor.
- [Feedback](https://aka.ms/sqlfeedback): rapportera buggar och begär funktioner.
- [Reddit](https://www.reddit.com/r/SQLServer/): diskutera SQL Server.

## <a name="next-steps"></a>Nästa steg

- På [sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/) hittar du kostnads jämförelser och räknare för enskilda databaser och elastiska pooler.
- Kom igång genom att se följande snabb starter:

  - [Skapa en databas på Azure-portalen](single-database-create-quickstart.md)  
  - [Skapa en databas med Azure CLI](az-cli-script-samples-content-guide.md)
  - [Skapa en databas med PowerShell](powershell-script-content-guide.md)

- En uppsättning Azure CLI- och PowerShell-exempel finns här:
  - [Azure CLI-exempel för SQL Database](az-cli-script-samples-content-guide.md)
  - [Azure PowerShell-exempel för SQL Database](powershell-script-content-guide.md)

- Information om nya funktioner när de visas finns i Azure- [Översikt för SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Se [Azure SQL Database blogg](https://azure.microsoft.com/blog/topics/database)där SQL Server produkt Teams medlemmar bloggar om SQL Database nyheter och funktioner.