---
title: Ad hoc-rapporterings frågor över flera databaser
description: Kör ad hoc-rapporterings frågor över flera Azure SQL-databaser i ett exempel på en app för flera klient organisationer.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 800592b7a8b263fea2883fdd3e030f78f72647dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459930"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Köra Ad hoc Analytics-frågor över flera databaser (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien kör du distribuerade frågor över hela uppsättningen klient databaser för att aktivera Ad hoc-interaktiv rapportering. Dessa frågor kan extrahera insikter som ligger inom den dagliga drift informationen för Wingtip Ticket SaaS-appen. Om du vill göra dessa utdrag distribuerar du en ytterligare analys databas till katalog servern och använder elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]
> 
> * Så här distribuerar du en ad hoc-rapporterings databas
> * Så här kör du distribuerade frågor över alla klient databaser


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS-appen för flera klient organisationer har distribuerats. Om du vill distribuera på mindre än fem minuter läser du [distribuera och utforska Wingtip-biljetterna SaaS-databas program för flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) har installerats. Information om hur du hämtar och installerar SSMS finns i [hämta SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc-rapporterings mönster

![rapport mönster för adhoc](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-program kan analysera den stora mängden klient data som lagras centralt i molnet. Analyserna avslöjar insikter om driften och användningen av ditt program. Dessa insikter kan hjälpa till med utveckling av funktioner, användbarhets förbättringar och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk fråga](elastic-query-overview.md)som aktiverar frågor i en distribuerad uppsättning databaser med ett gemensamt schema. Dessa databaser kan distribueras mellan olika resurs grupper och prenumerationer. Men en gemensam inloggning måste ha åtkomst för att extrahera data från alla databaser. Elastisk fråga använder en enda *huvud* databas där externa tabeller definieras som speglar tabeller eller vyer i de distribuerade (klient) databaserna. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk fråga använder Shard-kartan i katalog databasen för att bestämma platsen för alla klient databaser. Det är enkelt att konfigurera och fråga med hjälp av standard [Transact-SQL](/sql/t-sql/language-reference), och stöder ad hoc-frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klient databaserna ger elastiska frågor omedelbar insyn i direkt produktions data. Eftersom en elastisk fråga hämtar data från potentiellt många databaser kan fråge svars tiden ibland vara högre än för motsvarande frågor som skickas till en enda databas för flera innehavare. Se till att utforma frågor för att minimera de data som returneras. Elastiska frågor passar ofta bäst för frågor mot små mängder real tids data, i stället för att skapa ofta använda eller komplexa analys frågor eller rapporter. Om frågor inte fungerar bra tittar du på [körnings planen](/sql/relational-databases/performance/display-an-actual-execution-plan) för att se vilken del av frågan som har flyttats ned till fjärrdatabasen. Och utvärdera hur mycket data som returneras. Frågor som kräver komplex analytisk bearbetning kan hanteras bättre genom att spara de extraherade klient data i en databas som är optimerad för analys frågor. SQL Database och Azure Synapse Analytics kunde vara värd för sådan analys databas.

Det här mönstret för analys beskrivs i [själv studie kursen för klient analys](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip-biljetterna SaaS källkod och skript för databas program för flera innehavare

Wingtip-biljetterna SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljnings data

Om du vill köra frågor mot en mer intressant data uppsättning skapar du biljett försäljnings data genom att köra biljett generatorn.

1. I *POWERSHELL ISE* öppnar du... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *Demo-AdhocReporting.ps1* -skript och ange följande värden:
   * **$DemoScenario** = 1, **Köp biljetter för händelser på alla platser**.
2. Tryck på **F5** för att köra skriptet och generera biljett försäljning. Fortsätt med stegen i den här själv studie kursen medan skriptet körs. Biljett data frågas i avsnittet *köra Ad hoc-frågor* och väntar på att biljett generatorn ska slutföras.

## <a name="explore-the-tenant-tables"></a>Utforska klient tabellerna 

I Wingtip-biljetterna SaaS för flera klient organisationer lagras klienter i en hanterings modell för Hybrid innehavare – där klient data antingen lagras i en databas för flera innehavare eller en enskild klient databas och kan flyttas mellan de två. När du frågar över alla klient databaser är det viktigt att elastiska frågor kan behandla data som om de ingår i en enda logisk databas shardade av klienten. 

För att uppnå det här mönstret innehåller alla klient tabeller en *VenueId* -kolumn som identifierar vilken klient som data tillhör. *VenueId* beräknas som en hash av plats namnet, men alla metoder kan användas för att införa ett unikt värde för den här kolumnen. Den här metoden liknar hur klient nyckeln beräknas för användning i katalogen. Tabeller som innehåller *VenueId* används av elastisk fråga för att parallellisera frågor och skicka dem till rätt fjärran sluten klient databas. Detta minskar dramatiskt mängden data som returneras och resulterar i en bättre prestanda, särskilt när det finns flera klienter vars data lagras i en enda klient databas.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuera databasen som används för ad hoc-distribuerade frågor

Den här övningen distribuerar *AdHocReporting* -databasen. Det här är huvud databasen som innehåller det schema som används för frågor över alla klient databaser. Databasen distribueras till den befintliga katalog servern, som är den server som används för alla hanterings relaterade databaser i exempel programmet.

1. Öppna... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *Demo-AdhocReporting.ps1* i *PowerShell ISE* och ange följande värden:
   * **$DemoScenario** = 2, **distribuera ad hoc Analytics-databasen**.

2. Tryck på **F5** för att köra skriptet och skapa *AdHocReporting* -databasen.

I nästa avsnitt lägger du till schema i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera Head-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (den externa data källan och externa tabell definitioner) till ad hoc-rapporterings databasen som aktiverar frågor över alla klient databaser.

1. Öppna SQL Server Management Studio och Anslut till adhoc-rapporterings databasen som du skapade i föregående steg. Namnet på databasen är *AdHocReporting*.
2. Öppna. ..\Learning Modules\Operational Analytics\Adhoc repor ting \ *Initialize-AdhocReportingDB. SQL* i SSMS.
3. Granska SQL-skriptet och Observera följande:

   Elastisk fråga använder en databas med begränsade autentiseringsuppgifter för att komma åt var och en av klient databaserna. Den här autentiseringsuppgiften måste vara tillgänglig i alla databaser och bör normalt beviljas de minsta rättigheter som krävs för att aktivera dessa ad hoc-frågor.

    ![skapa autentiseringsuppgift](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Genom att använda katalog databasen som extern data källa distribueras frågor till alla databaser som är registrerade i katalogen när frågan körs. Eftersom Server namn skiljer sig åt för varje distribution får det här initierings skriptet platsen för katalog databasen genom att hämta den aktuella servern (@ @servername ) där skriptet körs.

    ![Skapa extern data Källa](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externa tabeller som refererar till klient organisations tabeller definieras med **distribution = shardade (VenueId)**. Detta dirigerar en fråga för en viss *VenueId* till lämplig databas och förbättrar prestanda för många scenarier som visas i nästa avsnitt.

    ![skapa externa tabeller](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Den lokala tabellen *VenueTypes* som skapas och fylls i. Den här referens data tabellen är vanlig i alla klient databaser, så den kan visas här som en lokal tabell och fyllas i med gemensamma data. För vissa frågor kan detta minska mängden data som flyttas mellan klient databaserna och *AdHocReporting* -databasen.

    ![Skapa tabell](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Om du inkluderar referens tabeller på det här sättet måste du uppdatera tabellens schema och data när du uppdaterar klient databaserna.

4. Tryck på **F5** för att köra skriptet och initiera *AdHocReporting* -databasen. 

Nu kan du köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-ad-hoc-distributed-queries"></a>Köra Ad hoc-distribuerade frågor

Nu när *AdHocReporting* -databasen har kon figurer ATS kan du gå vidare och köra några distribuerade frågor. Ta med körnings planen för att få en bättre förståelse för var frågans bearbetning sker. 

När du inspekterar körnings planen kan du hovra över plan ikonerna för mer information. 

1. Öppna i *SSMS*... \\ Learning modules \\ Operational Analytics \\ adhoc repor ting \\ *demo-AdhocReportingQueries. SQL*.
2. Se till att du är ansluten till **AdHocReporting** -databasen.
3. Välj menyn **fråga** och klicka på **Inkludera faktisk körnings plan**
4. Markera *vilka platser som är registrerade för tillfället?* fråga och tryck på **F5**.

   Frågan returnerar hela plats listan, som illustrerar hur snabbt och enkelt det är att fråga över alla klienter och returnera data från varje klient.

   Granska planen och se att hela kostnaden är den fjärranslutna frågan eftersom vi bara ska gå till varje klient databas och välja plats information.

   ![Välj * från dbo. Platser](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan ansluter till data från klient databaserna och den lokala *VenueTypes* -tabellen (lokal, som den är en tabell i *AdHocReporting* -databasen).

   Kontrol lera planen och se att merparten av kostnaden är fjärrfrågan eftersom vi frågar efter klientens plats information (dbo. Platser) och gör en snabb lokal anslutning med den lokala *VenueTypes* -tabellen för att visa det egna namnet.

   ![Anslut till fjärranslutna och lokala data](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Välj nu den *dag där de mest Biljetterna såldes?* fråga och tryck på **F5**.

   Den här frågan gör en mer komplex anslutning och agg regering. Det är viktigt att Observera att det mesta av bearbetningen görs via fjärr anslutning, och en gång till får du tillbaka bara de rader vi behöver, och returnerar bara en rad för varje platss sammanlagda biljett försäljning per dag.

   ![DocumentDB](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en ad hoc-rapporterings databas och Lägg till schemat i den för att köra distribuerade frågor.

Testa nu [själv studie kursen för klient analys](saas-multitenantdb-tenant-analytics.md) för att utforska extrahering av data till en separat analys databas för mer komplex analys bearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastisk fråga](elastic-query-overview.md)