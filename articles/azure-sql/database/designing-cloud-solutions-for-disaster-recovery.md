---
title: Designa globalt tillgängliga tjänster
description: Lär dig mer om program design för tjänster med hög tillgänglighet med hjälp av Azure SQL Database.
keywords: moln haveri beredskap, katastrof återställnings lösningar, säkerhets kopiering av AppData, geo-replikering, planering av affärs kontinuitet
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91321687"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Designa globalt tillgängliga tjänster med hjälp av Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

När du skapar och distribuerar moln tjänster med Azure SQL Database använder du [aktiva geo-replikering](active-geo-replication-overview.md) eller [grupper med automatisk redundans](auto-failover-group-overview.md) för att ge återhämtning till regionala avbrott och katastrof fel. Med samma funktion kan du skapa globalt distribuerade program som är optimerade för lokal åtkomst till data. I den här artikeln beskrivs vanliga program mönster, inklusive fördelarna och de olika alternativen för att handla.

> [!NOTE]
> Om du använder Premium-eller Affärskritisk databaser och elastiska pooler kan du göra dem flexibla till regionala drifts störningar genom att konvertera dem till zonens redundant distributions konfiguration. Se [zoner-redundanta databaser](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scenario 1: använda två Azure-regioner för affärs kontinuitet med minimal stillestånds tid

I det här scenariot har programmen följande egenskaper:

* Programmet är aktivt i en Azure-region
* Alla Databassessioner kräver Läs-och skriv åtkomst (RW) till data
* Webb nivån och data nivån måste vara samordnad för att minska svars tid och trafik kostnader
* I grunden är nedtid en högre affärs risk för dessa program än data förlust

I det här fallet är program distributionens topologi optimerad för att hantera regionala haverier när alla program komponenter behöver växlas över tillsammans. Diagrammet nedan visar topologin. För geografisk redundans distribueras programmets resurser till region A och B. Men resurserna i region B används inte förrän regionen A Miss lyckas. En failover-grupp konfigureras mellan de två regionerna för att hantera databas anslutning, replikering och redundans. Webb tjänsten i båda regionerna har kon figurer ATS för åtkomst till databasen via Read-Write Listener **&lt; redundans-Group-name &gt; . Database.Windows.net** (1). Azure Traffic Manager har kon figurer ATS för att använda [prioritets cirkulations metod](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) används endast i den här artikeln för illustrations syfte. Du kan använda valfri belastnings Utjämnings lösning som stöder metoden prioriterad routning.

Följande diagram visar den här konfigurationen före ett avbrott:

![Scenario 1. Konfiguration före avbrottet.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Efter ett avbrott i den primära regionen identifierar SQL Database att den primära databasen inte är tillgänglig och utlöser redundansväxling till den sekundära regionen baserat på parametrarna i den automatiska redundansväxlingen (1). Beroende på ditt service avtal för program kan du konfigurera en Respitperiod som styr tiden mellan identifieringen av avbrottet och redundansväxlingen. Det är möjligt att Azure Traffic Manager initierar slut punkts redundansväxlingen innan gruppen för växling vid fel utlöser redundansväxlingen av databasen. I så fall kan inte webb programmet återansluta direkt till-databasen. Men åter anslutningar kommer att lyckas automatiskt så snart databasen har slutförts. När den felande regionen återställs och online igen återansluter den gamla primära servern automatiskt som en ny sekundär. Diagrammet nedan visar konfigurationen efter redundansväxlingen.

> [!NOTE]
> Alla transaktioner som har utförts efter redundansväxlingen förloras under åter anslutningen. När redundansväxlingen är klar kan programmet i region B kunna återansluta och starta om bearbetningen av användar förfrågningarna. Både webb programmet och den primära databasen finns nu i region B och är samplacerade.

![Scenario 1. Konfiguration efter redundans](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Om ett avbrott inträffar i region B pausas replikeringen mellan den primära databasen och den sekundära databasen, men länken mellan de två förblir intakt (1). Traffic Manager upptäcker att anslutningen till region B är bruten och markerar slut punktens webbapp 2 som degraderad (2). Programmets prestanda påverkas inte i det här fallet, men databasen exponeras och därmed högre risk för data förlust i fall region A Miss lyckas i följd.

> [!NOTE]
> Vid haveri beredskap rekommenderar vi att konfigurationen med program distribution är begränsad till två regioner. Detta beror på att de flesta av Azures geografiska områden bara har två regioner. Den här konfigurationen skyddar inte ditt program från ett samtidigt oåterkalleligt haveri i båda regionerna. Om det är osannolikt att ett fel uppstår kan du återställa databaserna i en tredje region med [geo Restore-åtgärden](disaster-recovery-guidance.md#recover-using-geo-restore).
>

 När avbrottet har begränsats omsynkroniseras den sekundära databasen automatiskt med den primära databasen. Under synkroniseringen kan prestanda för den primära påverkas. Den speciella påverkan beror på mängden data som den nya primära primära förvärvade sedan redundansväxlingen. 

> [!NOTE]
> När avbrottet har begränsats kommer Traffic Manager att börja dirigera anslutningarna till programmet i region A som en slut punkt med högre prioritet. Om du vill behålla primärt i region B en stund bör du ändra prioritets tabellen i trafic Manager-profilen. 
>
 
 Följande diagram illustrerar ett avbrott i den sekundära regionen:

![Scenario 1. Konfiguration efter ett avbrott i den sekundära regionen.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

De främsta **fördelarna** med det här design mönstret är:

* Samma webb program distribueras till båda regionerna utan en landsspecifika konfiguration och kräver ingen ytterligare logik för att hantera redundans.
* Program prestanda påverkas inte av redundans eftersom webb programmet och databasen alltid är samplacerade.

Den största **kompromissen** är att program resurserna i region B är underutnyttjade som mest av tiden.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scenario 2: Azure-regioner för affärs kontinuitet med maximal data bevarande

Det här alternativet passar bäst för program med följande egenskaper:

* Data förlust är höga affärs risker. Det går bara att använda redundansväxlingen för databasen som en sista utväg om avbrottet orsakas av ett oåterkalleligt fel.
* Programmet har stöd för skrivskyddade och Läs-och skriv åtgärder och kan köras i "skrivskyddat läge" under en viss tids period.

I det här mönstret växlar programmet till skrivskyddat läge när Läs-och skriv anslutningar börjar få timeout-fel. Webb programmet distribueras till båda regionerna och innehåller en anslutning till Läs-och skriv lyssnar-slutpunkten och en annan anslutning till den skrivskyddade slut punkten för lyssnare (1). Den Traffic Manager profilen ska använda [prioriterad routning](../../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Slut punkts övervakningen](../../traffic-manager/traffic-manager-monitoring.md) ska vara aktive rad för program slut punkten i varje region (2).

Följande diagram visar den här konfigurationen före ett avbrott:

![Scenario 2. Konfiguration före avbrottet.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

När Traffic Manager identifierar ett anslutnings problem till region A växlar den automatiskt användar trafik till program instansen i region B. Med det här mönstret är det viktigt att du ställer in Grace-perioden med data förlust till ett tillräckligt högt värde, till exempel 24 timmar. Det säkerställer att data förlust förhindras om avbrott minimeras inom den tiden. När webb programmet i region B är aktiverat startar inte Läs-och skriv åtgärder. Vid det här läget bör den växla till skrivskyddat läge (1). I det här läget dirigeras begär Anden automatiskt till den sekundära databasen. Om avbrottet orsakas av ett oåterkalleligt haveri, kan det bero på att det inte kan undvikas inom respitperioden. När den går ut utlöser redundansväxlingen. När den Läs-och skriv lyssnaren blir tillgänglig och anslutningarna stoppas (2). Följande diagram illustrerar de två stegen i återställnings processen.

> [!NOTE]
> Om avbrottet i den primära regionen minskas inom respitperioden, Traffic Manager identifierar anslutningen i den primära regionen och växlar användar trafiken tillbaka till program instansen i region A. Den program instansen återupptas och körs i Läs-och skriv läge med hjälp av den primära databasen i regionen A som illustreras i föregående diagram.

![Scenario 2. Katastrof återställnings steg.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Om ett avbrott inträffar i region B identifierar Traffic Manager slut punktens webb-app-2 i region B och markerar den nedgraderad (1). Under tiden växlar redundans gruppen till den skrivskyddade lyssnaren till region A (2). Detta avbrott påverkar inte slutanvändarens upplevelse, men den primära databasen exponeras under avbrottet. Följande diagram illustrerar ett haveri i den sekundära regionen:

![Scenario 2. Avbrott i den sekundära regionen.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

När avbrottet har begränsats synkroniseras den sekundära databasen omedelbart med den primära databasen och den skrivskyddade lyssnaren växlas tillbaka till den sekundära databasen i region B. Under synkroniseringen av den primära kundens prestanda påverkas något beroende på mängden data som måste synkroniseras.

Det här design mönstret har flera **fördelar**:

* Det förhindrar data förlust under de tillfälliga drift stoppen.
* Stillestånds tiden beror bara på hur snabbt Traffic Manager identifierar anslutnings fel, vilket kan konfigureras.

**Kompromissen** är att programmet måste kunna köras i skrivskyddat läge.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scenario 3: program flyttning till en annan geografi utan data förlust och nästan noll stillestånds tid

I det här scenariot har programmet följande egenskaper:

* Slutanvändarna får åtkomst till programmet från olika geografiska områden
* Programmet innehåller skrivskyddade arbets belastningar som inte är beroende av fullständig synkronisering med de senaste uppdateringarna
* Skriv åtkomst till data bör stödjas i samma geografi för merparten av användarna
* Läs fördröjning är avgörande för slut användar upplevelsen

För att uppfylla dessa krav måste du garantera att användar enheten **alltid** ansluter till programmet som distribueras i samma geografi för de skrivskyddade åtgärderna, till exempel att bläddra bland data, analyser osv. OLTP-åtgärderna bearbetas i samma geografi som det **mesta av tiden**. Till exempel under dagen då OLTP-åtgärder bearbetas i samma geografi, men under de tids perioder som de kunde bearbetas i en annan geografi. Om slut användar aktiviteten oftast sker under arbets tiden kan du garantera optimala prestanda för de flesta av användarna. Följande diagram visar den här topologin.

Programmets resurser bör distribueras i varje geografiskt område där du har avsevärd användning efter frågan. Om ditt program till exempel används aktivt i USA, måste Europeiska unionen och Asien, sydöstra programmet distribueras till alla dessa geografiska områden. Den primära databasen ska växlas dynamiskt från en geografi till nästa i slutet av arbets tiden. Den här metoden kallas "Följ solen". OLTP-arbetsbelastningen ansluter alltid till databasen via Read-Write Listener **&lt; redundans-Group-name &gt; . Database.Windows.net** (1). Den skrivskyddade arbets belastningen ansluter till den lokala databasen direkt med databaserna Server slut punkts **&lt; server-name &gt; . Database.Windows.net** (2). Traffic Manager konfigureras med [routningsmetod för prestanda](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Det garanterar att slutanvändarens enhet är ansluten till webb tjänsten i den närmaste regionen. Traffic Manager konfigureras med slut punkts övervakning aktiverat för varje slut punkt för webb tjänst (3).

> [!NOTE]
> Konfigurationen av redundanskonfiguration definierar vilken region som används för redundans. Eftersom den nya primären finns i en annan geografi, resulterar redundansväxlingen i längre latens för både OLTP-och skrivskyddade arbets belastningar tills den berörda regionen är online igen.

![Scenario 3. Konfiguration med primär i östra USA.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

I slutet av dagen, till exempel vid en lokal tid på 11 PM, ska de aktiva databaserna växlas till nästa region (Europa, norra). Den här uppgiften kan automatiseras helt med hjälp av [Azure Logic Apps](../../logic-apps/logic-apps-overview.md). Uppgiften omfattar följande steg:

* Byt primär server i gruppen redundans till norra Europa med vänlig redundans (1)
* Ta bort gruppen för redundans mellan USA, östra och Europa, norra
* Skapa en ny failover-grupp med samma namn, men mellan Nord Europa och Asien, östra (2).
* Lägg till den primära i Nord Europa och sekundär i Asien, östra till den här gruppen för redundans (3).

Följande diagram illustrerar den nya konfigurationen efter den planerade redundansväxlingen:

![Scenario 3. Över gång från primär till Nord Europa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Om ett avbrott inträffar i Nord Europa initieras den automatiska databas växlings filen av gruppen redundans, vilket effektivt leder till att programmet flyttas till nästa region före schemat (1).  I så fall är östra USA den enda återstående sekundära regionen tills norra Europa är online igen. De återstående två regionerna betjänar kunderna i alla tre geografiska områden genom att växla roller. Azure Logic Apps måste justeras i enlighet med detta. Eftersom de återstående regionerna får ytterligare användar trafik från Europa påverkas programmets prestanda inte bara av ytterligare svars tid, utan också med ett ökat antal slut användar anslutningar. När avbrottet har begränsats i Nord Europa synkroniseras den sekundära databasen omedelbart med den aktuella primära databasen. Följande diagram illustrerar ett avbrott i Nord Europa:

![Scenario 3. Avbrott i Nord Europa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Du kan minska tiden då slutanvändarens upplevelse i Europa försämras av lång latens. Om du vill göra det proaktivt ska du distribuera en program kopia och skapa den eller de sekundära databaserna i en annan lokal region (Västeuropa) som en ersättning för instansen offline i Nord Europa. När den senare är online kan du välja om du vill fortsätta att använda Västeuropa eller om du vill ta bort kopian av programmet där och gå tillbaka till att använda Nord Europa.

De främsta **fördelarna** med den här designen är:

* Det skrivskyddade arbets belastnings programmet har alltid åtkomst till data i region stängnings regionen.
* Läs-och skriv arbets belastningen har åtkomst till data i den närmaste regionen under perioden för den högsta aktiviteten i varje geografi
* Eftersom programmet distribueras till flera regioner, kan det överleva en förlust av en av regionerna utan betydande stillestånds tid.

Men det finns vissa **kompromisser**:

* Ett regionalt avbrott resulterar i att geografin påverkas av längre latens. Både Läs-och skriv åtgärder hanteras av programmet i en annan geografi.
* De skrivskyddade arbets belastningarna måste ansluta till en annan slut punkt i varje region.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planering av affärs kontinuitet: Välj en program design för haveri beredskap för molnet

Din specifika strategi för haveri beredskap för molnet kan kombinera eller utöka de här design mönstren så att de passar bäst för ditt program.  Som tidigare nämnts är den strategi du väljer baserad på det service avtal som du vill erbjuda dina kunder och topologin för program distribution. I följande tabell jämförs de val som baseras på återställnings punkt mål (återställnings punkt mål) och beräknad återställnings tid (ERT) för att hjälpa ditt beslut.

| Mönster | RPO | ERT |
|:--- |:--- |:--- |
| Aktiv-passiv distribution för haveri beredskap med samplacerad databas åtkomst |Läs-och skriv åtkomst < 5 SEK |Tid för identifiering av identifiering + DNS TTL |
| Aktiv-aktiv distribution för belastnings utjämning för program |Läs-och skriv åtkomst < 5 SEK |Tid för identifiering av identifiering + DNS TTL |
| Aktiv-passiv distribution för data bevarande |Skrivskyddad åtkomst < 5 SEK | Skrivskyddad åtkomst = 0 |
||Läs-och Skriv behörighet = noll | Läs-och Skriv behörighet = identifierings tid för identifiering + respitperiod med data förlust |
|||

## <a name="next-steps"></a>Nästa steg

* För en översikt över verksamhets kontinuitet och scenarier, se [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Mer information om aktiv geo-replikering finns i [aktiv geo-replikering](active-geo-replication-overview.md).
* Mer information om grupper för automatisk redundans finns i [grupper för automatisk redundans](auto-failover-group-overview.md).
* Information om aktiv geo-replikering med elastiska pooler finns i [katastrof återställnings strategier för elastisk pool](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
