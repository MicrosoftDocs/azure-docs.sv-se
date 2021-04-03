---
title: Rapporterings frågor över flera databaser
description: Rapportering mellan klienter med hjälp av distribuerade frågor.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793270"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Rapportering mellan klienter med hjälp av distribuerade frågor
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien kör du distribuerade frågor över hela uppsättningen klient databaser för rapportering. Dessa frågor kan extrahera insikter som ligger inom den dagliga drifts informationen för Wingtip Ticket SaaS-klienter. Om du vill göra detta distribuerar du en ytterligare rapport databas till katalog servern och använder elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]
> 
> * Så här distribuerar du en rapport databas
> * Så här kör du distribuerade frågor över alla klient databaser
> * Hur globala vyer i varje databas kan möjliggöra effektiv fråga mellan klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:


* Wingtip biljetter SaaS-databasen per klient-app distribueras. Om du vill distribuera på mindre än fem minuter, se [distribuera och utforska Wingtip-biljetter SaaS-databas per klient program](./saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) har installerats. Information om hur du hämtar och installerar SSMS finns i [hämta SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Rapporterings mönster för flera innehavare

![mönster för distribuerade frågor mellan klienter](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

En möjlighet med SaaS-program är att använda den stora mängden klient data som lagras i molnet för att få insikter om driften och användningen av ditt program. Dessa insikter kan hjälpa till med utveckling av funktioner, användbarhets förbättringar och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk fråga](elastic-query-overview.md)som aktiverar frågor i en distribuerad uppsättning databaser med ett gemensamt schema. Dessa databaser kan distribueras mellan olika resurs grupper och prenumerationer, men du måste dela en vanlig inloggning. Elastisk fråga använder en enda *huvud* databas där externa tabeller definieras som speglar tabeller eller vyer i de distribuerade (klient) databaserna. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk fråga använder Shard-kartan i katalog databasen för att bestämma platsen för alla klient databaser. Det är enkelt att konfigurera och fråga på huvud databasen med hjälp av standard [Transact-SQL](/sql/t-sql/language-reference)och stöd för frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klient databaserna ger elastiska frågor omedelbar insyn i direkt produktions data. Eftersom en elastisk fråga hämtar data från potentiellt många databaser, kan svars tiden vara högre än motsvarande frågor som skickas till en enda databas för flera innehavare. Utforma frågor för att minimera de data som returneras till huvud databasen. Elastiska frågor passar ofta bäst för frågor mot små mängder real tids data, i stället för att skapa ofta använda eller komplexa analys frågor eller rapporter. Om frågor inte fungerar bra kan du titta på [körnings planen](/sql/relational-databases/performance/display-an-actual-execution-plan) för att se vilken del av frågan som skickas ned till fjärrdatabasen och hur mycket data som returneras. Frågor som kräver komplex agg regering eller analytisk bearbetning kan vara bättre hanterade genom att extrahera klient data till en databas eller ett data lager som är optimerat för analys frågor. Det här mönstret beskrivs i [själv studie kursen för klient analys](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS-databas per klient program skript

Wingtip-biljetterna SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljnings data

Om du vill köra frågor mot en mer intressant data uppsättning skapar du biljett försäljnings data genom att köra biljett generatorn.

1. I *POWERSHELL ISE* öppnar du... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *Demo-AdhocReporting.ps1* -skript och ange följande värde:
   * **$DemoScenario** = 1, **Köp biljetter för händelser på alla platser**.
2. Tryck på **F5** för att köra skriptet och generera biljett försäljning. Fortsätt med stegen i den här själv studie kursen medan skriptet körs. Biljett data frågas i avsnittet *köra Ad hoc-frågor* och väntar på att biljett generatorn ska slutföras.

## <a name="explore-the-global-views"></a>Utforska de globala vyerna

I Wingtip-biljetterna SaaS-databasen per klient program tilldelas varje klient organisation en databas. Därför är data i databas tabellerna begränsade till en enda klients perspektiv. Men när du frågar över alla databaser är det viktigt att elastiska frågor kan behandla data som om de ingår i en enda logisk databas shardade av klienten. 

För att simulera det här mönstret läggs en uppsättning "globala" vyer till i klient databasen som projicerar ett klient-ID i varje tabell som frågas globalt. Vyn *VenueEvents* lägger till exempel till en beräknad *VenueId* till de kolumner som projiceras från tabellen *händelser* . På samma sätt lägger vyerna *VenueTicketPurchases* och *VenueTickets* till en beräknad *VenueId* -kolumn som projiceras från deras respektive tabeller. Dessa vyer används av elastisk fråga för att parallellisera frågor och push-överföra dem till lämplig fjärran sluten klient databas när en *VenueId* -kolumn finns. Detta minskar dramatiskt mängden data som returneras och resulterar i en avsevärd ökning av prestanda för många frågor. Dessa globala vyer har skapats i förväg i alla klient databaser.

1. Öppna SSMS och [Anslut till tenants1- &lt; User- &gt; servern](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expandera **databaser**, högerklicka på _Contosoconcerthall_ och välj **ny fråga**.
1. Kör följande frågor för att utforska skillnaden mellan tabellerna med enskild klient och globala vyer:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

I dessa vyer beräknas *VenueId* som en hash av plats namnet, men alla metoder kan användas för att införa ett unikt värde. Den här metoden liknar hur klient nyckeln beräknas för användning i katalogen.

Så här granskar du definitionen av vyn *platser* :

1. Expandera  **contosoconcerthall**-  >  **vyer** i Object Explorer:

   ![Skärm bild som visar innehållet i vyer-noden, inklusive fyra typer av platser d b o.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Högerklicka på **dbo. Platser**.
3. Välj **skript visning som**  >  **skapa till**  >  **nytt Frågeredigeraren**

Skripta någon av de andra *platsernas* vyer för att se hur de lägger till *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Distribuera databasen som används för distribuerade frågor

Den här övningen distribuerar _AdHocReporting_ -databasen. Det här är huvud databasen som innehåller det schema som används för frågor över alla klient databaser. Databasen distribueras till den befintliga katalog servern, som är den server som används för alla hanterings relaterade databaser i exempel programmet.

1. i *POWERSHELL ISE* öppnar du... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *Demo-AdhocReporting.ps1*. 

1. Ange **$DemoScenario = 2**, _distribuera ad hoc-rapporterings databas_.

1. Tryck på **F5** för att köra skriptet och skapa *AdHocReporting* -databasen.

I nästa avsnitt lägger du till schema i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera Head-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (den externa data källan och externa tabell definitioner) till _AdHocReporting_ -databasen för att aktivera frågor över alla klient databaser.

1. Öppna SQL Server Management Studio och Anslut till adhoc-rapporterings databasen som du skapade i föregående steg. Namnet på databasen är *AdHocReporting*.
2. Öppna. ..\Learning Modules\Operational Analytics\Adhoc repor ting \ _Initialize-AdhocReportingDB. SQL_ i SSMS.
3. Granska SQL-skriptet och Obs:

   Elastisk fråga använder en databas med begränsade autentiseringsuppgifter för att komma åt var och en av klient databaserna. Den här autentiseringsuppgiften måste vara tillgänglig i alla databaser och bör normalt beviljas de minsta rättigheter som krävs för att aktivera dessa frågor.

    ![skapa autentiseringsuppgift](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Med katalog databasen som den externa data källan distribueras frågor till alla databaser som registrerats i katalogen när frågan körs. Eftersom Server namn skiljer sig åt för varje distribution hämtar det här skriptet platsen för katalog databasen från den aktuella servern (@ @servername ) där skriptet körs.

    ![Skapa extern data Källa](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externa tabeller som refererar till de globala vyer som beskrivs i föregående avsnitt och definieras med **distribution = shardade (VenueId)**. Eftersom varje *VenueId* mappar till en enskild databas förbättrar detta prestanda för många scenarier, som du ser i nästa avsnitt.

    ![skapa externa tabeller](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Den lokala tabellen _VenueTypes_ som skapas och fylls i. Den här referens data tabellen är vanlig i alla klient databaser, så den kan visas här som en lokal tabell och fyllas i med gemensamma data. För vissa frågor kan den här tabellen som definierats i huvud databasen minska mängden data som behöver flyttas till huvud databasen.

    ![Skapa tabell](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Om du inkluderar referens tabeller på det här sättet måste du uppdatera tabellens schema och data när du uppdaterar klient databaserna.

4. Tryck på **F5** för att köra skriptet och initiera *AdHocReporting* -databasen. 

Nu kan du köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-distributed-queries"></a>Köra distribuerade frågor

Nu när *AdHocReporting* -databasen har kon figurer ATS kan du gå vidare och köra några distribuerade frågor. Ta med körnings planen för att få en bättre förståelse för var frågans bearbetning sker. 

När du inspekterar körnings planen kan du hovra över plan ikonerna för mer information. 

Viktigt att observera, är att inställnings **fördelning = shardade (VenueId)** när den externa data källan har definierats förbättrar prestanda för många scenarier. När varje *VenueId* mappas till en enskild databas, kan filtreringen enkelt utföras på distans, och bara returnera de data som behövs.

1. Öppna... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *demo-ADHOCREPORTINGQUERIES. SQL* i SSMS.
2. Se till att du är ansluten till **AdHocReporting** -databasen.
3. Välj menyn **fråga** och klicka på **Inkludera faktisk körnings plan**
4. Markera *vilka platser som är registrerade för tillfället?* fråga och tryck på **F5**.

   Frågan returnerar hela listan över platser, som illustrerar hur snabbt och enkelt det är att fråga över alla klienter och returnera data från varje klient.

   Kontrol lera planen och se att hela kostnaden finns i fjärrfrågan. Varje klient databas kör frågan via en fjärr anslutning och returnerar plats informationen till huvud databasen.

   ![Välj * från dbo. Platser](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan ansluter till data från klient databaserna och den lokala *VenueTypes* -tabellen (lokal, som den är en tabell i *AdHocReporting* -databasen).

   Kontrol lera planen och se att merparten av kostnaden är fjärrfrågan. Varje klient databas returnerar sitt plats information och utför en lokal anslutning med den lokala *VenueTypes* -tabellen för att visa det egna namnet.

   ![Anslut till fjärranslutna och lokala data](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Välj nu den *dag där de mest Biljetterna såldes?* fråga och tryck på **F5**.

   Den här frågan gör en mer komplex anslutning och agg regering. Merparten av bearbetningen sker via fjärr anslutning.  Endast enstaka rader, som innehåller varje platss dagliga biljett försäljning per dag, returneras till huvud databasen.

   ![DocumentDB](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en rapport databas och definiera det schema som krävs för att köra distribuerade frågor.


Testa nu [själv studie kursen för klient analys](saas-tenancy-tenant-analytics.md) för att utforska extrahering av data till en separat analys databas för mer komplex analys bearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip-biljetter SaaS-databas per klient program](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastisk fråga](elastic-query-overview.md)