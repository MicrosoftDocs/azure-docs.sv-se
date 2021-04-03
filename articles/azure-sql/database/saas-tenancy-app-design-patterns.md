---
title: SaaS mönster för flera innehavare
description: Lär dig mer om kraven och vanliga data arkitektur mönster i SaaS-databaser (program vara som en tjänst) för flera innehavare som körs i moln miljön i Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793236"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>SaaS-databas för flera klient organisationer
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs de olika hyres modeller som är tillgängliga för ett SaaS-program med flera innehavare.

När du designar ett SaaS-program för flera innehavare måste du noga välja den innehavs modell som passar bäst för ditt program.  En hyres modell avgör hur varje klients data mappas till lagringen.  Ditt val av innehavs modell påverkar programmets design och hantering.  Att växla till en annan modell senare är ibland kostsamt.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS koncept och terminologi

I SaaS-modellen (Software as a Service) säljer företaget inte *licenser* för din program vara. I stället gör varje kund hyres betalningar till ditt företag, vilket gör varje kund *till ditt* företag.

För att betala hyra får varje klient åtkomst till dina SaaS program komponenter och har sina data lagrade i SaaS-systemet.

Termen *innehav modell* avser hur klient organisationens lagrade data är ordnade:

- *Single-innehav:* &nbsp; Varje databas lagrar endast data från en klient.
- *Flera innehavare:* &nbsp; Varje databas lagrar data från flera separata klienter (med mekanismer för att skydda data integriteten).
- Hybrid innehavare modeller är också tillgängliga.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Så här väljer du lämplig hyres modell

I allmänhet påverkar den innehavande modellen inte funktionen för ett program, men det påverkar förmodligen andra aspekter av den övergripande lösningen.  Följande kriterier används för att utvärdera var och en av modellerna:

- **Bättre**
    - Antal klienter.
    - Lagring per klient.
    - Lagring i mängd.
    - Arbets belastning.

- **Klient isolering:** &nbsp; Data isolering och prestanda (oavsett om en innehavares arbets belastning påverkar andra).

- **Kostnad per klient:** &nbsp; Databas kostnader.

- **Utvecklings komplexitet:**
    - Ändringar av schemat.
    - Ändringar i frågor (krävs av mönstret).

- **Drifts komplexitet:**
    - Övervakning och hantering av prestanda.
    - Schema hantering.
    - Återställa en klient.
    - Haveriberedskap.

- **Anpassningsbarhet:** &nbsp; Enklare att stödja schema anpassningar som antingen är klient-eller klient klass-/regionsspecifika.

Den innehavande diskussionen fokuserar på *data* lagret.  Men tänk på ett tag i *program* lagret.  Program lagret behandlas som en monolitisk-enhet.  Om du delar upp programmet i många små komponenter kan valet av innehav modell ändras.  Du kan behandla vissa komponenter annorlunda än andra angående både innehavande och lagrings teknik eller plattform som används.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Fristående app med en enda klient med en databas för en klient

#### <a name="application-level-isolation"></a>Isolering på program nivå

I den här modellen installeras hela programmet upprepade gånger, en gång för varje klient.  Varje instans av appen är en fristående instans, så den aldrig interagerar med någon annan fristående instans.  Varje instans av appen har bara en klient och behöver därför bara en databas.  Klienten har själva databasen.

![Design av fristående app med exakt en databas med en enda klient.][image-standalone-app-st-db-111a]

Varje App-instans installeras i en separat Azure-resurs grupp.  Resurs gruppen kan tillhöra en prenumeration som ägs av antingen program varu leverantören eller klient organisationen.  I båda fallen kan leverantören hantera program varan för klient organisationen.  Varje program instans är konfigurerad för att ansluta till motsvarande databas.

Varje klient databas distribueras som en enskild databas.  Den här modellen ger den största databas isoleringen.  Men isoleringen kräver att tillräckliga resurser allokeras till varje databas för att hantera högsta belastning.  Här är det viktigt att elastiska pooler inte kan användas för databaser som har distribuerats i olika resurs grupper eller till olika prenumerationer.  Den här begränsningen gör att den här fristående appar modellen med en klient modell är den dyraste lösningen från ett övergripande databas kostnads perspektiv.

#### <a name="vendor-management"></a>Leverantörs hantering

Leverantören har åtkomst till alla databaser i alla fristående App-instanser, även om app-instanserna är installerade i olika klient prenumerationer.  Åtkomsten uppnås via SQL-anslutningar.  Den här kors instans åtkomsten kan göra det möjligt för leverantören att centralisera schema hantering och kors databas frågor för rapporterings-och analys syfte.  Om den här typen av centraliserad hantering önskas måste en katalog distribueras som mappar klient identifierare till databas-URI: er.  Azure SQL Database tillhandahåller ett horisontell partitionering-bibliotek som används tillsammans för att tillhandahålla en katalog.  Horisontell partitionering-biblioteket har formellt namnet [Elastic Database-klient biblioteket][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. App för flera klient organisationer med databas per klient

I nästa mönster används ett program med flera klienter med många databaser, alla som är en enda klient databas.  En ny databas tillhandahålls för varje ny klient.  Program nivån skalas *upp* lodrätt genom att lägga till fler resurser per nod.  Eller så skalas appen *ut* vågrätt genom att lägga till fler noder.  Skalningen baseras på arbets belastningen och är oberoende av antalet eller skalan för de enskilda databaserna.

![Design av appen för flera klient organisationer med databas per klient.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Anpassa för en klient

Precis som i det fristående app-mönstret ger användningen av databaserna för en enskild klient en stark klient isolering.  I alla appar vars modell bara anger en enda klient databas kan schemat för en viss databas anpassas och optimeras för dess klient.  Den här anpassningen påverkar inte andra klienter i appen. En klient kanske behöver data utöver de grundläggande data fält som alla klienter behöver.  Dessutom kan fältet extra data behöva ett index.

Med en databas per klient är det enkelt att få anpassning av schemat för en eller flera enskilda klienter.  Program leverantören måste utforma procedurer för att noggrant hantera schema anpassningar i stor skala.

#### <a name="elastic-pools"></a>Elastiska pooler

När databaser distribueras i samma resurs grupp kan de grupperas i elastiska pooler.  Pooler ger ett kostnads effektivt sätt att dela resurser över flera databaser.  Det här alternativet är billigare än att kräva att varje databas måste vara tillräckligt stor för att rymma den användnings toppar som det upplever.  Även om poolbaserade databaser delar åtkomst till resurser kan de fortfarande uppnå hög grad av prestanda isolering.

![Design av en app med flera klient organisationer med en databas per klient, med en elastisk pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database innehåller de verktyg som krävs för att konfigurera, övervaka och hantera delningen.  Både prestanda mått på pool-och databas nivå är tillgängliga i Azure Portal och genom Azure Monitor loggar.  Måtten kan ge bra insikter om både agg regerings och klient organisations-/regionsspecifika prestanda.  Enskilda databaser kan flyttas mellan pooler för att tillhandahålla reserverade resurser till en viss klient.  Med de här verktygen kan du säkerställa bästa prestanda på ett kostnads effektivt sätt.

#### <a name="operations-scale-for-database-per-tenant"></a>Drift skalning för databas per klient

Azure SQL Database har många hanterings funktioner som är utformade för att hantera stora mängder databaser i stor skala, till exempel bra över 100 000-databaser.  Dessa funktioner gör ett mönster för databas per klient plausible.

Anta till exempel att ett system har en 1000-innehavaradministratör som endast en databas.  Databasen kan ha 20 index.  Om systemet konverteras till att ha 1000-databaser med en enda klient, ökar antalet index till 20 000.  I Azure SQL Database som en del av [Automatisk justering][docu-sql-db-automatic-tuning-771a]är automatiska indexerings funktioner aktiverade som standard.  Automatisk indexering hanterar alla 20 000-index och de pågående Optimeringarna för att skapa och släppa.  Dessa automatiserade åtgärder sker i en enskild databas och de är inte koordinerade eller begränsade av liknande åtgärder i andra databaser.  Automatisk indexering behandlar index på olika sätt i en upptagen databas än i en mindre upptagen databas.  Den här typen av anpassning av index hantering skulle vara opraktisk vid skalning av databasen per klient om denna enorma hanterings aktivitet måste utföras manuellt.

Andra hanterings funktioner som skalar bra omfattar följande:

- Inbyggda säkerhets kopior.
- Hög tillgänglighet.
- Kryptering på disk.
- Prestanda telemetri.

#### <a name="automation"></a>Automation

Hanterings åtgärderna kan skriptas och erbjudas genom en [DevOps][http-visual-studio-devops-485m] modell.  Åtgärderna kan till och med automatiseras och exponeras i programmet.

Du kan till exempel automatisera återställningen av en enskild klient till en tidigare tidpunkt.  Återställningen behöver bara återställa den enda-klient databas som lagrar klienten.  Den här återställningen påverkar inte andra klienter, vilket bekräftar att hanterings åtgärderna är på en detaljerad nivå av varje enskild klient organisation.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. App för flera klienter med databaser för flera klienter

Ett annat tillgängligt mönster är att lagra många klienter i en databas med flera innehavare.  Program instansen kan ha valfritt antal databaser för flera klienter.  Schemat för en databas för flera innehavare måste ha en eller flera kolumner för klient-ID så att data från en specifik klient kan hämtas selektivt.  Dessutom kan schemat kräva ett fåtal tabeller eller kolumner som bara används av en delmängd av klienterna.  Statisk kod och referens data lagras dock bara en gång och delas av alla klienter.

#### <a name="tenant-isolation-is-sacrificed"></a>Klient isoleringen har avlivats

*Data:* &nbsp; En databas med flera klienter avlivar nödvändigt vis klient isolering.  Data för flera klienter lagras tillsammans i en databas.  Under utvecklingen ser du till att frågor aldrig visar data från fler än en klient.  SQL Database stöder [säkerhet på radnivå][docu-sql-svr-db-row-level-security-947w], som kan genomdriva att data som returneras från en fråga besvaras till en enda klient.

*Bearbetar:* &nbsp; En databas för flera innehavare delar beräknings-och lagrings resurser över alla klienter.  Databasen som helhet kan övervakas för att säkerställa att den godkänns.  Men Azure-systemet har inget inbyggt sätt att övervaka eller hantera användningen av dessa resurser av en enskild klient.  Därför medför databasen för flera innehavare en ökad risk för att stöta på problem med störningar, där arbets belastningen för en överaktiv klient påverkar prestanda upplevelsen för andra klienter i samma databas.  Ytterligare övervakning på program nivå kan övervaka prestanda på klient nivå.

#### <a name="lower-cost"></a>Lägre kostnad

I allmänhet har flera klient databaser lägsta kostnad per klient.  Resurs kostnader för en enskild databas är lägre än för en likvärdigt storlek elastisk pool.  För scenarier där klienterna bara behöver begränsad lagring kan eventuellt miljon tals klienter lagras i en enda databas.  Ingen elastisk pool kan innehålla miljon tals databaser.  En lösning som innehåller 1000-databaser per pool, med 1000 pooler, kan emellertid uppnå skalan på miljon tals risk för att bli svårhanterligt att hantera.

Två varianter av en databas modell med flera klienter beskrivs i vad som följer, med shardade-modellen för flera klienter som är mest flexibel och skalbar.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. App för flera klienter med en enda databas för flera innehavare

Det enklaste databas mönstret för flera klient organisationer använder en enda databas som värd för data för alla klienter.  När fler klienter läggs till, skalas databasen upp med mer lagrings-och beräknings resurser.  Den här skalan kan vara allt som behövs, även om det alltid finns en ultimat skalnings gräns.  Men långt innan gränsen nås blir databasen svårhanterligt för hantering.

Hanterings åtgärder som fokuserar på enskilda klienter är mer komplexa att implementera i en databas med flera innehavare.  Och vid skalning kan de här åtgärderna bli oacceptabelt långsamma.  Ett exempel är en tidpunkts återställning av data för bara en klient.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. App för flera klient organisationer med shardade-databaser för flera innehavare

De flesta SaaS-program får bara åtkomst till data för en klient i taget.  Detta åtkomst mönster gör att klient data kan distribueras över flera databaser eller Shards, där alla data för en klient organisation finns i en Shard.  Med en shardade-modell som kombineras med ett databas mönster för flera innehavare, tillåts en-modell nästan obegränsad skalning.

![Design av en app för flera klient organisationer med shardade-databaser för flera innehavare.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Hantera Shards

Horisontell partitionering ökar komplexiteten både i design-och drift hantering.  En katalog krävs för att kunna underhålla mappningen mellan klienter och databaser.  Dessutom krävs hanterings procedurer för att hantera Shards och klient populationen.  Till exempel måste procedurer utformas för att lägga till och ta bort Shards och för att flytta klient data mellan Shards.  Ett sätt att skala är att lägga till en ny Shard och fylla den med nya klienter.  Vid andra tillfällen kan du dela upp en tätt ifylld Shard i två mindre täta, Shards.  När flera klienter har flyttats eller avbrutits kan du slå samman sparse-Shards tillsammans.  Sammanslagningen skulle resultera i mer kostnads effektiv resursutnyttjande.  Klienter kan också flyttas mellan Shards för att balansera arbets belastningar.

SQL Database innehåller ett verktyg för delning/sammanslagning som fungerar tillsammans med horisontell partitionering-biblioteket och katalog databasen.  Den tillhandahållna appen kan dela och slå samman Shards och kan flytta klient data mellan Shards.  Appen underhåller också katalogen under dessa åtgärder och markerar berörda innehavare som offline innan de flyttas.  Efter flytten uppdaterar appen katalogen igen med den nya mappningen och markerar klienten som online igen.

#### <a name="smaller-databases-more-easily-managed"></a>Mindre databaser lättare att hantera

Genom att distribuera klienter i flera databaser resulterar shardade för flera klienter i mindre databaser som är enklare att hantera.  Om du till exempel återställer en viss klient till en tidigare tidpunkt, innebär det nu att du återställer en enda mindre databas från en säkerhets kopia i stället för en större databas som innehåller alla klienter. Databasens storlek och antalet klienter per databas kan väljas för att balansera arbets belastningen och hanterings aktiviteterna.

#### <a name="tenant-identifier-in-the-schema"></a>Klient-ID i schemat

Beroende på den horisontell partitionering metod som används kan ytterligare begränsningar införas i databasschemat.  SQL Database dela/sammanfoga program kräver att schemat inkluderar horisontell partitionering-nyckeln, som vanligt vis är klient-ID.  Klient-ID är det ledande elementet i primär nyckeln för alla shardade-tabeller.  Klient-ID: n gör att delnings-/kopplings programmet snabbt kan hitta och flytta data som är associerade med en specifik klient.

#### <a name="elastic-pool-for-shards"></a>Elastisk pool för Shards

Shardade-databaser för flera klienter kan placeras i elastiska pooler.  I allmänhet är det så kostnads effektivt att ha många-databaser i en pool som kostnads effektiv som att ha många klienter i några få flera klient databaser.  Databaser med flera klienter är förmånliga när det finns ett stort antal relativt inaktiva klienter.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>&. Databas modell för Hybrid shardade för flera innehavare

I hybrid modellen har alla databaser klient-ID i sitt schema.  Databaserna har bara möjlighet att lagra fler än en klient och databaserna kan vara shardade.  I schema Sense är de alla databaser med flera klienter.  Men i praktiken innehåller vissa av dessa databaser bara en klient.  Oavsett hur många klienter som lagras i en specifik databas har databasschemat ingen påverkan på databasschemat.

#### <a name="move-tenants-around"></a>Flytta klienterna runt

Du kan när som helst flytta en viss klient organisation till en egen databas för flera innehavare.  Du kan när som helst ändra dina saker och flytta klienten tillbaka till en databas som innehåller flera klienter.  Du kan också tilldela en klient till en ny databas för en klient organisation när du etablerar den nya databasen.

Hybrid modellen skiner när det finns stora skillnader mellan resurs behoven för identifierbara grupper av klienter.  Anta till exempel att innehavare som deltar i en kostnads fri utvärderings version inte garanterar samma höga prestanda nivå som det som prenumererar på klient organisationerna är.  Principen kan vara för klienter i den kostnads fria utvärderings fasen som ska lagras i en databas för flera innehavare som delas av alla kostnads fria utvärderings klienter.  När en kostnads fri utvärderings klient prenumererar på den grundläggande tjänst nivån, kan klienten flyttas till en annan databas med flera innehavare som kan ha färre klienter.  En prenumerant som betalar för Premium service-nivån kan flyttas till en ny databas med en enda klient.

#### <a name="pools"></a>Pooler

I den här hybrid modellen kan databaserna för en enda klient organisation för prenumeranter placeras i resurspooler för att minska databas kostnaderna per klient.  Detta görs också i modellen för databas per klient.

## <a name="i-tenancy-models-compared"></a>I. Överliggande modeller jämförs

I följande tabell sammanfattas skillnaderna mellan de huvudsakliga innehavande modellerna.

| Mått | Fristående app | Databas per klient | Shardade flera innehavare |
| :---------- | :------------- | :------------------ | :------------------- |
| Skala | Medel<br />1-100s | Mycket hög <br />1 – 100, tal | Obegränsat<br />1 – 1 000, tal |
| Isolering av klientorganisation | Mycket hög  | Högt | Börjar Förutom för en enskild klient (som är ensam i en MT dB). |
| Databas kostnad per klient | Högt storlek för toppar. | Börjar pooler som används. | Lägsta, för små klienter i MT databaser. |
| Prestanda övervakning och hantering | Endast per klient | Aggregera + per-klient | Mängden även om det bara är per klient för Singles. |
| Utvecklings komplexitet | Låg | Låg | Säker på grund av horisontell partitionering. |
| Drifts komplexitet | Låg hög. Individuellt enkelt, komplext i skala. | Låg-medels Tor. Mönster för att hantera komplexitet i skala. | Låg hög. Individuell klient hantering är komplex. |
| &nbsp; ||||

## <a name="next-steps"></a>Nästa steg

- [Distribuera och utforska ett Wingtip-program med flera innehavare som använder SaaS-modellen för databas per innehavare – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Välkommen till Wingtip Ticket-exemplet SaaS Azure SQL Database-arbetsappen][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design av fristående app med exakt en databas med en enda klient."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design av appen för flera klient organisationer med databas per klient."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Design av en app med flera klient organisationer med en databas per klient, med en elastisk pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design av en app för flera klient organisationer med shardade-databaser för flera innehavare."