---
title: Referens arkitekturer för Oracle-databaser på Azure | Microsoft Docs
description: Refererar arkitekturer för att köra Oracle Database Enterprise Edition databaser på Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: bbaf34c977546891c6ac05fbd4b5feb15f333e04
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737820"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referens arkitekturer för Oracle Database Enterprise Edition på Azure

Den här guiden innehåller information om hur du distribuerar en Oracle-databas med hög tillgänglighet på Azure. Dessutom dykningar guiden om att tänka på haveri beredskap. Dessa arkitekturer har skapats baserat på kund distributioner. Den här guiden gäller endast för Oracle Database Enterprise Edition.

Om du är intresse rad av att lära dig mer om hur du maximerar prestandan för Oracle-databasen kan du läsa skapa [en Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Antaganden

- Du har en förståelse för de olika begreppen i Azure, till exempel [tillgänglighets zoner](../../../availability-zones/az-overview.md)
- Du kör Oracle Database Enterprise Edition 12C eller senare
- Du är medveten om och bekräftar licensierings konsekvenserna när du använder lösningarna i den här artikeln

## <a name="high-availability-for-oracle-databases"></a>Hög tillgänglighet för Oracle-databaser

Att uppnå hög tillgänglighet i molnet är en viktig del av varje organisations planering och design. Microsoft Azure erbjuder [tillgänglighets zoner](../../../availability-zones/az-overview.md) och tillgänglighets uppsättningar (som ska användas i regioner där tillgänglighets zoner inte är tillgängliga). Läs mer om att [Hantera tillgängligheten för dina virtuella datorer](../../manage-availability.md) för att utforma molnet.

Förutom Cloud-inbyggda verktyg och erbjudanden tillhandahåller Oracle lösningar för hög tillgänglighet som [Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [data Guard med FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [horisontell partitionering](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)och [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) som kan konfigureras i Azure. Den här guiden beskriver referens arkitekturer för var och en av dessa lösningar.

När du migrerar eller skapar program för molnet är det viktigt att du anpassar program koden för att lägga till molnets egna mönster, till exempel mönster för [återförsök](/azure/architecture/patterns/retry) och [krets brytare](/azure/architecture/patterns/circuit-breaker). Ytterligare mönster som definieras i [guiden för moln design mönster](/azure/architecture/patterns/) kan hjälpa ditt program att vara mer elastiskt.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC i molnet

Oracle Real Application Cluster (RAC) är en lösning av Oracle för att hjälpa kunder att uppnå stora data flöden genom att ha många instanser som har åtkomst till en databas lagring (delade – alla arkitektur mönster). Oracle RAC kan också användas för hög tillgänglighet lokalt, men Oracle RAC kan endast användas för hög tillgänglighet i molnet eftersom det bara skyddar mot instans nivå problem och inte mot problem som uppstår på radnivå eller data Center. Av den anledningen rekommenderar Oracle att använda Oracle data Guard med din databas (oavsett om det är en instans eller RAC) för hög tillgänglighet. Kunderna kräver vanligt vis ett högt service avtal för att köra sina verksamhets kritiska program. Oracle RAC är för närvarande inte certifierat eller stöds inte av Oracle på Azure. Azure erbjuder dock funktioner som Azure erbjuder Tillgänglighetszoner och planerat underhålls fönster som hjälper till att skydda mot fel på instans nivå. Förutom detta kan kunder använda tekniker som Oracle data Guard, Oracle GoldenGate och Oracle horisontell partitionering för hög prestanda och återhämtning genom att skydda sina databaser från racknivå samt data Center-och geo-politiska fel.

När du kör Oracle-databaser över flera [tillgänglighets zoner](../../../availability-zones/az-overview.md) tillsammans med Oracle data Guard eller GoldenGate, kan kunder få ett service avtal för drift tid på 99,99%. I Azure-regioner där tillgänglighets zoner ännu inte finns kan kunder använda [tillgänglighets uppsättningar](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) och uppnå ett service avtal för drift tid på 99,95%.

>Obs! Du kan ha ett drift tids mål som är mycket högre än service avtalet för drift tid som tillhandahålls av Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Haveri beredskap för Oracle-databaser

När du är värd för verksamhets kritiska program i molnet är det viktigt att utforma för hög tillgänglighet och katastrof återställning.

För Oracle Database Enterprise Edition är Oracle data Guard en användbar funktion för haveri beredskap. Du kan konfigurera en databas instans för vänte läge i en [länkad Azure-region](../../../best-practices-availability-paired-regions.md) och konfigurera data Guard-redundans för haveri beredskap. För att förhindra data förlust rekommenderar vi att du distribuerar en Oracle data Guard långt Sync-instans utöver Active data Guard. 

Överväg att konfigurera data Guard långt Sync-instansen i en annan tillgänglighets zon än den primära Oracle-databasen om ditt program tillåter svars tiden (grundlig testning krävs). Använd ett **maximalt tillgänglighets** läge om du vill konfigurera synkron transport av dina gör om-filer till den mycket synkroniserade instansen. Filerna överförs sedan asynkront till standby-databasen. 

Om ditt program inte tillåter prestanda förlust vid inställning av en avlägsen synkroniseringsrelation i en annan tillgänglighets zon i det **maximala tillgänglighets** läget (synkront), kan du konfigurera en mycket synkroniserad instans i samma tillgänglighets zon som din primära databas. Om du vill ha ytterligare tillgänglighet kan du överväga att konfigurera flera instanser av en instans nära din primära databas och minst en instans nära databasen för vänte läge (om roll över gångar). Läs mer om Oracle data Guard långt synkronisering i det här [Oracle Active data Guard-dokumentet för synkronisering](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

När du använder Oracle Standard Edition-databaser finns det ISV-lösningar som DBVisit standby som gör att du kan konfigurera hög tillgänglighet och haveri beredskap.

## <a name="reference-architectures"></a>Referensarkitekturer

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle data Guard garanterar hög tillgänglighet, data skydd och haveri beredskap för företags data. Data Guard upprätthåller vänte läges databaser som transaktionella konsekventa kopior av den primära databasen. Beroende på avståndet mellan de primära och sekundära databaserna och program toleransen för svars tid kan du konfigurera synkron eller asynkron replikering. Om den primära databasen inte är tillgänglig på grund av ett planerat eller oplanerat avbrott kan data Guard växla alla vänte läges databaser till den primära rollen och minimera stillestånds tiden. 

När du använder Oracle data Guard kan du också öppna den sekundära databasen i skrivskyddat syfte. Den här konfigurationen kallas Active data Guard. Oracle Database 12C introducerade en funktion som kallas data Guard långt Sync-instans. Med den här instansen kan du ställa in en noll data förlust konfiguration för Oracle-databasen utan att behöva kompromissa med prestanda.

> [!NOTE]
> Active data Guard kräver ytterligare licensiering. Den här licensen krävs också för att använda funktionen för mycket synkronisering. Kontakta din Oracle-representant för att diskutera konsekvenserna av licensen.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle data Guard med FSFO
Oracle data Guard med Fast-Start redundans (FSFO) kan ge ytterligare återhämtning genom att konfigurera koordinatorn på en annan dator. Data Guard Broker och den sekundära databasen båda kör observatören och studerar den primära databasen för stillestånds tid. Detta möjliggör även redundans i din data Guard-installationsprogram. 

Med Oracle Database version 12,2 och senare, är det också möjligt att konfigurera flera övervakare med en enda Oracle data Guard Broker-konfiguration. Den här installationen ger ytterligare tillgänglighet, i händelse av en övervakare och drift stopp i den sekundära databasen. Data Guard Broker är Lightweight och kan finnas på en relativt liten virtuell dator. Om du vill veta mer om data Guard Broker och dess fördelar kan du gå till [Oracle-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) i det här avsnittet.

Följande diagram är en rekommenderad arkitektur för att använda Oracle data Guard i Azure med tillgänglighets zoner. Den här arkitekturen gör att du kan få ett service avtal för VM-drift tid på 99,99%.

![Diagram som visar en rekommenderad arkitektur för användning av Oracle data Guard i Azure med tillgänglighets zoner.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

I föregående diagram ansluter klient systemet till ett anpassat program med Oracle-backend via webben. Webb klient delen har kon figurer ATS i en belastningsutjämnare. Webb klient delen gör ett anrop till rätt program Server för att hantera arbetet. Program servern frågar den primära Oracle-databasen. Oracle-databasen har kon figurer ATS med hjälp av en [optimerad virtuell dator](../../sizes-memory.md) med hög tråds teknik med [begränsade kärn virtuella processorer](../../../virtual-machines/constrained-vcpu.md) för att spara på licens kostnader och maximera prestanda. Flera Premium-eller Ultra-diskar (Managed Disks) används för prestanda och hög tillgänglighet.

Oracle-databaser placeras i flera tillgänglighets zoner för hög tillgänglighet. Varje zon består av ett eller flera data Center som är utrustade med oberoende strömförsörjning, kylning och nätverk. För att garantera återhämtning, konfigureras minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighets zoner inom en region skyddar data från data Center problem. Dessutom har två FSFO-observatörer kon figurer ATS över två tillgänglighets zoner för att initiera och redundansväxla databasen till den sekundära när ett avbrott inträffar. 

Du kan skapa ytterligare övervakare och/eller standby-databaser i en annan tillgänglighets zon (AZ 1, i det här fallet) än zonen som visas i föregående arkitektur. Slutligen övervakas Oracle-databaser för drift tid och prestanda av Oracle Enterprise Manager (OEM). Med OEM kan du också generera olika prestanda-och användnings rapporter.

I regioner där tillgänglighets zoner inte stöds kan du använda tillgänglighets uppsättningar för att distribuera Oracle Database på ett mycket tillgängligt sätt. Med tillgänglighets uppsättningar kan du få en VM-drifts tid på 99,95%. Följande diagram är en referens arkitektur för detta bruk:

![Oracle Database att använda tillgänglighets uppsättningar med data Guard Broker – FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Den virtuella Oracle Enterprise Manager-datorn behöver inte placeras i en tillgänglighets uppsättning eftersom det bara finns en instans av OEM som distribueras.
> * Ultra disks stöds för närvarande inte i en konfiguration med tillgänglighets uppsättningar.

#### <a name="oracle-data-guard-far-sync"></a>Oracle data Guard, lång synkronisering

Oracle data Guard långt Sync ger ingen skydds funktion för data förlust för Oracle-databaser. Den här funktionen gör att du kan skydda dig mot data förlust i om databas datorn slutar fungera. Oracle data Guard långt synkronisering måste installeras på en separat virtuell dator. Avlägsen synkronisering är en Lightweight Oracle-instans som bara har en kontroll fil, lösen ords fil, SPFile och standby-loggar. Det finns inga datafiler eller Rego-loggfiler. 

För inget skydd mot data förlust måste det finnas synkron kommunikation mellan din primära databas och den mycket synkroniserade instansen. Den mycket Sync-instansen tar emot en nysynkronisering från den primära på ett synkront sätt och vidarebefordrar den omedelbart till alla databaser som väntar på asynkront sätt. Den här konfigurationen minskar också omkostnaderna på den primära databasen, eftersom det bara behöver skicka om göra om till den mycket synkroniserade instansen i stället för alla databaser i vänte läge. Om en mycket synkroniserad instans Miss lyckas använder data Guard automatiskt asynkron transport till den sekundära databasen från den primära databasen för att bibehålla data förlust skyddet nära noll. För extra återhämtning kan kunderna distribuera flera instanser av en instans per databas instans (primär och sekundär).

Följande diagram är en arkitektur med hög tillgänglighet som använder Oracle data Guard långt synkronisering:

![Oracle Database med hjälp av tillgänglighets zoner med data Guard långt Sync & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

I föregående arkitektur finns en mycket synkroniserad instans som distribueras i samma tillgänglighets zon som databas instansen för att minska svars tiden mellan de två. I de fall där programmet är i vänte läge kan du överväga att distribuera databasen och en mycket synkroniserad instans eller instanser i en [närhets placerings grupp](../../../virtual-machines/linux/proximity-placement-groups.md).

Följande diagram är en arkitektur som använder Oracle data Guard-FSFO och mycket synkronisering för att uppnå hög tillgänglighet och katastrof återställning:

![Oracle Database att använda tillgänglighets zoner för haveri beredskap med data Guard långt Sync & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate möjliggör utbyte och manipulering av data på transaktions nivå mellan flera heterogena plattformar i företaget. Den flyttar genomförda transaktioner med transaktions integritet och minimal belastning på din befintliga infrastruktur. Dess modulära arkitektur ger dig flexibilitet att extrahera och replikera valda data poster, transaktions ändringar och ändringar av DDL (Data Definition Language) över flera olika topologier.

Med Oracle-GoldenGate kan du konfigurera databasen för hög tillgänglighet genom att tillhandahålla dubbelriktad replikering. På så sätt kan du konfigurera en konfiguration **med flera huvud** servrar eller **aktiv-aktiv**. Följande diagram är en rekommenderad arkitektur för Oracle GoldenGate-aktiv-aktiv-installation på Azure. I följande arkitektur har Oracle-databasen kon figurer ATS med hjälp av en högtrådad [minnes optimerad virtuell dator](../../sizes-memory.md) med [begränsade kärn virtuella processorer](../../../virtual-machines/constrained-vcpu.md) för att spara på licens kostnaderna och maximera prestandan. Flera Premium-eller Ultra-diskar (Managed Disks) används för prestanda och tillgänglighet.

![Oracle Database att använda tillgänglighets zoner med data Guard Broker – FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> En liknande arkitektur kan konfigureras med hjälp av tillgänglighets uppsättningar i regioner där tillgänglighets zoner inte är tillgängliga för närvarande.

Oracle-GoldenGate har processer som extrahering, pump och replikering som hjälper dig att replikera data asynkront från en Oracle-databasserver till en annan. Med de här processerna kan du konfigurera en dubbelriktad replikering för att säkerställa hög tillgänglighet för din databas om det finns tillgänglighets avbrott på zon nivån. I det föregående diagrammet körs extraherings processen på samma server som Oracle-databasen, medan data pumpen och de replikerade processerna körs på en separat server i samma tillgänglighets zon. Den replikerade processen används för att ta emot data från databasen i den andra tillgänglighets zonen och allokera data till Oracle-databasen i sin tillgänglighets zon. På samma sätt skickar data pump processen data som har extraherats av extraherings processen till den replikerade processen i den andra tillgänglighets zonen. 

Medan det föregående arkitektur diagrammet visar data pumpen och replik processen som kon figurer ATS på en separat server, kan du konfigurera alla Oracle GoldenGate-processer på samma server, baserat på serverns kapacitet och användning. Se alltid din AWR-rapport och måtten i Azure för att förstå användnings mönstret för servern.

När du konfigurerar en dubbelriktad replikering av Oracle-GoldenGate i olika tillgänglighets zoner eller i olika regioner, är det viktigt att se till att svars tiden mellan de olika komponenterna är acceptabel för ditt program. Svars tiden mellan tillgänglighets zoner och regioner kan variera beroende på flera faktorer. Vi rekommenderar att du konfigurerar prestandatester mellan program nivån och databas nivån i olika tillgänglighets zoner och/eller regioner för att bekräfta att det uppfyller dina program prestanda krav.

Program nivån kan konfigureras i sitt eget undernät och databas nivån kan delas upp i sitt eget undernät. När det är möjligt bör du överväga att använda [Azure Application Gateway](../../../application-gateway/overview.md) för belastnings Utjämnings trafik mellan program servrarna. Azure Application Gateway är en robust belastnings utjämning för webb trafik. Den tillhandahåller cookie-baserad sessionsgräns som håller en användarsession på samma server, vilket minimerar konflikterna i databasen. Alternativ för Application Gateway är [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) och [Azure-Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle-horisontell partitionering

Horisontell partitionering är ett mönster för data nivåer som introducerades i Oracle 12,2. Du kan använda den för att vågrätt partitionera och skala data mellan oberoende databaser. Det är en resurs-Nothing-arkitektur där varje databas finns på en dedikerad virtuell dator, vilket möjliggör hög Läs-och Skriv data flöde förutom återhämtning och ökad tillgänglighet. Det här mönstret eliminerar enskilda fel punkter, ger fel isolering och möjliggör löpande uppgraderingar utan drift avbrott. Stillestånds tiden för en Shard eller ett data center nivå haveri påverkar inte prestanda eller tillgänglighet för andra Shards i andra data Center. 

Horisontell partitionering är lämpligt för Högflödes-OLTP-program som inte kan ge några drift avbrott. Alla rader med samma horisontell partitionering-nyckel garanteras alltid vara på samma Shard, vilket ökar prestandan som ger hög konsekvens. Program som använder horisontell partitionering måste ha en väldefinierad data modell och strategi för data distribution (konsekvent hash, intervall, lista eller sammansatt) som främst har åtkomst till data med hjälp av en horisontell partitionering-nyckel (till exempel *customerId* eller *accountNum*). Med horisontell partitionering kan du också lagra specifika data uppsättningar närmare slutanvändarna, vilket hjälper dig att uppfylla dina krav på prestanda och efterlevnad.

Vi rekommenderar att du replikerar din Shards för hög tillgänglighet och haveri beredskap. Den här installationen kan göras med hjälp av Oracle-tekniker som Oracle data Guard eller Oracle GoldenGate. En enhet med replikering kan vara en Shard, en del av en Shard eller en grupp av Shards. Tillgängligheten för en shardade-databas påverkas inte av ett avbrott eller en minskning av en eller flera Shards. För hög tillgänglighet kan standby-Shards placeras i samma tillgänglighets zon där den primära Shards placeras. För haveri beredskap kan standby-Shards finnas i en annan region. Du kan också distribuera Shards i flera regioner för att betjäna trafik i dessa regioner. Läs mer om hur du konfigurerar hög tillgänglighet och replikering av din shardade-databas i [Oracle horisontell partitionering-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle-horisontell partitionering består främst av följande komponenter. Mer information om dessa komponenter finns i [Oracle horisontell partitionering-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Shard-katalog** – särskild Oracle-databas för särskilda ändamål som är ett beständigt arkiv för alla Shard-databas konfigurations data. Alla konfigurations ändringar, till exempel att lägga till eller ta bort Shards, mappning av data och DDLs i en Shard-databas initieras i Shard-katalogen. Shard-katalogen innehåller också huvud kopian av alla duplicerade tabeller i en SDB. 

  Shard-katalogen använder materialiserade vyer för att automatiskt replikera ändringar till duplicerade tabeller i alla Shards. Shard Catalog-databasen fungerar också som en frågeplan som används för att bearbeta frågor i flera Shard och frågor som inte anger en horisontell partitionering nyckel. 
  
  Att använda Oracle data Guard tillsammans med tillgänglighets zoner eller tillgänglighets uppsättningar för Shard-katalogen är en rekommenderad metod. Tillgängligheten för Shard-katalogen påverkar inte tillgängligheten för shardade-databasen. En nedtid i Shard-katalogen påverkar bara underhålls åtgärder och multishard-frågor under den korta perioden som data Guard-redundansväxlingen har slutförts. Onlinetransaktioner fortsätter att dirigeras och köras av SDB och påverkas inte av ett katalog avbrott.

- **Shard-chefer** – Lightweight-tjänster som måste distribueras i varje region/tillgänglighets zon som din Shards finns i. Shard-chefer är globala tjänst hanterare som distribueras i samband med Oracle horisontell partitionering. För hög tillgänglighet rekommenderar vi att du distribuerar minst en Shard-regissör i varje tillgänglighets zon som din Shards finns i. 

  När du ansluter till databasen från början konfigureras routningsinformation av en Shard-regissör och cachelagras för efterföljande begär Anden, vilket kringgår Shard-regissören. När sessionen har upprättats med en Shard stöds alla SQL-frågor och DML i omfånget för den angivna Shard. Den här dirigeringen är snabb och används för alla OLTP-arbetsbelastningar som utför transaktioner inom Shard. Vi rekommenderar att du använder dirigerad routning för alla OLTP-arbetsbelastningar som kräver högsta prestanda och tillgänglighet. Routningstjänsten uppdateras automatiskt när en Shard blir otillgänglig eller om det sker ändringar i horisontell partitionering-topologin. 
  
  För högpresterande data beroende routning rekommenderar Oracle att du använder en anslutningspool vid åtkomst till data i shardade-databasen. Oracle-anslutningspooler, språkspecifika bibliotek och driv rutiner har stöd för Oracle-horisontell partitionering. Mer information finns i [Oracle horisontell partitionering-dokumentationen](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) .

- **Global tjänst** – global tjänst liknar den reguljära databas tjänsten. Förutom alla egenskaper för en databas tjänst har en global tjänst egenskaper för shardade-databaser, till exempel region tillhörighet mellan klienter och Shard och avvikelse tolerans för replikering. Endast en global tjänst behöver skapas för att läsa/skriva data till/från en shardade-databas. När du använder Active data Guard och konfigurerar skrivskyddade repliker av Shards kan du skapa en annan gGobal-tjänst för skrivskyddade arbets belastningar. Klienten kan använda dessa globala tjänster för att ansluta till databasen.

- **Shard-databaser** – Shard-databaser är dina Oracle-databaser. Varje databas replikeras med hjälp av Oracle data Guard i en Broker-konfiguration med Fast-Start redundans (FSFO) aktiverat. Du behöver inte konfigurera data Guard-redundans och-replikering på varje Shard. Detta konfigureras och distribueras automatiskt när den delade databasen skapas. Om en viss Shard Miss lyckas växlar Oracle-delningen automatiskt över databas anslutningar från den primära till vänte läge.

Du kan distribuera och hantera Oracle shardade-databaser med två gränssnitt: Oracle Enterprise Manager Cloud control GUI och/eller `GDSCTL` kommando rads verktyget. Du kan även övervaka olika Shards för tillgänglighet och prestanda med hjälp av moln kontroll. `GDSCTL DEPLOY`Kommandot skapar automatiskt Shards och deras respektive lyssnare. Dessutom distribuerar det här kommandot automatiskt den replikeringskonfiguration som används för att Shard hög tillgänglighet som anges av administratören.

Det finns olika sätt att Shard en databas:

* Systemhanterad horisontell partitionering – distribuerar automatiskt mellan Shards med partitionering
* Användardefinierad horisontell partitionering – gör att du kan ange mappning av data till Shards, vilket fungerar bra om det finns regler eller krav för data lokalisering.
* Sammansatt horisontell partitionering – en kombination av systemhanterade och användardefinierade horisontell partitionering för olika _shardspaces_
* Tabell underpartitioner – liknande en vanlig partitionerad tabell.

Läs mer om de olika [horisontell partitionering-metoderna](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) i Oracle-dokumentationen.

Även om en shardade-databas kan se ut som en enkel databas till program och utvecklare, vid migrering från en icke-shardade-databas till en shardade-databas, krävs noggrann planering för att avgöra vilka tabeller som ska dupliceras jämfört med shardade. 

Duplicerade tabeller lagras på alla Shards, medan shardade-tabeller distribueras över olika Shards. Rekommendationen är att duplicera små och dimensionella tabeller och distribuera/Shard fakta tabeller. Data kan läsas in i shardade-databasen med hjälp av antingen Shard-katalogen som den centrala koordinatorn eller genom att data pumpen körs på varje Shard. Läs mer om hur [du migrerar data till en shardade-databas](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) i Oracle-dokumentationen.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle-horisontell partitionering med data Guard

Oracle data Guard kan användas för horisontell partitionering med systemhanterade, användardefinierade och sammansatta horisontell partitionering-metoder.

Följande diagram är en referens arkitektur för Oracle-horisontell partitionering med Oracle data Guard som används för hög tillgänglighet för varje Shard. Arkitektur diagrammet visar en _sammansatt horisontell partitionering-metod_. Arkitektur diagrammet skiljer sig troligen för program med olika krav för data plats, belastnings utjämning, hög tillgänglighet, haveri beredskap osv. och kan använda olika metoder för horisontell partitionering. Med Oracle-horisontell partitionering kan du uppfylla dessa krav och skala horisontellt och effektivt genom att tillhandahålla dessa alternativ. En liknande arkitektur kan även distribueras med hjälp av Oracle GoldenGate.

![Oracle Database horisontell partitionering med hjälp av tillgänglighets zoner med data Guard Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Även om systemhanterade horisontell partitionering är det enklaste sättet att konfigurera och hantera, användardefinierade horisontell partitionering eller sammansatt horisontell partitionering passar bra för scenarier där dina data och program är geo-distribuerade eller i scenarier där du måste ha kontroll över replikeringen av varje Shard. 

I den föregående arkitekturen används sammansatt horisontell partitionering för att geo-distribuera data och skala ut dina program nivåer vågrätt. Sammansatt horisontell partitionering är en kombination av systemhanterade och användardefinierade horisontell partitionering och ger därmed fördelarna med båda metoderna. I föregående scenario är data först shardade över flera shardspaces åtskilda efter region. Sedan partitioneras data ytterligare genom konsekvent hash över flera Shards i shardspace. Varje shardspace innehåller flera shardgroups. Varje shardgroup har flera Shards och är en "enhet" av replikering, i det här fallet. Varje shardgroup innehåller alla data i shardspace. Shardgroups a1 och B1 är primära Shardgroups, medan Shardgroups a2 och B2 är vänte läge. Du kan välja att ha enskilda Shards som enhet för replikering, i stället för en shardgroup.

I föregående arkitektur distribueras en GSM/Shard-regissör i varje tillgänglighets zon för hög tillgänglighet. Rekommendationen är att distribuera minst en GSM/Shard-regissör per Data Center/region. Dessutom distribueras en instans av program servern i alla tillgänglighets zoner som innehåller en shardgroup. Med den här inställningen kan programmet behålla svars tiden mellan program servern och databasen/shardgroup låg. Om en databas Miss lyckas kan program servern i samma zon som standby-databasen hantera begär anden när över gången till databas rollen sker. Azure Application Gateway och Shard-regissören håller koll på begäran och svars svars tid och dirigera begär detta.

Från ett program syn gör klient systemet en begäran om att Azure Application Gateway (eller andra belastnings Utjämnings tekniker i Azure) som omdirigerar begäran till den region som är närmast klienten. Azure Application Gateway stöder också tröga sessioner, så alla begär Anden som kommer från samma klient dirigeras till samma program Server. Program servern använder anslutningspoolen i data åtkomst driv rutiner. Den här funktionen är tillgänglig i driv rutiner som JDBC, ODP.NET, OCI osv. Driv rutinerna kan identifiera horisontell partitionering-nycklar som anges som en del av begäran. [Oracle Universal anslutningspoolen (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) för JDBC-klienter kan aktivera icke-Oracle-programklienter som Apache Tomcat och IIS att arbeta med Oracle-horisontell partitionering. 

Under den inledande begäran ansluter program servern till Shard-regissören i sin region för att hämta routningsinformation för den Shard som begäran måste dirigeras till. Baserat på horisontell partitionering-nyckeln skickade Director-servern program servern till respektive Shard. Program servern cachelagrar den här informationen genom att skapa en karta och för efterföljande begär Anden, kringgå Shard-regissören och dirigera begär Anden direkt till Shard.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle-horisontell partitionering med GoldenGate

Följande diagram är en referens arkitektur för Oracle-horisontell partitionering med Oracle GoldenGate för hög tillgänglighet för varje Shard. Till skillnad från föregående arkitektur portrays den här arkitekturen bara hög tillgänglighet inom en enda Azure-region (flera tillgänglighets zoner). En shardade-databas med hög tillgänglighet (liknar föregående exempel) kan distribueras med hjälp av Oracle GoldenGate.

![Oracle Database horisontell partitionering med hjälp av tillgänglighets zoner med GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

I föregående referens arkitektur används den _systemhanterade_ horisontell partitionering-metoden för att Shard data. Eftersom Oracle GoldenGate-replikering görs på en segment nivå kan hälften av de data som replikeras till en Shard replikeras till en annan Shard. Den andra halvan kan replikeras till en annan Shard. 

Hur data replikeras beror på replikeringslänken. Med en replikeringsrelation på 2 kommer du att ha två kopior av varje data segment över dina tre Shards i shardgroup. På samma sätt kommer alla data i varje Shard att replikeras till alla andra Shard i shardgroup, med en replikeringsrelation på 3 och tre Shards i din shardgroup. Varje Shard i shardgroup kan ha en annan replikeringsrelation. Med den här inställningen kan du definiera designen för hög tillgänglighet och haveri beredskap effektivt inom en shardgroup och över flera shardgroups.

I den föregående arkitekturen innehåller shardgroup A och shardgroup B båda samma data men finns i olika tillgänglighets zoner. Om både shardgroup A och shardgroup B har samma replikeringsrelation på 3 kommer varje rad/segment i din shardade-tabell att replikeras 6 gånger över de två shardgroups. Om shardgroup A har en replikeringsrelation på 3 och shardgroup B har en replikeringsrelation på 2 replikeras varje rad/segment fem gånger över de två shardgroups.

Den här installationen förhindrar data förlust om det uppstår ett fel på instans nivå eller tillgänglighet på zon nivå. Program lagret kan läsa från och skriva till varje Shard. För att minimera konflikter anger Oracle-horisontell partitionering ett "huvud segment" för varje intervall med hash-värden. Den här funktionen säkerställer att skrivningar begär Anden för ett visst segment dirigeras till motsvarande segment. Dessutom ger Oracle GoldenGate automatisk konflikt identifiering och lösning för att hantera eventuella konflikter som kan uppstå. Mer information och begränsningar för att implementera GoldenGate med Oracle horisontell partitionering finns i Oracle-dokumentationen om hur du använder [Oracle-GoldenGate med en shardade-databas](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

I föregående arkitektur distribueras en GSM/Shard-regissör i varje tillgänglighets zon för hög tillgänglighet. Rekommendationen är att distribuera minst en GSM/Shard-regissör per Data Center eller region. Dessutom distribueras en instans av program servern i alla tillgänglighets zoner som innehåller en shardgroup. Med den här inställningen kan programmet behålla svars tiden mellan program servern och databasen/shardgroup låg. Om en databas Miss lyckas kan program servern i samma zon som vänte läges databasen hantera begär anden när databas rollerna övergår. Azure Application Gateway och Shard-regissören håller koll på begäran och svars svars tid och dirigera begär detta.

Från ett program syn gör klient systemet en begäran om att Azure Application Gateway (eller andra belastnings Utjämnings tekniker i Azure) som omdirigerar begäran till den region som är närmast klienten. Azure Application Gateway stöder också tröga sessioner, så alla begär Anden som kommer från samma klient dirigeras till samma program Server. Program servern använder anslutningspoolen i data åtkomst driv rutiner. Den här funktionen är tillgänglig i driv rutiner som JDBC, ODP.NET, OCI osv. Driv rutinerna kan identifiera horisontell partitionering-nycklar som anges som en del av begäran. [Oracle Universal anslutningspoolen (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) för JDBC-klienter kan aktivera icke-Oracle-programklienter som Apache Tomcat och IIS att arbeta med Oracle-horisontell partitionering. 

Under den inledande begäran ansluter program servern till Shard-regissören i sin region för att hämta routningsinformation för den Shard som begäran måste dirigeras till. Baserat på horisontell partitionering-nyckeln skickade Director-servern program servern till respektive Shard. Program servern cachelagrar den här informationen genom att skapa en karta och för efterföljande begär Anden, kringgå Shard-regissören och dirigera begär Anden direkt till Shard.

## <a name="patching-and-maintenance"></a>Uppdatering och underhåll

När du distribuerar dina Oracle-arbetsbelastningar till Azure tar Microsoft hand om all korrigering av värd operativ system nivå. Alla planerade underhåll av OS-nivåer skickas till kunder i förväg för att tillåta kunden att göra det här planerade underhållet. Två servrar från två olika Tillgänglighetszoner korrigeras aldrig samtidigt. Mer information om underhåll och korrigeringar för virtuella datorer finns i [Hantera tillgängligheten för virtuella datorer](../../manage-availability.md) . 

Korrigering av den virtuella datorns operativ system kan automatiseras med hjälp av [Azure Automation uppdateringshantering](../../../automation/update-management/overview.md). Uppdatering och underhåll av Oracle-databasen kan automatiseras och schemaläggas med hjälp av [Azure-pipeliner](/azure/devops/pipelines/get-started/what-is-azure-pipelines) eller [Azure Automation uppdateringshantering](../../../automation/update-management/overview.md) för att minimera stillestånds tiden. Se [kontinuerliga leveranser och blå/gröna distributioner](/azure/devops/learn/what-is-continuous-delivery) för att förstå hur det kan användas i samband med dina Oracle-databaser.

## <a name="architecture-and-design-considerations"></a>Arkitektur och design överväganden

- Överväg att använda en högtrådad [minnes optimerad virtuell dator](../../sizes-memory.md) med [begränsade kärn virtuella processorer](../../../virtual-machines/constrained-vcpu.md) för din Oracle Database VM för att spara på licens kostnader och maximera prestanda. Använd flera Premium-eller Ultra-diskar (Managed Disks) för prestanda och tillgänglighet.
- När du använder hanterade diskar kan disk-och enhets namnet ändras vid omstarter. Vi rekommenderar att du använder enhets-UUID i stället för namnet för att se till att dina monteringar behålls över omstarter. Läs mer [här](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Använd tillgänglighets zoner för att uppnå hög tillgänglighet i regionen.
- Överväg att använda Ultra disks (när det är tillgängligt) eller Premium diskar för Oracle-databasen.
- Överväg att konfigurera en Oracle-databas för vänte läge i en annan Azure-region med Oracle data Guard.
- Överväg att använda [närhets placerings grupper](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) för att minska svars tiden mellan programmet och databas nivån.
- Konfigurera [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) för hantering, övervakning och loggning.
- Överväg att använda hantering av Oracle automatisk lagring (ASM) för strömlinjeformad lagrings hantering för din databas.
- Använd [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) för att hantera korrigeringar och uppdateringar av databasen utan drift avbrott.
- Ändra program koden för att lägga till molnbaserade mönster, till exempel [återförsöks mönster](/azure/architecture/patterns/retry), [krets brytar mönster](/azure/architecture/patterns/circuit-breaker)och andra mönster som definierats i [design mönster guiden för molnet](/azure/architecture/patterns/) som kan hjälpa ditt program att vara mer elastiskt.

## <a name="next-steps"></a>Nästa steg

Granska följande Oracle Reference-artiklar som gäller för ditt scenario.

- [Introduktion till Oracle data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle data Guard Broker-koncept](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurera Oracle-GoldenGate för Active-Active hög tillgänglighet](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Översikt över Oracle-horisontell partitionering](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active data Guard, lång synkronisering, noll data förlust vid valfritt avstånd](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
