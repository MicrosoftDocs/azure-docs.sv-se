---
title: Haveri beredskap för SaaS-appar med geo-replikering
description: Lär dig hur du använder Azure SQL Database geo-Replicas för att återställa en SaaS-app med flera innehavare i händelse av ett avbrott
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d2c1ddd1e0b5a080050e1ffeb28eded98dbfea3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652117"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Haveri beredskap för ett SaaS-program för flera innehavare med hjälp av databas geo-replikering
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du utforska ett fullständigt haveri beredskaps scenario för ett SaaS-program med flera innehavare som implementeras med hjälp av modellen för databas per klient. För att skydda appen från ett avbrott använder du [_geo-replikering_](active-geo-replication-overview.md) för att skapa repliker för katalogen och klient databaserna i en alternativ återställnings region. Om ett avbrott inträffar kan du snabbt växla över till dessa repliker för att återuppta normala affärs åtgärder. Vid redundansväxling blir databaserna i den ursprungliga regionen sekundära repliker av databaserna i återställnings regionen. När replikerna kommer tillbaka online bevaras de automatiskt upp till tillstånd för databaserna i återställnings regionen. När avbrottet har åtgärd ATS växlar du tillbaka till databaserna i den ursprungliga produktions regionen.

I den här självstudien utforskas arbets flöden för redundans och återställning efter fel. Du lär dig följande:
> [!div class="checklist"]
> 
> * Synkronisera databas och konfigurations information för elastisk pool i klient katalogen
> * Konfigurera en återställnings miljö i en alternativ region, som omfattar program, servrar och pooler
> * Använda _geo-replikering_ för att replikera katalogen och klient databaserna till återställnings regionen
> * Redundansväxla program-och katalog-och klient databaserna till återställnings regionen 
> * Senare, redundansväxla program-, katalog-och klient databaserna tillbaka till den ursprungliga regionen efter det att avbrott har åtgärd ATS
> * Uppdatera katalogen eftersom varje klient databas har redundansväxlats för att spåra den primära platsen för varje klients databas
> * Se till att programmet och den primära klient organisations databasen alltid befinner sig i samma Azure-region för att minska svars tiden  


Kontrol lera att följande krav är uppfyllda innan du påbörjar den här självstudien:
* Wingtip biljetter SaaS-databasen per klient-app distribueras. Om du vill distribuera på mindre än fem minuter, se [distribuera och utforska Wingtip-biljetter SaaS-databas per klient program](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introduktion till återställnings mönstret för geo-replikering

![Återställnings arkitektur](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)

Haveri beredskap (DR) är ett viktigt övervägande för många program, oavsett om det gäller efterföljandekrav eller affärs kontinuitet. Om det finns ett långvarigt tjänst avbrott kan en väl för beredd katastrof-plan minimera verksamhets störningar. Med geo-replikering får du de lägsta återställnings-och RTO genom att underhålla databas repliker i en återställnings region som kan växlas över till kort varsel.

En DR-plan baserad på geo-replikering består av tre distinkta delar:
* Konfigurera, skapa och underhålla återställnings miljön
* Återställnings fel i appen och databaserna till återställnings miljön om ett avbrott inträffar,
* Repatriation – redundansväxlingen av appen och databaserna tillbaka till den ursprungliga regionen när programmet har lösts 

Alla delar måste beaktas noggrant, särskilt om de körs i stor skala. Som helhet måste planen utföra flera mål:

* Konfiguration
    * Upprätta och underhålla en spegel avbildnings miljö i återställnings regionen. Att skapa elastiska pooler och replikera alla databaser i den här återställnings miljön reserverar kapaciteten i återställnings regionen. Att underhålla den här miljön innefattar att replikera nya klient databaser när de är etablerade.  
* Återställning
    * Om en skalad återställnings miljö används för att minimera dagliga kostnader måste pooler och databaser skalas upp för att få full drift kapacitet i återställnings regionen
     * Aktivera ny klient etablering i återställnings regionen så snart som möjligt  
     * Vara optimerad för återställning av klienter i prioritetsordning
     * Vara optimerad för att få klient organisationer online så snabbt som möjligt genom att utföra steg parallellt där det är praktiskt
     * Var elastisk för att Miss lyckas, startas om och idempotenta
     * Det går att avbryta processen i mitten-flygning om den ursprungliga regionen är online.
* Repatriation 
     * Redundansväxla databaser från återställnings regionen till repliker i den ursprungliga regionen med minimal påverkan på klienter: ingen data förlust och minsta period som är offline per klient.

I den här självstudien åtgärdas de här utmaningarna med hjälp av funktionerna i Azure SQL Database och Azure-plattformen:

* [Azure Resource Manager mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)för att reservera all nödvändig kapacitet så snabbt som möjligt. Azure Resource Manager mallar används för att etablera en spegel avbildning av produktions servrarna och elastiska pooler i återställnings regionen.
* [Geo-replikering](active-geo-replication-overview.md), för att skapa asynkront replikerade skrivskyddade sekundär servrar för alla databaser. Under ett avbrott växlar du över till replikerna i återställnings regionen.  När avbrottet har åtgärd ATS växlar du tillbaka till databaserna i den ursprungliga regionen utan data förlust.
* [Asynkrona](../../azure-resource-manager/management/async-operations.md) redundansväxlingen skickas i klient organisations prioritetsordning för att minimera redundansväxlingen för stora mängder databaser.
* [Shard hantering av återställnings funktioner](elastic-database-recovery-manager.md), för att ändra databas poster i katalogen under återställning och Repatriation. Dessa funktioner tillåter att appen ansluter till klient databaser oavsett plats utan att konfigurera om appen.
* [SQL Server-DNS-alias](./dns-alias-overview.md)för att möjliggöra sömlös etablering av nya klienter oavsett vilken region appen körs i. DNS-alias används också för att tillåta katalogens synkronisering för att ansluta till den aktiva katalogen oavsett dess plats.

## <a name="get-the-disaster-recovery-scripts"></a>Hämta Disaster Recovery-skript 

> [!IMPORTANT]
> Precis som alla hanterings skript för Wingtip Ticket, är DR-skripten exempel kvalitet och ska inte användas i produktionen. 

De återställnings skript som används i den här självstudien och Wingtip-programmets källkod är tillgängliga i [Wingtip Ticket SaaS-databas per innehavare GitHub-lagringsplats](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera hanterings skript för Wingtip Ticket.

## <a name="tutorial-overview"></a>Självstudier – översikt
I den här självstudien använder du först geo-replikering för att skapa repliker av Wingtip Ticket-programmet och dess databaser i en annan region. Sedan växlar du över till den här regionen för att simulera återställning från ett avbrott. När det är klart fungerar programmet fullständigt i återställnings regionen.

Senare i ett separat Repatriation-steg växlar du över katalogen och klient databaserna i återställnings regionen till den ursprungliga regionen. Programmet och databaserna förblir tillgängliga i hela Repatriation. När det är klart fungerar programmet fullständigt i den ursprungliga regionen.

> [!Note]
> Programmet återställs till den _kopplade regionen_ i den region där programmet har distribuerats. Mer information finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md).

## <a name="review-the-healthy-state-of-the-application"></a>Granska det felfria tillståndet för programmet

Innan du påbörjar återställnings processen granskar du det normala hälso tillståndet för programmet.
1. I webbläsaren öppnar du Wingtip Ticket Events Hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net – Ersätt &lt; User &gt; med distributionens användar värde).
    * Rulla längst ned på sidan och Lägg märke till katalog serverns namn och plats i sidfoten. Platsen är den region där du distribuerade appen.
    *Tips: Hovra musen över platsen för att förstora visningen.* 
     ![ Felfritt tillstånd för events Hub i den ursprungliga regionen](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Klicka på Contoso konsert Hall-klienten och öppna dess händelse sida.
    * Lägg märke till klient serverns namn i sidfoten. Platsen kommer att vara samma som katalog serverns plats.

3. Öppna resurs gruppen där appen har distribuerats i [Azure Portal](https://portal.azure.com)
    * Lägg märke till den region där servrarna har distribuerats. 

## <a name="sync-tenant-configuration-into-catalog"></a>Synkronisera klient konfiguration i katalog

I den här uppgiften startar du en process som synkroniserar konfigurationen av servrarna, elastiska pooler och databaser i klient katalogen. Processen ser till att den här informationen är aktuell i katalogen.  Processen fungerar med den aktiva katalogen, oavsett om den finns i den ursprungliga regionen eller i återställnings regionen. Konfigurations informationen används som en del av återställnings processen för att säkerställa att återställnings miljön är konsekvent med den ursprungliga miljön och senare under Repatriation för att säkerställa att den ursprungliga regionen blir konsekvent med ändringar som gjorts i återställnings miljön. Katalogen används också för att hålla reda på återställnings statusen för klient resurser

> [!IMPORTANT]
> För enkelhetens skull implementeras Sync-processen och andra tids krävande återställnings-och Repatriation-processer i de här självstudierna som lokala PowerShell-jobb eller sessioner som körs under klientens användar inloggning. De autentiseringstoken som utfärdas när du loggar in upphör att gälla efter flera timmar och jobben kommer att Miss sen. I ett produktions scenario bör långvariga processer implementeras som pålitliga Azure-tjänster av någon typ, som körs under ett huvud namn för tjänsten. Se [använda Azure PowerShell för att skapa ett huvud namn för tjänsten med ett certifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. Öppna filen. ..\Learning Modules\UserConfig.psm1 i _POWERSHELL ISE_. Ersätt `<resourcegroup>` och `<user>` på raderna 10 och 11 med det värde som används när du distribuerade appen.  Spara filen!

2. I *POWERSHELL ISE* öppnar du ..\Learning Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptet och anger:
    * **$DemoScenario = 1**, starta ett bakgrunds jobb som synkroniserar klient servern och konfigurations information för poolen i katalogen

3. Tryck på **F5** för att köra Sync-skriptet. En ny PowerShell-session öppnas för att synkronisera konfigurationen av klient resurserna.
![Skärm bild som visar den nya PowerShell-sessionen som är öppen för att synkronisera konfigurationen av klient resurserna.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Låt PowerShell-fönstret köras i bakgrunden och fortsätt med resten av självstudien. 

> [!Note]
> Synkroniseringsprocessen ansluter till katalogen via ett DNS-alias. Det här aliaset ändras under återställnings-och Repatriation så att det pekar på den aktiva katalogen. Vid synkroniseringen hålls katalogen uppdaterad med alla ändringar av databas-eller konfigurations ändringar som gjorts i återställnings regionen.  Under Repatriation tillämpas dessa ändringar på motsvarande resurser i den ursprungliga regionen.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Skapa sekundära databas repliker i återställnings regionen

I den här uppgiften startar du en process som distribuerar en duplicerad App-instans och replikerar katalogen och alla klient databaser till en återställnings region.

> [!Note]
> Den här självstudien lägger till skydd för geo-replikering till exempel programmet Wingtip-biljetter. I ett produktions scenario för ett program som använder geo-replikering skulle varje klient tillhandahållas med en geo-replikerad databas från början. Se [utforma tjänster med hög tillgänglighet med hjälp av Azure SQL Database](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. I *POWERSHELL ISE* öppnar du ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptet och anger följande värden:
    * **$DemoScenario = 2**, skapa spegel avbildnings återställnings miljö och replikera katalog-och klient databaser

2. Tryck **F5** för att köra skriptet. En ny PowerShell-session öppnas för att skapa replikerna.
![Synkronisera process](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Granska det normala program läget

I det här läget körs programmet normalt i den ursprungliga regionen och skyddas nu av geo-replikering.  Skrivskyddade sekundära repliker finns i återställnings regionen för alla databaser. 

1. I Azure Portal tittar du på resurs grupperna och noterar att en resurs grupp har skapats med-Recovery-suffixet i återställnings regionen. 

2. Utforska resurserna i återställnings resurs gruppen.  

3. Klicka på Contoso konsert Hall-databasen på _tenants1-DPT- &lt; User &gt; -Recovery-_ servern.  Klicka på Geo-Replication till vänster. 

    ![Contoso konsert geo-replikeringslänken-länk](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

I kartan över Azure-regioner noterar du länken för geo-replikering mellan den primära i den ursprungliga regionen och den sekundära i återställnings regionen.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Redundansväxla programmet till återställnings regionen

### <a name="geo-replication-recovery-process-overview"></a>Översikt över återställnings processen för geo-replikering

Återställnings skriptet utför följande uppgifter:

1. Inaktiverar Traffic Manager-slutpunkten för webbappen i den ursprungliga regionen. Om du inaktiverar slut punkten hindras användare från att ansluta till appen i ett ogiltigt tillstånd om den ursprungliga regionen är online under återställningen.

1. Använder en Tvingad redundansväxling av katalog databasen i återställnings regionen för att göra den till den primära databasen och uppdaterar _activecatalog_ -aliaset så att det pekar på återställnings katalog servern.

1. Uppdaterar _newtenant_ -aliaset så att det pekar på klient servern i återställnings regionen. Om du ändrar det här aliaset säkerställs att databaserna för alla nya klienter är etablerade i återställnings regionen. 

1. Markerar alla befintliga klienter i återställnings katalogen som offline för att förhindra åtkomst till klient databaser innan de har redundansväxlats.

1. Uppdaterar konfigurationen av alla elastiska pooler och replikerade enskilda databaser i återställnings regionen för att spegla deras konfiguration i den ursprungliga regionen. (Den här uppgiften behövs bara om pooler eller replikerade databaser i återställnings miljön skalas ned under normal drift för att minska kostnaderna).

1. Aktiverar Traffic Manager-slutpunkten för webbappen i återställnings regionen. Om du aktiverar den här slut punkten kan programmet etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

1. Skickar batchar över begär Anden om att framtvinga redundans över databaser i prioritetsordning.
    * Batchar är organiserade så att databaser redundansväxlas parallellt över alla pooler.
    * Redundansväxlingen skickas med asynkrona åtgärder så att de skickas snabbt och flera begär Anden kan bearbetas samtidigt.

   > [!Note]
   > I ett avbrott är de primära databaserna i den ursprungliga regionen offline.  Framtvinga redundans på den sekundära avbryter anslutningen till den primära utan att försöka tillämpa några kvarvarande transaktioner i kö. I ett DR-scenario som den här självstudien, om det finns någon uppdaterings aktivitet vid tidpunkten för redundansväxlingen, kan data gå förlorade. Senare, under Repatriation, när du växlar över databaser i återställnings regionen tillbaka till den ursprungliga regionen, används normal redundans för att säkerställa att det inte finns någon data förlust.

1. Övervakar tjänsten för att fastställa när databaser har redundansväxlats. När en klient databas har redundansväxlats, uppdaterar den katalogen för att registrera återställnings läget för klient databasen och markera klienten som online.
    * Klient databaser kan nås av programmet så snart de är markerade online i katalogen.
    * En summa av ROWVERSION-värden i klient databasen lagras i katalogen. Det här värdet fungerar som ett finger avtryck som gör att Repatriation-processen kan avgöra om databasen har uppdaterats i återställnings regionen.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Kör skriptet för att redundansväxla till återställnings regionen

Tänk på att det finns ett avbrott i den region där programmet distribueras och kör återställnings skriptet:

1. I *POWERSHELL ISE* öppnar du ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptet och anger följande värden:
    * **$DemoScenario = 3**, Återställ appen till en återställnings region genom att redundansväxla till repliker

2. Tryck **F5** för att köra skriptet.  
    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en serie med PowerShell-jobb som körs parallellt. Dessa jobb växlar över klient databaser till återställnings regionen.
    * Återställnings regionen är den _kopplade region_ som är kopplad till den Azure-region där du distribuerade programmet. Mer information finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md). 

3. Övervaka status för återställnings processen i PowerShell-fönstret.
    ![redundansväxling](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Om du vill utforska koden för återställnings jobben granskar du PowerShell-skripten i mappen. ..\Learning Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen

När program slut punkten är inaktive rad i Traffic Manager är programmet inte tillgängligt. När katalogen har redundansväxlats till återställnings regionen och alla klienter har marker ATS offline, så kommer programmet att tas online igen. Även om programmet är tillgängligt visas varje klient offline i händelse navet tills databasen har redundansväxlats. Det är viktigt att utforma ditt program för att hantera offline-klient databaser.

1. Efter att katalog databasen har återställts uppdaterar du Wingtip Ticket Events Hub i webbläsaren.
   * I sidfoten ser du till att katalog server namnet har ett suffix för _återställning_ och finns i återställnings regionen.
   * Observera att klienter som ännu inte har återställts markeras som offline och inte kan väljas.  

     > [!Note]
     > Om det bara finns några få databaser att återställa kanske du inte kan uppdatera webbläsaren innan återställningen har slutförts, så du kanske inte ser klient organisationerna när de är offline. 

     ![Events Hub offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Om du öppnar en offline-klients händelse sida direkt visas ett meddelande om klientens offline. Om contoso konsert Hall till exempel är offline kan du försöka öppna http://events.wingtip-dpt.&lt . användare &gt; . Trafficmanager.net/contosoconcerthall ![ contoso offline-sida](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställnings regionen
Du kan etablera nya klienter i återställnings regionen även innan alla befintliga klient databaser har redundansväxlats.  

1. I *POWERSHELL ISE* öppnar du ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptet och anger följande egenskap:
    * **$DemoScenario = 4**, etablera en ny klient i återställnings regionen

2. Tryck på **F5** för att köra skriptet och etablera den nya klienten. 

3. Sidan Hawthorn Hall-händelser öppnas i webbläsaren när den är klar. Observera från sidfoten att Hawthorn-databasen är etablerad i återställnings regionen.
    ![Sidan Hawthorn, evenemang](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. I webbläsaren uppdaterar du sidan Wingtip Ticket Events Hub för att se Hawthorn Hall ingår. 
    * Om du etablerade Hawthorn Hall utan att behöva vänta på att de andra klienterna ska kunna återställas, kan andra klienter fortfarande vara offline.


## <a name="review-the-recovered-state-of-the-application"></a>Granska programmets återställda tillstånd

När återställnings processen har slutförts fungerar programmet och alla klienter fullständigt i återställnings regionen. 

1. När visningen i fönstret i PowerShell-konsolen visar att alla klienter återställs, uppdaterar du Events-hubben.  Klienterna kommer att visas online, inklusive den nya klienten, Hawthorn Hall.

    ![återställda och nya klienter i hubben Events](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Öppna listan över resurs grupper i [Azure Portal](https://portal.azure.com).  
    * Lägg märke till den resurs grupp som du har distribuerat, plus återställnings resurs gruppen med _-återställnings-_ suffixet.  Återställnings resurs gruppen innehåller alla resurser som skapats under återställnings processen, plus nya resurser som skapas under avbrottet.  

3. Öppna återställnings resurs gruppen och Lägg märke till följande objekt:
   * Återställnings versionerna av katalogen och tenants1-servrar, med _-Recovery-_ suffix.  Den återställda katalogen och klient databaserna på dessa servrar har alla namn som används i den ursprungliga regionen.

   * _Tenants2-DPT- &lt; User &gt; -Recovery_ SQL Server.  Den här servern används för att tillhandahålla nya klienter under avbrottet.
   * App Service som heter, _Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User&gt_;, som är återställnings instansen av events-appen. 

     ![Azure Recovery-resurser](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 

4. Öppna SQL Server _-tenants2-DPT- &lt; User &gt; -Recovery_ .  Observera att den innehåller databasen _hawthornhall_ och den elastiska poolen _Pool1_.  _Hawthornhall_ -databasen har kon figurer ATS som en elastisk databas i den elastiska _Pool1_ -poolen.

5. Gå tillbaka till resurs gruppen och klicka på Contoso konsert Hall-databasen på _tenants1-DPT- &lt; User &gt; -Recovery-_ servern. Klicka på Geo-Replication till vänster.

    ![Contoso-databas efter redundans](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Ändra klient data 
I den här uppgiften uppdaterar du en av klient databaserna. 

1. Leta upp händelse listan för Contoso konsert Hall i webbläsaren och anteckna det senaste händelse namnet.
2. I *POWERSHELL ISE*, i ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skript, anger du följande värde:
    * **$DemoScenario = 5** Ta bort en händelse från en klient i återställnings regionen
3. Tryck på **F5** för att köra skriptet
4. Uppdatera sidan contoso konsert evenemang ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net/contosoconcerthall – ersätta &lt; användare &gt; med din distributions användar värde) och Observera att den senaste händelsen har tagits bort.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriate programmet till den ursprungliga produktions regionen

Den här aktiviteten repatriates programmet till dess ursprungliga region. I ett verkligt scenario initierar du Repatriation när avbrottet har lösts.

### <a name="repatriation-process-overview"></a>Översikt över Repatriation-processen

![Repatriation-arkitektur](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Repatriation-processen:
1. Avbryter eventuella utestående återställnings begär Anden för databas återställning.
2. Uppdaterar _newtenant_ -aliaset så att det pekar på klientens server i ursprungs regionen. Om du ändrar det här aliaset säkerställs att databaserna för alla nya klienter nu kommer att tillhandahållas i käll regionen.
3. Fröer alla ändrade klient data till den ursprungliga regionen
4. Växlar över klient databaser i prioritetsordning.

Vid redundansväxling flyttas databasen till den ursprungliga regionen effektivt. När databasen växlar över släpps alla öppna anslutningar och databasen är inte tillgänglig under några sekunder. Program ska skrivas med logik för omprövning för att säkerställa att de ansluter igen.  Även om den här korta anslutningen ofta inte har märkts, kan du välja att repatriate databaser utanför kontors tid. 


### <a name="run-the-repatriation-script"></a>Kör Repatriation-skriptet
Nu ska vi föreställa dig att avbrottet är löst och köra Repatriation-skriptet.

1. I *POWERSHELL ISE* går du till ..\Learning Modules\Business-kontinuitet och katastrof Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 skriptet.

2. Kontrol lera att processen för katalog synkronisering fortfarande körs i PowerShell-instansen.  Om det behövs startar du om det genom att ange:
    * **$DemoScenario = 1**, Starta synkronisering av klient server, pool och konfigurations information för databas i katalogen
    * Tryck **F5** för att köra skriptet.

3.  Starta Repatriation-processen genom att ange:
    * **$DemoScenario = 6**, Repatriate appen i sin ursprungliga region
    * Tryck på **F5** för att köra återställnings skriptet i ett nytt PowerShell-fönster.  Repatriation tar flera minuter och kan övervakas i PowerShell-fönstret.
    ![Repatriation process](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. När skriptet körs uppdaterar du sidan Events Hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net)
    * Observera att alla klienter är online och tillgängliga i hela den här processen.

5. När Repatriation har slutförts uppdaterar du Events-hubben och öppnar sidan händelser för Hawthorn Hall. Observera att den här databasen har repatriated till den ursprungliga regionen.
    ![Events Hub-repatriated](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Utforma programmet för att säkerställa att appen och databasen samplaceras 
Programmet har utformats så att det alltid ansluter från en instans i samma region som klient databasen. Den här designen minskar svars tiden mellan programmet och databasen. Den här optimeringen förutsätter att interaktion mellan app-till-databas är chattier än användar-till-app-interaktionen.  

Klient databaser kan spridas över återställnings-och original regioner under en tid under Repatriation. För varje databas söker appen upp den region där databasen finns genom att göra en DNS-sökning på klient serverns namn. I SQL Database är Server namnet ett alias. Namnet på den aliasna servern innehåller regionens namn. Om programmet inte finns i samma region som databasen omdirigeras det till instansen i samma region som-servern. Omdirigera till instans i samma region som databasen minimerar svars tiden mellan appen och databasen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> 
> * Synkronisera databas och konfigurations information för elastisk pool i klient katalogen
> * Konfigurera en återställnings miljö i en alternativ region, som omfattar program, servrar och pooler
> * Använda _geo-replikering_ för att replikera katalogen och klient databaserna till återställnings regionen
> * Redundansväxla program-och katalog-och klient databaserna till återställnings regionen 
> * Återställ program-, katalog-och klient databaserna till den ursprungliga regionen efter det att avbrott har åtgärd ATS

Du kan lära dig mer om tekniker Azure SQL Database ger affärs kontinuitet i [översikts dokumentationen för affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md) .

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)