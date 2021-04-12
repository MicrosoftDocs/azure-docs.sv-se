---
title: 'Snabb start: skapa en ny app i LUIS-portalen'
description: I den här snabb starten skapar du grundläggande delar av en app, avsikter och entiteter samt test med exempel uttryck på LUIS-portalen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: c2f2bafe68dd3f3dad704ba12cdd4d01bf4f6588
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278770"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Snabb start: skapa en ny app i LUIS-portalen

I den här snabb starten skapar du en ny app i LUIS-portalen. Börja med att skapa grundläggande delar av en app, **avsikter** och **entiteter**. Testa sedan appen genom att tillhandahålla ett exempel på användarens uttryck i den interaktiva test panelen för att få den förväntade avsikten.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Skapa en app

Om du vill skapa ett program väljer du **+ ny app**. 

I fönstret som visas anger du följande information:

|Name  |Beskrivning  |
|---------|---------|
|Name     | Ett namn för din app. Till exempel "Start automatisering". Krävs.        |
|Kultur     | Det språk som din app förstår och pratar med. Krävs.   |
|Beskrivning | En beskrivning av din app. Valfritt.
|Förutsägelse resurs | Den förutsägelse resurs som tar emot frågor. Valfritt. |

Välj **Klar**.

>[!NOTE]
>Kulturen kan inte ändras när appen har skapats.


## <a name="create-intents"></a>Skapa avsikter

När LUIS-appen har skapats måste du skapa avsikter. Avsikter är ett sätt att klassificera text från användare. Till exempel kan en personal-app ha två funktioner. För att hjälpa personer:

 1. Sök efter och Ansök om jobb
 1. Sök efter formulär som ska användas för jobb

Appens två olika _avsikter_ anpassas till följande syften:

|Avsikt|Exempel text från användare<br>kallas en _uttryck_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Utför följande steg för att skapa avsikter:

1. När appen har skapats kontrollerar du att du är på sidan **syften** i avsnittet **build** . Välj **Skapa**.

   [![Skärm bild som visar hur du väljer Skapa för att skapa ny avsikt](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Ange namnet på avsikten `FindForm` och välj sedan **färdig**.

## <a name="add-an-example-utterance"></a>Lägg till ett exempel uttryck

Du lägger till exempel yttranden när du har skapat avsikter. Exempel på yttranden är text som en användare anger i en chat-robot eller något annat klient program. De mappar avsikten med användarens text till ett LUISt syfte. I det här exempel programmets `FindForm` syfte innehåller exempel yttranden formulär numret. Klient programmet behöver formulär numret för att uppfylla användarens begäran, så det är viktigt att inkludera det i uttryck.

På sidan **avsikter** för `FindForm` lägger du till följande exempel yttranden under **exempel uttryck**, 

|#|Exempel på yttranden|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Som design kan dessa exempel yttranden variera på följande sätt:

* uttryck längd
* [skiljetecken](luis-reference-application-settings.md#punctuation-normalization)
* Word-alternativ
* verb (är, är, är)
* ord ordning


## <a name="create-a-regular-expression-entity"></a>Skapa en entitet för reguljära uttryck

Om du vill returnera formulär numret i svars förutsägelsens svar måste formulär numret extraheras som en entitet. Eftersom formulär nummer texten är mycket strukturerad kan du använda en entitet för reguljära uttryck. Skapa entiteten för reguljära uttryck med följande steg:

1. Välj **entiteter** från menyn till vänster.

1. Välj **skapa** på sidan **entiteter** .

1. Ange namnet `FormNumber` och välj typ av **regex** -enhet.

1. Ange det reguljära uttrycket `hrf-[0-9]{6}` i fältet **regex** . Den här posten matchar litteral tecken, `hrf-` och tillåter exakt sex siffror och väljer sedan **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av entiteten för att skapa reguljära uttryck](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Den här entiteten extraherar all text som matchar det reguljära uttrycket i något av syftet.

## <a name="add-example-utterances-to-the-none-intent"></a>Lägga till exempelyttranden i avsikten Ingen

**Ingen** avsikt är återställnings avsikten och får inte vara tom. Avsikten bör innehålla en uttryck för varje 10-exempel yttranden som du har lagt till för andra avsikter av appen.

Yttranden för **inget** exempel bör ligga utanför din klient program domän.

1. Välj **avsikter** på menyn till vänster och välj sedan **ingen** från listan över försök.

1. Lägg till följande exempel yttranden till avsikten:

   |Ingen avsikts exempel yttranden|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   I den här appen är dessa exempel yttranden utanför domänen. Om din domän innehåller djur eller havs, bör du använda olika exempel-yttranden för **ingen** avsikt.

## <a name="train-the-app"></a>Träna appen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Titta på entiteten reguljära uttryck i exemplet yttranden

1. Verifiera att entiteten finns i **FindForm**  avsikt genom att välja **avsikter** på den vänstra menyn. Välj sedan **FindForm** avsikt.

   Entiteten är markerad där den visas i exemplet yttranden.

   > [!div class="mx-imgBorder"]
   > [![Skärm bild av alla exempel yttranden markerade med entiteter](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testa din nya app med det interaktiva test fönstret

Använd det interaktiva **test** fönstret i Luis-portalen för att verifiera att entiteten extraheras från en ny yttranden att appen inte har setts än.

1. Välj **test** på menyn längst upp till höger.

1. Lägg till en ny uttryck och tryck sedan på RETUR:

   ```Is there a form named hrf-234098```

    Välj **Granska** för att se enhets förutsägelser.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild som visar hur du testar nya uttryck i test fönstret](./media/get-started-portal-build-app/test-new-utterance.png)

   Den mest förväntade avsikten är korrekt **FindForm** med över 90% konfidensgrad (0,977). **FormNumber** -entiteten extraheras med värdet hrf-234098.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starten och inte fortsätter till nästa snabb start väljer du **Mina appar** på den översta navigerings menyn. Markera sedan appens vänstra kryss ruta i listan och välj  **ta bort** från verktygsfältet kontext ovanför listan.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en app](get-started-portal-deploy-app.md)
