---
title: Skapa en funktion som kan integreras med Azure Logic Apps
description: Skapa en funktion som integreras med Azure Logic Apps och Azure Cognitive Services. Det resulterande arbetsflödet kategoriserar tweetsentiment som skickar e-postmeddelanden.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388686"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Självstudie: Skapa en funktion för att integrera med Azure Logic Apps

Azure Functions kan integreras med Azure Logic Apps i Logikappdesignern. Med den här integreringen kan du använda beräkningskraften i Functions i orkestrering med andra Azure- och tjänster från tredje part.

Den här självstudien visar hur du skapar ett arbetsflöde för att analysera Twitter-aktivitet. När tweets utvärderas skickar arbetsflödet meddelanden när positiva sentiment identifieras.

I de här självstudierna får du lära dig att:

> [!div class="checklist"]
> * Skapa en API-resurs för Cognitive Services.
> * Skapa en funktion som kategoriserar tweetsentiment.
> * Skapa en logikapp som ansluter till Twitter.
> * Lägg till sentimentidetntifiering till logikappen.
> * Anslut logikappen till funktionen.
> * Skicka ett e-postmeddelande baserat på svar från funktionen.

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt [Twitter](https://twitter.com/)-konto.
* Ett [Outlook.com](https://outlook.com/)-konto (för att skicka meddelanden).

> [!NOTE]
> Om du vill använda Gmail-anslutningsappen kan endast G Suite-företagskonton använda den här anslutningsappen utan begränsningar i logikappar. Om du har ett Gmail-konsumentkonto kan du använda Gmail-anslutningsappen med endast specifika Google-godkända appar och tjänster, eller så kan du skapa en Google-klientapp som ska användas för autentisering i [Gmail-anslutningsappen.](/connectors/gmail/#authentication-and-bring-your-own-application) <br><br>Mer information finns i [Datasäkerhets- och sekretesspolicyer för Google-anslutningsappar i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Skapa Textanalys resurs

API:erna för Cognitive Services är tillgängliga i Azure som enskilda resurser. Använd Textanalys-API:et för att identifiera sentimentet hos publicerade tweets.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.

1. Under _Kategorier_ väljer du **AI + Machine Learning**

1. Under _Textanalys_ väljer du **Skapa**.

1. Ange följande värden på skärmen _Textanalys_ skapa konto.

    | Inställning | Värde | Kommentarer |
    | ------- | ----- | ------- |
    | Prenumeration | Namnet på din Azure-prenumeration | |
    | Resursgrupp | Skapa en ny resursgrupp med namnet **tweet-sentiment-tutorial** | Senare kan du ta bort den här resursgruppen för att ta bort alla resurser som skapades under den här självstudien. |
    | Region | Välj den region som är närmast dig | |
    | Name | **TweetSentimentApp** | |
    | Prisnivå | Välj **Kostnadsfri F0** | |

1. Välj **Granska + skapa**.

1. Välj **Skapa**.

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="get-text-analytics-settings"></a>Hämta Textanalys inställningar

När Textanalys resurs har skapats kopierar du några inställningar och reserverar dem för senare användning.

1. Välj **Nycklar och Slutpunkt.**

1. Kopiera **nyckel 1** genom att klicka på ikonen i slutet av inmatningsrutan.

1. Klistra in värdet i en textredigerare.

1. Kopiera **slutpunkten** genom att klicka på ikonen i slutet av inmatningsrutan.

1. Klistra in värdet i en textredigerare.

## <a name="create-the-function-app"></a>Skapa funktionsappen

1. I den övre sökrutan söker du efter och väljer **Funktionsapp**.

1. Välj **Skapa**.

1. Ange följande värden.

    | Inställning | Föreslaget värde | Kommentarer |
    | ------- | ----- | ------- |
    | Prenumeration | Ditt Azure-prenumerationsnamn | |
    | Resursgrupp | **tweet-sentiment-tutorial** | Använd samma resursgruppsnamn i den här självstudien. |
    | Funktionsappens namn | **TweetSentimentAPI** + ett unikt suffix | Funktionsprogramnamn är globalt unika. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`. |
    | Publicera | **Kod** | |
    | Körningsstack | **.NET** | Funktionskoden för dig finns i C#. |
    | Version | Välj det senaste versionsnumret | |
    | Region | Välj den region som är närmast dig | |

1. Välj **Granska + skapa**.

1. Välj **Skapa**.

1. När distributionen är klar väljer du **Gå till resurs**.

## <a name="create-an-http-triggered-function"></a>Skapa en HTTP-utlöst funktion  

1. På den vänstra menyn i _funktionsfönstret_ väljer du **Functions**.

1. Välj **Lägg** till på den översta menyn och ange följande värden.

    | Inställning | Värde | Kommentarer |
    | ------- | ----- | ------- |
    | Utvecklingsmiljö | **Utveckla i portalen** | |
    | Mall | **HTTP-utlösare** | |
    | Ny funktion | **TweetSentimentFunction** | Det här är namnet på din funktion. |
    | Auktoriseringsnivå | **Funktion** | |

1. Välj knappen **Lägg till**.

1. Välj knappen **Kod +** test.

1. Klistra in följande kod i kodredigeringsfönstret.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    En sentimentpoäng skickas till funktionen, som returnerar ett kategorinamn för värdet.

1. Välj knappen **Spara** i verktygsfältet för att spara ändringarna.

    > [!NOTE]
    > Om du vill testa funktionen väljer **du Test/Kör** på den översta menyn. På fliken _Indata_ anger du värdet `0.9` i rutan _Brödtext_ och väljer sedan **Kör**. Kontrollera att värdet positiv _returneras_ i rutan _HTTP-svarsinnehåll_ i _avsnittet Utdata._

Skapa sedan en logikapp som integreras med Azure Functions, Twitter och Cognitive Services-API:et.

## <a name="create-a-logic-app"></a>Skapa en logikapp

1. I den övre sökrutan söker du efter och väljer **Logic Apps**.

1. Välj **Lägg till**.

1. Välj **Förbrukning** och ange följande värden.

    | Inställning | Föreslaget värde |
    | ------- | --------------- |
    | Prenumeration | Ditt Azure-prenumerationsnamn |
    | Resursgrupp | **tweet-sentiment-tutorial** |
    | Logikappens namn | **TweetSentimentApp** |
    | Region | Välj den region som är närmast dig, helst samma region som du valde i föregående steg. |

    Acceptera standardvärden för alla andra inställningar.

1. Välj **Granska + skapa**.

1. Välj **Skapa**.

1. När distributionen är klar väljer du **Gå till resurs**.

1. Välj knappen **Tom logikapp.**

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Tom logikapp-knapp":::

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

Nu kan du använda Logic Apps Designer för att lägga till tjänster och utlösare i ditt program.

## <a name="connect-to-twitter"></a>Ansluta till Twitter

Skapa en anslutning till Twitter så att din app kan avs omröstning efter nya tweets.

1. Sök efter **Twitter** i den övre sökrutan.

1. Välj **Twitter-ikonen.**

1. Välj utlösaren **When a new tweet is posted** (När en ny tweet publiceras).

1. Ange följande värden för att konfigurera anslutningen.

    | Inställning |  Värde |
    | ------- | ---------------- |
    | Anslutningsnamn | **MyTwitterConnection** |
    | Autentiseringstyp | **Använd standard delat program** |

1. Välj **Logga in**.

1. Följ anvisningarna i popup-fönstret för att slutföra inloggningen på Twitter.

1. Ange sedan följande värden i rutan _När en ny tweet publiceras._

    | Inställning | Värde |
    | ------- | ----- |
    | Genomsöka text | **#my-twitter-tutorial** |
    | Hur gärna vill du söka efter objekt? | **15** i textrutan och <br> **Minut** i listrutan |

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

Anslut sedan till textanalys för att identifiera attityden hos insamlade tweets.

## <a name="add-text-analytics-sentiment-detection"></a>Lägga till Textanalys attitydidentifiering

1. Välj **Nytt steg**.

1. Sök efter **Textanalys** i sökrutan.

1. Välj ikonen **Textanalys** fil.

1. Välj **Identifiera sentiment** och ange följande värden.

    | Inställning | Värde |
    | ------- | ----- |
    | Anslutningsnamn | **TextAnalyticsConnection** |
    | Kontonyckel | Klistra in den Textanalys kontonyckel som du tidigare har reserverat. |
    | Webbplats-URL | Klistra in den Textanalys slutpunkt som du avsatt tidigare. |

1. Välj **Skapa**.

1. Klicka i _rutan Lägg till_ ny parameter och markera kryssrutan bredvid **dokument** som visas i popup-menyn.

1. Klicka i _textrutan dokument-ID – 1_ för att öppna popup-fönster med dynamiskt innehåll.

1. I _sökrutan för dynamiskt_ innehåll söker du efter **id och** klickar på **Tweet-ID.**

1. Klicka i _textrutan Text – 1_ för dokument för att öppna popup-fönster med dynamiskt innehåll.

1. I _sökrutan för_ dynamiskt innehåll söker du efter **text** och klickar på **Tweet-text.**

1. I **Välj en åtgärd** skriver du **Textanalys** och klickar sedan på åtgärden **Identifiera sentiment**.

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

Rutan _Detect Sentiment_ (Identifiera attityd) bör se ut som på följande skärmbild.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Identifiera attitydinställningar":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Ansluta sentimentutdata till funktionsslutpunkt

1. Välj **Nytt steg**.

1. Sök **efter Azure Functions** i sökrutan.

1. Välj ikonen **Azure Functions** fil.

1. Sök efter funktionsnamnet i sökrutan. Om du följde riktlinjerna ovan börjar funktionsnamnet med **TweetSentimentAPI**.

1. Välj funktionsikonen.

1. Välj **objektet TweetSentimentFunction.**

1. Klicka i _rutan Begärandetext_ och välj _objektet Detect Sentiment_ **score** (Identifiera attitydpoäng) i popup-fönstret.

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

## <a name="add-conditional-step"></a>Lägga till villkorssteg

1. Välj knappen **Lägg till en** åtgärd.

1. Klicka i _rutan_ Kontroll och sök efter och **välj Kontroll** i popup-fönstret.

1. Välj **Villkor.**

1. Klicka i _rutan Välj ett_ värde och välj objektet _TweetSentimentFunction Body (TweetSentimentFunction-brödtext)_  i popup-fönstret.

1. Ange **Positiv** i _rutan Välj ett_ värde.

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

## <a name="add-email-notifications"></a>Lägga till e-postmeddelanden

1. Under rutan _Sant_ väljer du knappen **Lägg till en** åtgärd.

1. Sök efter och **välj Office 365 Outlook** i textrutan.

1. Sök efter **skicka** och välj **Skicka ett e-postmeddelande** i textrutan.

1. Välj **knappen Logga in.**

1. Följ anvisningarna i popup-fönstret för att slutföra inloggningen till Office 365 Outlook.

1. Ange din e-postadress i _rutan Till._

1. Klicka i _rutan Ämne_ och klicka på objektet **Brödtext** under _TweetSentimentFunction_. Om _objektet Brödtext_ inte visas i listan klickar du på länken **Visa mer** för att expandera alternativlistan.

1. Efter objektet _Brödtext_ i _Ämne_ anger du texten **Tweet från:**.

1. Efter _texten Tweet från:_ klickar du på rutan igen och väljer **Användarnamn** från alternativlistan _When a new tweet is posted (När en ny tweet_ publiceras).

1. Klicka i rutan _Brödtext_ och välj **Tweet-text** under _alternativlistan When a new tweet is posted_ (När en ny tweet publiceras). Om _tweettextobjektet_ inte visas i listan klickar du på länken **Visa mer** för att expandera alternativlistan.

1. Välj knappen **Spara** i verktygsfältet för att spara förloppet.

E-postrutan bör nu se ut som den här skärmbilden.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-postavisering":::

## <a name="run-the-workflow"></a>Köra arbetsflödet

1. Från ditt Twitter-konto tweetar du följande text: Jag gillar **#my-twitter-tutorial**.

1. Gå tillbaka till Logic Apps Designer och välj **knappen** Kör.

1. Kontrollera e-postmeddelandet för ett meddelande från arbetsflödet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla Azure-tjänster och konton som skapats under den här självstudien tar du bort resursgruppen.

1. Sök efter **Resursgrupper** i den övre sökrutan.

1. Välj **tweet-sentiment-tutorial**.

1. Välj **Ta bort resursgrupp**

1. Ange **tweet-sentiment-tutorial** i textrutan.

1. Välj knappen **Ta bort**.

Om du vill kan du kanske återgå till ditt Twitter-konto och ta bort test-tweets från ditt flöde.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett API utan server med Azure Functions](functions-create-serverless-api.md)
