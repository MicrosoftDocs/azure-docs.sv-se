---
title: Självstudie Video Indexer anslutningsappar med Logic App och Power Automate.
description: Den här självstudien visar hur du låser upp nya upplevelser och intäktsskapande affärsmöjligheter Video Indexer anslutningsappar med Logic App och Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: caff6a2496e907da1bdc140860c47476d1842df4
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600683"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Självstudie: Använda Video Indexer med Logic App och Power Automate

Azure Media Services Video Indexer [v2 REST API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) stöder både server-till-server- och klient-till-server-kommunikation och gör det möjligt för Video Indexer-användare att enkelt integrera video- och ljudinsikter i sin programlogik, vilket ger nya upplevelser och intäktsmöjligheter.

För att göra integreringen ännu enklare stöder vi [Logic Apps](https://azure.microsoft.com/services/logic-apps/)   och [Power Automate-anslutningsappar](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)som är   kompatibla med vårt API. Du kan använda anslutningsapparna för att konfigurera anpassade arbetsflöden för att effektivt indexera och extrahera insikter från en stor mängd video- och ljudfiler, utan att behöva skriva en enda rad med kod. Dessutom ger användning av anslutningsapparna för din integrering bättre insyn i arbetsflödets hälsotillstånd och ett enkelt sätt att felsöka det.  

För att hjälpa dig att snabbt komma igång med Video Indexer-anslutningsapparna, kommer vi att göra en genomgång av ett exempel på en logikapp Power Automate en lösning som du kan konfigurera. Den här självstudien visar hur du ställer in flöden med Logic Apps. Redigeringsprogram och funktioner är dock nästan identiska i båda lösningarna, vilket innebär att diagrammen och förklaringarna gäller för både Logic Apps och Power Automate.

Scenariot "ladda upp och indexera videon automatiskt" som tas upp i den här självstudien består av två olika flöden som fungerar tillsammans. 
* Det första flödet utlöses när en blob läggs till eller ändras i ett Azure Storage konto. Den laddar upp den nya filen till Video Indexer med en återanrops-URL för att skicka ett meddelande när indexeringsåtgärden har slutförts. 
* Det andra flödet utlöses baserat på motringning-URL:en och sparar de extraherade insikterna tillbaka till en JSON-fil i Azure Storage. Den här metoden med två flöden används för att effektivt stödja asynkron uppladdning och indexering av större filer. 

Den här självstudien använder Logikapp för att visa hur du:

> [!div class="checklist"]
> * Konfigurera filuppladdningsflödet
> * Konfigurera JSON-extraheringsflödet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Till att börja med behöver du ett Video Indexer-konto tillsammans med [åtkomst till API:erna via API-nyckeln](video-indexer-use-apis.md). 
* Du behöver också ett Azure Storage konto. Anteckna åtkomstnyckeln för lagringskontot. Skapa två containrar – en för att lagra videor i och en för att lagra insikter som genereras Video Indexer in.  
* Därefter måste du öppna två separata flöden på antingen Logic Apps eller Power Automate (beroende på vilken du använder). 

## <a name="set-up-the-first-flow---file-upload"></a>Konfigurera det första flödet – filuppladdning   

Det första flödet utlöses när en blob läggs till i din Azure Storage container. När den har utlösts skapas en SAS-URI som du kan använda för att ladda upp och indexera videon i Video Indexer. I det här avsnittet skapar du följande flöde. 

![Filuppladdningsflöde](./media/logic-apps-connector-tutorial/file-upload-flow.png)

För att konfigurera det första flödet måste du ange din API Video Indexer nyckel och autentiseringsuppgifter Azure Storage inloggningsuppgifter. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Anslutningsnamn och API-nyckel](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Om du tidigare har anslutit Azure Storage ett Video Indexer-konto till en logikapp lagras anslutningsinformationen och du ansluts automatiskt. <br/>Du kan redigera anslutningen  genom att klicka på Ändra anslutning längst ned i en Azure Storage (lagringsfönstret) eller klicka Video Indexer (spelarfönstret).

När du kan ansluta till Azure Storage och Video Indexer-konton hittar och väljer du utlösaren "När en blob läggs till eller ändras" **i Logic Apps Designer**.

Välj den container där du vill placera dina videofiler. 

![Skärmbild som visar dialogrutan När en blob läggs till eller ändras där du kan välja en container.](./media/logic-apps-connector-tutorial/container.png)

Leta sedan reda på och välj åtgärden "Skapa SAS-URI efter sökväg". I dialogrutan för åtgärden väljer du Lista över filsökväg bland alternativen för dynamiskt innehåll.  

Lägg även till en ny parameter för "Shared Access Protocol". Välj HttpsOnly som värde för parametern .

![SAS-URI efter sökväg](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Fyll i [din kontoplats och](regions.md) [konto-ID för](./video-indexer-use-apis.md#account-id)att hämta   Video Indexer-kontotoken.

![Hämta kontoåtkomsttoken](./media/logic-apps-connector-tutorial/account-access-token.png)

För "Ladda upp video och index" fyller du i de obligatoriska parametrarna och video-URL:en. Välj "Lägg till ny parameter" och välj Motringnings-URL. 

![Ladda upp och indexera](./media/logic-apps-connector-tutorial/upload-and-index.png)

Nu lämnar du motringning-URL:en tom. Du lägger bara till det när du har avslutat det andra flödet där motringning-URL:en skapas. 

Du kan använda standardvärdet för de andra parametrarna eller ange dem efter dina behov. 

Klicka **på** Spara så går vi vidare och konfigurerar det andra flödet för att extrahera insikterna när uppladdningen och indexeringen har slutförts. 

## <a name="set-up-the-second-flow---json-extraction"></a>Konfigurera det andra flödet – JSON-extrahering  

Slutförandet av uppladdning och indexering från det första flödet skickar en HTTP-begäran med rätt motringnings-URL för att utlösa det andra flödet. Sedan hämtas de insikter som genereras av Video Indexer. I det här exemplet lagras utdata från indexeringsjobbet i Azure Storage.  Det är dock upp till dig vad du kan göra med utdata.  

Skapa det andra flödet separat från det första. 

![JSON-extraheringsflöde](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

För att konfigurera det här flödet måste du ange din API Video Indexer nyckel och autentiseringsuppgifter Azure Storage igen. Du måste uppdatera samma parametrar som du gjorde för det första flödet. 

För utlösaren visas ett fält för HTTP POST-URL. URL:en genereras inte förrän du har sparat ditt flöde. Du behöver dock URL:en så småningom. Vi kommer tillbaka till det här. 

Fyll i [din kontoplats och](regions.md) ditt [konto-ID](./video-indexer-use-apis.md#account-id)   för att Video Indexer din kontotoken.  

Gå till åtgärden "Hämta videoindex" och fyll i de obligatoriska parametrarna. För Video-ID lägger du till följande uttryck: triggerOutputs()['queries']['id'] 

![video indexer-åtgärdsinformation](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Det här uttrycket talar om för anslutningsverktyget att hämta video-ID:t från utdata från utlösaren. I det här fallet blir utdata från utlösaren utdata från "Ladda upp video och index" i din första utlösare. 

Gå till åtgärden "Skapa blob" och välj sökvägen till den mapp där du vill spara insikterna. Ange namnet på bloben som du skapar. För Blob-innehåll lägger du till följande uttryck: body('Get_Video_Index') 

![Skapa blobåtgärd](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Det här uttrycket tar utdata från åtgärden "Hämta videoindex" från det här flödet. 

Klicka **på Spara flöde.** 

När flödet har sparats skapas en HTTP POST-URL i utlösaren. Kopiera URL:en från utlösaren. 

![Spara URL-utlösare](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Gå nu tillbaka till det första flödet och klistra in URL:en i åtgärden "Ladda upp video och index" för parametern Motringnings-URL. 

Se till att båda flödena har sparats och att du är redo! 

Prova din nyligen skapade logikapp eller Power Automate-lösning genom att lägga till en video i azure-blobcontainern och gå tillbaka några minuter senare för att se att insikterna visas i målmappen. 

## <a name="generate-captions"></a>Generera undertexter

Se följande blogg för de steg som visar [hur du skapar bildtexter med Video Indexer och Logic Apps](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

Artikeln visar också hur du indexerar en video automatiskt genom att kopiera den till OneDrive och hur du lagrar undertexter som genereras av Video Indexer i OneDrive.
 
## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien kan du behålla den här logikappen eller Power Automate-lösningen igång om du behöver. Men om du inte vill fortsätta att köra detta och inte vill debiteras inaktiverar du båda dina flöden om du använder Power Automate. Inaktivera båda flödena om du använder Logic Apps. 

## <a name="next-steps"></a>Nästa steg

Den här självstudien visade bara Video Indexer exempel på anslutningsappar. Du kan använda anslutningsapparna Video Indexer API-anrop som tillhandahålls av Video Indexer. Till exempel: ladda upp och hämta insikter, översätta resultaten, hämta inbäddade widgetar och till och med anpassa dina modeller. Dessutom kan du välja att utlösa dessa åtgärder baserat på olika källor, till exempel uppdateringar av fildatabaser eller e-postmeddelanden som skickas. Du kan sedan välja att uppdatera resultaten till vår relevanta infrastruktur eller program eller generera val annat antal åtgärdsobjekt.  

> [!div class="nextstepaction"]
> [Använda Video Indexer-API:et](video-indexer-use-apis.md)

Ytterligare resurser finns i det här dokumentet om [videoindexerare.](/connectors/videoindexer-v2/)
