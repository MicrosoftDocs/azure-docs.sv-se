---
title: Självstudie – analysera falska anrops data med Azure Stream Analytics och visualisera resultat i Power BI instrument panel
description: I den här självstudien finns en omfattande beskrivning av hur du använder Azure Stream Analytics för att analysera bedrägliga samtal i en telefonsamtalsström.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018878"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Självstudie: analysera bedrägliga anrops data med Stream Analytics och visualisera resultat i Power BI instrument panel

Den här självstudien visar hur du analyserar Telefonsamtals data med hjälp av Azure Stream Analytics. Telefonsamtals data som genereras av ett klient program innehåller bedrägliga anrop, som filtreras efter Stream Analytics jobb. Du kan använda metoderna från den här självstudien för andra typer av bedrägeri identifiering, till exempel kredit korts bedrägerier eller identitets stöld.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Generera exempel på Telefonsamtals data och skicka dem till Azure Event Hubs.
> * Skapa ett Stream Analytics-jobb.
> * Konfigurera indata och utdata för jobb.
> * Definiera frågor för att filtrera bedrägliga anrop.
> * Testa och starta jobbet.
> * Visualisera resultat i Power BI.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har slutfört följande steg:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ladda ned telefonsamtalets händelsegeneratorapp [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) från Microsoft Download Center eller hämta källkoden från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Du behöver Power BI-konto.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Innan Stream Analytics kan analysera dataströmmen med bedrägliga samtal behöver data skickas till Azure. I den här självstudien skickar du data till Azure med  [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Använd följande steg för att skapa en händelsehubb och skickar samtalsdata till den händelsehubben:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **Event Hubs**.

   ![Skapa en Azure-händelsehubb på portalen](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Fyll i fönstret **skapa namn område** med följande värden:

   |**Inställning**  |**Föreslaget värde** |**Beskrivning**  |
   |---------|---------|---------|
   |Name     | asaTutorialEventHub        |  Ett unikt namn för att identifiera händelsehubbens namnområde.       |
   |Prenumeration     |   \<Your subscription\>      |   Välj en Azure-prenumeration där du vill skapa händelsehubben.      |
   |Resursgrupp     |   MyASADemoRG      |  Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto.       |
   |Location     |   USA, västra 2      |    Plats där händelsehubbens namnområde kan distribueras.     |

4. Använd standard alternativen för återstående inställningar och välj **Granska + skapa**. Välj sedan **skapa** för att starta distributionen.

   ![Skapa en namnrymd för en händelsehubb på Azure-portalen](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. När namn rymden har distribuerats går du till **alla resurser** och hittar *asaTutorialEventHub* i listan över Azure-resurser. Välj *asaTutorialEventHub* för att öppna den.

6. Välj sedan **+ händelsehubben** och ange ett **namn** för händelsehubben. Ange **antalet partitioner** till 2.  Använd standard alternativen i de återstående inställningarna och välj **skapa**. Vänta sedan tills distributionen är klar.

   ![Konfiguration av händelsehubb på Azure-portalen](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan ett program kan skicka data till Azure Event Hubs måste händelsehubben ha en princip som tillåter åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1. Navigera till händelsehubben som du skapade i föregående steg, *MyEventHub*. Välj **Principer för delad åtkomst** under **Inställningar** och välj sedan **+ Lägg till**.

2. Ge principen namnet **MyPolicy** och se till att **Hantera** är markerat. Välj sedan **Skapa**.

   ![Skapa händelsehubb med delade åtkomstprinciper](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. När principen har skapats väljer du princip namnet för att öppna principen. Hitta **anslutnings strängen – primär nyckel**. Välj knappen **Kopiera** bredvid anslutnings strängen.

   ![Spara anslutningssträngen för princip för delad åtkomst](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen i nästa avsnitt.

   Anslutningssträngen ser ut så här:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Lägg märke till att anslutningssträngen innehåller flera nyckel/värde-par som är separerade med semikolon: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** och **EntityPath**.

## <a name="start-the-event-generator-application"></a>Starta händelsegeneratorprogrammet

Innan du startar appen TelcoGenerator ska du konfigurera den så att den skickar data till de Azure Event Hubs du skapade tidigare.

1. Extrahera innehållet i filen [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Öppna `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` filen i valfri text redigerare det finns mer än en `.config` fil, så se till att du öppnar rätt fil.

3. Uppdatera elementet `<appSettings>` i config-filen med följande information:

   * Ange värdet för nyckeln *EventHubName* till värdet för EntityPath i anslutningssträngen.
   * Ställ in värdet för nyckeln *Microsoft.ServiceBus.ConnectionString* till anslutningssträngen utan EntityPath-värdet. Glöm inte att ta bort semikolonet som föregår EntityPath-värdet.

4. Spara filen.

5. Öppna sedan ett kommandofönster och växla till den mapp där du packade upp programmet TelcoGenerator. Ange sedan följande kommando:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Kommandot stöder följande parametrar:
   * Antal samtalsdataposter per timme.
   * Procentandel för sannolikhet för bedrägeri, vilket är hur ofta appen ska simulera ett bedrägligt samtal. Värdet 0.2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.
   * Längd i timmar, vilket är det antal timmar som appen ska köras. Du kan också stoppa appen när du vill genom att avsluta processen (**CTRL + C**) på kommando raden.

   Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb. Telefonsamtalsdata innehåller följande fält:

   |**Post**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Tidsstämpeln för samtalets starttid.       |
   |SwitchNum     |  Telefonväxeln används för att ansluta samtalet. I det här exemplet är växlarna strängar som representerar ursprungslandet/regionen (USA, Kina, Storbritannien, Tyskland eller Australien).       |
   |CallingNum     |  Uppringarens telefonnummer.       |
   |CallingIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för uppringaren.       |
   |CalledNum     |   Telefonnumret till mottagaren.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för mottagaren.       |

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu nr du har en ström av anropshändelser kan du skapa ett Stream Analytics-jobb som läser data från händelsehubben.

1. Du skapar du ett Stream Analytics-jobb genom att gå till [Azure-portalen](https://portal.azure.com/).

2. Välj **skapa en resurs** och sök efter **Stream Analytics jobb**. Välj panelen **Stream Analytics jobb** och välj * Skapa * *.

3. Fyll i formuläret **ny Stream Analytics jobb** med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Jobbnamn     |  ASATutorial       |   Ett unikt namn för att identifiera händelsehubbens namnområde.      |
   |Prenumeration    |  \<Your subscription\>   |   Välj en Azure-prenumeration där du vill skapa jobbet.       |
   |Resursgrupp   |   MyASADemoRG      |   Välj **Använd befintlig** och ange ett nytt resursgruppsnamn för ditt konto.      |
   |Location   |    USA, västra 2     |      Plats där jobbet kan distribueras. Vi rekommenderar att du placerar jobbet och händelsehubben i samma region för bästa prestanda och så att du inte ska betala för att överföra data mellan regioner.      |
   |Värdmiljö    | Moln        |     Stream Analytics-jobb kan distribueras till molnet eller edge. Med molnet kan du distribuera till Azure-molnet, och med Edge kan du distribuera till en IoT Edge enhet.    |
   |Strömningsenheter     |    1       |      Strömningsenheter representerar de bearbetningsresurser som krävs för att köra ett jobb. Standardvärdet är 1. Mer information om skalning av strömningsenheter finns i artikeln om att [förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md).      |

4. Använd standard alternativen för återstående inställningar, Välj **skapa** och vänta tills distributionen har slutförts.

   ![Skapa ett Azure Stream Analytics-jobb](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurera jobbindata

Nästa steg är att definiera en indatakälla som jobbet använder för att läsa data med hjälp av den händelsehubb som du skapade i föregående avsnitt.

1. Öppna sidan **alla resurser** från Azure Portal och leta upp Stream Analyticss jobbet för *ASATutorial* .

2. I avsnittet **jobb stoation** i Stream Analytics jobb väljer du **indata**.

3. Välj **+ Lägg till strömindata** och **Händelsehubb**. Fyll i indatamängden med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias     |  CallStream       |  Ange ett eget namn som identifierar din inmatning. Indataaliaset får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3-63 tecken långt.       |
   |Prenumeration    |   \<Your subscription\>      |   Välj en Azure-prenumeration där du skapade händelsehubben. Händelsehubben kan finnas i samma eller en annan prenumeration som Stream Analytics-jobbet.       |
   |Namnområde för händelsehubb    |  asaTutorialEventHub       |  Välj namnområdet för händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbnamnområden som finns i din aktuella prenumeration visas i listrutan.       |
   |Namn på händelsehubb    |   MyEventHub      |  Välj händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbar som finns i din aktuella prenumeration visas i listrutan.       |
   |Principnamn för Event Hub   |  Policy       |  Välj händelsehubbens delade princip som du skapade i föregående avsnitt. Alla händelsehubbprinciper som finns i din aktuella prenumeration visas i listrutan.       |

4. Använd standardalternativ för de återstående inställningarna och välj **Spara**.

   ![Konfigurera Azure Stream Analytics-indata](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurera jobbutdata

Det sista steget är att definiera en utgående mottagare där jobbet kan skriva transformerade data. I den här självstudien matar du ut och visualiserar data med Power BI.

1. Öppna **alla resurser** från Azure Portal och välj jobbet *ASATutorial* Stream Analytics.

2. I avsnittet **jobb stoation** i Stream Analytics jobb väljer du alternativet **utdata** .

3. Välj **+ Lägg till**  >  **Power BI**. Välj sedan **auktorisera** och följ anvisningarna för att autentisera Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="knappen auktorisera för Power BI":::

4. Fyll i formuläret utdata med följande information och välj **Spara**:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Utdataalias  |  MyPBIoutput  |
   |Grupp arbets yta| Min arbetsyta |
   |Namn på datauppsättning  |   ASAdataset  |
   |Tabellnamn |  ASATable  |
   | Autentiseringsläge | Användartoken |

   ![Konfigurera Stream Analytics-utdata](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   I den här självstudien används autentiseringsläget *för användartoken.* Om du vill använda hanterad identitet läser du [Använd hanterad identitet för att autentisera ditt Azure Stream Analytics jobb till Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Skapa frågor för att transformera real tids data

Nu har du ett Stream Analytics jobb konfigurerat för att läsa en inkommande data ström. Nästa steg är att skapa en fråga som analyserar data i real tid. Frågorna använder ett SQL-liknande språk som har vissa tillägg som är specifika för Stream Analytics.

I det här avsnittet av självstudien skapar du och testar flera frågor för att lära dig några sätt som du kan omvandla en indata ström till för analys. 

Frågorna som du skapar här visar bara omvandlade data till skärmen. I ett senare avsnitt skriver du transformerade data till Power BI.

Mer information om språket finns i [språk referens för Azure Stream Analytics frågor](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Testa med en direkt fråga

Om du vill arkivera varje händelse kan du använda en direkt fråga för att läsa alla fält i händelsens nytto Last.

1. Navigera till ditt Stream Analytics jobb i Azure Portal och välj **fråga** under *jobb sto pol Ogin*. 

2. I frågefönstret anger du den här frågan:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Precis som med SQL är nyckelord inte Skift läges känsliga och blank steg är inte signifikant.

    I den här frågan `CallStream` är det alias som du angav när du skapade indatamängden. Om du har använt ett annat alias använder du det namnet i stället.

3. Välj **test fråga**.

    Stream Analytics jobbet kör frågan mot exempel data från indata och visar utdata längst ned i fönstret. Resultatet visar att Händelsehubben och streaming Analytics-jobbet har kon figurer ATS korrekt.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Exempel på utdata från test frågan":::

    Det exakta antalet poster som visas beror på hur många poster som samlats in i exemplet.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Minska antalet fält med en kolumn projektion

I många fall behöver inte analysen alla kolumner från indataströmmen. Du kan använda en fråga för att projicera en mindre uppsättning av returnerade fält än i direkt frågan.

Kör följande fråga och Lägg märke till utdata.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Räkna inkommande samtal per region: rullande-fönster med agg regering

Anta att du vill räkna antalet inkommande samtal per region. När du vill utföra mängd funktioner som inventering i strömmande data måste du segmentera strömmen till temporala enheter, eftersom själva data strömmen är i själva verket oändligt. Du gör detta med hjälp av en [funktion](stream-analytics-window-functions.md)för strömnings analys fönster. Sedan kan du arbeta med data i det fönstret som en enhet.

För den här omvandlingen vill du ha en sekvens av temporala fönster som inte överlappar – varje fönster har en diskret uppsättning data som du kan gruppera och aggregera. Den här typen av fönster kallas ett rullande- *fönster*. I fönstret rullande kan du få ett antal inkommande anrop grupperade efter `SwitchNum` , som representerar landet/regionen där anropet kommer.

1. Klistra in följande fråga i Frågeredigeraren:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Den här frågan använder `Timestamp By` nyckelordet i `FROM` satsen för att ange vilket tidsstämpelfält i den indataströmmen som ska användas för att definiera rullande-fönstret. I det här fallet delar fönstret data i segment efter `CallRecTime` fältet i varje post. (Om inget fält har angetts används den tidpunkt då varje händelse kommer till händelsehubben. Se "ankomst tid vs Application Time" i [Stream Analytics språk referens för frågor](/stream-analytics-query/stream-analytics-query-language-reference). 

    Projektionen innehåller `System.Timestamp` , som returnerar en tidsstämpel för slutet av varje fönster. 

    Om du vill ange att du vill använda ett rullande-fönster använder du funktionen [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) i- `GROUP BY` satsen. I funktionen anger du en tidsenhet (var som helst från en mikrosekund till en dag) och en fönster storlek (hur många enheter). I det här exemplet består fönstret rullande av 5-sekunders intervall, så du får ett antal per land/region för var 5: e sekunds värt samtal.

2. Välj **test fråga**. I resultatet ser du att tidsstämplar under **WindowEnd** är i steg om 5 sekunder.

### <a name="detect-sim-fraud-using-a-self-join"></a>Identifiera SIM-bedrägerier med en själv koppling

I det här exemplet bör du tänka på bedräglig användning för att vara anrop som kommer från samma användare men på olika platser inom 5 sekunder från varandra. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt.

Om du vill söka efter dessa fall kan du använda en själv koppling av strömmande data för att ansluta data strömmen till sig själv baserat på `CallRecTime` värdet. Sedan kan du söka efter anrops poster där `CallingIMSI` värdet (det ursprungliga numret) är detsamma, men `SwitchNum` värdet (ursprungsland/region) är inte samma.

När du använder en anslutning med strömmande data måste du ange vissa gränser för hur långt de matchande raderna kan vara åtskilda i tiden. Som tidigare nämnts är strömmande data i praktiken oändliga. Tids gränserna för relationen anges i `ON` kopplings instruktionen med hjälp av `DATEDIFF` funktionen. I det här fallet baseras kopplingen på ett 5 sekunders intervall anrops data.

1. Klistra in följande fråga i Frågeredigeraren:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Den här frågan liknar alla SQL-kopplingar förutom `DATEDIFF` funktionen i kopplingen. Den här versionen av `DATEDIFF` är bara för strömnings analys och måste visas i- `ON...BETWEEN` satsen. Parametrarna är en tidsenhet (sekunder i det här exemplet) och alias för de två källorna för kopplingen. Detta skiljer sig från standard-SQL- `DATEDIFF` funktionen.

    `WHERE`Satsen innehåller villkoret som flaggar det bedrägliga anropet: de ursprungliga växlarna är inte desamma.

2. Välj **test fråga**. Granska utdata och välj sedan **Spara fråga**.

## <a name="start-the-job-and-visualize-output"></a>Starta jobbet och visualisera utdata

1. Starta jobbet genom att gå till jobb **översikten** och välja **Starta**.

2. Välj **Nu** som starttid för jobbutdata och välj **Start**. Du kan se dess status i meddelandefältet.

3. När jobbet är klart går du till [Power BI](https://powerbi.com/) och loggar in med ditt arbets- eller skolkonto. Om Stream Analytics-jobbfrågan genererar resultat finns den *ASAdataset*-datamängd som du skapade under fliken **Datamängder**.

4. Från din Power BI-arbetsyta väljer du **+ Skapa** för att skapa en new instrumentpanel med namnet *Fraudulent Calls* (Bedrägliga samtal).

5. Längst upp i fönstret väljer du **redigera** och **Lägg till panel**. Välj sedan **Anpassade strömmande data** och **Nästa**. Välj **ASAdataset** under **Dina datamängder**. Välj **kort** i list rutan **visualiserings typ** och Lägg till **falska anrop** till **fält**. Välj **Nästa** för att ange ett namn på panelen och välj sedan **Applicera** för att skapa panelen.

   ![Skapa paneler på Power BI-instrumentpanelen](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Följ steg 5, med följande alternativ:
   * När du kommer till Visualiseringstyp väljer du Linjediagram.
   * Lägg till en axel och välj **windowend**.
   * Lägg till ett värde och välj **fraudulentcalls**.
   * För **Tidsfönster att visa** väljer du de senaste 10 minuterna.

7. Din instrumentpanel bör se ut som i exemplet nedan när båda panelerna har lagts till. Observera att om avsändaren av Event Hub-programmet och streaming Analytics-programmet körs uppdateras Power BI instrument panelen regelbundet när nya data anländer.

   ![Visa resultat på Power BI-instrumentpanelen](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Bädda in din Power BI-instrumentpanel i ett webb program

I den här delen av självstudien använder du ett exempel på [ASP.net](https://asp.net/) -webbprogram som skapats av Power BI-teamet för att bädda in instrument panelen. Mer information om att bädda in instrumentpaneler finns i artikeln [inbäddning med Power BI](/power-bi/developer/embedding).

Om du vill konfigurera programmet går du till GitHub [-lagringsplatsen PowerBI-Developer-samples](https://github.com/Microsoft/PowerBI-Developer-Samples) och följer anvisningarna under avsnittet **användare äger data** (Använd URL: erna för omdirigering och start sidan under underavsnittet **integrera-Web-App** ). Eftersom vi använder exempel på instrument panelen använder du exempel koden **integrera-Web-App** som finns i [GitHub-lagringsplatsen](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
När du har fått igång programmet i webbläsaren följer du anvisningarna för att bädda in instrumentpanelen du skapade tidigare på webbsidan:

1. Välj **Logga in för att Power BI**, vilket ger programmet åtkomst till instrument panelerna i ditt Power BI-konto.

2. Välj knappen **Hämta instrumentpaneler** som visar kontots instrumentpaneler i en tabell. Leta reda på namnet på instrumentpanelen du skapade tidigare **powerbi-embedded-dashboard** och kopiera motsvarande **EmbedUrl**.

3. Slutligen ska du klistra in **EmbedUrl** i motsvarande textfält och välja **Bädda in instrumentpanel**. Nu kan du visa samma instrumentpanel inbäddad i ett webbprogram.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett enkelt Stream Analytics-jobb, analyserade inkommande data och presenterade resultatet på en Power BI-instrumentpanel. Om du vill veta mer om Stream Analytics-jobb kan du fortsätta till nästa självstudie:

> [!div class="nextstepaction"]
> [Köra Azure Functions från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md)
