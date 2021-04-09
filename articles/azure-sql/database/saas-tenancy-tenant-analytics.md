---
title: Analys av flera klienter med extraherade data
description: Analys frågor mellan klienter med data som extraherats från flera Azure SQL-databaser i en enda klient-app.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 98896b5b728a729a29f989b3b9a76f29131af8d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93305978"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Analys av flera klienter med extraherade data – en-klient-app
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
I den här självstudien får du gå igenom ett komplett analys scenario för en enda klient implementering. Scenariot visar hur analyser kan göra det möjligt för företag att fatta smarta beslut. Med hjälp av data som extraheras från varje klient databas använder du analyser för att få insikter om klient beteende, inklusive deras användning av SaaS-programmet Wingtip ticks. I det här scenariot ingår tre steg: 

1.  **Extrahera** data från varje klient databas och **Läs in** dem i ett analys lager.
2.  **Transformera extraherade data** för analys bearbetning.
3.  Använd **Business Intelligence** verktyg för att dra nytta av användbara insikter som hjälper dig att fatta beslut. 

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> - Skapa butiken för klient analys för att extrahera data till.
> - Använd elastiska jobb för att extrahera data från varje klient databas till Analytics Store.
> - Optimera de extraherade data (organisera om i ett stjärn schema).
> - Fråga Analytics-databasen.
> - Använd Power BI för data visualisering för att markera trender i klient data och ge rekommendationer för förbättringar.

![Diagrammet visar en översikt över arkitekturen som används för den här artikeln.](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Analys mönster för offline-klient

SaaS-program för flera innehavare har vanligt vis en mängd klient data som lagras i molnet. Dessa data ger en omfattande källa till insikter om hur ditt program fungerar och hur det fungerar och hur klienterna fungerar. Dessa insikter kan hjälpa till med utveckling av funktioner, användbarhets förbättringar och andra investeringar i appen och plattformen.

Det är enkelt att komma åt data för alla klienter när alla data bara finns i en databas med flera innehavare. Men åtkomsten är mer komplex när den distribueras i skala över potentiellt tusentals databaser. Ett sätt att molndata komplexiteten och minimera effekten av analys frågor på transaktions data är att extrahera data till ett syfte som är utformat för analys databaser eller informations lager.

I den här självstudien presenteras ett komplett analys scenario för Wingtip ticks SaaS-program. Först används *elastiska jobb* för att extrahera data från varje klient databas och läsa in dem i tillfälliga tabeller i ett analys lager. Analytics Store kan antingen vara en SQL Database eller en dedikerad SQL-pool. För storskalig data extrahering rekommenderas [Azure Data Factory](../../data-factory/introduction.md) .

Sedan omvandlas aggregerade data till en uppsättning av [stjärn schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna består av en central fakta tabell plus relaterade dimensions tabeller.  För Wingtip-biljetter:

- Den centrala fakta tabellen i Star-schemat innehåller biljett data.
- Dimensions tabellerna beskriver platser, händelser, kunder och inköps datum.

De centrala tabellerna i fakta och dimension möjliggör effektiv analytisk bearbetning. Det stjärn schema som används i den här självstudien visas i följande bild:
 
![architectureOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Slutligen frågas analys lagret med **Power BI** för att framhäva insikter om klientens beteende och användningen av Wingtip Ticket-programmet. Du kör frågor som:
 
- Visa den relativa populariteten för varje plats
- Markera mönster i biljett försäljning för olika händelser
- Visa den relativa framgången för olika platser där du säljer deras evenemang

Att förstå hur varje klient använder tjänsten används för att utforska alternativ för att använda tjänsten och förbättra tjänsten för att hjälpa klienter att bli mer framgångs rik. Den här självstudien innehåller grundläggande exempel på olika typer av insikter som kan uppnår från klient data.

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Förutsättningar

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

- Wingtip Ticket SaaS-databas per klient program har distribuerats. Information om hur du distribuerar på mindre än fem minuter finns i [distribuera och utforska programmet Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md)
- Wingtip-biljetterna SaaS-databas per klient skript och program [käll kod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) hämtas från GitHub. Se hämtnings instruktioner. Se till att *avblockera zip-filen innan du* extraherar dess innehåll. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.
- Power BI Desktop har installerats. [Hämta Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- Batchen med ytterligare klienter har etablerats finns i [**självstudien etablera klient organisationer**](./saas-dbpertenant-provision-and-catalog.md).
- Ett jobb konto och en jobb konto databas har skapats. Se lämpliga steg i [**själv studie kursen om schema hantering**](./saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Skapa data för demon

I den här självstudien utförs analysen på biljett försäljnings data. I det aktuella steget genererar du biljett data för alla klienter.  Senare extraheras dessa data för analys. *Se till att du har etablerad batchen över klienter enligt beskrivningen ovan, så att du har en meningsfull mängd data*. En tillräckligt stor mängd data kan exponera en rad olika biljett inköps mönster.

1. I PowerShell ISE öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* och anger följande värde:
    - **$DemoScenario**  =  **1** Köp biljetter för händelser på alla platser
2. Tryck på **F5** för att köra skriptet och skapa biljett inköps historik för varje händelse på varje plats.  Skriptet körs i flera minuter för att generera flera tusen biljetter.

### <a name="deploy-the-analytics-store"></a>Distribuera Analytics Store
Det finns ofta flera transaktions databaser som tillsammans innehåller alla klient data. Du måste aggregera klient data från de många transaktions databaserna till ett analys lager. Agg regeringen möjliggör effektiv data frågor. I den här självstudien används en Azure SQL Database för att lagra aggregerade data.

I följande steg distribuerar du Analytics Store, som kallas **tenantanalytics**. Du distribuerar även fördefinierade tabeller som fylls i senare i självstudien:
1. I PowerShell ISE öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Ange $DemoScenario variabel i skriptet för att matcha ditt val av analys lager:
    - Ange **$DemoScenario**  =  **2** om du vill använda SQL Database utan kolumn lagring
    - Om du vill använda SQL Database med kolumn lagring anger du **$DemoScenario**  =  **3**  
3. Tryck på **F5** för att köra demonstrations skriptet (som anropar skriptet *Deploy-TenantAnalytics \<XX> . ps1* ) som skapar klient analys arkivet. 

Nu när du har distribuerat programmet och fyllt det med intressanta klient data använder du [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta **tenants1-DPT- &lt; User &gt;** och **Catalog-DPT- &lt; User &gt; -** servrar med login = *Developer*, Password = *P \@ ssword1*. Mer information finns i [introduktions kursen](./saas-dbpertenant-wingtip-app-overview.md) .

![Skärm bild som visar den information som behövs för att ansluta till SQL Server.](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Utför följande steg i Object Explorer:

1. Expandera *tenants1-DPT- &lt; User &gt;* Server.
2. Expandera noden databaser och se listan över klient databaser.
3. Expandera *katalogen-DPT- &lt; User &gt;* Server.
4. Kontrol lera att Analytics Store och jobaccount-databasen visas.

Se följande databas objekt i SSMS-Object Explorer genom att expandera noden analys Arkiv:

- Tabellerna **TicketsRawData** och **EventsRawData** innehåller råa extraherade data från klient databaserna.
- De stjärn schema tabellerna är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** och **dim_Dates**.
- Den lagrade proceduren används för att fylla i stjärn schema tabeller från rå data tabeller.

![Skärm bild av de databas objekt som visas i SSMS-Object Explorer.](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Data extrahering 

### <a name="create-target-groups"></a>Skapa mål grupper 

Innan du fortsätter bör du kontrol lera att du har distribuerat jobb kontot och jobaccount-databasen. I nästa uppsättning steg används elastiska jobb för att extrahera data från varje klient databas och för att lagra data i Analytics Store. Sedan shreds det andra jobbet data och lagrar dem i tabeller i stjärn schemat. De här två jobben körs mot två olika mål grupper, nämligen **TenantGroup** och **AnalyticsGroup**. Extraherings jobbet körs mot TenantGroup, som innehåller alla klient databaser. Fragmenterings jobbet körs mot AnalyticsGroup, som bara innehåller analys lagret. Skapa mål grupper med hjälp av följande steg:

1. I SSMS ansluter du till **jobaccount** -databasen i katalogen-DPT- &lt; User &gt; .
2. I SSMS öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ TargetGroups. SQL* 
3. Ändra @User variabeln överst i skriptet och Ersätt `<User>` med det användar värde som användes när du distribuerade Wingtip SaaS-appen.
4. Tryck på **F5** för att köra skriptet som skapar de två mål grupperna.

### <a name="extract-raw-data-from-all-tenants"></a>Extrahera rå data från alla klienter

Omfattande data ändringar kan uppstå oftare för *biljett-och kund* information än för *händelse-och* plats data. Därför bör du överväga att extrahera biljett-och kunddata separat och oftare än att extrahera händelse-och plats data. I det här avsnittet definierar och schemalägger du två separata jobb:

- Extrahera biljett-och kund information.
- Extrahera händelse-och plats data.

Varje jobb extraherar data och publicerar dem i Analytics Store. Det finns ett separat jobb som shreds de extraherade data i analys stjärn schema.

1. I SSMS ansluter du till **jobaccount** -databasen i katalogen-DPT- &lt; User &gt; Server.
2. I SSMS öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Ändra @User överst i skriptet och Ersätt `<User>` med det användar namn som användes när du distribuerade Wingtip SaaS-appen 
4. Tryck på F5 för att köra skriptet som skapar och kör jobbet som extraherar biljetter och kund data från varje klient databas. Jobbet sparar data i Analytics Store.
5. Fråga TicketsRawData-tabellen i tenantanalytics-databasen för att säkerställa att tabellen fylls med biljett information från alla klienter.

![Skärm bild som visar ExtractTickets-databasen med TicketsRawData d b o valt i Object Explorer.](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Upprepa föregående steg, förutom den här gången Ersätt **\ExtractTickets.SQL** med **\ExtractVenuesEvents.SQL** i steg 2.

Jobbet som körs fyller i EventsRawData-tabellen i Analytics Store med nya händelser och information om platser från alla klienter. 

## <a name="data-reorganization"></a>Data organisation

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Shred extraherade data för att fylla i stjärn schema tabeller

Nästa steg är att Shred extraherade rå data till en uppsättning tabeller som är optimerade för analys frågor. Ett stjärn schema används. En central fakta tabell innehåller enskilda försäljnings poster för biljetter. Andra tabeller fylls med relaterade data om platser, händelser och kunder. Och det finns tids dimensions tabeller. 

I det här avsnittet av självstudien definierar och kör du ett jobb som sammanfogar extraherade rå data med data i stjärn schema tabeller. När sammanfognings jobbet är klart raderas rå data, och de tabeller som är klara att fyllas i av nästa klient data extraherings jobb tas bort.

1. I SSMS ansluter du till **jobaccount** -databasen i katalogen-DPT- &lt; User &gt; .
2. I SSMS öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Tryck på **F5** för att köra skriptet för att definiera ett jobb som anropar sp_ShredRawExtractedData lagrade proceduren i Analytics Store.
4. Tillåt tillräckligt med tid för att jobbet ska kunna köras.
    - Kontrol lera **livs cykel** kolumnen i Jobs.jobs_execution tabell för jobb status. Kontrol lera att jobbet **har slutförts** innan du fortsätter. En lyckad körning visar data som liknar följande diagram:

![fragmentering](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Data utforskning

### <a name="visualize-tenant-data"></a>Visualisera klient data

Data i stjärn schema-tabellen innehåller alla biljett försäljnings data som behövs för din analys. För att göra det lättare att se trender i stora data mängder måste du visualisera det grafiskt.  I det här avsnittet får du lära dig hur du använder **Power BI** för att manipulera och visualisera de klient data som du har extraherat och ordnat.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI skriv bord.
2. Från menyfliksområdet start väljer du **Hämta data** och väljer **mer...** från menyn.
3. I fönstret **Hämta data** väljer du Azure SQL Database.
4. I fönstret databas inloggning anger du Server namnet (Catalog-DPT- &lt; User &gt; . Database.Windows.net). Välj **Importera** för **data anslutnings läge** och klicka sedan på OK. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Välj **databas** i den vänstra rutan och ange sedan användar namn = *utvecklare* och ange Password = *P \@ ssword1*. Klicka på **Anslut**.  

    ![Skärm bild som visar dialog rutan SQL Server databas där du kan ange ett användar namn och ett lösen ord.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. I **navigerings** fönstret, under Analytics-databasen, väljer du stjärn schema tabeller: fact_Tickets, dim_Events, dim_Venues, dim_Customers och dim_Dates. Välj sedan **load**. 

Grattis! Du har läst in data i Power BI. Nu kan du börja utforska intressanta visualiseringar för att få insikter om dina klienter. Härnäst går du igenom hur analyser kan ge dig möjlighet att tillhandahålla data drivna rekommendationer till affärs teamet för Wingtip-biljetter. Rekommendationerna kan hjälpa till att optimera affärs modellen och kund upplevelsen.

Du börjar genom att analysera biljett försäljnings data för att se variationen i användning på platserna. Välj följande alternativ i Power BI för att rita ett stapeldiagram med det totala antalet biljetter som sålts av varje plats. På grund av slumpmässig variation i biljett generatorn kan resultatet skilja sig.
 
![Skärm bild som visar en effekt på Power B I visualisering och kontroller för data visualiseringen på höger sida.](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Föregående område bekräftar att antalet biljetter som säljs av varje plats varierar. Platser som säljer fler biljetter använder tjänsten mer kraftigt än platser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resurs tilldelningen efter olika klient behov.

Du kan analysera data ytterligare för att se hur biljett försäljningen varierar över tid. Välj följande alternativ i Power BI för att rita upp det totala antalet biljetter som säljs varje dag under en period på 60 dagar.
 
![Skärm bild som visar Power B I-visualiseringen med rubriken biljett Sälj distribution respektive försäljnings dag.](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Föregående diagram visar att biljett försäljnings insamling för vissa platser. Dessa toppar förstärker tanken att vissa platser kan använda system resurser oproportionerligt. Så långt finns det inga uppenbara mönster i när topparna inträffar.

Härnäst vill du undersöka betydelsen av dessa topp försäljnings dagar. När sker de här belastningarna när biljetterna går på att sälja? Välj följande alternativ i Power BI för att rita in biljetter som sålts per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Föregående område visar att vissa platser säljer många biljetter på den första dagen av försäljningen. Så snart biljetterna går på försäljningen på dessa platser verkar det vara en Mad skynda. Denna burst-aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan öka detalj nivån för data igen för att se om den här Mad skynda är sann för alla händelser som finns på dessa platser. I föregående observationer noterade du att contoso konsert Hall säljer många biljetter och att contoso också har en topp i biljett försäljningen på vissa dagar. Experimentera med Power BI alternativ för att rita in kumulativa biljett försäljning för Contoso konsert Hall, och fokusera på försäljnings trender för var och en av dess händelser. Följer alla händelser samma Sälj mönster?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

Föregående rityta för Contoso konsert Hall visar att Mad skynda inte inträffar för alla händelser. Experimentera med filter alternativen för att se försäljnings trender för andra platser.

Insikterna i biljett Sälj mönstren kan leda Wingtip-biljetter för att optimera sina affärs modeller. I stället för att debitera alla klienter på samma sätt bör Wingtip införa tjänst nivåer med olika beräknings storlekar. Större platser som behöver sälja fler biljetter per dag kan erbjudas en högre nivå med ett högre service nivå avtal (SLA). Dessa platser kan ha sina databaser placerade i poolen med högre resurs gränser per databas. Varje tjänst nivå kan ha en tilldelning per timme, med ytterligare avgifter som debiteras för att överskrida tilldelningen. Större platser som har regelbundna försäljnings nivåer skulle ha nytta av de högre nivåerna, och Wingtip-biljetter kan använda tjänsten mer effektivt.

Samtidigt har vissa Wingtip-biljetter för kunder klagat att sälja tillräckligt med biljetter för att motivera tjänste kostnaden. I dessa insikter finns det en möjlighet att öka biljett försäljningen för att under utföra platser. Högre försäljning skulle öka det uppfattade värdet för tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet med namnet **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Välj följande visualiserings alternativ för att rita upp procentuella biljetter som säljs av varje plats för att fastställa deras relativa framgång.

![Skärm bild som visar effekt B I visualiseringen med rubriken Genomsnittligt antal biljetter som sålts av varje plats.](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Föregående område visar att även om de flesta platser säljer över 80% av deras biljetter, är det kämpar att fylla mer än hälften av platserna. Experimentera med bra värden och välj högsta eller lägsta procent andel biljetter som säljs för varje plats.

Tidigare deepened du din analys för att upptäcka att biljett försäljningen tenderar att följa förutsägbara mönster. Den här identifieringen kan göra att Wingtip-biljetter kan göra det möjligt för affärs platser att öka biljett försäljningen genom att rekommendera dynamisk prissättning. Den här identifieringen kan avslöja en möjlighet att använda Machine Learning-tekniker för att förutsäga biljett försäljningen för varje händelse. Förutsägelser kan också göras för att påverka intäkterna från vinst rabatter för biljett försäljning. Power BI Embedded kan integreras i ett program för händelse hantering. Integrationen kan hjälpa till att visualisera förväntad försäljning och effekterna av olika rabatter. Programmet kan hjälpa till att utforma en optimal rabatt som ska användas direkt från analys visningen.

Du har observerat trender i klient data från WingTip-programmet. Du kan välja mellan andra sätt som appen kan meddela affärs beslut för SaaS program leverantörer. Leverantörer kan bättre tillgodose behoven hos sina klienter. Förhoppnings vis har den här självstudien med verktyg som behövs för att utföra analyser på klient data för att hjälpa dina företag att fatta data drivna beslut.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Distribuerat en klient analys databas med fördefinierade stjärn schema tabeller
> - Använda elastiska jobb för att extrahera data från alla klient databaser
> - Sammanfoga extraherade data i tabeller i ett stjärn schema utformat för analys
> - Fråga en Analytics-databas 
> - Använd Power BI för data visualisering för att iaktta trender i klient data 

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-programmet](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Elastiska jobb](./elastic-jobs-overview.md).
- [Analys av flera klienter med extraherade data-multi-Apps-appen](./saas-multitenantdb-tenant-analytics.md)
