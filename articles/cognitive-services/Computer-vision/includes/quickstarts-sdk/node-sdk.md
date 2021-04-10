---
title: 'Snabb start: klient biblioteket för optiskt teckensnitts igenkänning för Node.js'
description: Kom igång med klient biblioteket för optiskt teckensnitts igenkänning för Node.js med den här snabb starten
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: cb4679152740b73d6bb9cf7288fcaa811b6d6141
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073442"
---
<a name="HOLTop"></a>

Använd det optiska tecken igenkännings klient biblioteket för att läsa tryckt och handskriven text med Read API.

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-computervision/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil.

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `ms-rest-azure` paketet och `@azure/cognitiveservices-computervision` NPM:

```console
npm install @azure/cognitiveservices-computervision
```

Installera även den asynkrona modulen:

```console
npm install async
```

Appens `package.json` fil kommer att uppdateras med beroenden.

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js), som innehåller kod exemplen i den här snabb starten.

Skapa en ny fil, *index.js* och öppna den i en text redigerare.

### <a name="find-the-subscription-key-and-endpoint"></a>Hitta prenumerations nyckeln och slut punkten

Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din prenumerations nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 

Skapa variabler för din Visuellt innehåll prenumerations nyckel och slut punkt. Klistra in prenumerations nyckeln och slut punkten i följande kod där det anges. Din Visuellt innehåll-slutpunkt har formuläret `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Kom ihåg att ta bort prenumerations nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i OCR-Node.js SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med klient biblioteket för OCR för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten


Instansiera en klient med din slut punkt och nyckel. Skapa ett [ApiKeyCredentials](/python/api/msrest/msrest.authentication.apikeycredentials) -objekt med din nyckel och slut punkt och Använd det för att skapa ett [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) -objekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

Definiera sedan en funktion `computerVision` och deklarera en async-serie med funktionen primär funktion och motringning. Du kommer att lägga till snabb starts koden i den primära funktionen och anropa `computerVision` längst ned i skriptet. Resten av koden i den här snabb starten går in i `computerVision` funktionen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#read-printed-and-handwritten-text) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

Med OCR-tjänsten kan du extrahera synlig text i en bild och konvertera den till en tecken ström. I det här exemplet används Läs åtgärder.

### <a name="set-up-test-images"></a>Konfigurera test avbildningar

Spara en referens till URL: en för de avbildningar som du vill extrahera text från.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

> [!NOTE]
> Du kan också läsa text från en lokal avbildning. Se [ComputerVisionClient](/javascript/api/@azure/cognitiveservices-computervision/computervisionclient) -metoderna, till exempel **readInStream**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) för scenarier som involverar lokala avbildningar.

### <a name="call-the-read-api"></a>Anropa Read API

Definiera följande fält i din funktion för att ange status värden för läsnings anrop.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_statuses)]

Lägg till koden nedan, som anropar `readTextFromURL` funktionen för de aktuella avbildningarna.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definiera `readTextFromURL` funktionen. Detta anropar metoden **Read** på klient objekt, som returnerar ett ÅTGÄRDS-ID och startar en asynkron process för att läsa innehållet i avbildningen. Sedan använder den åtgärds-ID: t för att kontrol lera åtgärds statusen tills resultatet returneras. De returnerar de extraherade resultaten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definiera sedan hjälp funktionen `printRecText` , som skriver ut resultaten av Läs åtgärder till-konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

> [!div class="nextstepaction"]
> [Jag](?success=read-printed-handwritten-text#run-the-application) [fick ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=read-printed-handwritten-text) med att läsa texten

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Javascript&Section=clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du installerar klient biblioteket för OCR och använder Read API. Läs sedan mer om API-funktionerna för läsning.

> [!div class="nextstepaction"]
>[Anropa Read API](../../Vision-API-How-to-Topics/call-read-api.md)

* [Översikt över OCR](../../overview-ocr.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
