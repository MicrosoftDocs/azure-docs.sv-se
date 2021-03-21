---
title: Köra Analytics-frågor mot klient databaser
description: Analys frågor över flera klienter med data som har extraherats från Azure SQL Database, Azure Synapse Analytics, Azure Data Factory eller Power BI.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: dc47c996748b126841cbeff1ea3f6f18f423951f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96457640"
---
# <a name="explore-saas-analytics-with-azure-sql-database-azure-synapse-analytics-data-factory-and-power-bi"></a>Utforska SaaS Analytics med Azure SQL Database, Azure Synapse Analytics, Data Factory och Power BI
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du gå igenom ett scenario från slut punkt till slut punkt. Scenariot visar hur analys över klient data kan göra det möjligt för program varu leverantörer att fatta smarta beslut. Med hjälp av data som extraheras från varje klient databas använder du analyser för att få insikter om klient beteende, inklusive deras användning av SaaS-programmet Wingtip ticks. I det här scenariot ingår tre steg:

1. **Extrahera data** från varje klient databas till ett analys lager, i det här fallet en dedikerad SQL-pool.
2. **Optimera de extraherade data** för analys bearbetning.
3. Använd **Business Intelligence** -verktyg för att dra nytta av användbara insikter som hjälper dig att fatta beslut.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> - Skapa klient analys arkivet för inläsning.
> - Använd Azure Data Factory (ADF) för att extrahera data från varje klient databas till analys informations lagret.
> - Optimera de extraherade data (organisera om i ett stjärn schema).
> - Fråga analys informations lagret.
> - Använd Power BI för data visualisering för att markera trender i klient data och ge rekommendationer för förbättringar.

![architectureOverView](./media/saas-tenancy-tenant-analytics-adf/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>Analys över extraherade klient data

SaaS-program innehåller en potentiell mängd klient data i molnet. Dessa data kan ge en omfattande källa till insikter om hur programmet fungerar och hur det används och hur klienterna fungerar. Dessa insikter kan hjälpa till med utveckling av funktioner, användbarhets förbättringar och andra investeringar i apparna och plattformarna.

Det är enkelt att komma åt data för alla klienter när alla data bara finns i en databas med flera innehavare. Men åtkomsten är mer komplex när den distribueras i stor skala över tusentals databaser. Ett sätt att molndata komplexiteten är att extrahera data till en Analytics-databas eller ett informations lager för frågor.

I den här självstudien presenteras ett scenario från slut punkt till slut punkt för Wingtip Ticket-programmet. Först används [Azure Data Factory (ADF)](../../data-factory/introduction.md) som Orchestration-verktyg för att extrahera biljett försäljning och relaterade data från varje klient databas. Dessa data läses in i mellanlagrings tabeller i ett analys lager. Analytics-butiken kan antingen vara en SQL Database eller en dedikerad SQL-pool. I den här självstudien används [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) som analys lager.

Sedan omvandlas de extraherade data till en uppsättning med [stjärn schema](https://www.wikipedia.org/wiki/Star_schema) tabeller. Tabellerna består av en central fakta tabell plus relaterade dimensions tabeller:

- Den centrala fakta tabellen i Star-schemat innehåller biljett data.
- Dimensions tabellerna innehåller data om platser, händelser, kunder och inköps datum.

De centrala tabellerna och dimensions tabellerna möjliggör effektiv analytisk bearbetning. Det stjärn schema som används i den här självstudien visas i följande bild:

![Diagram som visar det stjärn schema som används i den här självstudien.](./media/saas-tenancy-tenant-analytics-adf/starschematables.JPG)

Slutligen frågas stjärn schema-tabellerna. Frågeresultat visas visuellt med Power BI för att framhäva insikter om klientens beteende och deras användning av programmet. Med det här stjärn schemat kan du köra frågor som visar:

- Vem köper biljetter och från vilka platser.
- Mönster och trender i försäljningen av biljetter.
- Den relativa populariteten för varje plats.

Den här självstudien innehåller grundläggande exempel på insikter som kan uppnår från Wingtip Ticket-data. Om du förstår hur varje plats använder tjänsten kan det leda till att Wingtip Ticket-leverantören funderar på olika tjänst planer som riktar sig till mer eller mindre aktiva platser, till exempel.

## <a name="setup"></a>Konfiguration

### <a name="prerequisites"></a>Förutsättningar

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

- Wingtip Ticket SaaS-databas per klient program har distribuerats. Information om hur du distribuerar på mindre än fem minuter finns i [distribuera och utforska Wingtip SaaS-programmet](./saas-dbpertenant-get-started-deploy.md).
- Wingtip-biljetterna SaaS-databas per klient skript och program [käll kod](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) hämtas från GitHub. Se hämtnings instruktioner. Se till att *avblockera zip-filen innan du* extraherar dess innehåll.
- Power BI Desktop har installerats. [Ladda ned Power BI Desktop](https://powerbi.microsoft.com/downloads/).
- Batchen med ytterligare klienter har etablerats finns i [**självstudien etablera klient organisationer**](./saas-dbpertenant-provision-and-catalog.md).

### <a name="create-data-for-the-demo"></a>Skapa data för demon

Den här självstudien utforskar analys över biljett försäljnings data. I det här steget genererar du biljett data för alla klienter. I ett senare steg extraheras dessa data för analys. _Se till att du har upprättat batchen över klient organisationer_ (enligt beskrivningen ovan) så att du har tillräckligt med data för att exponera ett antal olika biljett inköps mönster.

1. I PowerShell ISE öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* och anger följande värde:
    - **$DemoScenario**  =  **1** Köp biljetter för händelser på alla platser
2. Tryck på **F5** för att köra skriptet och skapa biljett inköps historik för alla platser. Med 20 klienter genererar skriptet flera tusentals biljetter och kan ta 10 minuter eller mer.

### <a name="deploy-azure-synapse-analytics-data-factory-and-blob-storage"></a>Distribuera Azure Synapse Analytics, Data Factory och Blob Storage

I Wingtip biljetter-appen distribueras klientens transaktions data över flera databaser. Azure Data Factory (ADF) används för att dirigera extrahering, belastning och transformering (ELT) av dessa data till data lagret. För att läsa in data i Azure Synapse Analytics, extraherar ADF data till mellanliggande BLOB-filer och använder [PolyBase](../../synapse-analytics/sql-data-warehouse/design-elt-data-loading.md) för att läsa in data till data lagret.

I det här steget distribuerar du ytterligare resurser som används i självstudien: en dedikerad SQL-pool med namnet _tenantanalytics_, ett Azure Data Factory som kallas _dbtodwload- \<user\>_ och ett Azure Storage-konto som kallas _wingtipstaging \<user\>_. Lagrings kontot används för att tillfälligt lagra extraherade datafiler som blobbar innan de läses in i data lagret. Det här steget distribuerar även data lagrets schema och definierar de ADF-pipeliner som dirigerar ELT-processen.

1. I PowerShell ISE öppnar du *. ..\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1* och anger:
    - **$DemoScenario**  =  **2** distribuera klient analys data lager, Blob Storage och Data Factory
1. Tryck på **F5** för att köra demonstrations skriptet och Distribuera Azure-resurserna.

Granska nu de Azure-resurser som du har distribuerat:

#### <a name="tenant-databases-and-analytics-store"></a>Klient databaser och analys lager

Använd [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) för att ansluta till **tenants1-DPT &lt; - &gt; User-** och **Catalog-DPT- &lt; User &gt; -** servrar. Ersätt &lt; User &gt; med det värde som används när du distribuerade appen. Använd login = *Developer* och Password = *P \@ ssword1*. Mer information finns i [introduktions kursen](./saas-dbpertenant-wingtip-app-overview.md) .

![Anslut till SQL Database från SSMS](./media/saas-tenancy-tenant-analytics-adf/ssmsSignIn.JPG)

I Object Explorer:

1. Expandera *tenants1-DPT- &lt; User &gt;* Server.
1. Expandera noden databaser och se listan över klient databaser.
1. Expandera *katalogen-DPT- &lt; User &gt;* Server.
1. Kontrol lera att Analytics Store innehåller följande objekt:
    1. Tabeller **raw_Tickets**, **raw_Customers**, **raw_Events** och **raw_Venues** lagra rå data från klient databaserna.
    1. De stjärn schema tabellerna är **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** och **dim_Dates**.
    1. Den lagrade proceduren **sp_transformExtractedData** används för att transformera data och läsa in dem i stjärn schema tabeller.

![Skärm bild som visar Object Explorer med tabeller expanderade för att visa olika databas objekt.](./media/saas-tenancy-tenant-analytics-adf/DWtables.JPG)

#### <a name="blob-storage"></a>Blob Storage

1. I [Azure Portal](https://ms.portal.azure.com)navigerar du till den resurs grupp som du använde för att distribuera programmet. Verifiera att ett lagrings konto med namnet **wingtipstaging \<user\>** har lagts till.

   ![DWtables](./media/saas-tenancy-tenant-analytics-adf/adf-staging-storage.PNG)

1. Klicka **på \<user\> wingtipstaging** lagrings konto för att utforska de objekt som finns.
1. Klicka på **blobs** -panelen
1. Klicka på behållaren **configfile**
1. Kontrol lera att **configfile** innehåller en JSON-fil **med namnetTableConfig.jspå**. Den här filen innehåller namn på käll-och mål tabell, kolumn namn och spår kolumn namn.

#### <a name="azure-data-factory-adf"></a>Azure Data Factory (ADF)

I [Azure Portal](https://ms.portal.azure.com) i resurs gruppen kontrollerar du att en Azure Data Factory som heter _dbtodwload- \<user\>_ har lagts till.

 ![adf_portal](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory-portal.png)

Det här avsnittet utforskar data fabriken som skapats.
Följ stegen nedan för att starta data fabriken:

1. I portalen klickar du på data fabriken som heter **dbtodwload- \<user\>**.
2. Klicka på Redigera panelen för **& övervakare** för att starta Data Factory designer på en separat flik.

## <a name="extract-load-and-transform-data"></a>Extrahera, läsa in och transformera data

Azure Data Factory används för att dirigera extrahering, inläsning och transformering av data. I den här självstudien extraherar du data från fyra olika SQL-vyer från var och en av klient databaserna: **rawTickets**, **rawCustomers**, **rawEvents** och **rawVenues**. Dessa vyer innehåller plats-ID så att du kan diskriminera data från varje plats i data lagret. Data läses in i motsvarande mellanlagrings tabeller i data lagret: **raw_Tickets** **raw_customers** **raw_Events** och **raw_Venue**. En lagrad procedur transformerar rå data och fyller i stjärn schema tabeller: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** och **dim_Dates**.

I föregående avsnitt har du distribuerat och initierat nödvändiga Azure-resurser, inklusive Data Factory. Den distribuerade data fabriken innehåller pipelines, data uppsättningar, länkade tjänster osv., som krävs för att extrahera, läsa in och transformera klient data. Låt oss utforska dessa objekt ytterligare och Utlös sedan pipelinen för att flytta data från klient databaser till data lagret.

### <a name="data-factory-pipeline-overview"></a>Översikt över Data Factory pipeline

I det här avsnittet utforskas de objekt som skapats i data fabriken. I följande figur beskrivs det övergripande arbets flödet för den ADF-pipeline som används i den här självstudien. Om du hellre vill utforska pipelinen senare och se resultatet först kan du gå vidare till nästa avsnitt **Utlös pipeline-körningen**.

![adf_overview](./media/saas-tenancy-tenant-analytics-adf/adf-data-factory.PNG)

På sidan Översikt växlar du till fliken **författare** i den vänstra panelen och Observera att det finns tre [pipelines](../../data-factory/concepts-pipelines-activities.md) och tre [data uppsättningar](../../data-factory/concepts-datasets-linked-services.md) skapas.
![adf_author](./media/saas-tenancy-tenant-analytics-adf/adf_author_tab.JPG)

De tre kapslade pipelinen är: SQLDBToDW, DBCopy och TableCopy.

**Pipeline 1-SQLDBToDW** letar upp namnen på de klient databaser som lagras i katalog databasen (tabell namn: [__ShardManagement]. [ ShardsGlobal]) och för varje klient databas körs pipelinen **DBCopy** . Vid slutförandet utförs det angivna schemat för **sp_TransformExtractedData** lagrade proceduren. Den här lagrade proceduren omvandlar inlästa data i mellanlagrings tabeller och fyller i stjärn schema tabeller.

**Pipeline 2 – DBCopy** letar upp namnen på käll tabellerna och kolumnerna från en konfigurations fil som lagras i Blob Storage.  **TableCopy** -pipeline körs sedan för var och en av de fyra tabellerna: TicketFacts, CustomerFacts, EventFacts och VenueFacts. **[Förgrunds](../../data-factory/control-flow-for-each-activity.md)** aktiviteten körs parallellt för alla 20 databaser. ADF tillåter att maximalt 20 upprepnings iterationer körs parallellt. Överväg att skapa flera pipelines för fler databaser.

**Pipeliner 3-TableCopy** använder rad versions nummer i SQL Database (_ROWVERSION_) för att identifiera rader som har ändrats eller uppdaterats. Den här aktiviteten söker upp start-och slut rads versionen för att extrahera rader från käll tabellerna. **CopyTracker** -tabellen som lagras i varje klient databas spårar den sista raden som extraheras från varje käll tabell i varje körning. Nya eller ändrade rader kopieras till motsvarande mellanlagrings tabeller i data lagret: **raw_Tickets**, **raw_Customers**, **raw_Venues** och **raw_Events**. Slutligen är den sista rad versionen Sparad i **CopyTracker** -tabellen som ska användas som den första rad versionen för nästa extrahering.

Det finns också tre parametriserade länkade tjänster som länkar data fabriken till SQL-källans SQL-databaser, den dedikerade SQL-poolen och den mellanliggande blob-lagringen. På fliken **författare** klickar du på **anslutningar** för att utforska de länkade tjänsterna, som du ser i följande bild:

![adf_linkedservices](./media/saas-tenancy-tenant-analytics-adf/linkedservices.JPG)

Det finns tre data uppsättningar som motsvarar de tre länkade tjänsterna och som refererar till de data som du använder i pipeline-aktiviteterna som indata eller utdata. Utforska varje data uppsättning för att se vilka anslutningar och parametrar som används. _AzureBlob_ pekar på konfigurations filen som innehåller käll-och mål tabeller och kolumner, samt kolumnen spår i varje källa.
  
### <a name="data-warehouse-pattern-overview"></a>Översikt över informations lager mönster

Azure Synapse används som Analytics Store för att utföra agg regering på klient data. I det här exemplet används PolyBase för att läsa in data till data lagret. Rå data läses in i tillfälliga tabeller som har en identitets kolumn för att hålla reda på rader som har omvandlats till stjärn schema tabeller. Följande bild visar inläsnings mönstret: ![ diagrammet visar inläsnings mönstret för databas tabeller.](./media/saas-tenancy-tenant-analytics-adf/loadingpattern.JPG)

SCD-typ 1-dimensions tabeller (långsamt ändring av dimension) används i det här exemplet. Varje dimension har en surrogat nyckel definierad med en identitets kolumn. Som bästa praxis är datum dimensions tabellen i förväg ifylld för att spara tid. För de andra dimensions tabellerna visas en CREATE TABLE som Välj... (CTAS)-instruktionen används för att skapa en temporär tabell som innehåller befintliga ändrade och icke-ändrade rader, tillsammans med surrogat nycklar. Detta görs med IDENTITY_INSERT = på. Nya rader infogas sedan i tabellen med IDENTITY_INSERT = OFF. För enkel återställning ändras namnet på den befintliga dimensions tabellen och den temporära tabellen byter namn till den nya dimensions tabellen. Innan varje körning tas den gamla dimensions tabellen bort.

Dimensions tabeller läses in före fakta tabellen. Den här ordningsföljden säkerställer att alla refererade dimensioner redan finns för varje tillkommande faktum. När fakta läses in matchas affärs nyckeln för varje motsvarande dimension och motsvarande surrogat nycklar läggs till i varje faktum.

Det sista steget i transformeringen tar bort de mellanlagrings data som är klara för nästa körning av pipelinen.

### <a name="trigger-the-pipeline-run"></a>Utlös pipeline-körningen

Följ stegen nedan för att köra den fullständiga pipelinen Extract, Load och Transform för alla klient databaser:

1. På fliken **författare** i ADF-användargränssnittet väljer du **SQLDBToDW** pipeline i det vänstra fönstret.
1. Klicka på **utlösare** och på den nedrullningsbara menyn Klicka på **Utlös nu**. Den här åtgärden kör pipelinen direkt. I ett produktions scenario definierar du en tidsplan för att köra pipelinen för att uppdatera data enligt ett schema.
  ![Skärm bild som visar fabriks resurser för en pipeline med namnet S Q L D B till D W med utlösnings alternativet expanderat och utlösare nu valt.](./media/saas-tenancy-tenant-analytics-adf/adf_trigger.JPG)
1. På sidan **pipeline-körning** klickar du på **Slutför**.

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. I användar gränssnittet för ADF växlar du till fliken **övervakare** från menyn till vänster.
1. Klicka på **Uppdatera** tills SQLDBToDW pipelines status har **slutförts**.
  ![Skärm bild som visar S Q L D B till D W-pipeline med statusen lyckades.](./media/saas-tenancy-tenant-analytics-adf/adf_monitoring.JPG)
1. Anslut till informations lagret med SSMS och fråga stjärn schema-tabellerna för att verifiera att data har lästs in i dessa tabeller.

När pipelinen har slutförts innehåller fakta tabellen biljett försäljnings data för alla platser och dimensions tabellerna fylls i med motsvarande platser, händelser och kunder.

## <a name="data-exploration"></a>Data utforskning

### <a name="visualize-tenant-data"></a>Visualisera klient data

Data i stjärn schema innehåller alla biljett försäljnings data som behövs för din analys. Visualisering av data grafiskt gör det lättare att se trender i stora data mängder. I det här avsnittet använder du **Power BI** för att manipulera och visualisera klient data i data lagret.

Använd följande steg för att ansluta till Power BI och för att importera de vyer som du skapade tidigare:

1. Starta Power BI skriv bord.
2. Från menyfliksområdet start väljer du **Hämta data** och väljer **mer...** från menyn.
3. I fönstret **Hämta data** väljer du **Azure SQL Database**.
4. I fönstret databas inloggning anger du Server namnet (**Catalog-DPT- &lt; User &gt; . Database.Windows.net**). Välj **Importera** för **data anslutnings läge** och klicka sedan på **OK**.

    ![Logga in till Power BI](./media/saas-tenancy-tenant-analytics-adf/powerBISignIn.PNG)

5. Välj **databas** i den vänstra rutan och ange sedan användar namn = *utvecklare* och ange Password = *P \@ ssword1*. Klicka på **Anslut**.  

    ![databas inloggning](./media/saas-tenancy-tenant-analytics-adf/databaseSignIn.PNG)

6. I **navigerings** fönstret, under Analytics-databasen, väljer du stjärn schema tabeller: **fact_Tickets**, **dim_Events**, **dim_Venues**, **dim_Customers** och **dim_Dates**. Välj sedan **load**.

Grattis! Du har läst in data i Power BI. Utforska nu intressanta visualiseringar för att få insikter om dina klienter. Låt oss gå igenom hur Analytics kan ge vissa data drivna rekommendationer till affärs teamet för Wingtip-biljetter. Rekommendationerna kan hjälpa till att optimera affärs modellen och kund upplevelsen.

Börja med att analysera biljett försäljnings data för att se variationen i användning över platserna. Välj de alternativ som visas i Power BI för att rita ett stapeldiagram med det totala antalet biljetter som sålts av varje plats. (På grund av slumpmässig variation i biljett generatorn kan resultatet skilja sig.)

![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/TotalTicketsByVenues-DW.PNG)

Föregående område bekräftar att antalet biljetter som säljs av varje plats varierar. Platser som säljer fler biljetter använder tjänsten mer kraftigt än platser som säljer färre biljetter. Det kan finnas en möjlighet att skräddarsy resurs tilldelningen efter olika klient behov.

Du kan analysera data ytterligare för att se hur biljett försäljningen varierar över tid. Välj de alternativ som visas i följande bild i Power BI för att rita upp det totala antalet biljetter som säljs varje dag under en period på 60 dagar.

![SaleVersusDate](./media/saas-tenancy-tenant-analytics-adf/SaleVersusDate-DW.PNG)

Föregående diagram visar att biljett försäljnings inökningen för vissa platser. Dessa toppar förstärker tanken att vissa platser kan använda system resurser oproportionerligt. Så långt finns det inga uppenbara mönster i när topparna inträffar.

Nu ska vi undersöka betydelsen av dessa höga försäljnings dagar. När sker de här belastningarna när biljetterna går på att sälja? Välj de alternativ som visas i följande bild i Power BI för att rita in biljetter sålda per dag.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics-adf/SaleDistributionPerDay-DW.PNG)

Det här området visar att vissa platser säljer ett stort antal biljetter på den första försäljnings dagen. Så snart biljetterna går på försäljningen på dessa platser verkar det vara en Mad skynda. Denna burst-aktivitet av några platser kan påverka tjänsten för andra klienter.

Du kan öka detalj nivån för data igen för att se om den här Mad skynda är sann för alla händelser som finns på dessa platser. I föregående områden såg du att contoso konsert Hall säljer många biljetter och att contoso också har en topp i biljett försäljningen på vissa dagar. Experimentera med Power BI alternativ för att rita in kumulativa biljett försäljning för Contoso konsert Hall, och fokusera på försäljnings trender för var och en av dess händelser. Följer alla händelser samma Sälj mönster? Försök att skapa en rityta som den nedan.

![ContosoSales](./media/saas-tenancy-tenant-analytics-adf/EventSaleTrends.PNG)

Det här området av kumulativ biljett försäljning över tid för Contoso konsert Hall för varje händelse visar att Mad skynda inte inträffar för alla händelser. Experimentera med filter alternativen för att utforska försäljnings trender för andra platser.

Insikterna i biljett Sälj mönstren kan leda Wingtip-biljetter för att optimera sina affärs modeller. I stället för att debitera alla klienter på samma sätt bör Wingtip införa tjänst nivåer med olika beräknings storlekar. Större platser som behöver sälja fler biljetter per dag kan erbjudas en högre nivå med ett högre service nivå avtal (SLA). Dessa platser kan ha sina databaser placerade i poolen med högre resurs gränser per databas. Varje tjänst nivå kan ha en tilldelning per timme, med ytterligare avgifter som debiteras för att överskrida tilldelningen. Större platser som har regelbundna försäljnings nivåer skulle ha nytta av de högre nivåerna, och Wingtip-biljetter kan använda tjänsten mer effektivt.

Samtidigt har vissa Wingtip-biljetter för kunder klagat att sälja tillräckligt med biljetter för att motivera tjänste kostnaden. I dessa insikter finns det en möjlighet att öka biljett försäljningen för att under utföra platser. Högre försäljning skulle öka det uppfattade värdet för tjänsten. Högerklicka på fact_Tickets och välj **nytt mått**. Ange följande uttryck för det nya måttet med namnet **AverageTicketsSold**:

```sql
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Välj följande visualiserings alternativ för att rita upp procentuella biljetter som säljs av varje plats för att fastställa deras relativa framgång.

![AvgTicketsByVenues](./media/saas-tenancy-tenant-analytics-adf/AvgTicketsByVenues-DW.PNG)

Ritningen ovan visar att även om de flesta platser säljer mer än 80% av deras biljetter, är det kämpar att fylla mer än hälften av deras platser. Experimentera med bra värden och välj högsta eller lägsta procent andel biljetter som säljs för varje plats.

## <a name="embedding-analytics-in-your-apps"></a>Bädda in analyser i dina appar

Den här självstudien har fokuserat på analys av flera klienter som används för att förbättra program varu leverantörens förståelse för sina klienter. Analytics kan även ge insikter till _klienterna_, så att de kan hantera sin verksamhet mer effektivt.

I Wingtip Ticket-exemplet har du tidigare identifierat att biljett försäljningen tenderar att följa förutsägbara mönster. Den här insikten kan användas för att under lätta undersökandet av Sälj platser. Det kanske finns möjlighet att använda Machine Learning-tekniker för att förutse biljett försäljning för evenemang. Effekterna av pris ändringar kan också modelleras för att tillåta effekten av att erbjuda rabatter. Power BI Embedded kan integreras i ett händelse hanterings program för att visualisera förväntad försäljning, inklusive inverkan på rabatter på totalt antal sålda platser och intäkter för händelser med låg försäljning. Med Power BI Embedded kan du till och med integrera rabatten på biljett priserna, direkt i visualiserings upplevelsen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> - Skapa klient analys arkivet för inläsning.
> - Använd Azure Data Factory (ADF) för att extrahera data från varje klient databas till analys informations lagret.
> - Optimera de extraherade data (organisera om i ett stjärn schema).
> - Fråga analys informations lagret.
> - Använd Power BI för data visualisering för att markera trender i klient data och ge rekommendationer för förbättringar.

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

- Ytterligare [självstudier som bygger på Wingtip SaaS-programmet](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).