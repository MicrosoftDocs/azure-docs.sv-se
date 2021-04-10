---
title: Analys av sociala medier med Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Stream Analytics för analys av sociala medier med Twitter-klientens API. Steg-för-steg-anvisningar från Event-generering till data på en Live-instrumentpanel.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e2369e8f4349a3b4a83c1729c6414d319dd8419e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589675"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Analys av sociala medier med Azure Stream Analytics

Den här artikeln lär dig hur du skapar en sentiment analys lösning för sociala medier genom att ta real tids händelser i real tid till Azure Event Hubs. Du skriver en Azure Stream Analytics fråga för att analysera data och lagra resultatet för senare användning eller skapa en [Power BI](https://powerbi.com/) instrument panel för att ge insikter i real tid.

Med hjälp av verktyg för sociala media Analytics kan organisationer förstå trender i ämnen. Avsnitt om trender är ämnen och Attitudes som har stora mängder inlägg på sociala medier. Sentiment-analys, som också kallas för att avhjälpa *utvinning*, använder verktyg för sociala medier för att fastställa Attitudes till en produkt eller idé. 

Real tids analys av Twitter-trender är ett bra exempel på ett analys verktyg eftersom prenumerations modellen hashtagg gör att du kan lyssna på vissa nyckelord (hashtagg) och utveckla sentiment analys av flödet.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenario: sentiment analys av sociala medier i real tid

Ett företag som har en nyhets medie webbplats är intresse rad av att få en fördel av sina konkurrenter genom att ha plats innehåll som är direkt relevant för läsarna. Företaget använder analys av sociala medier för ämnen som är relevanta för läsare genom sentiment analys av Twitter-data i real tid.

För att kunna identifiera trender i ämnen i real tid på Twitter behöver företaget real tids analys av tweet-volymen och sentiment för viktiga ämnen.

## <a name="prerequisites"></a>Förutsättningar

I den här instruktions guiden använder du ett klient program som ansluter till Twitter och letar efter Tweets som har vissa hashtagg (som du kan ställa in). Om du vill köra programmet och analysera tweets med Azure streaming Analytics måste du ha följande:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Ett [Twitter](https://twitter.com) -konto.

* TwitterClientCore-programmet som läser Twitter-flödet. Hämta [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)för att hämta det här programmet.

* Installera [.net Core CLI](/dotnet/core/tools/?tabs=netcore2x) version 2.1.0.

Nedan visas den lösnings arkitektur som du kommer att implementera.

   ![Ett diagram som visar olika delar av tjänster och program som används för att bygga lösningen.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Lösnings diagram")

## <a name="create-an-event-hub-for-streaming-input"></a>Skapa en Event Hub för strömmande data

Exempel programmet genererar händelser och skickar dem till en Azure Event Hub. Azure Event Hubs är den bästa metoden för händelse inmatning för Stream Analytics. Mer information finns i [Azure Event Hubs-dokumentationen](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Skapa ett namn område för händelse hubb och händelsehubben
I det här avsnittet skapar du ett namn område för Event Hub och lägger till en händelsehubben till det namn området. Namn områden för Event Hub används för att logiskt gruppera relaterade händelse buss instanser. 

1. Logga in på Azure Portal och välj **skapa en resurs**. Dra. Sök efter **Event Hubs** och välj **skapa**.

2. På sidan **skapa namn område** anger du ett namn på namn området. Du kan använda namn områdets namn, men namnet måste vara giltigt för en URL och det måste vara unikt i Azure. 
    
3. Välj en pris nivå och en prenumeration och skapa eller Välj en resurs grupp. Välj sedan en plats och välj **skapa**. 
 
4. När namn rymden har distribuerats går du till resurs gruppen och letar upp namn området för händelsehubben i listan med Azure-resurser. 

5. I det nya namn området väljer du **+ &nbsp; händelsehubben**. 

6. Namnge den nya händelsehubben *socialtwitter-händelsehubbnamnområde*. Du kan använda ett annat namn. Om du gör det, gör du en anteckning om det, eftersom du behöver namnet senare. Du behöver inte ange andra alternativ för händelsehubben.
 
7. Välj **Skapa**.

### <a name="grant-access-to-the-event-hub"></a>Bevilja åtkomst till händelsehubben

Innan en process kan skicka data till en händelsehubben, behöver händelsehubben en princip som tillåter åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1.  I navigerings fältet på vänster sida av Event Hub-namnområdet väljer du **Event Hubs** som finns i avsnittet **entiteter** . Välj sedan den händelsehubben som du nyss skapade.

2.  I navigerings fältet på vänster sida väljer du principer för **delad åtkomst** som finns under **Inställningar**.

    >[!NOTE]
    >Det finns ett alternativ för delade åtkomst principer under för Event Hub-namnområdet och för händelsehubben. Se till att du arbetar i kontexten för händelsehubben, inte i namn området för den totala händelsehubben.

3.  På sidan åtkomst princip väljer du **+ Lägg till**. Ange sedan *socialtwitter-Access* som **princip namn** och markera kryss rutan **Hantera** .
 
4.  Välj **Skapa**.

5.  När principen har distribuerats väljer du principen i listan över principer för delad åtkomst.

6.  Leta upp rutan med etiketten **anslutnings sträng primär nyckel** och välj knappen Kopiera bredvid anslutnings strängen.
 
7.  Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutnings strängen för nästa avsnitt när du har gjort små ändringar.

   Anslutnings strängen ser ut så här:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observera att anslutnings strängen innehåller flera nyckel/värde-par, separerade med semikolon: `Endpoint` , `SharedAccessKeyName` , `SharedAccessKey` , och `EntityPath` .  

   > [!NOTE]
   > Av säkerhets nivå har delar av anslutnings strängen i exemplet tagits bort.

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurera och starta Twitter-klient programmet

Klient programmet hämtar tweet-händelser direkt från Twitter. För att göra det måste du ha behörighet att anropa Twitter streaming-API: erna. Om du vill konfigurera den behörigheten skapar du ett program i Twitter, som genererar unika autentiseringsuppgifter (till exempel en OAuth-token). Sedan kan du konfigurera klient programmet att använda dessa autentiseringsuppgifter när det gör API-anrop. 

### <a name="create-a-twitter-application"></a>Skapa ett Twitter-program
Om du inte redan har ett Twitter-program som du kan använda för den här instruktions guiden kan du skapa ett. Du måste redan ha ett Twitter-konto.

> [!NOTE]
> Den exakta processen i Twitter för att skapa ett program och hämta nycklar, hemligheter och token kan ändras. Om dessa anvisningar inte matchar det du ser på Twitter-webbplatsen, se dokumentationen om Twitter-utvecklare.

1. I en webbläsare går du till [Twitter för utvecklare](https://developer.twitter.com/en/apps), skapar ett Developer-konto och väljer **skapa en app**. Du kan se ett meddelande om att du måste använda för ett Twitter-konto för utvecklare. Det är kostnads fritt att göra det och när ditt program har godkänts bör du se ett bekräftelse meddelande. Det kan ta flera dagar att godkännas för ett Developer-konto.

   ![Skärm bild som visar knappen Skapa en app.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Information om Twitter-program")

2. På sidan **Skapa ett program** anger du information om den nya appen. Välj sedan **Create your Twitter application** (Skapa ditt Twitter-program).

   ![Skärm bild som visar fönstret information om appar där du kan ange värden för din app.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Information om Twitter-program")

3. På sidan program väljer du fliken **nycklar och tokens** och kopierar värdena för klient-API- **nyckel** och **hemlig nyckel för konsument-API**. Välj också **skapa** under **åtkomst-token och åtkomst till token Secret** för att generera åtkomsttoken. Kopiera värdena för **åtkomsttoken** och **åtkomsttokenhemligheten**.

   Spara de värden som du hämtade för Twitter-programmet. Du behöver värdena senare.

> [!NOTE]
> Nycklarna och hemligheterna för Twitter-programmet ger åtkomst till ditt Twitter-konto. Behandla denna information som känslig, på samma sätt som du gör ditt Twitter-lösenord. Bädda till exempel inte in den här informationen i ett program som du ger andra. 

### <a name="configure-the-client-application"></a>Konfigurera klient programmet

Vi har skapat ett klient program som ansluter till Twitter-data med [Twitter: s strömnings-API: er](https://dev.twitter.com/streaming/overview) för att samla in tweet-händelser om en specifik uppsättning ämnen.

Innan programmet körs krävs viss information från dig, t. ex. Twitter-nycklar och Event Hub-anslutningssträngen.

1. Kontrol lera att du har laddat ned [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) -programmet enligt anvisningarna.

2. Använd en text redigerare för att öppna filen *App.config* . Gör följande ändringar i `<appSettings>` elementet:

   * Ange `oauth_consumer_key` till Twitter-konsument nyckeln (API-nyckel). 
   * Ange `oauth_consumer_secret` som Twitter-konsument hemlighet (API hemlig nyckel).
   * Ange `oauth_token` till Twitter-åtkomsttoken.
   * Ange `oauth_token_secret` till hemligheten för Twitter-åtkomsttoken.
   * Ange `EventHubNameConnectionString` till anslutnings strängen.
   * Ange `EventHubName` till Event Hub-namnet (det är värdet för enhetens sökväg).

3. Öppna kommando raden och navigera till den katalog där din TwitterClientCore-app finns. Använd kommandot `dotnet build` för att bygga projektet. Använd sedan kommandot `dotnet run` för att köra appen. Appen skickar tweets till Händelsehubben.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu när tweet-händelser strömmas i real tid från Twitter, kan du konfigurera ett Stream Analytics jobb för att analysera händelserna i real tid.

1. I Azure Portal navigerar du till din resurs grupp och väljer **+ Lägg till**. Sök sedan efter **Stream Analytics jobb** och välj **skapa**.

2. Namnge jobbet `socialtwitter-sa-job` och ange en prenumeration, en resurs grupp och en plats.

    Det är en bra idé att placera jobbet och händelsehubben i samma region för bästa prestanda och så att du inte betalar för att överföra data mellan regioner.

3. Välj **Skapa**. Gå sedan till jobbet när distributionen är färdig.

## <a name="specify-the-job-input"></a>Ange jobb ingången

1. I ditt Stream Analytics jobb väljer du **indata** från den vänstra menyn under **jobb sto pol Ogin**.

2. Välj **+ &nbsp; Lägg till Stream-indata**-händelsehubben  >  . Fyll i det **nya indata** -formuläret med följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias| *TwitterStream* | Ange ett alias för indatamängden. |
   |Prenumeration  | \<Your subscription\> |  Välj den Azure-prenumeration som du vill använda. |
   |Namnområde för händelsehubb | *ASA-Twitter-eventhub* |
   |Namn på händelsehubb | *socialtwitter – händelsehubbnamnområde* | Välj *Använd befintlig*. Välj sedan den Händelsehubben som du skapade.|
   |Händelse komprimerings typ| GZip | Data komprimerings typen.|

   Lämna kvar standardvärdena och välj **Spara**.

## <a name="specify-the-job-query"></a>Ange jobb frågan

Stream Analytics stöder en enkel, deklarativ frågeplan som beskriver transformeringar. Mer information om språket finns i [språk referens för Azure Stream Analytics frågor](/stream-analytics-query/stream-analytics-query-language-reference). Den här instruktions guiden hjälper dig att skapa och testa flera frågor över Twitter-data.

Om du vill jämföra antalet omnämnanden mellan ämnen kan du använda ett [rullande-fönster](/stream-analytics-query/tumbling-window-azure-stream-analytics) för att få antalet omnämnanden efter var femte sekund.

1. I din jobb **Översikt** väljer du **Redigera fråga** nära det övre högra hörnet i rutan fråga. Azure visar indata och utdata som har kon figurer ATS för jobbet och gör att du kan skapa en fråga för att transformera indataströmmen när den skickas till utdata.

2. Ändra frågan i Frågeredigeraren till följande:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Händelse data från meddelandena ska visas i **förhands gransknings** fönstret under frågan. Se till att **vyn** är inställd på **JSON**. Om du inte ser några data kontrollerar du att data generatorn skickar händelser till händelsehubben och att du har valt **gzip** som komprimerings typ för indata.

4. Välj **Testa fråga** och observera resultatet i fönstret **test resultat** under frågan.

5. Ändra frågan i kod redigeraren till följande och välj **test fråga**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Den här frågan returnerar alla Tweets som innehåller nyckelordet *Azure*.

## <a name="create-an-output-sink"></a>Skapa en utgående mottagare

Nu har du definierat en händelse ström, en Event Hub-indata för inmatnings händelser och en fråga för att utföra en omvandling över strömmen. Det sista steget är att definiera en utgående mottagare för jobbet.  

I den här instruktions guiden skriver du de aggregerade tweet-händelserna från jobb frågan till Azure Blob Storage.  Du kan också skicka resultaten till Azure SQL Database, Azure Table Storage, Event Hubs eller Power BI, beroende på dina program behov.

## <a name="specify-the-job-output"></a>Ange jobbets utdata

1. I avsnittet **jobb sto pol Ogin** på den vänstra navigerings menyn väljer du **utdata**. 

2. På sidan **utdata** klickar du på **+ &nbsp; Lägg till** och **blob-lagring/data Lake Storage Gen2**:

   * **Utdata-alias**: Använd namnet `TwitterStream-Output` . 
   * **Import alternativ**: Välj **Välj lagring från dina prenumerationer**.
   * **Lagrings konto**. Välj ditt lagringskonto.
   * **Container**. Välj **Skapa ny** och ange `socialtwitter` .
   
4. Välj **Spara**.   

## <a name="start-the-job"></a>Starta jobbet

Du har angett jobbets indata, fråga och utdata. Du är redo att starta Stream Analytics jobbet.

1. Kontrol lera att TwitterClientCore-programmet körs. 

2. I jobb översikten väljer du **Start**.

3. På sidan **starta jobb** väljer du **nu** **Start tid för jobbets utdata** och väljer sedan **Starta**.

## <a name="get-support"></a>Få support
Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)
