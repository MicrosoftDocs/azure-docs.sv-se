---
title: Flera-turn-konversationer – QnA Maker
description: Använd prompter och kontext för att hantera flera sätt, så kallade flera varv, för din robot från en fråga till en annan. Med flera varv kan du få en snabb konversation där föregående frågas sammanhang påverkar nästa fråga och svar.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: b04a5535ada9532d62d395f7070d9bcd8aa4380c
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591787"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Använd uppföljande frågor för att skapa en längre konversation

Använd uppföljnings-och kontext hantering för att hantera flera _sätt_, så kallade flera varv, för din robot från en fråga till en annan.

Se följande demonstrations video om du vill se hur multi-turn fungerar:

[![Flera inaktiverade konversationer i QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Vad är en konversation med flera varv?

Vissa frågor kan inte besvaras i en enda tur. När du utformar dina klient program (Chat robot)-konversationer kan en användare ställa en fråga som behöver filtreras eller finjusteras för att fastställa rätt svar. Du gör det här flödet genom de frågor som är möjliga genom att presentera användaren med *uppföljnings* anvisningarna.

När en användare ställer en fråga returnerar QnA Maker svaret _och_ eventuella uppföljnings frågor. Med det här svaret kan du Visa uppföljnings frågorna som alternativ.

> [!CAUTION]
> Frågor med flera frågor extraheras inte från dokument med vanliga frågor och svar. Om du behöver extrahering på flera sätt tar du bort de frågetecken som anger QnA-par som vanliga frågor och svar.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Exempel på flera samtal med Chat-roboten

Med flera turn hanterar en chat-robot en konversation med en användare för att fastställa det slutliga svaret, som du ser i följande bild:

![En dialog ruta med flera inaktivitet med instruktioner som vägleder användaren via en konversation](../media/conversational-context/conversation-in-bot.png)

I föregående bild har en användare startat en konversation genom att ange **mitt konto**. Kunskaps basen har tre länkade fråga-och-svar-par. Användaren kan förfina svaret genom att välja ett av de tre alternativen i kunskaps basen. Frågan (#1) har tre uppföljnings anvisningar som visas i Chat-roboten som tre alternativ (#2).

När användaren väljer ett alternativ (#3) visas nästa lista över raffinerings alternativ (#4). Den här sekvensen fortsätter (#5) tills användaren bestämmer rätt, slut svar (#6).


### <a name="use-multi-turn-in-a-bot"></a>Använda multi-turn i en robot

När du har publicerat din KB kan du välja knappen **skapa bot** för att distribuera QNA Maker robot till Azure bot service. Prompterna visas i de chat-klienter som du har aktiverat för din robot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Skapa en konversation med flera varv från ett dokuments struktur

När du skapar en kunskaps bas visar avsnittet **fylla i ditt KB** en **Aktivera extrahering av flera turn-filer från URL: er,. PDF eller. docx-filer** .

![Kryss ruta för att aktivera extrahering av flera sätt](../media/conversational-context/enable-multi-turn.png)

När du väljer det här alternativet extraherar QnA Maker hierarkin som finns i dokument strukturen. Hierarkin konverteras i för att följa upp prompter och roten i hierarkin fungerar som överordnad QnA. I vissa dokument saknar roten i hierarkin innehåll som kan fungera som ett svar. du kan ange standard svars texten som ska användas som ett ersättnings svar för att extrahera sådana hierarkier.

Flera-turn-strukturen kan bara härledas från URL: er, PDF-filer eller DOCX-filer. Ett exempel på en struktur finns i en bild av en [manuell PDF-fil för Microsoft Surface-användare](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="Skärm bild som visar ett exempel på strukturen i en Användar handbok." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Skapa ett eget dokument med flera vändare

Kom ihåg följande rikt linjer om du skapar ett dokument med flera vändare:

* Använd rubriker och under rubriker för att beteckna hierarkier. Till exempel kan du ange den överordnade QnA och H2 för att ange den QnA som ska tas med i frågan. Använd liten rubrik storlek för att beteckna efterföljande hierarkier. Använd inte formatmall, färg eller någon annan mekanism för att göra en struktur i ditt dokument, QnA Maker inte extraherar flera inaktiverade prompter.

* Första bokstaven i rubriken måste vara kapitaliserad.

* Avsluta inte en rubrik med ett frågetecken, `?` .

* Du kan använda [exempel dokumentet](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) som ett exempel för att skapa ett eget dokument med flera dokument.

### <a name="adding-files-to-a-multi-turn-kb"></a>Lägga till filer i en multi-turn KB

När du lägger till ett hierarkiskt dokument bestämmer QnA Maker uppföljnings anvisningarna från strukturen för att skapa ett konversations flöde.

1. I QnA Maker väljer du en befintlig kunskaps bas som skapats med **Aktivera extrahering av flera turn från URL: er, PDF-eller docx-filer.** aktiva.
1. Gå till sidan **Inställningar** och välj den fil eller URL som du vill lägga till.
1. **Spara och träna** kunskaps basen.

> [!Caution]
> Stöd för att använda en exporterad TSV eller XLS-fil med flera inaktiverade kunskaps bas som data källa för en ny eller tom kunskaps bas stöds inte. Du måste **Importera** den filtypen från sidan **Inställningar** på QNA Maker-portalen för att kunna lägga till exporterade prompter med flera turn i en kunskaps bas.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Skapa kunskaps bas med flera turn-prompter med Create API

Du kan skapa ett kunskaps ärende med flera turn-prompter med hjälp av [QNA Maker skapa API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Prompterna läggs till i `context` egenskaps `prompts` mat ris.

## <a name="show-questions-and-answers-with-context"></a>Visa frågor och svar med kontext

Minska antalet frågor och svar-par som visas till endast de med sammanhangsbaserade konversationer.

Välj **visnings alternativ** och välj sedan **Visa kontext**. I listan visas fråga-och-svar-par som innehåller uppföljnings anvisningarna.

![Filtrera frågor och svar-par efter sammanhangsbaserade konversationer](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontexten för flera turn visas i den första kolumnen.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="Skärm bild som visar avsnittet om kontext markerat." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

I föregående bild anger **#1** fet text i kolumnen som visar den aktuella frågan. Den överordnade frågan är det översta objektet på raden. Alla frågor nedan är de länkade frågorna och svars paren. Dessa objekt är valbara, så att du kan gå direkt till andra kontext objekt.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett befintligt fråga-och-svar-par som en uppföljnings fråga

Den ursprungliga frågan, **mitt konto**, har uppföljnings frågor, till exempel **konton och inloggning**.

![Svar och Uppföljnings frågor för "konton och signering"](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Lägg till en uppföljnings fråga till ett befintligt fråga-och-svar-par som inte är länkat. Eftersom frågan inte är länkad till några frågor och svar-par måste den aktuella visnings inställningen ändras.

1. Om du vill länka ett befintligt fråga-och-svar-par som en uppföljnings fråga väljer du raden för fråga-och-svar-paret. För Surface manual söker du efter **Logga ut** för att minska listan.
1. Välj **Lägg till uppföljnings fråga** i kolumnen **svar** i raden för att **Logga in**.
1. Ange följande värden i fälten i popup-fönstret för **uppföljnings prompten** :

    |Fält|Värde|
    |--|--|
    |Visningstext|Ange **Stäng av enheten**. Det här är en anpassad text som visas i uppföljnings anvisningarna.|
    |Endast Sammanhangs beroende| Markera den här kryss rutan. Ett svar returneras bara om frågan anger sammanhang.|
    |Länk till svar|Ange **Använd inloggnings skärmen** för att hitta det befintliga fråga-och-svar-paret.|


1.  En matchning returneras. Välj det här svaret som uppföljning och välj sedan **Spara**.

    ![Sidan "uppföljnings prompt (förhands granskning)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. När du har lagt till uppföljnings anvisningarna väljer du **Spara och träna** i det övre navigerings fönstret.

### <a name="edit-the-display-text"></a>Redigera visnings texten

När en uppföljnings fråga skapas och ett befintligt fråga-och-svar-par anges som **länk till svar**, kan du ange en ny **visnings text**. Den här texten ersätter inte den befintliga frågan och lägger inte till någon ny alternativ fråga. Den är separat från dessa värden.

1. Om du vill redigera visnings texten söker du efter och väljer frågan i **kontext** fältet.
1. På raden för den frågan väljer du uppföljnings anvisningarna i kolumnen svar.
1. Välj den visnings text som du vill redigera och välj sedan **Redigera**.

    ![Kommandot edit för visnings texten](../media/conversational-context/edit-existing-display-text.png)

1. Ändra den befintliga visnings texten i popup-fönstret för **uppföljnings frågan** .
1. När du är klar med att redigera visnings texten väljer du **Spara**.
1. I det övre navigerings fältet **sparar och tränar** du.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Lägg till ett nytt fråga-och-svar-par som en uppföljnings fråga

När du lägger till ett nytt fråga-och-svar-par i kunskaps basen ska varje par länkas till en befintlig fråga som en uppföljnings fråga.

1. I verktygsfältet kunskaps bas söker du efter och väljer det befintliga fråge-och svars paret för **konton och loggar in**.

1. Välj **Lägg till uppföljnings fråga** i **svars** kolumnen för den här frågan.
1. Under **uppföljnings prompt (för hands version)** skapar du en ny uppföljning genom att ange följande värden:

    |Fält|Värde|
    |--|--|
    |Visningstext|*Skapa ett Windows-konto*. Den anpassade text som ska visas i uppföljnings anvisningarna.|
    |Endast Sammanhangs beroende|Markera den här kryss rutan. Svaret returneras endast om frågan anger sammanhang.|
    |Länk till svar|Ange följande text som svar:<br>*[Skapa](https://account.microsoft.com/) ett Windows-konto med ett nytt eller befintligt e-postkonto*.<br>När du sparar och tränar databasen kommer den här texten att konverteras. |
    |||

    ![Skapa en ny fråga och svara på frågor](../media/conversational-context/create-child-prompt-from-parent.png)


1. Välj **Skapa ny** och välj sedan **Spara**.

    Den här åtgärden skapar ett nytt par för frågor och svar och länkar den valda frågan som en uppföljnings fråga. I kolumnen **context** visas en uppföljnings relation för båda frågorna.

1. Välj **visnings alternativ** och välj sedan [**Visa kontext (för hands version)**](#show-questions-and-answers-with-context).

    Den nya frågan visar hur den är länkad.

    ![Skapa en ny uppföljnings prompt](../media/conversational-context/new-qna-follow-up-prompt.png)

    Den överordnade frågan visar en ny fråga som ett alternativ.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="Skärm bild som visar kolumnen kontext, för båda frågorna, indikerar en uppföljnings relation." lightbox="../media/conversational-context/child-prompt-created.png":::

1. När du har lagt till uppföljnings anvisningarna väljer du **Spara och träna** i det övre navigerings fältet.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Visa flera-turn under testning av uppföljnings anvisningarna

När du testar frågan med uppföljnings anvisningarna i **test** fönstret, innehåller svaret uppföljnings anvisningarna.

![Svaret innehåller uppföljnings anvisningarna](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>En JSON-begäran för att returnera ett första svar och Uppföljnings frågor

Använd det tomma `context` objektet för att begära svar på användarens fråga och inkludera uppföljnings frågor.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett första svar och Uppföljnings frågor

Föregående avsnitt begärde ett svar och eventuella uppföljnings frågor till **konton och inloggning**. Svaret innehåller prompt-information som finns i *svar [0]. context* och texten som ska visas för användaren.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts`Matrisen innehåller text i `displayText` egenskapen och `qnaId` värdet. Du kan visa svaren som nästa alternativ i konversations flödet och sedan skicka `qnaId` tillbaka tillbaka till QNA Maker i följande begäran.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>En JSON-begäran om att returnera ett svar som inte är första gången och Uppföljnings frågor

Fyll i `context` objektet så att föregående kontext tas med.

I följande JSON-begäran använder den aktuella frågan *Windows Hello för att logga in* och föregående fråga var *konton och loggade in*.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Ett JSON-svar för att returnera ett icke-initialt svar och Uppföljnings frågor

_GenerateAnswer_ JSON-svaret innehåller följande prompter i `context` egenskapen för det första objektet i `answers` objektet: QNA Maker

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Fråga kunskaps basen med QnA Maker-ID

Om du skapar ett anpassat program med hjälp av funktionen för flera sätt. Vid den första frågans svar returneras eventuella uppföljnings frågor och dess associerade `qnaId` . Nu när du har ID: t kan du skicka detta i begär ande texten för uppföljnings frågan. Om texten i begäran innehåller `qnaId` , och objektet context (som innehåller föregående QNA Maker egenskaper), returnerar GenerateAnswer den exakta frågan efter ID, i stället för att använda rangordnings algoritmen för att hitta svaret från frågetexten.


## <a name="display-order-is-supported-in-the-update-api"></a>Visnings ordningen stöds i uppdaterings-API: et

[Visnings texten och visnings ordningen](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), som returneras i JSON-svaret, stöds för redigering av [uppdaterings-API: et](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Lägg till eller ta bort prompter med uppdaterings-API: et

Du kan lägga till eller ta bort prompter med flera turn-meddelanden med hjälp av [API: et QNA Maker Update](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Prompterna läggs till i `context` egenskaps `promptsToAdd` mat ris och `promptsToDelete` matrisen.

## <a name="export-knowledge-base-for-version-control"></a>Exportera kunskaps bas för versions kontroll

QnA Maker stöder versions kontroll genom att inkludera konversations steg med flera turn i den exporterade filen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om sammanhangsbaserade konversationer från det här [dialog exemplet](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) eller Lär dig mer om [konceptuella robot design för flera-turn-konversationer](/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)