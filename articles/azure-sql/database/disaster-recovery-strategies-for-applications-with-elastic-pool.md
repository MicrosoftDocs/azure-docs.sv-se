---
title: Design katastrof återställnings lösningar
description: Lär dig hur du utformar din moln lösning för haveri beredskap genom att välja rätt växlings mönster.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91357798"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Strategier för haveri beredskap för program som använder Azure SQL Database elastiska pooler
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database innehåller flera funktioner som du kan använda för att få affärs kontinuiteten i ditt program när det uppstår oåterkalleliga incidenter. [Elastiska pooler](elastic-pool-overview.md) och enkla databaser har stöd för samma typ av haveri beredskap (Dr). I den här artikeln beskrivs flera DR-strategier för elastiska pooler som utnyttjar dessa Azure SQL Database funktioner för affärs kontinuitet.

I den här artikeln används följande kanoniska program mönster för SaaS-ISV:

Ett modernt molnbaserad webb program tillhandahåller en databas för varje slutanvändare. ISV har många kunder och använder därför många databaser, som kallas klient databaser. Eftersom klient databaserna vanligt vis har oförutsägbara aktivitets mönster, använder ISV en elastisk pool för att göra databas kostnaden förutsägbar över längre tid. Den elastiska poolen fören klar också prestanda hanteringen när användar aktiviteten är på gång. Förutom klient databaserna använder programmet också flera databaser för att hantera användar profiler, säkerhet, samla in användnings mönster osv. Tillgänglighet för enskilda klienter påverkar inte programmets tillgänglighet som helhet. Tillgänglighet och prestanda för hanterings databaser är dock viktiga för programmets funktion och om hanterings databaserna är offline är hela programmet offline.

Den här artikeln beskriver DR-strategier som omfattar ett antal scenarier från kostnads känsliga start program till dem med strängare tillgänglighets krav.

> [!NOTE]
> Om du använder Premium-eller Affärskritisk databaser och elastiska pooler kan du göra dem flexibla till regionala drifts störningar genom att konvertera dem till zonens redundant distributions konfiguration. Se [zoner-redundanta databaser](high-availability-sla.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kostnads känslig start

Jag är en start av företag och är extremt extremt kostnads känslig.  Jag vill förenkla distributionen och hanteringen av programmet och jag kan ha ett begränsat service avtal för enskilda kunder. Men jag vill se till att hela programmet inte är offline.

För att uppfylla det enkla kravet kan du distribuera alla klient databaser till en elastisk pool i Azure-regionen som du väljer och distribuera hanterings databaser som geo-replikerade enkla databaser. För haveri beredskap för klient organisationer använder du geo-återställning, som inte ingår i någon extra kostnad. För att säkerställa tillgängligheten för hanterings databaserna geo replikerar du dem till en annan region med hjälp av en grupp för automatisk redundans (steg 1). Den löpande kostnaden för haveri beredskaps konfigurationen i det här scenariot motsvarar den totala kostnaden för de sekundära databaserna. Den här konfigurationen illustreras i nästa diagram.

![Bild 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Om ett avbrott uppstår i den primära regionen, illustreras återställnings stegen för att ta ditt program online med nästa diagram.

* Gruppen redundans initierar automatisk redundansväxling av hanterings databasen till DR-regionen. Programmet återansluts automatiskt till den nya primära och alla nya konton och klient databaser skapas i DR-regionen. De befintliga kunderna ser till att deras data är tillfälligt otillgängliga.
* Skapa den elastiska poolen med samma konfiguration som den ursprungliga poolen (2).
* Använd geo-återställning för att skapa kopior av klient databaserna (3). Du kan överväga att utlösa enskilda återställningar från slut användar anslutningarna eller använda andra programspecifika prioritets scheman.

I det här skedet är ditt program online igen i DR-regionen, men vissa kunder upplever fördröjning vid åtkomst till sina data.

![Bild 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Om avbrottet var tillfälligt, är det möjligt att den primära regionen återställs av Azure innan alla databas återställningar har slutförts i DR-regionen. I det här fallet dirigerar du tillbaka programmet till den primära regionen. Processen tar stegen som illustreras i nästa diagram.

* Avbryt alla utestående geo Restore-begäranden.
* Redundansväxla hanterings databaserna till den primära regionen (5). Efter regionens återställning har den gamla presidentval automatiskt blivit sekundär. De byter nu roller igen.
* Ändra programmets anslutnings sträng så att den pekar tillbaka till den primära regionen. Nu skapas alla nya konton och klient databaser i den primära regionen. Vissa befintliga kunder ser sina data tillfälligt otillgängliga.
* Ange att alla databaser i DR-poolen ska vara skrivskyddade för att säkerställa att de inte kan ändras i DR-regionen (6).
* Byt namn på eller ta bort motsvarande databaser i den primära poolen (7) för varje databas i DR-poolen som har ändrats sedan återställningen.
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (8).
* Ta bort DR-poolen (9)

I det här läget är ditt program online i den primära regionen med alla klient databaser som är tillgängliga i den primära poolen.

![Bild 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Fördelar

De främsta fördelarna med den här strategin är en låg kontinuerlig kostnad för redundans av data nivåer. Azure SQL Database säkerhetskopierar automatiskt databaser utan att program skrivs om utan extra kostnad. Kostnaden påförs bara när de elastiska databaserna återställs. 

### <a name="trade-off"></a>Kompromiss

Kompromissen är att hela återställningen av alla klient databaser tar lång tid. Hur lång tid det tar beror på det totala antalet återställningar som du initierar i DR-regionen och den övergripande storleken på klient databaserna. Även om du prioriterar vissa klienters återställningar över andra, konkurrerar du med alla andra återställningar som initieras i samma region som tjänstens arbitrates och begränsningar för att minimera den övergripande påverkan på befintliga kunders databaser. Dessutom kan återställningen av klient databaserna inte starta förrän den nya elastiska poolen i DR-regionen har skapats.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Vuxen program med skiktad tjänst

Jag är en vuxen SaaS-app med en nivå med tjänst erbjudanden och olika service avtal för utvärderings kunder och för att betala kunderna. För utvärderings kunder måste jag minska kostnaderna så mycket som möjligt. Utvärderings kunder kan ta stillestånds tid men jag vill minska sannolikheten. För betalande kunder är alla stillestånds tider en flyg risk. Jag vill se till att betala kunder alltid kan komma åt sina data.

För att stödja det här scenariot separerar du utvärderings klienterna från betalda klienter genom att sätta dem i separata elastiska pooler. Utvärderings kunder har lägre eDTU-eller virtuella kärnor per klient och lägre service avtal med en längre återställnings tid. De betalar kunderna finns i en pool med högre eDTU-eller virtuella kärnor per klient och ett högre service avtal. För att garantera den lägsta återställnings tiden är de betalande kundernas klient databaser geo-replikerade. Den här konfigurationen illustreras i nästa diagram.

![Diagrammet visar en primär region och en D R-region som använder geo-replikering mellan hanterings databasen och betald kunds primära pool och den sekundära poolen utan replikering för den utvärderings kunder som har en pool.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Som i det första scenariot är hanterings databaserna ganska aktiva så att du kan använda en enda geo-replikerad databas för den (1). Detta säkerställer förutsägbar prestanda för nya kund prenumerationer, profil uppdateringar och andra hanterings åtgärder. Den region där presidentval för hanterings databaserna finns är den primära regionen och den region där sekundärerna för hanterings databaserna finns är DR-regionen.

De betalande kundernas klient databaser har aktiva databaser i poolen "betald" som tillhandahålls i den primära regionen. Etablera en sekundär pool med samma namn i DR-regionen. Varje klient är geo-replikerad till den sekundära poolen (2). Detta möjliggör snabb återställning av alla klient databaser med redundans.

Om ett avbrott uppstår i den primära regionen visas återställnings stegen för att ta ditt program online i nästa diagram:

![Diagrammet visar ett avbrott för den primära regionen, med redundans till hanterings databasen, betald kund sekundär pool och skapande och återställning av utvärderings kunder.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Växla omedelbart över hanterings databaserna till DR-regionen (3).
* Ändra programmets anslutnings sträng så att den pekar på DR-regionen. Nu skapas alla nya konton och klient databaser i DR-regionen. De befintliga utvärderings kunderna ser sina data tillfälligt otillgängliga.
* Redundansväxla den betalda klientens databaser till poolen i DR-regionen för att omedelbart återställa deras tillgänglighet (4). Eftersom redundansväxlingen är en snabb ändring av metadata-nivå, bör du överväga en optimering där de enskilda redundansväxlingen utlöses på begäran av slut användar anslutningarna.
* Om den sekundära poolens eDTU-storlek eller vCore-värde var lägre än den primära eftersom de sekundära databaserna endast krävde kapaciteten för att bearbeta ändrings loggarna när de var sekundära, ökar du omedelbart pool kapaciteten nu för att hantera hela arbets belastningen för alla klienter (5).
* Skapa den nya elastiska poolen med samma namn och samma konfiguration i DR-regionen för utvärderings kundernas databaser (6).
* När utvärderings kundernas pool har skapats använder du geo-återställning för att återställa de enskilda utvärderings klient databaserna till den nya poolen (7). Överväg att utlösa enskilda återställningar från slut användar anslutningarna eller använda andra programspecifika prioritets scheman.

I det här läget är ditt program online igen i DR-regionen. Alla betalande kunder har till gång till sina data medan utvärderings kunder upplever fördröjning vid åtkomst till sina data.

När den primära regionen återställs av Azure *efter* att du har återställt programmet i Dr-regionen kan du fortsätta att köra programmet i den regionen, eller så kan du välja att växla tillbaka till den primära regionen. Om den primära regionen återställs *innan* redundansväxlingen är klar bör du överväga att återställa direkt. Återställnings proceduren utför stegen som illustreras i nästa diagram:

![Diagrammet visar återställnings steg som implementeras efter återställning av den primära regionen.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Avbryt alla utestående geo Restore-begäranden.
* Redundansväxla hanterings databaser (8). Efter regions återställningen blir den gamla primären automatiskt den sekundära. Nu blir det primärt igen.  
* Redundansväxla de betalda klient databaserna (9). Efter återställningen av regionen blir det gamla presidentval automatiskt de sekundära. Nu blir de presidentval igen.
* Ange de återställda utvärderings databaser som har ändrats i DR-regionen till skrivskyddad (10).
* För varje databas i en utvärderings versions kund DR-pool som har ändrats sedan återställningen, byter du namn på eller tar bort motsvarande databas i den primära poolen för utvärderings kunder (11).
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (12).
* Ta bort DR-poolen (13).

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera återställnings tiden är det viktigt att du kör kommandot klient databasers redundans i batchar av minst 20 databaser.

### <a name="benefit"></a>Fördelar

De främsta fördelarna med den här strategin är att den ger det högsta service avtalet för de betalande kunderna. Det garanterar också att de nya utvärderingarna avblockeras så snart som en utvärderings version av en utvärderings version skapas. 

### <a name="trade-off"></a>Kompromiss

Den här inställningen ökar den totala kostnaden för klient databaserna med kostnaden för den sekundära DR-poolen för betalda kunder. Dessutom, om den sekundära poolen har en annan storlek, kan de betalande kunderna uppleva lägre prestanda efter redundansväxlingen tills pool uppgraderingen i DR-regionen har slutförts.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografiskt distribuerat program med skiktad tjänst

Jag har ett vuxen SaaS-program med service erbjudanden på nivå. Jag vill erbjuda ett mycket aggressivt SLA till mina betalda kunder och minimera risken för påverkan på grund av avbrott, eftersom även ett kort avbrott kan orsaka att kunden blir missnöjd. Det är viktigt att de betalar kunderna alltid kan komma åt sina data. Testerna är kostnads fria och inget service avtal erbjuds under utvärderings perioden.

Använd tre separata elastiska pooler för att stödja det här scenariot. Etablera två lika stora pooler med hög eDTU: er eller virtuella kärnor per databas i två olika regioner som ska innehålla de betalda kundernas klient databaser. Den tredje poolen som innehåller utvärderings klienterna kan ha lägre eDTU: er eller virtuella kärnor per databas och vara etablerad i någon av de två regionerna.

För att garantera den lägsta återställnings tiden under drifts tiden är de betalande kundernas klient databaser geo-replikerade med 50% av de primära databaserna i var och en av de två regionerna. På samma sätt har varje region 50% av de sekundära databaserna. På det här sättet, om en region är offline, påverkas endast 50% av de betalda kundernas databaser och måste återställas. De andra databaserna förblir intakta. Den här konfigurationen illustreras i följande diagram:

![Diagram visar ett primärt område som kallas region A och en sekundär region som kallas region B, som använder geo-replikering mellan hanterings databasen och betald kunds primära pool och sekundär pool utan replikering för en pool med utvärderings kunder.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Som i föregående scenarier är hanterings databaserna ganska aktiva så att de konfigureras som enskilda geo-replikerade databaser (1). Detta säkerställer förutsägbar prestanda för de nya kund prenumerationerna, profil uppdateringar och andra hanterings åtgärder. Region A är den primära regionen för hanterings databaserna och region B används för återställning av hanterings databaser.

De betalande kundernas klient databaser är också geo-replikerade, men med presidentval och sekundär REBINDING mellan region A och region B (2). På så sätt kan de primära klient databaserna som påverkas av avbrottet redundansväxla till den andra regionen och blir tillgängliga. Den andra halvan av klient databaserna påverkas inte alls.

Nästa diagram illustrerar de återställnings steg som ska vidtas om ett avbrott inträffar i region A.

![Diagrammet visar ett avbrott för den primära regionen, med redundans till hanterings databasen, betald kund sekundär pool och skapande och återställning av utvärderings kunder till region B.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Växla omedelbart över hanterings databaserna till region B (3).
* Ändra programmets anslutnings sträng så att den pekar på hanterings databaserna i region B. ändra hanterings databaserna för att se till att de nya kontona och klient databaserna skapas i region B och att befintliga klient databaser även finns där. De befintliga utvärderings kunderna ser sina data tillfälligt otillgängliga.
* Redundansväxla den betalda klientens databaser till pool 2 i region B för att omedelbart återställa deras tillgänglighet (4). Eftersom redundansväxlingen är en snabb ändring av metadata kan du överväga en optimering där de enskilda redundansväxlingen utlöses på begäran av slut användar anslutningarna.
* Eftersom nu pool 2 bara innehåller primära databaser, ökar den totala arbets belastningen i poolen och kan omedelbart öka dess eDTU-storlek (5) eller antalet virtuella kärnor.
* Skapa den nya elastiska poolen med samma namn och samma konfiguration i region B för utvärderings kundernas databaser (6).
* När poolen har skapats använder du geo-återställning för att återställa den enskilda utvärderings klient databasen till poolen (7). Du kan överväga att utlösa enskilda återställningar från slut användar anslutningarna eller använda andra programspecifika prioritets scheman.

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera återställnings tiden är det viktigt att du kör kommandot klient Databass-redundans i batchar av minst 20 databaser.

I det här läget är ditt program online igen i region B. Alla betalande kunder har till gång till sina data medan utvärderings kunder upplever fördröjning vid åtkomst till sina data.

När region A återställs måste du bestämma om du vill använda region B för utvärderings kunder eller återställning efter fel för att använda poolen utvärderings kunder i region A. Ett kriterium kan vara% av de databas för utvärdering av klient databaser som ändrades sedan återställningen. Oavsett det beslutet måste du balansera om de betalande klienterna mellan två pooler. Nästa diagram illustrerar processen när en utvärderings klient databas växlar tillbaka till region A.  

![Diagrammet visar återställnings steg som ska implementeras efter återställning av region A.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Avbryt alla väntande geo-återställnings begär Anden för att prova DR-poolen.
* Redundansväxla hanterings databasen (8). Efter återställningen av regionen blev den gamla primära automatiskt den sekundära. Nu blir det primärt igen.  
* Välj vilka betalda klient databaser som ska växlas tillbaka till pool 1 och initiera redundans till deras sekundära servrar (9). Efter regionens återställning blev alla databaser i pool 1 automatiskt sekundära. Nu 50% av dem blir presidentval igen.
* Minska storleken på pool 2 till den ursprungliga eDTU: en (10) eller antalet virtuella kärnor.
* Ange alla återställda utvärderings databaser i region B till skrivskyddad (11).
* För varje databas i den version av en utvärderings version som har ändrats sedan återställningen, Byt namn på eller ta bort motsvarande databas i den primära poolen för utvärdering (12).
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (13).
* Ta bort DR-poolen (14).

### <a name="benefit"></a>Fördelar

Viktiga fördelar med den här strategin är:

* Den har stöd för det mest aggressiva service avtalet för de kunder som betalar, eftersom det garanterar att ett avbrott inte kan påverka över 50% av klient databaserna.
* Det garanterar att de nya utvärderingarna är avblockerade så snart som den avslutande DR-poolen skapas under återställningen.
* Det ger en mer effektiv användning av poolens kapacitet som 50% av de sekundära databaserna i pool 1 och pool 2, vilket garanterar att den är mindre aktiv än de primära databaserna.

### <a name="trade-offs"></a>Kompromisser

De främsta kompromisserna är:

* CRUD-åtgärder mot hanterings databaser har kortare latens för slutanvändarna som är anslutna till regionen A än för slutanvändarna som är anslutna till region B när de utförs mot primärt för hanterings databaserna.
* Det kräver mer komplex design av hanterings databasen. Varje klient post har exempelvis en plats tagg som behöver ändras under redundansväxling och återställning efter fel.  
* De betalande kunderna kan uppleva lägre prestanda än vanligt förrän pool uppgraderingen i region B har slutförts.

## <a name="summary"></a>Sammanfattning

Den här artikeln fokuserar på katastrof återställnings strategier för den databas nivå som används av ett SaaS ISV-program för flera innehavare. Den strategi du väljer baseras på programmets behov, t. ex. affärs modellen, det service avtal som du vill erbjuda dina kunder, budget begränsning osv. Varje beskrivande strategi beskriver fördelarna och kompromisserna så att du kan fatta ett välgrundat beslut. Dessutom innehåller ditt specifika program andra Azure-komponenter. Vi går igenom rikt linjerna för affärs kontinuitet och dirigerar återställningen av databas nivån med dem. Mer information om hur du hanterar återställning av databas program i Azure finns i [utforma moln lösningar för haveri beredskap](designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [Azure SQL Database automatiska säkerhets kopieringar](automated-backups-overview.md).
* En översikt över kontinuitet och scenarier för affärs kontinuitet finns i [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](recovery-using-backups.md).
* Läs mer om snabbare återställnings alternativ i aktiva grupper för [geo-replikering](active-geo-replication-overview.md) och [Automatisk redundans](auto-failover-group-overview.md).
* Information om hur du använder automatiserade säkerhets kopieringar för arkivering finns i [databas kopiering](database-copy.md).
