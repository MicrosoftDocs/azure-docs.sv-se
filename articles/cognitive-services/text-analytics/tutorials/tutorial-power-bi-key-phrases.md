---
title: 'Självstudie: integera Power BI med tjänsten kognitiv textanalys'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API för textanalys för att extrahera viktiga fraser från text som lagras i Power BI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 47feddb88fd7ddae1f8be54709019b4c339d177d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599178"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>Självstudie: integera Power BI med tjänsten kognitiv textanalys

Microsoft Power BI Desktop är ett kostnadsfritt program som låter dig ansluta till, transformera och visualisera dina data. Tjänsten textanalystjänsten är del av Microsoft Azure Cognitive Services och ger naturlig språkbearbetning. Den kan ta rå ostrukturerad text och utvinna de viktigaste fraserna, analysera avsikter och identifiera välkända koncept, till exempel varumärken. Tillsammans kan dessa verktyg hjälpa dig att snabbt se vad kunderna talar om och vad de anser om det.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Använda Power BI Desktop för att importera och transformera data
> * Skapa en anpassad funktion i Power BI Desktop
> * Integrera Power BI Desktop med API:et textanalys för nyckelfraser
> * Använd API:et textanalys för nyckelfraser för att hämta de viktigaste fraserna från kundåterkoppling
> * Skapa ett ordmoln från kundåterkoppling

## <a name="prerequisites"></a>Förutsättningar
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop. [Hämta utan kostnad](https://powerbi.microsoft.com/get-started/).
- Ett Microsoft Azure-konto. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/cognitive-services/) eller [Logga](https://portal.azure.com/)in.
- Ett API-konto för Cognitive Services med textanalys-API. Om du inte har ett kan du [registrera dig](../../cognitive-services-apis-create-account.md) och använda den kostnadsfria nivån för 5 000 transaktioner/månad (se [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) för att slutföra den här självstudien.
- [Åtkomstnyckeln textanalys](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som har skapats för dig under inloggningen.
- Kundkommentarer. Du kan använda [våra exempeldata](https://aka.ms/cogsvc/ta) eller dina egna data. Den här självstudien utgår från att du använder våra exempeldata.

## <a name="load-customer-data"></a>Hämta kundinformation
<a name="LoadingData"></a>

Börja med att öppna Power BI Desktop och läsa in den kommaavgränsade filen (CSV-filen)`FabrikamComments.csv` som du hämtade i [Förhandskrav](#Prerequisites). Den här filen representerar en dags hypotetisk aktivitet i ett supportforum hos ett fiktivt småföretag.

> [!NOTE]
> Power BI kan använda data från en mängd olika källor, till exempel Facebook eller en SQL-databas. Läs mer på [Facebook-integrering med Power BI](https://powerbi.microsoft.com/integrations/facebook/) och [SQL Server-integrering med Power BI](https://powerbi.microsoft.com/integrations/sql-server/).

I huvudfönstret i Power BI Desktop väljer du menyfliksområdet **Start**. I gruppen **Externa data** på menyfliken öppnar du listrutan **Hämta data** och väljer **Text/CSV**.

![[Knappen Hämta data]](../media/tutorials/power-bi/get-data-button.png)

Dialogrutan Öppna visas. Navigera till din mapp för nedladdningar eller till den mapp där du hämtade filen `FabrikamComments.csv`. Klicka på `FabrikamComments.csv` och sedan knappen **Öppna**. Dialogrutan för CSV-import visas.

![[Dialogrutan för CSV-import]](../media/tutorials/power-bi/csv-import.png)

Via dialogrutan för .csv-import kan du kontrollera att Power BI Desktop har identifierat rätt teckenuppsättning, avgränsare, rubrikrader och kolumntyper. Den här informationen stämmer. Klicka på **Läs in**.

Visa inlästa data genom att klicka på knappen **Datavy** längs till vänster på Power BI-arbetsytan. En tabell med våra data öppnas, t.ex. i Microsoft Excel.

![[Startvy för importerade data]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>Förbereda data
<a name="PreparingData"></a>

Du kan behöva transformera data i Power BI Desktop innan de är klara att bearbetas med nyckelfras-API för textanalystjänsten.

Våra exempeldata inkluderar en `subject`-kolumn och en `comment`-kolumn. Med funktionen slå ihop kolumner i Power BI Desktop kan du extrahera nyckelfraser från data i båda dessa kolumner, i stället för endast kolumnen `comment`.

I Power BI Desktop väljer du menyfliksområdet **Start**. I gruppen **Externa data** klickar du på **Redigera frågor**.

![[Gruppen med externa data på startmenyfliken]](../media/tutorials/power-bi/edit-queries.png)

Välj `FabrikamComments` i **frågelistan** på vänster sida av fönstret om det inte redan är valt.

Välj nu båda kolumnerna, `subject` och `comment`, i tabellen. Du kan behöva bläddra vågrätt för att se de här kolumnerna. Klicka först på kolumnrubriken `subject`. Håll sedan ned Ctrl-tangenten och klicka sedan på kolumnrubriken `comment`.

![[Markera fält som ska slås samman]](../media/tutorials/power-bi/select-columns.png)

Välj menyfliksområdet **Transformera**. I gruppen **Textkolumner** i menyfliksområdet för klickar du på **Slå ihop kolumner**. Dialogrutan Slå ihop kolumner visas.

![[Slå samman fält med dialogrutan Slå ihop kolumner]](../media/tutorials/power-bi/merge-columns.png)

I dialogrutan Slå ihop kolumner väljer du `Tab` som avgränsare. Klicka sedan på **OK.**

Du kan också överväga att filtrera bort tomma meddelanden med filtret Ta bort tom och att ta bort icke utskrivbara tecken med Rensa-transformeringen. Om det i dina data finns en kolumn som kolumnen `spamscore` i vår exempelfil kan du hoppa över skräpkommentarer med hjälp av ett nummerfilter.

## <a name="understand-the-api"></a>Förstå API:et
<a name="UnderstandingAPI"></a>

[Nyckelfras-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/operations/KeyPhrases) för textanalystjänsten kan bearbeta upp till tusen textdokument per HTTP-begäran. Power BI föredrar dock att hantera poster en i taget, så att våra anrop till API:et i den här självstudien innehåller endast ett enskilt dokument. Nyckelfras-API:et kräver följande fält för varje dokument som bearbetas.

| Fält | Beskrivning |
| - | - |
| `id`  | En unik identifierare för det här dokumentet inom begäran. Svaret innehåller även det här fältet. På så sätt kan du enkelt koppla de extraherade nyckelfraserna med det dokument de kom från, om du bearbetar flera dokument. Eftersom du endast bearbetar ett dokument per begäran i denna självstudie kan du hårdkoda värdet för `id` så att det är samma som begäran.|
| `text`  | Texten som ska bearbetas. Värdet för det här fältet kommer från kolumnen `Merged` som du skapade i [föregående avsnitt](#PreparingData) och som innehåller den kombinerade ämnesrad- och kommentarstexten. Enligt nyckelfras-API:et får dessa data inte vara längre än cirka 5 120 tecken.|
| `language` | Koden för det naturliga språket som dokumentet är skrivet i. Alla meddelanden i exempelfilen är skrivna på engelska så du kan hårdkoda värdet `en` för detta fält.|

## <a name="create-a-custom-function"></a>Skapa anpassad funktion
<a name="CreateCustomFunction"></a>

Nu är du redo att skapa den anpassade funktionen som integrerar Power BI och textanalysen. Funktionen tar emot texten som ska behandlas som parameter. Den konverterar data till och från JSON-formatet som krävs och gör en HTTP-begäran till nyckelfras-API-slutpunkten. När vi har tolkat svaret returnerar funktionen en sträng från API:et som innehåller en kommaavgränsad lista över extraherade nyckelfraser.

> [!NOTE]
> De anpassade funktionerna i Power BI Desktop är skrivna med [Power Query M-formelspråket](/powerquery-m/power-query-m-reference), även kallat M. M är ett funktionellt programmeringsspråk som baseras på [F#](/dotnet/fsharp/). Du behöver inte vara programmerare för att genomföra den här självstudien. Den kod som krävs finns nedan.

Kontrollera att du fortfarande befinner dig i frågeredigerarfönstret i Power BI Desktop. Om du inte är där väljer du menyfliksområdet **Start**. I gruppen **externa data** klickar du sedan på **Redigera frågor**.

Nu, i den **Startmenyfliksområdet** i gruppen **Ny fråga** öppnar du listrutan **Ny källa** och väljer **Tom fråga**. 

En ny fråga, inledningsvis med namnet `Query1` visas i listan med frågor. Dubbelklicka på den här posten och ge den namnet `KeyPhrases`.

Nu, i den **Startmenyfliksområdet** i gruppen **Fråga** öppnar du **Aancerad redigerare** för att öppna den avancerade redigeraren. Ta bort den kod som redan finns i fönstret och klistra in följande kod. 

> [!NOTE]
> Ersätt exempel slut punkten nedan (som innehåller `<your-custom-subdomain>` ) med den slut punkt som genererats för din textanalys-resurs. Du kan hitta den här slut punkten genom att logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/), välja textanalys prenumeration och välja `Quick start` .


```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics" & "/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Ersätt `YOUR_API_KEY_HERE` med en textanalysåtkomstnyckel. Du kan också hitta denna nyckel genom att logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/), välja din textanalysprenumeration och välja sidan Översikt. Kom ihåg att lämna de dubbla citattecknen före och efter nyckeln. Klicka sedan på **Klar**.

## <a name="use-the-custom-function"></a>Använda anpassad funktion
<a name="UseCustomFunction"></a>

Du kan nu hämta nyckelfraser i var och en av våra kundkommentarer med den anpassade funktionen. Vi lagrar dem i en ny kolumn i tabellen. 

Växla tillbaka till frågan `FabrikamComments` i frågeredigerarfönstret i Power BI Desktop. Välj menyfliksområdet **Lägg till kolumn**. I gruppen **Allmänt** klickar du på **Anropa anpassad funktion**.

![[Knappen Anropa anpassad funktion]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Dialogrutan Anropa anpassad funktion visas. Ange `keyphrases` i **Nytt kolumnnamn**. I **Funktionsfråga** väljer du den anpassad funktionen som du skapade, `KeyPhrases`.

Ett nytt fält visas i dialogrutan **text (valfritt)**. Det här fältet frågar vilken kolumn som ska användas för att ange värden för `text` -parametern i nyckelfras-API:et. (Kom ihåg att du redan har hårdkodat värdena för `language` `id` parametrarna och.) Välj `Merged` (kolumnen du skapade [tidigare](#PreparingData) genom att sammanfoga ämnet och meddelande fälten) på den nedrullningsbara menyn.

![[Anropa anpassad funktion]](../media/tutorials/power-bi/invoke-custom-function.png)

Klicka slutligen på **OK**.

Om allt är klart anropar Power BI din anpassade funktion en gång för varje rad i tabellen. Den skickar frågor till nyckelfras-API:et och lägger till en ny kolumn i tabellen där resultaten lagras. Innan det händer kan du behöva ange inställningar för autentisering och sekretess.

## <a name="authentication-and-privacy"></a>Autentisering och sekretess
<a name="Authentication"></a>

När du stänger dialogrutan Anropa anpassad funktion visas en banderoll där du uppmanas att ange hur du vill ansluta till nyckelfras-API:et.

![[meddelande om autentiseringsuppgifter]](../media/tutorials/power-bi/credentials-banner.png)

Klicka på **Redigera autentiseringsuppgifter**. Se till att `Anonymous` är vald i dialogrutan. Klicka sedan på **Anslut**. 

> [!NOTE]
> Du väljer `Anonymous` eftersom textanalystjänsten autentiserar dig med åtkomstnyckeln. Power BI behöver inte ange autentiseringsuppgifter för HTTP-begäran.

> [!div class="mx-imgBorder"]
> ![[ange autentisering som anonym]](../media/tutorials/power-bi/access-web-content.png)

Om du ser meddelandet för att redigera autentiseringsuppgifter även när du har valt anonym åtkomst kan du ha glömt att klistra in åtkomstnyckeln för textanalys i koden i den  [anpassade funktionen](#CreateCustomFunction) för `KeyPhrases`.

Nu kan ett meddelande visas där du uppmanas att ange information om datakällornas sekretess. 

![[sekretessmeddelande]](../media/tutorials/power-bi/privacy-banner.png)

Klicka på **Fortsätt** och välj `Public` för var och en av datakällorna i dialogrutan. Klicka sedan på **Spara.**

![[ange datakällas sekretess]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>Skapa ordmoln
<a name="WordCloud"></a>

När du har åtgärdat eventuella banderoller som visas klickar du på **Stäng och tillämpa** i startmenyfliken för att stänga frågeredigeraren.

Det tar en stund innan Power BI Desktop har slutfört nödvändiga HTTP-begäranden. För varje rad i tabellen innehåller den nya `keyphrases`-kolumnen nyckelfraser som identifieras i texten av nyckelfras-API:et. 

Nu ska du generera ett ordmoln med den här kolumnen. Börja med att klicka på knappen **Rapport** i Power BI Desktop-huvudfönstret, till vänster om arbetsytan.

> [!NOTE]
> Varför generera ett ordmoln med extraherade nyckelfraser i stället för hela texten i varje kommentar? Nyckelfraser förser oss med de *viktiga* orden från kundkommentarerna, inte bara de *vanligaste* orden. Ordstorleken i det resulterande molnet påverkas dessutom inte av ord som används ofta i ett relativt litet antal kommentarer.

Installera den anpassade ordmolnsvyn om du inte redan har den. I panelen visualiseringar till höger om arbets ytan klickar du på de tre punkterna (**...**) och väljer **Importera från marknaden**. Om ordet "moln" inte är bland de visade visualiserings verktygen i listan kan du söka efter "Cloud" och klicka på knappen **Lägg till** bredvid molnet för ord i molnet. Power BI installerar ordmolnsvyn och bekräftar att installationen slutfördes.

![[lägga till anpassad visuell vy]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

Klicka först på ikonen Ordmoln på panelen visualiseringar.

![[ordmolnsikon i visualiseringspanel]](../media/tutorials/power-bi/visualizations-panel.png)

En ny rapport visas i arbetsytan. Dra fältet `keyphrases` från fältpanelen till kategorifältet på panelen Visualiseringar. Ordmolnet visas i rapporten.

Växla nu till sidan Format på panelen Visualiseringar. I kategorin Stoppord slår du på **Standard-stoppord** för att slippa korta och vanliga ord som ”och” i molnet. Men eftersom vi visualiserar viktiga fraser kan de inte innehålla stoppord.

![[aktivera standard-stoppord]](../media/tutorials/power-bi/default-stop-words.png)

Lite längre ned på panelen slår du av **Rotera texten** och **Title** (rubrik/titel).

![[aktivera fokusläge]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

Klicka på verktyget Fokusläge i rapporten för att titta närmare på ordmolnet. Verktyget expanderar ordmolnet så att det fyller hela arbetsytan. Se nedan.

![[ett ordmoln]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>Fler textanalystjänster
<a name="MoreServices"></a>

Textanalyssjänsten, som ingår i Cognitive Services i Microsoft Azure, omfattar också attitydanalysen och språkidentifiering. Språkidentifieringen är särskilt användbart om du får kundfeedback på andra språk än engelska.

Båda dessa andra API:er liknar nyckelfras-API:et. Det innebär att du kan integrera dem med Power BI Desktop med hjälp av anpassade funktioner som nästan är identiska med den som du skapade i den här självstudien. Skapa helt enkelt en tom fråga och klistra in lämplig kod nedan i den avancerade redigeraren, som du gjorde tidigare. (Glöm inte din åtkomst nyckel!) Sedan, som före, använder du funktionen för att lägga till en ny kolumn i tabellen.

Attitydanalysen returnerar ett värde som anger hur positiva åsikterna som uttrycks i texten är.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[detectedLanguage][confidenceScore] in  sentiment
```

Här följer två typer av språkidentifiering. Den första returnerar ISO-språkkoden (t.ex. `en` för engelska), och den andra returnerar ett smeknamn (t.ex. `English`). Du kanske märker att bara den sista raden i texten skiljer sig åt mellan de två versionerna.

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp [documents]{0}[detectedLanguage] [iso6391Name] in language 
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    jsonresp [documents]{0}[detectedLanguage] [iso6391Name] in language 
```

Här är en variant av nyckelfrasfunktionen som redan visas. Den returnerar fraserna som listobjekt snarare än som en sträng med kommaseparerade fraser. 

> [!NOTE]
> Returnerar en enkel sträng, förenklad, vårt ordmolnsexempel. En lista är å andra sidan ett mer flexibelt format för att arbeta med returnerade fraser i Power BI. Du kan ändra listobjekten i Power BI Desktop. Använd gruppen Strukturerad kolumn på frågeredigerarens transformeringsmenyflik.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://<your-custom-subdomain>.cognitiveservices.azure.com" & "/text/analytics/v3.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>Nästa steg
<a name="NextSteps"></a>

Läs mer om textanalystjänsten, Power Query M-formelspråket och Power BI.

> [!div class="nextstepaction"]
> [API för textanalys – referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0)

> [!div class="nextstepaction"]
> [Power Query M-referens](/powerquery-m/power-query-m-reference)

> [!div class="nextstepaction"]
> [Power BI-dokumentation](https://powerbi.microsoft.com/documentation/powerbi-landing-page/)
