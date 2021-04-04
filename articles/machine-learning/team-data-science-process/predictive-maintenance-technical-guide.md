---
title: Guide till förutsägande underhåll för flyg teamets data vetenskaps process
description: En teknisk guide till lösnings mal len för förutsägande underhåll i flyg, verktyg och transport.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321994"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Teknisk guide till lösnings mal len för förutsägande underhåll i Aerospace

> [!Important]
> Den här artikeln är föråldrad. Diskussionen om förutsägelse underhåll i Aerospace är fortfarande relevant, men för aktuell information, se [lösnings översikt för företags mål grupper](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace).


Solution templates har utformats för att påskynda processen med att skapa en E2E-demo. En distribuerad mall etablerar din prenumeration med nödvändiga komponenter och skapar sedan relationerna mellan dem. Den dirigerar också data pipelinen med exempel data från ett data Generator program, som du hämtar och installerar på den lokala datorn efter att du har distribuerat lösnings mal len. Data från generatorn torkar data pipelinen och börjar generera Machine Learning-förutsägelser, som sedan kan visualiseras på Power BI instrument panelen.

Distributions processen vägleder dig genom flera steg för att ställa in dina autentiseringsuppgifter för lösningen. Se till att du registrerar autentiseringsuppgifterna som lösnings namn, användar namn och lösen ord som du anger under distributionen. 


Syftet med den här artikeln är att:
- Beskriv referens arkitekturen och de komponenter som tillhandahålls i din prenumeration.
- Visar hur du ersätter exempel data med dina egna data. 
- Visa hur du ändrar lösnings mal len.  

## <a name="overview"></a>Översikt
![Arkitektur för förutsägelse underhåll](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

När du distribuerar lösningen aktiverar den Azure-tjänster, inklusive Händelsehubben, Stream Analytics, HDInsight, Data Factory och Machine Learning. Arkitektur diagrammet visar hur mallen för förutsägelse underhåll för Aerospace-lösningen är konstruerad. Du kan undersöka dessa tjänster i Azure Portal genom att klicka på dem i lösnings mal Lav diagrammet som skapats med lösnings distributionen (förutom för HDInsight, som är etablerad på begäran när relaterade pipeline-aktiviteter krävs för att köra och tas bort efteråt).
Ladda ned en [full storleks version av diagrammet](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I följande avsnitt beskrivs lösnings delarna.

## <a name="data-source-and-ingestion"></a>Data källa och inmatning
### <a name="synthetic-data-source"></a>Syntetisk data Källa
För den här mallen genereras data källan som används från ett nedladdat Skriv bords program som du kör lokalt efter en lyckad distribution.

Om du vill hitta instruktioner för att hämta och installera det här programmet väljer du den första noden, data generator för förebyggande underhåll i lösnings mal Lav diagrammet. Anvisningarna finns i fältet egenskaper. I det här programmet matas tjänsten [Azure Event Hub](#azure-event-hub) med data punkter eller händelser som används i resten av lösnings flödet. Den här data källan härleds från offentligt tillgängliga data från data [lagringen för NASA](https://c3.nasa.gov/dashlink/resources/139/) med [turbofan-datauppsättningen för motor degraderingen](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Programmet Event Generation fyller i Azure Event Hub bara när det körs på datorn.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Tjänsten [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) är mottagaren av de indata som tillhandahålls av den syntetiska data källan.

## <a name="data-preparation-and-analysis"></a>Förberedelse och analys av data  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Använd [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) för att tillhandahålla analys i nära real tid i indata-dataströmmen från tjänsten [Azure Event Hub](#azure-event-hub) . Sedan publicerar du resultaten på en [Power BI](https://powerbi.microsoft.com) instrument panel och arkiverar alla rå inkommande händelser till tjänsten [Azure Storage](https://azure.microsoft.com/services/storage/) för senare bearbetning av [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -tjänsten.

### <a name="hdinsight-custom-aggregation"></a>Anpassad agg regering för HDInsight
Kör [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skript (dirigerad av Azure Data Factory) med HDInsight för att tillhandahålla agg regeringar för de råa händelser som arkiveras med hjälp av Azure Stream Analytics resursen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Gör förutsägelser för återstående livs längd (RUL) för en viss flyg Plans motor med hjälp av de indata som tagits emot med [Azure Machine Learning-tjänsten](https://azure.microsoft.com/services/machine-learning/) (dirigerad av Azure Data Factory). 

## <a name="data-publishing"></a>Data publicering
### <a name="azure-sql-database"></a>Azure SQL Database
Använd [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) för att lagra förutsägelserna som tagits emot av Azure Machine Learning, som sedan används i [Power BI](https://powerbi.microsoft.com) instrument panelen.

## <a name="data-consumption"></a>Dataförbrukning
### <a name="power-bi"></a>Power BI
Använd [Power BI](https://powerbi.microsoft.com) för att visa en instrument panel som innehåller agg regeringar och aviseringar som tillhandahålls av [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), samt RUL förutsägelser lagrade i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som har producerats med [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Så här hämtar du dina egna data
I det här avsnittet beskrivs hur du hämtar dina data till Azure och vilka områden som kräver ändringar av de data som du använder i den här arkitekturen.

Det är osannolikt att din data uppsättning matchar den data uppsättning som används av den [turbofan](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) som används för den här lösnings mal len. Att förstå dina data och kraven är viktiga i hur du ändrar mallen så att den fungerar med dina egna data. 

I följande avsnitt beskrivs de delar av mallen som kräver ändringar när en ny data uppsättning introduceras.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub är generisk; data kan skickas till hubben i CSV-eller JSON-format. Ingen särskild bearbetning sker i Azure Event Hub, men det är viktigt att du förstår de data som matas in i den.

Det här dokumentet beskriver inte hur du matar in dina data, men du kan enkelt skicka händelser eller data till en Azure Event Hub med Event Hub-API: erna.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Använd Azure Stream Analytics-resursen för att tillhandahålla analys i nära real tid genom att läsa data strömmar och skicka data till valfritt antal källor.

För mallen för förutsägelse underhåll för Aerospace-lösning består den Azure Stream Analytics frågan av fyra under frågor, varje fråga som använder händelser från tjänsten Azure Event Hub, med utdata till fyra olika platser. Dessa utdata består av tre Power BI data uppsättningar och en Azure Storage plats.

Azure Stream Analyticss frågan kan hittas av:

* Anslut till Azure Portal
* Hitta Stream Analytics jobb ![ Stream Analytics ikonen ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) som genererades när lösningen distribuerades (*till exempel* **maintenancesa02asapbi** och **maintenancesa02asablob** för lösningen för förebyggande underhåll)
* Valt
  
  * ***Indata*** för att Visa frågans indata
  * ***Fråga*** för att Visa själva frågan
  * ***Utdata*** för att visa olika utdata

Information om hur du Azure Stream Analytics Query-konstruktion finns i [referensen Stream Analytics frågor](/stream-analytics-query/stream-analytics-query-language-reference) på MSDN.

I den här lösningen skickar frågorna tre data uppsättningar med information om den inkommande data strömmen till en Power BI instrument panel som finns som en del av den här lösnings mal len. Eftersom det finns en implicit kunskap om det inkommande data formatet, måste dessa frågor ändras baserat på ditt data format.

Frågan i det andra Stream Analytics jobb- **maintenancesa02asablob** skapar helt enkelt alla [Event Hub](https://azure.microsoft.com/services/event-hubs/) -händelser som ska [Azure Storage](https://azure.microsoft.com/services/storage/) och kräver därför ingen ändring oavsett ditt data format eftersom all händelse information strömmas till lagrings utrymmet.

### <a name="azure-data-factory"></a>Azure Data Factory
Tjänsten [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) dirigerar flyttningen och bearbetningen av data. I det förutsägande underhållet av flyg lösnings mal len består data fabriken av tre [pipelines](../../data-factory/concepts-pipelines-activities.md) som flyttar och bearbetar data med hjälp av olika tekniker.  Öppna din data fabrik genom att öppna noden Data Factory längst ned i lösnings mal Lav diagrammet som skapats med distributionen av lösningen. Fel under dina data uppsättningar beror på att Data Factory har distribuerats innan data generatorn startades. Dessa fel kan ignoreras och förhindrar inte att data fabriken fungerar.

![Data Factory data uppsättnings fel](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

I det här avsnittet beskrivs nödvändiga [pipeliner och aktiviteter](../../data-factory/concepts-pipelines-activities.md) som finns i [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Här är en diagramvy över lösningen.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Två av pipelinen i den här fabriken innehåller [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skript som används för att partitionera och samla in data. När detta anges finns skripten i det [Azure Storage](https://azure.microsoft.com/services/storage/) konto som skapades under installationen. Deras plats är: maintenancesascript \\ \\ -Skriptets \\ \\ Hive \\ \\ (eller https://[ditt lösnings namn]. blob. Core. Windows. net/maintenancesascript).

Precis som med [Azure Stream Analytics](#azure-stream-analytics-1) frågor, har [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skripten implicit kunskap om det inkommande data formatet och måste ändras baserat på ditt data format.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – en [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktivitet som använder en [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) som kör ett [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skript för att partitionera data i [Azure Storage](https://azure.microsoft.com/services/storage/) under [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobbet.

[Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skriptet för den här partitionerings aktiviteten är ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Den här [pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller flera aktiviteter vars slut resultat är de poäng förutsägelser från det [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet som är associerat med den här lösnings mal len.

Följande aktiviteter ingår:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktivitet med en [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) som kör ett [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skript för att utföra agg regeringar och funktions teknik som krävs för [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet.
  [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -skriptet för den här partitionerings aktiviteten är ***PrepareMLInput. HQL***.
* [Kopierings](/previous-versions/azure/dn835035(v=azure.100)) aktivitet som flyttar resultatet från [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -aktiviteten till en enda [Azure Storage](https://azure.microsoft.com/services/storage/) -blob som används av [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) -aktiviteten.
* [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) -aktiviteten anropar [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet med resultat som finns i en enda [Azure Storage](https://azure.microsoft.com/services/storage/) -blob.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Den [här pipelinen](../../data-factory/concepts-pipelines-activities.md) innehåller en enskild aktivitet – [en kopierings](/previous-versions/azure/dn835035(v=azure.100)) aktivitet som flyttar resultatet av [Azure Machine Learning](#azure-machine-learning) experimentet från ***MLScoringPipeline*** till den [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som ingår i installationen av lösnings mal len.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) experimentet som används för den här lösnings mal len tillhandahåller återstående livs längd (RUL) för en flyg Plans motor. Experimentet är särskilt för den mängd data som förbrukas och kräver ändring eller ersättning som är särskilt för de data som hämtas.

## <a name="monitor-progress"></a>Övervaka förlopp
När data generatorn har startats börjar pipelinen att dehydratiseras och de olika komponenterna i lösningen börjar i åtgärd efter de kommandon som utfärdas av data fabriken. Det finns två sätt att övervaka pipelinen.

* Ett av de Stream Analytics jobben skriver rå inkommande data till Blob Storage. Om du klickar på Blob Storage komponent i din lösning från skärmen har du distribuerat lösningen och klickar sedan på öppna i den högra panelen, så tar du till [Azure Portal](https://portal.azure.com/). När du är där klickar du på blobbar. I nästa panel visas en lista över behållare. Klicka på **maintenancesadata**. I nästa panel finns mappen **rawData** . Inuti mappen rawData finns mappar med namn som timme = 17 och timme = 18. Förekomsten av dessa mappar anger att rå data genereras på din dator och lagras i Blob Storage. Du bör se CSV-filer med begränsade storlekar i MB i dessa mappar.
* Det sista steget i pipelinen är att skriva data (till exempel förutsägelser från maskin inlärning) till SQL Database. Du kan behöva vänta högst tre timmar innan data visas i SQL Database. Ett sätt att övervaka hur mycket data som är tillgängligt i SQL Database är genom [Azure Portal](https://portal.azure.com/). Leta upp SQL-databaser på den vänstra panelen :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: och klicka på den. Leta sedan reda på databasens **pmaintenancedb** och klicka på den. Klicka på hantera på nästa sida längst ned.
   
    ![Ikonen hantera](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Här kan du klicka på ny fråga och fråga efter antalet rader (till exempel välja Count (*) från PMResult). När databasen växer bör antalet rader i tabellen öka.

## <a name="power-bi-dashboard"></a>Power BI-instrumentpanel

Konfigurera en Power BI instrument panel för att visualisera Azure Stream Analytics data (frekvent sökväg) och resultat från Azure Machine Learning (kall sökväg).

### <a name="set-up-the-cold-path-dashboard"></a>Konfigurera instrument panelen för kall vägar
I pipeline för kall Sök vägs data är målet att hämta förutsägande RUL (återstående nyttjande livs längd) för varje flyg Plans motor när den har slutfört en flygning (cykel). Förutsägelse resultatet uppdateras var 3: e timme för att förutsäga flyg Plans motorer som har avslutat en flygning under de senaste 3 timmarna.

Power BI ansluter till en Azure SQL Database som data källa där förutsägelse resultatet lagras. 

Obs! 
1.    När du distribuerar din lösning visas en förutsägelse i databasen inom tre timmar. Pbix-filen som medföljde Generator-hämtningen innehåller vissa Dirigerings data så att du kan skapa Power BI instrument panelen direkt. 
2.    I det här steget är förutsättningen att ladda ned och installera den kostnads fria program varan [Power BI Skriv bordet](/power-bi/fundamentals/desktop-get-the-desktop).

Följande steg vägleder dig om hur du ansluter pbix-filen till den SQL Database som har varit i kraft vid den tidpunkt då lösnings distributionen innehåller data (till exempel förutsägelse resultat) för visualisering.

1. Hämta autentiseringsuppgifterna för databasen.
   
   Du behöver **databas server namn, databas namn, användar namn och lösen ord** innan du fortsätter till nästa steg. Här följer stegen för att få hjälp att hitta dem.
   
   * När **Azure SQL Database** i lösningens diagram blir grönt, klickar du på det och sedan på **Öppna**.
   * Du ser en ny webbflik/fönster som visar Azure Portal sidan. Klicka på **resurs grupper** på den vänstra panelen.
   * Välj den prenumeration som du använder för att distribuera lösningen och välj sedan **"YourSolutionName \_ ResourceGroup"**.
   * I den nya popup-panelen klickar du på  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: ikonen för att få åtkomst till din databas. Ditt databas namn är intill den här ikonen (till exempel **' pmaintenancedb '**) och **databas Server namnet** visas under egenskapen Server namn och bör se ut ungefär som **YourSolutionName.Database.Windows.net**.
   * Ditt **användar namn** och **lösen ord** för databasen är samma som det användar namn och lösen ord som tidigare registrerades under distributionen av lösningen.
2. Uppdatera data källan för den kall Sök vägs rapport filen med Power BI Desktop.
   
   * I mappen där du laddade ned och zippa Generator-filen dubbelklickar du på filen **PowerBI \\ PredictiveMaintenanceAerospace. pbix** . Om du ser ett varnings meddelande när du öppnar filen, ignorera dem. Klicka på **Redigera frågor** överst i filen.
     
     ![Redigera frågor](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Du ser två tabeller, **RemainingUsefulLife** och **PMResult**. Markera den första tabellen och klicka på ![ ikonen frågeinställningar ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) bredvid **"källa"** under **"tillämpade steg"** på den högra panelens **inställnings** panel. Ignorera eventuella varningsmeddelanden som visas.
   * Ersätt **"Server"** och **"Database"** i fönstret för popup-fönster med dina egna Server-och databas namn och klicka sedan på **OK**. För Server namn, se till att du anger port 1433 (**YourSolutionName.Database.Windows.net, 1433**). Lämna databas fältet som **pmaintenancedb**. Ignorera varnings meddelandena som visas på skärmen.
   * I nästa popup-fönster visas två alternativ i den vänstra rutan (**Windows** och **databas**). Klicka på **"databas"**, Fyll i ditt **"username"** och **"Password"** (användar namn och lösen ord som du angav när du först distribuerade lösningen och skapade en Azure SQL Database). I ***Välj vilken nivå som inställningarna ska tillämpas på*** kontrollerar du alternativet på databas nivå. Klicka sedan på **Anslut**.
   * Klicka på den andra tabellens **PMResult** klicka sedan på ![ navigerings ikonen ](./media/predictive-maintenance-technical-guide/icon-navigation.png) bredvid **"källa"** under **"tillämpade steg"** på den högra panelens **inställnings** panel och uppdatera Server-och databas namnen som i ovanstående steg och klicka på OK.
   * När du har gå tillbaka till föregående sida stänger du fönstret. Ett meddelande visas – klicka på **Använd**. Klicka slutligen på knappen **Spara** för att spara ändringarna. Din Power BI-fil har nu upprättat en anslutning till servern. Om visualiseringarna är tomma ser du till att ta bort valen i visualiseringarna för att visualisera alla data genom att klicka på rader gummi-ikonen i det övre högra hörnet av förklaringarna. Använd knappen Uppdatera för att visa nya data på visualiseringarna. Först ser du bara Dirigerings data i dina visualiseringar när data fabriken har schemalagts att uppdateras var tredje timme. Efter 3 timmar visas nya förutsägelser i dina visualiseringar när du uppdaterar data.
3. Valfritt Publicera instrument panelen för kall bana till [Power BI online](https://www.powerbi.com/). Det här steget behöver ett Power BI konto (eller ett arbets-eller skol konto).
   
   * Klicka på **publicera** och få sekunder senare visas ett fönster som visar publicering till Power BI lyckades! med en grön bock markering. Klicka på länken nedan "Open PredictiveMaintenanceAerospace. pbix in Power BI". Detaljerade instruktioner finns i [publicera från Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * För att skapa en ny instrument panel: Klicka på **+** tecknet bredvid avsnittet **instrument paneler** i det vänstra fönstret. Ange namnet "demonstration av förebyggande underhåll" för den nya instrument panelen.
   * När du har öppnat rapporten klickar du på ![ Fäst ikon ](./media/predictive-maintenance-technical-guide/icon-pin.png) för att fästa alla visualiseringar på instrument panelen. Detaljerade instruktioner finns i [fästa en panel på en Power BI instrument panel från en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Gå till instrument panels sidan och justera storleken och platsen för dina visualiseringar och redigera deras titlar. Detaljerade anvisningar om hur du redigerar dina paneler finns i [Redigera en panel – ändra storlek, flytta, byta namn, fästa, ta bort, Lägg till hyperlänk](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Här är ett exempel på en instrument panel med en del kall vägs visualiseringar fästa på den.  Beroende på hur länge du kör data generatorn kan dina siffror på visualiseringarna vara olika.
     <br/>
     ![Slutgiltig vy](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Om du vill schemalägga uppdatering av data håller du mus pekaren över **PredictiveMaintenanceAerospace** -datauppsättningen, klickar på ![ ikonen ellips ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) och väljer sedan **Schemalägg uppdatering**.
     <br/>
     > [!NOTE]
     > Om du ser ett varnings meddelande klickar du på **Redigera autentiseringsuppgifter** och kontrollerar att autentiseringsuppgifterna för databasen är desamma som de som beskrivs i steg 1.
     <br/>
     ![Uppdatera schema](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Expandera avsnittet **schema uppdatering** . Aktivera "Håll dina data aktuella".
     <br/>
   * Schemalägg uppdateringen utifrån dina behov. Mer information finns i [data uppdatering i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Konfigurera snabb Sök vägs instrument panel
Följande steg vägleder dig hur du visualiserar datautdata från Stream Analytics jobb som har genererats vid distribution av lösningen. Det krävs ett [Power BI online](https://www.powerbi.com/) -konto för att utföra följande steg. Om du inte har något konto kan du [skapa ett](https://powerbi.microsoft.com/pricing).

1. Lägg till Power BI utdata i Azure Stream Analytics (ASA).
   
   * Du måste följa anvisningarna i [Azure Stream Analytics & Power BI: en analys instrument panel för real tids visning av strömmande data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) för att konfigurera utdata för ditt Azure Stream Analytics-jobb som Power BI instrument panel.
   * ASA-frågan har tre utdata som är **aircraftmonitor**, **aircraftalert** och **flightsbyhour**. Du kan visa frågan genom att klicka på fliken fråga. Som motsvarar var och en av dessa tabeller måste du lägga till utdata till ASA. När du lägger till den första utdatan (**aircraftmonitor**) kontrollerar du att **utdata-aliaset**, **data uppsättningens namn** och **tabell namnet** är samma (**aircraftmonitor**). Upprepa stegen för att lägga till utdata för **aircraftalert** och **flightsbyhour**. När du har lagt till alla tre utgående tabeller och startat jobbet ASA, bör du få ett bekräftelse meddelande ("startar Stream Analytics jobbet maintenancesa02asapbi lyckades").
2. Logga in på [Power BI online](https://www.powerbi.com)
   
   * På den vänstra panelen data uppsättningar i min arbets yta visas ***data uppsättningen** _ namn _ * aircraftmonitor * *, **aircraftalert** och **flightsbyhour** . Det här är de strömmande data som du har överfört från Azure Stream Analytics i föregående steg. Data uppsättningen **flightsbyhour** får inte visas samtidigt som de andra två data uppsättningarna på grund av SQL-frågans natur. Det bör dock visas efter en timme.
   * Kontrol lera att ***visualiserings*** fönstret är öppet och visas på höger sida av skärmen.
3. När data flödar in i Power BI kan du börja visualisera strömmande data. Nedan visas ett exempel på en instrument panel med vissa frekventa visualiseringar av frekventa sökvägar. Du kan skapa andra instrument panels paneler baserat på lämpliga data uppsättningar. Beroende på hur länge du kör data generatorn kan dina siffror på visualiseringarna vara olika.

    ![Instrumentpanelsvy](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Här följer några steg för att skapa en av panelerna ovan – panelen "flottan av sensor 11 vs. Threshold 48,26":
   
   * Klicka på data uppsättning **aircraftmonitor** i avsnittet data uppsättningar för den vänstra panelen.
   * Klicka på **linje diagrams** ikonen.
   * Klicka på **bearbetad** i fönstret **fält** så att det visas under axel i fönstret **visualiseringar** .
   * Klicka på "S11" och "S11 \_ Alert" så att båda visas under "värden". Klicka på den lilla pilen bredvid **\_ aviseringen** **S11** och S11, ändra sum till Average.
   * Klicka på **Spara** högst upp och ge rapporten namnet "aircraftmonitor". Rapporten med namnet "aircraftmonitor" visas i avsnittet **rapporter** i **navigerings** fönstret till vänster.
   * Klicka på den **visuella ikonen fäst** i det övre högra hörnet i det här linje diagrammet. Ett fönster för att fästa på instrument panelen kan visas så att du kan välja en instrument panel. Välj demonstration av förebyggande underhåll och klicka sedan på Fäst.
   * Hovra över musen över den här panelen på instrument panelen, klicka på ikonen "redigera" i det övre högra hörnet för att ändra titeln till "flottan av sensor 11 vs. Threshold 48,26" och under rubrik till "genomsnitt över flottan över tid".

## <a name="delete-your-solution"></a>Ta bort din lösning
Se till att du stoppar data generatorn när du inte aktivt använder lösningen eftersom data generatorn kommer att ådra sig högre kostnader. Ta bort lösningen om du inte använder den. Om du tar bort lösningen tas alla komponenter som tillhandahålls i prenumerationen bort när du distribuerade lösningen. Om du vill ta bort lösningen klickar du på lösningens namn i den vänstra panelen i lösnings mal len och sedan på **ta bort**.

## <a name="cost-estimation-tools"></a>Verktyg för kostnads uppskattning
Följande två verktyg är tillgängliga för att hjälpa dig att bättre förstå de totala kostnaderna för att köra förebyggande underhåll för flyg lösnings mal len i din prenumeration:

* [Verktyget Microsoft Azure kostnads uppskattning (online)](https://azure.microsoft.com/pricing/calculator/)
* [Verktyget Microsoft Azure kostnads uppskattning (skriv bord)](https://www.microsoft.com/download/details.aspx?id=43376)