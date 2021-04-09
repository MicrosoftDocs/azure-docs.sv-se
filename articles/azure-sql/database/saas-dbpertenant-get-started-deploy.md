---
title: Själv studie kurs för databas per klient SaaS
description: Distribuera och utforska Wingtip Ticket SaaS-program för flera innehavare som demonstrerar mönstret för databas per klient och andra SaaS-mönster med hjälp av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 497e714289c834e026c6b9b767ed2b7af5442783
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780843"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Distribuera och utforska en SaaS-app med flera innehavare som använder mönstret för databas per klient organisation med Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien distribuerar och utforskar du Wingtip-biljetterna SaaS Database-per klient program (Wingtip). Appen använder en databas per klient mönster för att lagra data för flera klienter. Appen är utformad för att demonstrera funktioner i Azure SQL Database som fören klar hur du aktiverar SaaS-scenarier.

Fem minuter efter att du har valt **distribuera till Azure** har du ett SaaS-program för flera innehavare. Appen innehåller en databas som körs i Azure SQL Database. Appen distribueras med tre exempel klienter, var och en med sin egen databas. Alla databaser distribueras till en elastisk SQL-pool. Appen distribueras till din Azure-prenumeration. Du har fullständig åtkomst till att utforska och arbeta med de enskilda komponenterna i appen. Application C#-källkoden och hanterings skripten är tillgängliga i [WingtipTicketsSaaS-DbPerTenant GitHub lagrings platsen][github-wingtip-dpt].

I den här guiden lär du dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip SaaS-programmet.
> - Var du kan hämta program käll koden och hanterings skripten.
> - Om servrar, pooler och databaser som utgör appen.
> - Hur klienter mappas till sina data med *katalogen*.
> - Så här etablerar du en ny klient.
> - Övervaka klient aktivitet i appen.

Det finns en [serie relaterade självstudier](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) för att utforska olika SaaS design-och hanterings mönster. Självstudierna bygger vidare på den här inledande distributionen. När du använder självstudierna kan du undersöka de angivna skripten för att se hur de olika SaaS-mönstren implementeras. Skripten visar hur funktionerna i SQL Database fören klar utvecklingen av SaaS-program.

## <a name="prerequisites"></a>Förutsättningar

Se till att Azure PowerShell är installerat för att slutföra den här självstudien. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuera Wingtip Ticket SaaS-program

### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du ett användar värde som används för att se till att resurs namn är globalt unika. Du anger också ett namn för resurs gruppen som innehåller alla resurser som har skapats av en distribution av appen. För en fiktiv person med namnet Ann Finley föreslår vi:

- **Användare**: *Af1* består av Ann-Finley initialer plus en siffra. Om du distribuerar appen en andra gång ska du använda ett annat värde. Ett exempel är AF2.
- **Resurs grupp**: *Wingtip-DPT-AF1* anger att det här är en app för databas per klient. Lägg till användar namnet AF1 för att korrelera resurs grupps namnet med namnen på de resurser som den innehåller.

Välj dina namn nu och skriv ned dem.

### <a name="steps"></a>Steg

1. Om du vill öppna distributions mal len Wingtip biljetter SaaS Database-per-innehavare i Azure Portal väljer du **distribuera till Azure**.

   [![Bild som visar en knapp med etiketten "distribuera till Azure".](https://azuredeploy.net/deploybutton.png)](https://aka.ms/deploywingtipdpt)

1. Ange värden i mallen för de obligatoriska parametrarna.

    > [!IMPORTANT]
    > Vissa autentiserings-och Server brand väggar är avsiktligt oskyddade i demonstrations syfte. Vi rekommenderar att du skapar en ny resurs grupp. Använd inte befintliga resurs grupper, servrar eller pooler. Använd inte det här programmet, skripten eller några distribuerade resurser för produktion. Ta bort den här resurs gruppen när du är klar med programmet för att stoppa relaterad fakturering.

    - **Resurs grupp**: Välj **Skapa ny** och ange det unika namn som du valde tidigare för resurs gruppen.
    - **Plats**: Välj en plats i den nedrullningsbara listan.
    - **Användare**: Använd användar namn svärdet som du valde tidigare.

1. Distribuera programmet.

    a. Välj att godkänna de allmänna villkoren.

    b. Välj **Köp**.

1. Om du vill övervaka distributions status väljer du **meddelanden** (klock ikonen till höger om sökrutan). Att distribuera Wingtip-biljetterna SaaS-appen tar cirka fem minuter.

   ![Distributionen lyckades](./media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Ladda ned och avblockera hanterings skript för Wingtip Ticket

När programmet distribueras kan du ladda ned käll koden och hanterings skripten.

> [!IMPORTANT]
> Körbart innehåll (skript och DLL-filer) kan blockeras av Windows när. zip-filer laddas ned från en extern källa och extraheras. Följ stegen för att avblockera. zip-filen innan du extraherar skripten. Om du avblockerar ser du till att skripten får köras.

1. Bläddra till [WingtipTicketsSaaS-DbPerTenant GitHub-lagrings platsen][github-wingtip-dpt].
1. Välj **Klona eller ladda ned**.
1. Välj **Hämta zip** och spara sedan filen.
1. Högerklicka på **WingtipTicketsSaaS-DbPerTenant-master.zip** -filen och välj sedan **Egenskaper**.
1. På fliken **Allmänt** väljer du **avblockera**  >  **Använd**.
1. Välj **OK** och extrahera filerna

Skript finns på... \\ WingtipTicketsSaaS – DbPerTenant-Master \\ Learning moduless-mappen.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Uppdatera användar konfigurations filen för den här distributionen

Innan du kör skript måste du uppdatera resurs gruppen och användar värdena i användar konfigurations filen. Ange de här variablerna till de värden som du använde under distributionen.

1. I PowerShell ISE öppnar du... \\ Learning-moduler \\ **userconfig. psm1**
1. Uppdatera **ResourceGroupName** och **namn** med de angivna värdena för din distribution (endast på raderna 10 och 11).
1. Spara ändringarna.

Dessa värden refereras till i nästan alla skript.

## <a name="run-the-application"></a>Kör programmet

I appen demonstreras platser som är värdar för händelser. Plats typerna omfattar konsert salar, Jazz klöver och idrotts klubbar. I Wingtip-biljetter registreras platser som klienter. Att vara en klient organisation ger ett enkelt sätt att lista händelser och sälja biljetter till sina kunder. Varje plats får en anpassad webbplats där de kan lista sina händelser och sälja biljetter.

Internt i appen hämtar varje klient organisation en databas som distribueras till en elastisk pool.

En **Hubbs** sida för centrala händelser innehåller en lista över länkar till klienterna i distributionen.

1. Använd URL: en för att öppna Events-hubben i webbläsaren: http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net. Ersätt &lt; användare &gt; med din distributions användar värde.

    ![Events Hub](./media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Välj **Fabrikam jazz-klubb** i hubben events.

    ![Händelser](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip-programmet använder [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md) för att kontrol lera distributionen av inkommande begär Anden. URL: en för att komma åt sidan händelser för en viss klient använder följande format:

- http://events.wingtip-dpt.&lt; User &gt; . trafficmanager.net/fabrikamjazzclub

    Delarna i föregående format förklaras i följande tabell.

    | URL-del        | Beskrivning       |
    | :-------------- | :---------------- |
    | Events. Wingtip-DPT | Händelse delarna i Wingtip-appen.<br /><br /> *-DPT* särskiljer implementeringen av en *databas per klient* för Wingtip-biljetter från andra implementeringar. Exempel är implementeringar av *en enda* app-per-klient (*-sa*) eller flera *klient databaser* (*-MT*). |
    | . *&lt; användare &gt;* | *AF1* i exemplet. |
    | . trafficmanager.net/ | Traffic Manager, bas-URL. |
    | fabrikamjazzclub | Identifierar klienten som heter Fabrikam jazz klubb. |
    | &nbsp; | &nbsp; |

- Klient organisationens namn är parsat från URL: en av events-appen.
- Klient namnet används för att skapa en nyckel.
- Nyckeln används för att komma åt katalogen för att hämta platsen för klientens databas.
  - Katalogen implementeras med hjälp av *Shard Map Management*.
- Events Hub använder utökade metadata i katalogen för att skapa sidan med webb adresser för List-of-Events för varje klient.

I en produktions miljö kan du vanligt vis skapa en CNAME DNS-post för att [*peka företagets Internet domän*](../../traffic-manager/traffic-manager-point-internet-domain.md) till Traffic Manager DNS-namnet.

> [!NOTE]
> Det kanske inte är direkt uppenbart vad användningen av Traffic Manager är i den här självstudien. Målet med den här serien med självstudier är att demonstrera mönster som kan hantera en komplex produktions Miljös skala. I sådana fall skulle du till exempel ha flera webbappar som distribueras över hela världen, tillsammans med databaser och du behöver Traffic Manager för att dirigera mellan dessa instanser.
En annan uppsättning självstudier som illustrerar användningen av Traffic Manager är att själv studie kurser för [geo-återställning](./saas-dbpertenant-dr-geo-restore.md) och [geo-replikering](./saas-dbpertenant-dr-geo-replication.md) . I de här självstudierna används Traffic Manager för att växla över till en återställnings instans av SaaS-appen i händelse av ett regionalt avbrott.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats kan vi placera den att fungera.

PowerShell *-skriptet demo-LoadGenerator* startar en arbets belastning som körs mot alla klient databaser. Den verkliga belastningen på många SaaS-appar är sporadisk och oförutsägbar. För att simulera den här typen av belastning skapar generatorn en belastning med slumpmässiga toppar eller burst-överföringar för varje klient. Burst-överföringarna sker med slumpmässiga intervall. Det tar flera minuter innan belastnings mönstret uppträder. Låt generatorn köras i minst tre eller fyra minuter innan du övervakar belastningen.

1. I PowerShell ISE öppnar du... \\ Verktyg för inlärnings moduler \\ \\ *Demo-LoadGenerator.ps1* skript.
2. Tryck på F5 för att köra skriptet och starta belastnings generatorn. Lämna standardvärdena för parameter för tillfället.
3. Logga in på ditt Azure-konto och välj den prenumeration som du vill använda, om det behövs.

Skriptet för belastnings generatorn startar ett bakgrunds jobb för varje databas i katalogen och stoppas sedan. Om du kör om belastnings Generator skriptet, stoppas alla bakgrunds jobb som körs innan nya påbörjas.

### <a name="monitor-the-background-jobs"></a>Övervaka bakgrunds jobb

Om du vill kontrol lera och övervaka bakgrunds jobben använder du följande cmdlets:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 åtgärder

*Demo-LoadGenerator.ps1* imiterar en aktiv arbets belastning för kund transaktioner. I följande steg beskrivs en sekvens med åtgärder som *Demo-LoadGenerator.ps1* initieras:

1. *Demo-LoadGenerator.ps1* börjar *LoadGenerator.ps1* i förgrunden.

    - Både. ps1-filer lagras under verktygen i Learning modules för mappar \\ \\ .

2. *LoadGenerator.ps1* slingor via alla klient databaser i katalogen.

3. *LoadGenerator.ps1* startar ett bakgrunds-PowerShell-jobb för varje klient databas:

    - Som standard körs bakgrunds jobben i 120 minuter.
    - Varje jobb orsakar en CPU-baserad belastning på en klient databas genom att köra *sp_CpuLoadGenerator*. Belastningens intensitet och varaktighet varierar beroende på `$DemoScenario` .
    - *sp_CpuLoadGenerator* slingor runt en SQL SELECT-instruktion som orsakar hög CPU-belastning. Tidsintervallet mellan problem med SELECT varierar enligt parameter värden för att skapa en kontrollerbar CPU-belastning. Belastnings nivåer och intervall är slumpmässiga för att simulera mer realistiska belastningar.
    - Den här. SQL-filen lagras *under \\ WingtipTenantDB \\ dbo \\ StoredProcedures*.

4. Om `$OneTime = $false` inläsnings generatorn startar bakgrunds jobben och fortsätter att köras. Var tionde sekund övervakar den för alla nya klienter som är etablerade. Om du ställer in `$OneTime = $true` startar LoadGenerator bakgrunds jobben och slutar sedan att köra i förgrunden. I den här självstudien lämnar du `$OneTime = $false` .

   Använd CTRL-C eller Avbryt åtgärd Ctrl-Break om du vill stoppa eller starta om belastnings generatorn.

   Om du lämnar belastnings generatorn som körs i förgrunden använder du en annan PowerShell ISE-instans för att köra andra PowerShell-skript.

&nbsp;

Innan du fortsätter med nästa avsnitt måste du lämna belastnings generatorn som körs i läget för jobb som startar.

## <a name="provision-a-new-tenant"></a>Etablera en ny klient

Den första distributionen skapar tre exempel klienter. Nu skapar du en annan klient för att se hur det distribuerade programmet påverkar. I Wingtip-appen förklaras arbets flödet för att etablera nya klienter i [själv studie kursen om etablering och katalog](saas-dbpertenant-provision-and-catalog.md). I det här steget skapar du en ny klient, som tar mindre än en minut.

1. Öppna en ny PowerShell ISE.
2. Öppna... \\ Lär dig Modules\Provision och katalog \\ *Demo-ProvisionAndCatalog.ps1*.
3. Tryck på F5 för att köra skriptet. Lämna standardvärdena för tillfället.

   > [!NOTE]
   > Många Wingtip SaaS-skript använder *$PSScriptRoot* för att bläddra bland mappar för att anropa funktioner i andra skript. Den här variabeln utvärderas bara när det fullständiga skriptet körs genom att trycka på F5. Om du markerar och kör en markering med F8 kan fel uppstå. Tryck på F5 för att köra skripten.

Den nya klient databasen är:

- Skapas i en elastisk SQL-pool.
- Initierats.
- Registrerad i katalogen.

Efter lyckad etablering visas *händelse* platsen för den nya klienten i webbläsaren.

![Ny klient](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Uppdatera Händelsehubben så att den nya klienten visas i listan.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Utforska servrar, pooler, och klientdatabaser

Nu när du har börjat köra en belastning mot samlingen av klienter kan vi titta på några av de resurser som har distribuerats.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till listan över SQL-servrar. Öppna sedan **katalogen-DPT- &lt; User &gt;** Server.
    - Katalog servern innehåller två databaser, **tenantcatalog** och **basetenantdb** (en mall som kopieras för att skapa nya klienter).

   ![Skärm bild som visar en översikts sida för en katalog server med de två databaserna.](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Gå tillbaka till listan över SQL-servrar.

3. Öppna **tenants1-DPT- &lt; User &gt; -** servern som innehåller klient databaserna.

4. Se följande objekt:

    - Varje klient databas är en **elastisk standard** databas i en standardpool med 50-eDTU.
    - Den röda-racer cykel databasen är klient databasen som du etablerade tidigare.

   ![Server med databaser](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Övervaka poolen

När *LoadGenerator.ps1* körts i flera minuter bör det finnas tillräckligt med data för att kunna börja titta på vissa övervaknings funktioner. Dessa funktioner är inbyggda i pooler och databaser.

Bläddra till servern **tenants1-DPT- &lt; User &gt;** och välj **Pool1** för att Visa resursutnyttjande för poolen. I följande diagram kördes belastnings generatorn i en timme.

   ![Övervaka pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Det första diagrammet, märkt **resursutnyttjande**, visar användning av pool-eDTU.
- Det andra diagrammet visar eDTU-användning för de fem mest aktiva databaserna i poolen.

De två diagrammen visar att elastiska pooler och SQL Database lämpar sig väl för oförutsägbara SaaS program arbets belastningar. Diagrammen visar att fyra databaser är varje burst-överföring till så mycket som 40 eDTU: er och att alla databaser är smidiga att stödjas av en 50-eDTU-pool. Poolen 50-eDTU har stöd för även kraftigare arbets belastningar. Om databaserna är etablerade som enskilda databaser måste var och en vara en S2 (50 DTU) för att stödja burst-överföringarna. Kostnaden för fyra enkla S2-databaser är nästan tre gånger priset för poolen. I verkliga situationer kan SQL Database kunder köra upp till 500 databaser i 200 eDTU-pooler. Mer information finns i [själv studie kursen om prestanda övervakning](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information finns i ytterligare [självstudier som bygger på Wingtip-biljetter SaaS Database-användarspecifika program](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Mer information om elastiska pooler finns i [Vad är en elastisk pool i Azure SQL?](elastic-pool-overview.md).
- Mer information om elastiska jobb finns i [Hantera utskalade moln databaser](./elastic-jobs-overview.md).
- Mer information om SaaS-program för flera innehavare finns i [design mönster för SaaS-program](saas-tenancy-app-design-patterns.md)med flera innehavare.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip Ticket SaaS-program.
> - Om servrar, pooler och databaser som utgör appen.
> - Hur klienter mappas till sina data med *katalogen*.
> - Hur du etablerar nya klienter.
> - Så här visar du användningen av poolen för att övervaka klient aktivitet.
> - Så här tar du bort exempel resurser för att stoppa relaterad fakturering.

Prova sedan [själv studie kursen om etablering och katalog](saas-dbpertenant-provision-and-catalog.md).

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant