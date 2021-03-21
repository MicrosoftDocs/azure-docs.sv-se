---
title: Använd Azure Cosmos DB ändra feed för att visualisera data analyser i real tid
description: Den här artikeln beskriver hur du kan använda ändrings flöden av ett detalj handels företag för att förstå användar mönster, utföra data analys och visualisering i real tid
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: e7b75c71d64054e38630677ecd38f8e3e2483c12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606342"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Använd Azure Cosmos DB ändra feed för att visualisera data analyser i real tid
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ändra feed är en mekanism för att få ett kontinuerligt och stegvist flöde av poster från en Azure Cosmos-behållare när dessa poster skapas eller ändras. Ändra feed-stöd genom att lyssna på behållare för eventuella ändringar. Funktionen returnerar sedan den sorterade listan över dokument som ändrats i den ordning de ändrades. Mer information om ändrings flöde finns i [arbeta med artikeln om ändrings flöde](change-feed.md) . 

Den här artikeln beskriver hur du kan använda ändrings flöden av ett e-handelsföretag för att förstå användar mönster, utföra data analys och visualisering i real tid. Du kommer att analysera händelser, till exempel en användare som visar ett objekt, lägga till ett objekt i sin varukorg eller köpa ett objekt. När en av dessa händelser inträffar skapas en ny post och de loggar för ändrings flöden som registreras. Ändra feed utlöser sedan en serie steg som resulterar i visualisering av mått som analyserar företagets prestanda och aktivitet. Exempel mått som du kan visualisera inkluderar intäkter, unika plats besökare, de mest populära objekten och genomsnitts priset för de artiklar som visas jämfört med den som har köpts till en kundvagn. Dessa exempel mått kan hjälpa ett e-handelsföretag att utvärdera sin plats popularitet, utveckla reklam-och prissättnings strategier och fatta beslut om vilka lager som ska investera i.

Vi vill titta på en video om lösningen innan du börjar, se följande videoklipp:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Lösningskomponenter
Följande diagram representerar det data flöde och de komponenter som ingår i lösningen:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Visuellt projekt" border="false":::
 
1. **Generering av data:** Data Simulator används för att generera detalj handels data som representerar händelser, till exempel en användare som visar ett objekt, lägger till ett objekt i sin varukorg och köper ett objekt. Du kan generera stora uppsättningar exempel data med hjälp av data generatorn. De genererade exempel data innehåller dokument i följande format:
   
    ```json
    {
      "CartID": 2486,
      "Action": "Viewed",
      "Item": "Women's Denim Jacket",
      "Price": 31.99
    }
    ```

2. **Cosmos DB:** Genererade data lagras i en Azure Cosmos-behållare.  

3. **Ändra feed:** Ändrings flödet kommer att lyssna efter ändringar i Azure Cosmos-behållaren. Varje gång ett nytt dokument läggs till i samlingen (det vill säga när en händelse inträffar som en användare visar ett objekt, lägger till ett objekt i sin varukorg eller köper ett objekt), utlöser ändrings flödet en [Azure-funktion](../azure-functions/functions-overview.md).  

4. **Azure-funktion:** Azure-funktionen bearbetar nya data och skickar dem till en [Azure Event Hub](../event-hubs/event-hubs-about.md).  

5. **Händelsehubben:** Azure Event Hub lagrar dessa händelser och skickar dem till [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) för att utföra ytterligare analyser.  

6. **Azure Stream Analytics:** Azure Stream Analytics definierar frågor för att bearbeta händelserna och utföra data analys i real tid. Dessa data skickas sedan till [Microsoft Power BI](/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI används för att visualisera data som skickas av Azure Stream Analytics. Du kan bygga en instrument panel för att se hur måtten ändras i real tid.  

## <a name="prerequisites"></a>Förutsättningar

* Microsoft .NET Framework 4.7.1 eller senare

* Microsoft .NET Core 2,1 (eller högre)

* Visual Studio med Universell Windows-plattform utveckling, .NET Desktop Development och ASP.NET och arbets belastningar för webb utveckling

* Microsoft Azure prenumeration

* Microsoft Power BI-konto

* Ladda ned [Azure Cosmos DB ändra feed Lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) från GitHub. 

## <a name="create-azure-resources"></a>Skapa Azure-resurser 

Skapa Azure-resurserna – Azure Cosmos DB, lagrings konto, Händelsehubben, Stream Analytics som krävs av lösningen. Du kommer att distribuera dessa resurser via en Azure Resource Manager-mall. Använd följande steg för att distribuera dessa resurser: 

1. Ange körnings principen för Windows PowerShell till **obegränsad**. Det gör du genom att öppna **Windows PowerShell som administratör** och köra följande kommandon:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Från GitHub-lagringsplatsen som du laddade ned i föregående steg navigerar du till mappen **Azure Resource Manager** och öppnar filen **parameters.jsi** filen.  

3. Ange värden för cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name, parametrar som anges i **parameters.jsi** filen. Du måste använda de namn du ger till var och en av dina resurser senare.  

4. Från **Windows PowerShell** navigerar du till mappen **Azure Resource Manager** och kör följande kommando:

   ```powershell
   .\deploy.ps1
   ```
5. När du uppmanas till det anger du ditt Azure **-prenumerations-ID**, **changefeedlab** för resurs grupps namnet och **Run1** för distributions namnet. När resurserna börjar distribuera kan det ta upp till 10 minuter innan det har slutförts.

## <a name="create-a-database-and-the-collection"></a>Skapa en databas och samlingen

Nu ska du skapa en samling för att lagra händelser för e-handelsplats. När en användare visar ett objekt, lägger till ett objekt i sin varukorg eller köper ett objekt, får samlingen en post som innehåller åtgärden ("visad", "tillagt" eller "köpt"), namnet på objektet som ingår, priset för objektet som ingår och ID-numret för den berörda användar vagnen.

1. Gå till [Azure Portal](https://portal.azure.com/) och hitta det **Azure Cosmos DBS konto** som har skapats av mall distributionen.  

2. I fönstret **datautforskaren** väljer du **ny samling** och fyller i formuläret med följande information:  

   * I fältet **databas-ID** väljer du **Skapa ny** och anger sedan **changefeedlabdatabase**. Lämna rutan **etablera databasens data flöde** omarkerad.  
   * I fältet **samlings** -ID anger du **changefeedlabcollection**.  
   * För fältet **partitionsnyckel** anger du **/item**. Detta är Skift läges känsligt, så se till att du anger rätt.  
   * I **data flödes** fältet anger du **10000**.  
   * Välj knappen **OK**.  

3. Skapa sedan en annan samling med namnet **lån** för bearbetning av ändrings flöden. Låne insamlingen samordnar bearbetning av ändrings flödet över flera arbetare. En separat samling används för att lagra lån med ett lån per partition.  

4. Gå tillbaka till **datautforskaren** fönstret och välj **ny samling** och fyll i formuläret med följande information:

   * I fältet **databas-ID** väljer du **Använd befintlig** och anger sedan **changefeedlabdatabase**.  
   * I fältet **samlings-ID** anger du **lån**.  
   * För **lagrings kapacitet** väljer du **fast**.  
   * Lämna fältet **data flöde** inställt på standardvärdet.  
   * Välj knappen **OK**.

## <a name="get-the-connection-string-and-keys"></a>Hämta anslutnings strängen och nycklarna

### <a name="get-the-azure-cosmos-db-connection-string"></a>Hämta Azure Cosmos DB anslutnings sträng

1. Gå till [Azure Portal](https://portal.azure.com/) och hitta det **Azure Cosmos DBS konto** som har skapats av mall distributionen.  

2. Navigera till rutan **nycklar** , kopiera den primära anslutnings strängen och kopiera den till ett anteckningar eller ett annat dokument som du kommer att ha åtkomst till i hela labbet. Du bör märka det **Cosmos DB anslutnings strängen**. Du måste kopiera strängen till koden senare, så ta en anteckning och komma ihåg var du lagrar den.

### <a name="get-the-storage-account-key-and-connection-string"></a>Hämta lagrings konto nyckeln och anslutnings strängen

Azure Storage-konton gör det möjligt för användare att lagra data. I det här labbet ska du använda ett lagrings konto för att lagra data som används av Azure-funktionen. Azure-funktionen utlöses när en ändring görs i samlingen.

1. Gå tillbaka till resurs gruppen och öppna lagrings kontot som du skapade tidigare  

2. Välj **åtkomst nycklar** på menyn till vänster.  

3. Kopiera värdena under **nyckel 1** till ett anteckningar eller ett annat dokument som du kommer att ha åtkomst till i labbet. Du bör märka **nyckeln** som **lagrings nyckel** och **anslutnings strängen** som **lagrings anslutnings sträng**. Du måste kopiera dessa strängar till din kod senare, så ta en anteckning och kom ihåg var du lagrar dem.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Hämta anslutnings strängen för Event Hub-namnområdet

En Azure Event Hub tar emot händelse data, butiker, processer och vidarebefordrar data. I det här labbet får Azure Event Hub ett dokument varje gång en ny händelse inträffar (d.v.s. ett objekt visas av en användare, läggs till i en användares varukorg eller köps av en användare) och vidarebefordrar sedan dokumentet till Azure Stream Analytics.

1. Gå tillbaka till din resurs grupp och öppna **Event Hub-namnområdet** som du skapade och namngivna i prelab.  

2. Välj **principer för delad åtkomst** på menyn på vänster sida.  

3. Välj **RootManageSharedAccessKey**. Kopiera **anslutnings strängen – primär nyckel** till ett anteckningar eller ett annat dokument som du kommer att ha åtkomst till i labbet. Du bör märka den till anslutnings strängen för **Event Hub-namnområdet** . Du måste kopiera strängen till koden senare, så ta en anteckning och komma ihåg var du lagrar den.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Konfigurera Azure-funktionen för att läsa ändrings flödet

När ett nytt dokument skapas, eller om ett aktuellt dokument ändras i en Cosmos-behållare, lägger ändrings flödet automatiskt till det ändrade dokumentet i historiken över samlings ändringar. Nu ska du skapa och köra en Azure-funktion som bearbetar ändrings flödet. När ett dokument skapas eller ändras i samlingen som du skapade, utlöses Azure-funktionen av Change-feeden. Sedan skickar Azure-funktionen det ändrade dokumentet till Händelsehubben.

1. Gå tillbaka till den lagrings plats som du har klonat på enheten.  

2. Högerklicka på filen med namnet **ChangeFeedLabSolution. SLN** och välj **Öppna med Visual Studio**.  

3. Navigera till **local.settings.js** i Visual Studio. Använd sedan de värden du registrerade tidigare för att fylla i de tomma.  

4. Gå till **ChangeFeedProcessor. cs**. I parametrarna för **Run** -funktionen utför du följande åtgärder:  

   * Ersätt texten **ditt samlings namn här** med namnet på din samling. Om du har följt tidigare instruktioner är namnet på din samling changefeedlabcollection.  
   * Ersätt texten som **ditt leasing avtals samlings namn här** med namnet på din låne samling. Om du har följt tidigare instruktioner är namnet på din leasing uppsättning **lån**.  
   * Överst i Visual Studio ser du till att rutan Start projekt till vänster om den gröna pilen står **ChangeFeedFunction**.  
   * Välj **Starta**  överst på sidan för att köra programmet  
   * Du kan bekräfta att funktionen körs när-konsol programmet säger att "jobb värden har startats".

## <a name="insert-data-into-azure-cosmos-db"></a>Infoga data i Azure Cosmos DB 

Om du vill se hur ändrings flödet bearbetar nya åtgärder på en e-handelsplats, måste du simulera data som representerar användare som visar objekt från produkt katalogen, lägga till dessa objekt i deras vagnar och köpa artiklarna i deras kundvagn. Dessa data är godtyckliga och kan användas för att replikera vilka data på en ecommerce plats som skulle se ut.

1. Gå tillbaka till lagrings platsen i Utforskaren och högerklicka på **ChangeFeedFunction. SLN** för att öppna den igen i ett nytt Visual Studio-fönster.  

2. Navigera till **App.config** -filen. I `<appSettings>` blocket lägger du till slut punkten och unik **primär nyckel** för ditt Azure Cosmos DB-konto som du hämtade tidigare.  

3. Lägg till i **samlings** -och **databas** namnen. (Dessa namn ska vara **changefeedlabcollection** och **changefeedlabdatabase** om du inte väljer att namnge ditt eget namn.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="Uppdatera anslutnings strängar":::
 
4. Spara ändringarna på alla filer som har redigerats.  

5. Överst i Visual Studio ser du till att rutan **Start projekt** till vänster om den gröna pilen står **DataGenerator**. Välj sedan **Starta** överst på sidan för att köra programmet.  
 
6. Vänta tills programmet körs. Stjärnorna innebär att data kommer in! Se till att programmet körs – det är viktigt att många data samlas in.  

7. Om du går till [Azure Portal](https://portal.azure.com/) och sedan till Cosmos DBS kontot i resurs gruppen, kommer du till **datautforskaren** att se de slumpmässiga data som importer ATS i din **changefeedlabcollection** .
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="Data som genereras i portalen":::

## <a name="set-up-a-stream-analytics-job"></a>Konfigurera ett Stream Analytics-jobb

Azure Stream Analytics är en helt hanterad moln tjänst för bearbetning av strömmande data i real tid. I det här labbet använder du Stream Analytics för att bearbeta nya händelser från Händelsehubben (d.v.s. När ett objekt visas, läggs till i en varukorg eller köpt), införliva dessa händelser i real tids data analys och skicka dem till Power BI för visualisering.

1. Från [Azure Portal](https://portal.azure.com/)navigerar du till din resurs grupp och sedan till **streamjob1** (Stream Analytics-jobbet som du skapade i prelab).  

2. Välj **indata** som visas nedan.  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Skapa inmatade":::

3. Välj **+ Lägg till strömmande data**. Välj sedan **Event Hub** i den nedrullningsbara menyn.  

4. Fyll i det nya indata-formuläret med följande information:

   * I fältet **inmatat** alias anger du **ininformation**.  
   * Välj alternativet för **Välj Event Hub från dina prenumerationer**.  
   * Ange fältet **prenumeration** för din prenumeration.  
   * I fältet **namn område för händelsehubben** anger du namnet på den Event Hub-namnrymd som du skapade under prelab.  
   * I fältet **namn på händelsehubben** väljer du alternativet för att **använda befintlig** och sedan **Event-hub1** på den nedrullningsbara menyn.  
   * Lämna fältet princip namn för **Event Hub** inställt på standardvärdet.  
   * Lämna **format för händelse serialisering** som **JSON**.  
   * Lämna **encoding-fältet** inställt på **UTF-8**.  
   * Lämna fältet **händelse komprimerings typ** inställt på **ingen**.  
   * Välj knappen **Spara**.

5. Gå tillbaka till sidan Stream Analytics-jobb och välj **utdata**.  

6. Välj **+ Lägg till**. Välj **Power BI** på den nedrullningsbara menyn.  

7. Utför följande åtgärder för att skapa en ny Power BI utdata för att visualisera genomsnitts priset:

   * I fältet **utdata-alias** anger du **averagePriceOutput**.  
   * Lämna fältet **grupp arbets yta** inställt på att **tillåta anslutning till att läsa in arbets ytor**.  
   * I fältet **data uppsättnings namn** anger du **averagePrice**.  
   * I fältet **tabell namn** anger du **averagePrice**.  
   * Välj knappen **auktorisera** och följ sedan anvisningarna för att auktorisera anslutningen till Power BI.  
   * Välj knappen **Spara**.  

8. Gå sedan tillbaka till **streamjob1** och välj **Redigera fråga**.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Redigera fråga":::
 
9. Klistra in följande fråga i frågefönstret. Den **genomsnittliga pris** frågan beräknar genomsnitts priset för alla objekt som visas av användarna, genomsnitts priset för alla objekt som läggs till i användarnas vagnar och det genomsnittliga priset för alla objekt som har köpts av användare. Det här måttet kan hjälpa e-handelsföretag att avgöra vilka priser som säljer artiklar till och vilka lager som ska investera i. Om till exempel genomsnitts priset för visade objekt är mycket högre än genomsnitts priset för de köpta objekten kan ett företag välja att lägga till mindre dyra artiklar i lagret.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Välj sedan **Spara** i det övre vänstra hörnet.  

11. Gå tillbaka till **streamjob1** och välj knappen **Start** överst på sidan. Azure Stream Analytics kan ta några minuter att starta, men om du vill kan du se att den ändras från "starta" till "körs".

## <a name="connect-to-power-bi"></a>Ansluta till Power BI

Power BI är en uppsättning verktyg för företagsanalys för att analysera och dela information. Det är ett bra exempel på hur du kan visualisera analyserade data strategiskt.

1. Logga in på Power BI och navigera till **min arbets yta** genom att öppna menyn till vänster på sidan.  

2. Välj **+ skapa** i det övre högra hörnet och välj sedan **instrument panel** för att skapa en instrument panel.  

3. Välj **+ Lägg till panelen** i det övre högra hörnet.  

4. Välj **anpassade strömmande data** och välj sedan knappen **Nästa** .  
 
5. Välj **averagePrice** från **dina data uppsättningar** och välj sedan **Nästa**.  

6. I fältet **typ av visualisering** väljer du **grupperat liggande diagram** på den nedrullningsbara menyn. Lägg till åtgärd under **axel**. Hoppa över **förklaring** utan att lägga till något. Lägg sedan till **AVG** i nästa avsnitt med namnet **Value**. Välj **Nästa**, sedan titel på diagrammet och välj **Använd**. Du bör se ett nytt diagram på instrument panelen!  

7. Om du nu vill visualisera fler mått kan du gå tillbaka till **streamjob1** och skapa tre fler utdata med följande fält.

   a. **Output-alias:** IncomingRevenueOutput, data uppsättnings namn: IncomingRevenue, tabell namn: incomingRevenue  
   b. **Output-alias:** Top5Output, data uppsättnings namn: top5, tabell namn: top5  
   c. **Output-alias:** UniqueVisitorCountOutput, data uppsättnings namn: UniqueVisitorCount, tabell namn: uniqueVisitorCount

   Välj sedan **Redigera fråga** och klistra in följande frågor **ovanför** den som du redan skrev.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   Den 5 främsta frågan beräknar de översta 5 objekten, rankade med antalet gånger som de har köpts. Det här måttet kan hjälpa nät handels företag att utvärdera vilka objekt som är mest populära och kan påverka företagets annonsering, prissättning och inventerings beslut.

   Frågan intäkter beräknar intäkter genom att summera priserna för alla objekt som har köpts per minut. Det här måttet kan hjälpa e-handelsföretag att utvärdera sina finansiella prestanda och även förstå vilka tider på dagen som bidrar till de flesta intäkter. Detta kan påverka den övergripande företags strategin, marknadsföring särskilt.

   Frågan unika besökare beräknar hur många unika besökare som finns på webbplatsen var femte sekund genom att identifiera unika shopping-ID: n. Det här måttet kan hjälpa e-handelsföretag att utvärdera sin webbplats aktivitet och utarbeta hur man kan köpa fler kunder.

8. Nu kan du lägga till paneler för dessa data uppsättningar också.

   * För de 5 främsta är det klokt att göra ett grupperat stående stapeldiagram med objekten som axlar och antalet som värdet.  
   * För intäkter skulle det vara klokt att göra ett linje diagram med tiden som axeln och summan av priserna som värde. Tidsfönstret som ska visas bör vara det största möjliga för att leverera så mycket information som möjligt.  
   * För unika besökare skulle det vara klokt att göra en kort visualisering med antalet unika besökare som värde.

   Så här ser ett exempel på en instrument panel ut med följande diagram:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="Skärm bild som visar ett exempel på en instrument panel med diagram som heter genomsnitts pris för objekt per åtgärd, unika besökare, intäkter och topp 5 objekt som köpts.":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Valfritt: visualisera med en E-handelswebbplats

Nu kommer du att se hur du kan använda det nya data analys verktyget för att ansluta till en riktig e-handelswebbplats. För att bygga e-Handelswebbplatsen använder du en Azure Cosmos-databas för att lagra listan över produkt kategorier (kvinno, män, unisex), produkt katalogen och en lista över de populäraste objekten.

1. Gå tillbaka till [Azure Portal](https://portal.azure.com/)och sedan till ditt **Cosmos DB-konto** och **datautforskaren**.  

   Lägg till två samlingar under **changefeedlabdatabase**-  -  **produkter** och- **Kategorier** med fast lagrings kapacitet.

   Lägg till en annan samling under **changefeedlabdatabase** med namnet **topItems** och **/item** som partitionsnyckel.

2. Välj samlingen **topItems** och under **skala och inställningar** anges **Time to Live** till **30 sekunder** så att topItems uppdateras var 30: e sekund.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Time to live":::

3. För att fylla i **topItems** -samlingen med de mest köpta objekten går du tillbaka till **streamjob1** och lägger till en ny **utmatning**. Välj **Cosmos DB**.

4. Fyll i de obligatoriska fälten enligt bilden nedan.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Cosmos-utdata":::
 
5. Om du har lagt till den valfria översta 5 frågan i föregående del av labbet fortsätter du till del 5a. Fortsätt annars till del 5b.

   5a. I **streamjob1** väljer du **Redigera fråga** och klistrar in följande fråga i din Azure Stream Analytics Frågeredigeraren under den översta 5 frågan, men ovanför resten av frågorna.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. I **streamjob1** väljer du **Redigera fråga** och klistrar in följande fråga i Azure Stream Analytics Frågeredigeraren ovanför alla andra frågor.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Öppna **EcommerceWebApp. SLN** och navigera till **Web.config** -filen i **Solution Explorer**.  

7. I `<appSettings>` blocket lägger du till den **URI** och **primär nyckel** som du sparade tidigare där det står **din URI här** och **din primär nyckel här**. Lägg sedan till i ditt **databas namn** och **samlings namn** enligt vad som anges. (Dessa namn ska vara **changefeedlabdatabase** och **changefeedlabcollection** om du inte har valt att namnge ditt eget namn.)

   Fyll i samlings **namn**, **Kategorier samlings namn** och **samlings namn för främsta objekt** enligt vad som anges. (Dessa namn bör vara **produkter, kategorier och topItems** om du inte har valt att namnge ditt eget namn.)  

8. Navigera till och öppna **mappen utcheckning** i **EcommerceWebApp. SLN.** Öppna sedan **Web.config** -filen i den mappen.  

9. I `<appSettings>` blocket lägger du till den **URI** och **primär nyckel** som du sparade tidigare, där det anges. Lägg sedan till i ditt **databas namn** och **samlings namn** enligt vad som anges. (Dessa namn ska vara **changefeedlabdatabase** och **changefeedlabcollection** om du inte har valt att namnge ditt eget namn.)  

10. Tryck på **Start** överst på sidan för att köra programmet.  

11. Nu kan du spela på e-Handelswebbplatsen. När du visar ett objekt, lägger till ett objekt i din varukorg, ändrar antalet för ett objekt i din varukorg eller köper ett objekt, kommer dessa händelser att skickas genom Cosmos DB ändra feed till Event Hub, ASA och sedan Power BI. Vi rekommenderar att du fortsätter att köra DataGenerator för att generera betydande webb trafik data och ger en realistisk uppsättning "frekventa produkter" på e-Handelswebbplatsen.

## <a name="delete-the-resources"></a>Ta bort resurserna

Om du vill ta bort de resurser som du skapade under det här labbet går du till resurs gruppen på [Azure Portal](https://portal.azure.com/)och väljer sedan **ta bort resurs grupp** på menyn längst upp på sidan och följer instruktionerna som visas.

## <a name="next-steps"></a>Nästa steg 
  
* Mer information om ändrings flöden finns [i arbeta med stöd för ändrings flöden i Azure Cosmos DB](change-feed.md)