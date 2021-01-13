---
title: Föreslagna frågor om aktiva utbildningar – QnA Maker
description: Förbättra kvaliteten på din kunskaps bas med aktiv inlärning. Granska, acceptera eller avvisa, Lägg till utan att ta bort eller ändra befintliga frågor.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6b9077fec13dd177ec4e07e7fbd7818ded2fd0a1
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98164948"
---
# <a name="accept-active-learning-suggested-questions-in-the-knowledge-base"></a>Acceptera föreslagna frågor om aktiva utbildningar i kunskaps basen


<a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>

Active Learning ändrar kunskaps basen eller Search Service när du har godkänt förslaget och sedan sparar och tränar. Om du godkänner förslaget kommer det att läggas till som en alternativ fråga.

## <a name="turn-on-active-learning"></a>Aktivera aktiv inlärning

För att kunna se föreslagna frågor måste du [Aktivera aktiv utbildning](../concepts/active-learning-suggestions.md) för din QNA Maker-resurs.

## <a name="view-suggested-questions"></a>Visa föreslagna frågor

1. Om du vill se de föreslagna frågorna går du till sidan **Redigera** kunskaps bas och väljer **visnings alternativ**. Välj sedan **Visa aktiva utbildnings förslag**. Det här alternativet inaktive ras om det inte finns några förslag för någon av fråga-och svars paren.  

    [![I redigera-avsnittet i portalen väljer du Visa förslag för att se de aktiva alternativen för inlärnings nya frågor.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrera kunskaps basen med fråge-och svars par för att bara visa förslag genom att välja **Filtrera efter förslag**.

    [![Använd alternativet filtrera efter förslag för att bara visa de aktiva inlärnings alternativ som föreslås av den aktuella frågan.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. Varje QnA-par föreslår den nya frågans alternativ med en bock markering, `✔` för att acceptera frågan eller `x` för att avvisa förslagen. Markera kryss rutan för att lägga till frågan.

    [![Välj eller avvisa den aktiva inlärningens föreslagna fråge alternativ genom att markera den gröna bocken eller det röda ta bort-märket.](../media/improve-knowledge-base/accept-active-learning-suggestions-small.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Du kan lägga till eller ta bort _alla förslag_ genom att välja **Lägg till alla** eller **Ignorera alla** i kontext verktygsfältet.

1. Välj **Spara och träna** för att spara ändringarna i kunskaps basen.

1. Välj **publicera** för att tillåta att ändringarna är tillgängliga från [GenerateAnswer-API: et](metadata-generateanswer-usage.md#generateanswer-request-configuration).

    Om 5 eller fler liknande frågor grupperas, var 30: e minut, rekommenderar QnA Maker de alternativa frågorna som du kan acceptera eller avvisa.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>Arkitektur flöde för att använda GenerateAnswer och träna API: er från en robot

En robot eller något annat klient program bör använda följande arkitektur flöde för att använda aktiv inlärning:

* Bot [hämtar svaret från kunskaps basen](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) med GenerateAnswer-API: et med hjälp av `top` egenskapen för att få ett antal svar.
* Bot avgör explicit feedback:
    * Filtrera ut låga resultat med din egen [anpassade affärs logik](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user).
    * I robot-eller klient programmet visar du en lista över möjliga svar på användaren och får användarens valda svar.
* Robot [skickar det valda svaret tillbaka till QNA Maker](#bot-framework-sample-code) med [träna-API: et](#train-api).


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>Använd egenskapen Top i GenerateAnswer-begäran för att få flera matchande svar

När du skickar en fråga till QnA Maker för ett svar `top` anger egenskapen för JSON-texten antalet svar som ska returneras.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>Använd score-egenskapen tillsammans med affärs logiken för att få en lista med svar för att visa användare

När klient programmet (t. ex. en chat-robot) tar emot svaret returneras de tre främsta frågorna. Använd `score` egenskapen för att analysera avståndet mellan poängen. Det här närhets intervallet bestäms av din egen affärs logik.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ *+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Uppföljning av klient program när frågor har liknande resultat

Ditt klient program visar frågorna med ett alternativ för användaren att välja _den enda fråga_ som representerar deras avsikt.

När användaren väljer en av de befintliga frågorna skickar klient programmet användarens val som feedback med hjälp av QnA Makers träna API. Den här feedbacken Slutför den aktiva inlärnings-loopen.

## <a name="train-api"></a>Tränings-API

Aktiva inlärnings synpunkter skickas till QnA Maker med anropet träna API POST. API-signaturen är:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Egenskap för HTTP-begäran|Namn|Typ|Syfte|
|--|--|--|--|
|URL-rutt parameter|Kunskaps bas-ID|sträng|Din kunskaps bas-GUID.|
|Anpassad under domän|Resurs namn för QnAMaker|sträng|Resurs namnet används som anpassad under domän för QnA Maker. Detta är tillgängligt på sidan inställningar när du har publicerat kunskaps basen. Den visas som `host` .|
|Huvud|Content-Type|sträng|Medie typen för den brödtext som skickas till API: et. Standardvärdet är: `application/json`|
|Huvud|Auktorisering|sträng|Din slut punkts nyckel (EndpointKey XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX).|
|Publicera brödtext|JSON-objekt|JSON|Feedback om utbildningen|

JSON-texten har flera inställningar:

|JSON-Body-egenskap|Typ|Syfte|
|--|--|--|--|
|`feedbackRecords`|matris|Lista över feedback.|
|`userId`|sträng|Användar-ID för den person som godkänner de föreslagna frågorna. Formatet för användar-ID är upp till dig. En e-postadress kan till exempel vara ett giltigt användar-ID i din arkitektur. Valfritt.|
|`userQuestion`|sträng|Den exakta texten för användarens fråga. Krävs.|
|`qnaID`|antal|ID för fråga som finns i [GenerateAnswer-svaret](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Ett exempel på en JSON-text ser ut så här:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Ett lyckat svar returnerar statusen 204 och ingen JSON-svars text.

### <a name="batch-many-feedback-records-into-a-single-call"></a>Gruppera många återkopplings poster till ett enda anrop

I program på klient sidan, till exempel en bot, kan du lagra data och sedan skicka många poster i en enda JSON-text i `feedbackRecords` matrisen.

Ett exempel på en JSON-text ser ut så här:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Exempel kod för bot Framework

Din bot Framework-kod måste anropa tågets API, om användarens fråga ska användas för aktiv inlärning. Det finns två typer av kod att skriva:

* Avgöra om frågan ska användas för aktiv inlärning
* Skicka tillbaka frågan till QnA Makers tåg-API för aktiv inlärning

I [Azure bot-exemplet](https://github.com/microsoft/BotBuilder-Samples)har båda dessa aktiviteter programmerats.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>Exempel C#-kod för träna API med bot Framework 4. x

Följande kod visar hur du skickar information tillbaka till QnA Maker med träna-API.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Exempel Node.js kod för träna API med bot Framework 4. x

Följande kod visar hur du skickar information tillbaka till QnA Maker med träna-API.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Aktiv inlärning sparas i den exporterade kunskaps basen

När du har aktiverat aktiv inlärning i appen och du exporterar appen, `SuggestedQuestions` behåller kolumnen i TSV-filen de aktiva inlärnings data.

`SuggestedQuestions`Kolumnen är ett JSON-objekt med information om implicit, `autosuggested` och explicit, `usersuggested` feedback. Ett exempel på detta JSON-objekt för en enskild användare som har skickat frågan av `help` är:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

När du importerar om den här appen fortsätter den aktiva inlärningen att samla in information och rekommendera förslag för din kunskaps bas.



## <a name="best-practices"></a>Bästa praxis

För bästa praxis när du använder aktiv inlärning, se [metod tips](../Concepts/best-practices.md#active-learning).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda metadata med API för GenerateAnswer](metadata-generateanswer-usage.md)
