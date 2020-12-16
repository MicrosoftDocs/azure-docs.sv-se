---
title: 'SaaS-appar: Geo-redundanta säkerhets kopieringar för haveri beredskap'
description: Lär dig att använda Azure SQL Database geo-redundanta säkerhets kopieringar för att återställa en SaaS-app med flera innehavare i händelse av ett avbrott
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607719"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Använd geo-återställning för att återställa ett SaaS-program med flera innehavare från databas säkerhets kopior
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien utforskas ett fullständigt haveri beredskaps scenario för SaaS-program med flera innehavare som implementeras med databasen per klient modell. Du använder [geo-återställning](recovery-using-backups.md) för att återställa katalogen och klient databaserna från automatiskt underhållna geo-redundanta säkerhets kopieringar till en alternativ återställnings region. När avbrottet har åtgärd ATS använder du [geo-replikering](active-geo-replication-overview.md) för att repatriate ändrade databaser till sin ursprungliga region.

![Diagrammet visar en original-och återställnings region, som båda har en app, katalog, original-eller speglings avbildning av servrar och pooler, automatisk säkerhets kopiering till lagring, med återställnings regionen som accepterar geo-replikering av säkerhets kopiering och har Server och pool för nya klienter.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Geo-återställning är en katastrof återställnings lösning med låg kostnad för Azure SQL Database. Att återställa från geo-redundanta säkerhets kopieringar kan dock leda till data förlust på upp till en timme. Det kan ta lång tid, beroende på storleken på varje databas. 

> [!NOTE]
> Återställ program med lägsta möjliga återställnings-och RTO med hjälp av geo-replikering i stället för geo-återställning.

Den här självstudien utforskar både Restore-och Repatriation-arbetsflöden. Lär dig att:
> [!div class="checklist"]
> 
> * Synkronisera databas och konfigurations information för elastisk pool i klient katalogen.
> * Konfigurera en speglad avbildnings miljö i en återställnings region som innehåller program, servrar och pooler.   
> * Återställa katalog-och klient databaserna med geo-återställning.
> * Använd geo-replikering för att repatriate klient katalogen och ändrade klient databaser när avbrottet har åtgärd ATS.
> * Uppdatera katalogen när varje databas återställs (eller repatriated) för att spåra den aktuella platsen för den aktiva kopian av varje innehavares databas.
> * Se till att program-och klient organisations databasen alltid är samordnad i samma Azure-region för att minska svars tiden. 
 

Innan du börjar den här självstudien måste du uppfylla följande krav:
* Distribuera Wingtip biljetter SaaS-databasen per klient-app. Om du vill distribuera på mindre än fem minuter kan du läsa [distribuera och utforska Wingtip Ticket SaaS-databas per klient program](saas-dbpertenant-get-started-deploy.md). 
* Installera Azure PowerShell. Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introduktion till återställnings mönstret för geo-återställning

Haveri beredskap (DR) är ett viktigt övervägande för många program, oavsett om det gäller efterföljandekrav eller affärs kontinuitet. Om det finns ett långvarigt tjänst avbrott kan en väl för beredd KATASTROFen minimera affärs störningar. En DR-plan baserad på Geo-återställning måste utföra flera mål:
 * Reservera all nödvändig kapacitet i den valda återställnings regionen så snart som möjligt för att säkerställa att den är tillgänglig att återställa klient databaser.
 * Upprätta en återställnings miljö för spegel avbildning som visar den ursprungliga poolen och databas konfigurationen. 
 * Tillåt annullering av återställnings processen i mitten-flygning om den ursprungliga regionen är online igen.
 * Aktivera klient etablering snabbt så att nya klient organisations registrering kan starta om så snart som möjligt.
 * Optimeras för återställning av klienter i prioritetsordning.
 * Vara optimerad för att få klient organisationer online så snart som möjligt genom att utföra steg parallellt där det är praktiskt.
 * Var elastisk för att Miss lyckas, startas om och idempotenta.
 * Repatriate databaser till sin ursprungliga region med minimal påverkan på klienter när avbrottet är löst.  

> [!NOTE]
> Programmet återställs till den kopplade regionen i den region där programmet har distribuerats. Mer information finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md).   

I den här självstudien används funktioner i Azure SQL Database och Azure-plattformen för att lösa dessa utmaningar:

* [Azure Resource Manager mallar](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)för att reservera all nödvändig kapacitet så snabbt som möjligt. Azure Resource Manager mallar används för att etablera en spegel avbildning av de ursprungliga servrarna och elastiska pooler i återställnings regionen. En separat server och pool skapas också för etablering av nya klienter.
* [Elastic Database klient bibliotek](elastic-database-client-library.md) (EDCL) för att skapa och underhålla en klient databas katalog. Den utökade katalogen innehåller regelbundet uppdaterad pool och databas konfigurations information.
* [Hanterings funktioner för Shard](elastic-database-recovery-manager.md) i EDCL, för att underhålla poster för plats poster i katalogen under återställnings-och Repatriation.  
* [Geo-återställning](../../key-vault/general/disaster-recovery-guidance.md), för att återställa katalogen och klient databaserna från automatiskt underhållna geo-redundanta säkerhets kopieringar. 
* [Asynkrona återställnings åtgärder](../../azure-resource-manager/management/async-operations.md)som skickas i klient organisations prioritets ordning placeras i kö för varje pool av systemet och bearbetas i batchar så att poolen inte överbelastas. De här åtgärderna kan avbrytas före eller under körningen om det behövs.   
* [Geo-replikering](active-geo-replication-overview.md), för att repatriate databaser till den ursprungliga regionen efter avbrottet. Det finns ingen data förlust och minimal påverkan på klienten när du använder geo-replikering.
* [SQL Server-DNS-alias](./dns-alias-overview.md)för att tillåta katalogens synkronisering att ansluta till den aktiva katalogen oavsett dess plats.  

## <a name="get-the-disaster-recovery-scripts"></a>Hämta Disaster Recovery-skript

De DR-skript som används i den här självstudien är tillgängliga i [Wingtip biljetter SaaS-databasen per klient GitHub-lagringsplats](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera hanterings skript för Wingtip Ticket.

> [!IMPORTANT]
> Precis som alla hanterings skript för Wingtip Ticket, är DR-skripten exempel kvalitet och ska inte användas i produktionen.

## <a name="review-the-healthy-state-of-the-application"></a>Granska det felfria tillståndet för programmet
Innan du påbörjar återställnings processen granskar du det normala hälso tillståndet för programmet.

1. I webbläsaren öppnar du Wingtip Ticket Events Hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net, ersätter &lt; användare &gt; med distributionens användar värde).
    
   Rulla längst ned på sidan och Lägg märke till katalog serverns namn och plats i sidfoten. Platsen är den region där du distribuerade appen.    

   > [!TIP]
   > Hovra musen över platsen för att förstora visningen.

   ![Felfritt tillstånd för events Hub i den ursprungliga regionen](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Välj contoso konsert Hall-klient och öppna dess händelse sida.

   Lägg märke till innehavarens Server namn i sidfoten. Platsen är samma som katalog serverns plats.

   ![Contoso konsert Hall, ursprunglig region](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. I [Azure Portal](https://portal.azure.com)granskar du och öppnar resurs gruppen där du distribuerade appen.

   Lägg märke till de resurser och den region där App Service-komponenterna och SQL Database har distribuerats.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Synkronisera klient konfigurationen i katalogen

I den här uppgiften startar du en process för att synkronisera konfigurationen av servrarna, elastiska pooler och databaser i klient katalogen. Den här informationen används senare för att konfigurera en speglad avbildnings miljö i återställnings regionen.

> [!IMPORTANT]
> För enkelhetens skull implementeras Sync-processen och andra långvariga återställnings-och Repatriation-processer i dessa exempel som lokala PowerShell-jobb eller sessioner som körs under klientens användar inloggning. De autentiseringstoken som utfärdas när du loggar in upphör att gälla efter flera timmar och jobben kommer att Miss sen. I ett produktions scenario bör långvariga processer implementeras som pålitliga Azure-tjänster av någon typ, som körs under ett huvud namn för tjänsten. Se [använda Azure PowerShell för att skapa ett huvud namn för tjänsten med ett certifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. Öppna filen. ..\Learning Modules\UserConfig.psm1 i PowerShell ISE. Ersätt `<resourcegroup>` och `<user>` på raderna 10 och 11 med det värde som används när du distribuerade appen. Spara filen.

2. I PowerShell ISE öppnar du ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet.

    I den här självstudien kör du vart och ett av scenarierna i det här PowerShell-skriptet, så se till att filen är öppen.

3. Ange följande:

    $DemoScenario = 1: starta ett bakgrunds jobb som synkroniserar klientens Server och konfigurations information för poolen i katalogen.

4. Om du vill köra Sync-skriptet väljer du F5. 

    Den här informationen används senare för att säkerställa att återställningen skapar en spegel avbildning av servrar, pooler och databaser i återställnings regionen.  

    ![Synkronisera process](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Låt PowerShell-fönstret köras i bakgrunden och fortsätt med resten av den här självstudien.

> [!NOTE]
> Synkroniseringsprocessen ansluter till katalogen via ett DNS-alias. Aliaset ändras under återställning och Repatriation så att det pekar på den aktiva katalogen. Synkroniseringsprocessen håller katalogen uppdaterad med alla ändringar i databasen eller poolens konfiguration som görs i återställnings regionen. Under Repatriation tillämpas dessa ändringar på motsvarande resurser i den ursprungliga regionen.

## <a name="geo-restore-recovery-process-overview"></a>Översikt över återställnings processen för geo-återställning

Återställnings processen för geo-återställning distribuerar programmet och återställer databaser från säkerhets kopior till återställnings regionen.

Återställnings processen gör följande:

1. Inaktiverar Azure Traffic Manager-slutpunkten för webbappen i den ursprungliga regionen. Om du inaktiverar slut punkten hindras användare från att ansluta till appen i ett ogiltigt tillstånd om den ursprungliga regionen är online under återställningen.

2. Etablerar en återställnings katalog server i återställnings regionen, geo-återställer katalog databasen och uppdaterar activecatalog-aliaset så att det pekar på den återställda katalog servern. Att ändra katalog Ali Aset säkerställer att synkroniseringen av katalogen alltid synkroniseras med den aktiva katalogen.

3. Markerar alla befintliga klienter i återställnings katalogen som offline för att förhindra åtkomst till klient databaser innan de återställs.

4. Etablerar en instans av appen i återställnings regionen och konfigurerar den att använda den återställda katalogen i den regionen. Exempel appen är utformad för att alltid ansluta till en klient databas i samma region för att hålla svars tiden minst.

5. Etablerar en server och elastisk pool där nya klienter etableras. Genom att skapa dessa resurser säkerställer du att etablering av nya klienter inte stör återställning av befintliga klienter.

6. Uppdaterar det nya klient Ali Aset så att det pekar på servern för nya klient databaser i återställnings regionen. Om du ändrar det här aliaset säkerställs att databaser för alla nya klienter är etablerade i återställnings regionen.
        
7. Etablerar servrar och elastiska pooler i återställnings regionen för återställning av klient databaser. Dessa servrar och pooler är en spegel avbildning av konfigurationen i den ursprungliga regionen. Etablering av pooler klient reserverar den kapacitet som krävs för att återställa alla databaser.

    Ett avbrott i en region kan leda till betydande belastning på de resurser som är tillgängliga i den kopplade regionen. Om du använder geo-återställning för DR, rekommenderar vi att du snabbt reserverar resurserna. Överväg geo-replikering om det är viktigt att ett program återställs i en specifik region. 

8. Aktiverar Traffic Manager-slutpunkten för webbappen i återställnings regionen. Om du aktiverar den här slut punkten kan programmet etablera nya klienter. I det här skedet är befintliga klienter fortfarande offline.

9. Skickar batchar över begär Anden för att återställa databaser i prioritetsordning. 

    * Batchar är organiserade så att databaserna återställs parallellt över alla pooler.  

    * Återställnings begär Anden skickas asynkront så att de skickas snabbt och placeras i kö för körning i varje pool.

    * Eftersom återställnings begär Anden bearbetas parallellt över alla pooler, är det bättre att distribuera viktiga klienter i flera pooler. 

10. Övervakar tjänsten för att fastställa när databaser återställs. När en klient databas har återställts är den markerad som online i katalogen och en ROWVERSION summa för klient databasen registreras. 

    * Klient databaser kan nås av programmet så snart de är markerade online i katalogen.

    * En summa av ROWVERSION-värden i klient databasen lagras i katalogen. Den här summan fungerar som ett finger avtryck som gör att Repatriation-processen kan avgöra om databasen har uppdaterats i återställnings regionen.

## <a name="run-the-recovery-script"></a>Kör återställnings skriptet

> [!IMPORTANT]
> Den här självstudien återställer databaser från geo-redundanta säkerhets kopieringar. Även om dessa säkerhets kopieringar vanligt vis är tillgängliga inom 10 minuter, kan det ta upp till en timme. Skriptet pausas tills de är tillgängliga.

Tänk dig att det finns ett avbrott i den region där programmet distribueras och kör återställnings skriptet:

1. I PowerShell ISE, i ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript, anger du följande värde:

    $DemoScenario = 2: Återställ appen till en återställnings region genom att återställa från geo-redundanta säkerhets kopieringar.

2. Välj F5 för att köra skriptet.  

    * Skriptet öppnas i ett nytt PowerShell-fönster och startar sedan en uppsättning PowerShell-jobb som körs parallellt. Dessa jobb återställer servrar, pooler och databaser till återställnings regionen.

    * Återställnings regionen är den kopplade region som är kopplad till den Azure-region där du distribuerade programmet. Mer information finns i [Azure-kopplade regioner](../../best-practices-availability-paired-regions.md). 

3. Övervaka status för återställnings processen i PowerShell-fönstret.

    ![Skärm bild som visar PowerShell-fönstret där du kan övervaka status för återställnings processen.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Om du vill utforska koden för återställnings jobben granskar du PowerShell-skripten i mappen. ..\Learning Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Granska programmets tillstånd under återställningen
När program slut punkten är inaktive rad i Traffic Manager är programmet inte tillgängligt. Katalogen återställs och alla klienter markeras offline. Program slut punkten i återställnings regionen aktive ras sedan och programmet är online igen. Även om programmet är tillgängligt visas klienterna offline i Event Hub tills deras databaser återställs. Det är viktigt att utforma ditt program för att hantera offline-klient databaser.

* När katalog databasen har återställts men innan klienterna är online igen uppdaterar du hubben Wingtip Ticket Events i webbläsaren.

  * I sidfoten ser du till att katalog server namnet har ett suffix för återställning och finns i återställnings regionen.

  * Observera att klienter som ännu inte har återställts markeras som offline och inte kan väljas.   
 
    ![Återställnings process](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Om du öppnar en klients händelse sida direkt när klienten är offline visas en klient offline-avisering i sidan. Om contoso konsert Hall till exempel är offline kan du försöka öppna http://events.wingtip-dpt.&lt . user &gt; . trafficmanager.net/contosoconcerthall.

    ![Skärm bild som visar sidan offline-händelser.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Etablera en ny klient i återställnings regionen
Du kan etablera nya klienter i återställnings regionen även innan klient databaserna återställs. Nya klient databaser som etablerades i återställnings regionen är repatriated med de återställda databaserna senare.   

1. I PowerShell ISE går du till ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet och anger följande egenskap:

    $DemoScenario = 3: etablera en ny klient i återställnings regionen.

2. Välj F5 för att köra skriptet.

3. Sidan Hawthorn Hall-händelser öppnas i webbläsaren när etableringen har slutförts. 

    Observera att Hawthorn Hall-databasen finns i återställnings regionen.

    ![Hawthorn Hall etablerad i återställnings regionen](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. I webbläsaren uppdaterar du sidan Wingtip Ticket Events Hub för att se Hawthorn Hall ingår. 

    Om du etablerade Hawthorn Hall utan att behöva vänta på att de andra klienterna ska kunna återställas, kan andra klienter fortfarande vara offline.

## <a name="review-the-recovered-state-of-the-application"></a>Granska programmets återställda tillstånd

När återställnings processen har slutförts fungerar programmet och alla klienter fullständigt i återställnings regionen. 

1. När visningen i fönstret i PowerShell-konsolen visar att alla klienter har återställts uppdaterar du Events-hubben. 

    Klient organisationerna visas online, inklusive den nya klienten, Hawthorn Hall.

    ![Återställda och nya klienter i hubben Events](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Klicka på Contoso konsert Hall och öppna evenemangs sidan. 

    Lägg märke till att databasen finns på återställnings servern i återställnings regionen i sidfoten.

    ![Contoso i återställnings regionen](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Öppna listan över resurs grupper i [Azure Portal](https://portal.azure.com).  

    Lägg märke till den resurs grupp som du har distribuerat, plus återställnings resurs gruppen med-återställnings-suffixet. Återställnings resurs gruppen innehåller alla resurser som skapats under återställnings processen, plus nya resurser som skapas under avbrottet. 

4. Öppna återställnings resurs gruppen och Lägg märke till följande objekt:

   * Återställnings versionerna av katalogen och tenants1-servrarna, med suffixet-Recovery. Den återställda katalogen och klient databaserna på dessa servrar har alla namn som används i den ursprungliga regionen.

   * Tenants2-DPT- &lt; User &gt; -Recovery SQL Server. Den här servern används för att tillhandahålla nya klienter under avbrottet.

   * App Service Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User &gt; , som är återställnings instansen av events-appen.

     ![Contoso-resurser i återställnings regionen](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Öppna SQL Server-tenants2-DPT- &lt; User &gt; -Recovery. Observera att den innehåller databasen hawthornhall och den elastiska poolen Pool1. Hawthornhall-databasen har kon figurer ATS som en elastisk databas i den elastiska Pool1-poolen.

## <a name="change-the-tenant-data"></a>Ändra klient data 
I den här uppgiften uppdaterar du en av de återställda klient databaserna. Repatriation-processen kopierar återställda databaser som har ändrats till den ursprungliga regionen. 

1. Leta upp händelse listan för Contoso konsert Hall i webbläsaren, bläddra igenom händelserna och Lägg märke till den sista händelsen, allvarligt Strauss.

2. I PowerShell ISE, i ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript, anger du följande värde:

    $DemoScenario = 4: ta bort en händelse från en klient i återställnings regionen.

3. Välj F5 för att köra skriptet.

4. Uppdatera sidan contoso konsert evenemang ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net/contosoconcerthall) och Observera att händelsen allvarligt Strauss saknas.

I den här kursen har du återställt programmet, som nu körs i återställnings regionen. Du har etablerad en ny klient i återställnings regionen och ändrat data för en av de återställda klient organisationerna.  

> [!NOTE]
> Andra självstudier i exemplet är inte utformade för att köras med appen i återställnings läge. Om du vill utforska andra självstudier måste du först repatriate programmet.

## <a name="repatriation-process-overview"></a>Översikt över Repatriation-processen

Repatriation-processen återställer programmet och dess databaser till den ursprungliga regionen när ett avbrott har åtgärd ATS.

![Geo-återställning Repatriation](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

Processen:

1. Stoppar eventuell pågående återställnings aktivitet och avbryter eventuella utestående återställnings begär Anden för databas återställning.

2. Reaktiverar i den ursprungliga regionens klient databaser som inte har ändrats sedan avbrottet. De här databaserna omfattar de som inte har återställts än och som återställts men inte ändrats efteråt. De återaktiverade databaserna är exakt samma som de som används av klienterna.

3. Tillhandahåller en spegel avbildning av den nya klientens Server och elastisk pool i den ursprungliga regionen. När den här åtgärden har slutförts uppdateras det nya klient Ali Aset så att det pekar på den här servern. Att uppdatera aliaset gör att den nya klient organisationens onboarding sker i den ursprungliga regionen i stället för återställnings regionen.

3. Använder geo-replikering för att flytta katalogen till den ursprungliga regionen från återställnings regionen.

4. Uppdatera poolens konfiguration i den ursprungliga regionen så att den är konsekvent med ändringar som gjorts i återställnings regionen under avbrottet.

5. Skapar de servrar och pooler som krävs för att vara värd för alla nya databaser som skapas under avbrottet.

6. Använder geo-replikering för att repatriate återställda klient databaser som har uppdaterats efter återställning och alla nya klient databaser som etablerats under avbrottet. 

7. Rensar resurser som skapats i återställnings regionen under återställnings processen.

För att begränsa antalet klient databaser som måste vara repatriated, utförs steg 1 till 3 snabbt.  

Steg 4 görs bara om katalogen i återställnings regionen har ändrats under avbrottet. Katalogen uppdateras om nya klienter skapas eller om någon databas eller pool konfiguration ändras i återställnings regionen.

Det är viktigt att steg 7 orsakar minimalt avbrott i klient organisationer och inga data går förlorade. Processen använder geo-replikering för att uppnå det här målet.

Innan varje databas är geo-replikerad tas motsvarande databas i den ursprungliga regionen bort. Databasen i återställnings regionen är sedan geo-replikerad, vilket skapar en sekundär replik i den ursprungliga regionen. När replikeringen är klar markeras klienten offline i katalogen, vilket bryter alla anslutningar till databasen i återställnings regionen. Databasen redundansväxlas sedan över, vilket orsakar väntande transaktioner för att bearbeta den sekundära så att inga data förloras. 

Vid redundans återställs databas rollerna. Den sekundära i den ursprungliga regionen blir den primära databasen med Läs-och Skriv behörighet och databasen i återställnings regionen blir skrivskyddad. Klient posten i katalogen uppdateras för att referera till databasen i den ursprungliga regionen och klienten är markerad som online. Nu är Repatriation av databasen slutförd. 

Program ska skrivas med logik för omprövning för att säkerställa att de återansluter automatiskt när anslutningarna bryts. När de använder katalogen för att hantera åter anslutningen ansluter de till repatriated-databasen i den ursprungliga regionen. Även om den korta från kopplingen ofta inte har märkts, kan du välja att repatriate databaser utanför kontors tid.

När en databas har repatriated kan den sekundära databasen i återställnings regionen tas bort. Databasen i den ursprungliga regionen förlitar sig sedan igen på Geo-återställning för katastrof skydd.

I steg 8 raderas resurser i återställnings regionen, inklusive återställnings servrar och pooler.

## <a name="run-the-repatriation-script"></a>Kör Repatriation-skriptet
Låt oss föreställa sig att avbrottet är löst och kör Repatriation-skriptet.

Om du har följt självstudien återaktiverar skriptet omedelbart den Fabrikam Jazz-klubben och Dogwood-Dojo i den ursprungliga regionen eftersom de är oförändrade. Sedan repatriates den nya klienten, Hawthorn hallen och contoso konsert Hall eftersom den har ändrats. Skriptet repatriates också katalogen, som uppdaterades när Hawthorn Hall etablerades.
  
1. I PowerShell ISE, i ..\Learning-Modules\Business kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skript, verifierar du att katalogens synkronisering fortfarande körs i PowerShell-instansen. Om det behövs startar du om det genom att ange:

    $DemoScenario = 1: Starta synkronisering av klient server, pool och databas konfigurations information i katalogen.

    Välj F5 för att köra skriptet.

2.  Starta Repatriation-processen genom att ange:

    $DemoScenario = 5: Repatriate appen i sin ursprungliga region.

    Om du vill köra återställnings skriptet i ett nytt PowerShell-fönster väljer du F5. Repatriation tar flera minuter och kan övervakas i PowerShell-fönstret.

3. När skriptet körs uppdaterar du sidan Events Hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net).

    Observera att alla klienter är online och tillgängliga i hela den här processen.

4. Välj föreningen Fabrikam Jazz för att öppna den. Om du inte modifierat den här klienten, Lägg märke till i sidfoten att servern redan har återställts till den ursprungliga servern.

5. Öppna eller uppdatera sidan contoso konsert Hall-händelser. I sidfoten som ursprungligen är databasen fortfarande på återställnings servern. 

6. Uppdatera sidan contoso konsert Hall-händelser när Repatriation-processen har slutförts och Lägg märke till att databasen nu finns i din ursprungliga region.

7. Uppdatera Events-hubben igen och öppna Hawthorn Hall. Observera att dess databas även finns i den ursprungliga regionen. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Rensa återställnings regions resurser efter Repatriation
När Repatriation har slutförts är det säkert att ta bort resurserna i återställnings regionen. 

> [!IMPORTANT]
> Ta bort de här resurserna om du vill stoppa all fakturering.

Återställnings processen skapar alla återställnings resurser i en återställnings resurs grupp. Rensnings processen tar bort den här resurs gruppen och tar bort alla referenser till resurserna från katalogen. 

1. I PowerShell ISE går du till ..\Learning Modules\Business-kontinuitet och katastrof Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 skriptet, ange:
    
    $DemoScenario = 6: ta bort föråldrade resurser från återställnings regionen.

2. Välj F5 för att köra skriptet.

När du har rensat skripten är programmet tillbaka där det startade. Nu kan du köra skriptet igen eller prova andra själv studie kurser.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Utforma programmet så att appen och databasen samplaceras 
Programmet är utformat för att alltid ansluta från en instans i samma region som klient organisationens databas. Den här designen minskar svars tiden mellan programmet och databasen. Den här optimeringen förutsätter att interaktion mellan app-till-databas är chattier än användar-till-app-interaktionen.  

Klient databaser kan spridas över återställnings-och original regioner under en tid under Repatriation. För varje databas söker appen upp den region där databasen finns genom att göra en DNS-sökning på klient serverns namn. Server namnet är ett alias. Namnet på den aliasna servern innehåller regionens namn. Om programmet inte finns i samma region som databasen omdirigeras det till instansen i samma region som-servern. Omdirigera till instansen i samma region som databasen minimerar svars tiden mellan appen och databasen.  

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> 
> * Använd klient katalogen för att hålla uppdaterad konfigurations information med jämna mellanrum, vilket gör att en spegel återställnings miljö kan skapas i en annan region.
> * Återställ databaser i återställnings regionen med geo-återställning.
> * Uppdatera klient katalogen så att den återspeglar platser för återställd klient databas. 
> * Använd ett DNS-alias för att göra det möjligt för ett program att ansluta till klient katalogen i utan omkonfiguration.
> * Använd geo-replikering för att repatriate återställda databaser till deras ursprungliga region efter att ett avbrott har åtgärd ATS.

Prova [haveri beredskap för ett SaaS-program med flera innehavare med hjälp av databasens vägledning för geo-replikering](./saas-dbpertenant-dr-geo-replication.md) för att lära dig hur du använder geo-replikering för att drastiskt minska den tid som krävs för att återställa ett storskaligt program för flera innehavare.

## <a name="additional-resources"></a>Ytterligare resurser

[Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)