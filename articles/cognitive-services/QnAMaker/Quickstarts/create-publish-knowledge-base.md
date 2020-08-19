---
title: 'Snabb start: skapa, träna och publicera kunskaps bas – QnA Maker'
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på hur du skapar en QnA Maker kunskaps bas från en enkel webb sida med vanliga frågor och svar för att besvara frågor QnA Maker.
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 77ddb2a0a149729af72066b53c17b1dcb08a2992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540762"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Snabb start: skapa, träna och publicera QnA Maker kunskaps bas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. Den här artikeln innehåller ett exempel på hur du skapar en QnA Maker kunskaps bas från en enkel webb sida med vanliga frågor och svar för att besvara frågor QnA Maker.

## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.
> * En QnA Maker [resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) som skapats i Azure Portal. Kom ihåg Azure Active Directory ID, prenumeration, QnA-resurs namn som du valde när du skapade resursen.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Skapa din första QnA Maker kunskaps bas

1. Logga in på [QnAMaker.AI](https://QnAMaker.ai) -portalen med dina Azure-autentiseringsuppgifter.

1. I QnA Maker-portalen väljer du **skapa en kunskaps bas**.

1. På sidan **skapa** hoppar du över **steg 1** om du redan har din QNA Maker-resurs.

    Om du inte har skapat resursen ännu väljer du **skapa en QNA-tjänst**. Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Kom ihåg Azure Active Directory ID, prenumeration, QnA-resurs namn som du valde när du skapade resursen.

    När du är klar med att skapa resursen i Azure Portal går du tillbaka till QnA Maker Portal, uppdaterar sidan webbläsare och fortsätter till **steg 2**.

1. I **steg 3**väljer du din Active Directory, prenumeration, tjänst (resurs) och språket för alla kunskaps baser som skapats i tjänsten.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Skärm bild av att välja en QnA Maker tjänst kunskaps bas":::

1. I **steg 3**namnger du kunskaps basen **mitt exempel på QNA KB**.

1. I **steg 4**konfigurerar du inställningarna med följande tabell:

    |Inställning|Värde|
    |--|--|
    |**Aktivera extrahering av flera adresser från URL: er, PDF-eller docx-filer.**|Markerad|
    |**Standard svars text**| `Quickstart - default answer not found.`|
    |**+ Lägg till URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Välj **Professional**|

1. I **steg 5**väljer **du skapa din KB**.

    Extraherings processen tar en stund att läsa dokumentet och identifiera frågor och svar.

    När QnA Maker har skapat kunskaps basen öppnas sidan **kunskaps bas** . Du kan redigera innehållet i kunskaps basen på den här sidan.

## <a name="add-a-new-question-and-answer-set"></a>Lägg till en ny fråga och svars uppsättning

1. På sidan **Redigera** på QNA Maker-portalen väljer du **+ Lägg till QNA-paret** från kontext verktygsfältet.
1. Lägg till följande fråga:

    `How many Azure services are used by a knowledge base?`

1. Lägg till svaret som formaterats med _markdown_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Lägg till frågan som text och svaret formaterat med markdown.":::

    Markdown-symbolen, `*` används för punkter. `\n`Används för en ny rad.

    På sidan **Redigera** visas markdown. När du använder **test** panelen senare ser du att markdown visas som den ska.

## <a name="save-and-train"></a>Spara och öva

I det övre högra hörnet väljer du **Spara och träna** för att spara dina ändringar och träna QNA Maker. Redigeringarna försvinner om de inte sparas.

## <a name="test-the-knowledge-base"></a>Testa kunskaps basen

1. I den QnA Maker portalen, i det övre högra hörnet, väljer du **test** för att testa att ändringarna du gjort har genomförts.
1. Ange ett exempel på en användar fråga i text rutan.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="Ange ett exempel på en användar fråga i text rutan.":::

1. Välj **Granska** för att undersöka svaret i detalj. Test fönstret används för att testa dina ändringar i kunskaps basen innan du publicerar din kunskaps bas.

1. Välj **testa** igen för att stänga **test** panelen.

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen

När du publicerar en kunskaps bas flyttas innehållet i kunskaps basen från `test` indexet till ett `prod` index i Azure Search.

![Skärm bild av hur du flyttar innehållet i kunskaps basen](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. I QnA Maker Portal väljer du **publicera**. Bekräfta sedan genom att välja **Publicera** på sidan.

    QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

    ![Skärm bild av lyckad publicering](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Skapa en robot

När du har publicerat kan du skapa en robot från **publicerings** sidan:

* Du kan skapa flera robotar snabbt, allt som pekar på samma kunskaps bas för olika regioner eller prissättnings planer för enskilda robotar.
* Om du bara vill ha en robot för kunskaps basen använder du länken **Visa alla robotar på Azure Portal** för att visa en lista över din aktuella robotar.

När du gör ändringar i kunskaps basen och återpublicerar behöver du inte vidta några åtgärder i bot-roboten. Den har redan kon figurer ATS för att fungera med kunskaps basen och fungerar med alla framtida ändringar i kunskaps basen. Varje gång du publicerar en kunskaps bas uppdateras alla robotar som är anslutna till den automatiskt.

1. På sidan **publicera** i QNA Maker-portalen väljer du **skapa bot**. Den här knappen visas bara när du har publicerat kunskaps basen.

    ![Skärm bild som visar hur du skapar en robot](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. En ny flik i webbläsaren öppnas för Azure Portal med sidan för att skapa Azure Bot Service. Konfigurera Azure bot service. Robot-och QnA Maker kan dela Web App Service-planen, men kan inte dela webbappen. Det innebär att **appens namn** måste vara ett annat än namnet på appen för tjänsten QNA Maker.

    * **Gör följande**
        * Ändra bot-referens – om den inte är unik.
        * Välj SDK-språk. När bot har skapats kan du ladda ned koden till din lokala utvecklings miljö och fortsätta utvecklings processen.
    * **Gör inte följande**
        * ändra följande inställningar i Azure Portal när du skapar bot-roboten. De är i förväg ifyllda för din befintliga kunskaps bas:
           * QnA auth-nyckel
           * App Service-plan och plats


1. När roboten har skapats öppnar du **bot-tjänstens** resurs.
1. Under **bot-hantering**väljer du **test i Web Chat**.
1. Skriv följande i Chat-prompten för att **skriva ditt meddelande**:

    `Azure services?`

    Chatt-roboten svarar med ett svar från din kunskaps bas.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Ange en användar fråga i test-webbchatten.":::

## <a name="what-did-you-accomplish"></a>Vad gjorde du?

Du har skapat en ny kunskaps bas, lagt till en offentlig URL i kunskaps basen, lagt till ditt eget QnA-par, tränat, testat och publicerat kunskaps basen.

När du har publicerat kunskaps basen har du skapat en robot och testat roboten.

Detta skedde om några minuter utan att behöva skriva någon kod eller rensa innehållet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte fortsätter med nästa snabb start tar du bort QnA Maker-och bot Framework-resurserna i Azure Portal.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till frågor med metadata](add-question-metadata-portal.md)

Mer information:

* [Markdown-format i svar](../reference-markdown-format.md)
* QnA Maker [data källor](../concepts/knowledge-base.md).


