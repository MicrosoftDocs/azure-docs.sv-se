---
title: 'Snabb start: klient bibliotek för optiskt teckensnitts igenkänning för go'
titleSuffix: Azure Cognitive Services
description: Kom igång med det optiska teckensnitts igenkännings klient biblioteket för att gå med i den här snabb starten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073714"
---
<a name="HOLTop"></a>

Använd klient biblioteket för OCR för att läsa utskrift och handskriven text från bilder.

[Referens dokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Paket](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den senaste versionen av [Go](https://golang.org/dl/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-go-project-directory"></a>Skapa en go-projektfil

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt, med namnet `my-app` och navigerar till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbets ytan kommer att innehålla tre mappar:

* **src** – den här katalogen kommer att innehålla källkod och paket. Alla paket som installeras med `go get` kommandot hamnar i den här katalogen.
* **pkg** – den här katalogen kommer att innehålla de kompilerade go-paket-objekten. De här filerna har ett `.a` fil namns tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när du kör `go install` .

> [!TIP]
> Läs mer om strukturen för en Go-arbetsyta i [språk dokumentationen för go](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om hur du ställer in `$GOPATH` och `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Installera klient biblioteket för go

Installera sedan klient biblioteket för Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Skapa ett Go-program 

Skapa sedan en fil i **src** -katalogen med namnet `sample-app.go` :

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), som innehåller kod exemplen i den här snabb starten.

Öppna `sample-app.go` i önskad IDE-eller text redigerare.

Deklarera en kontext i roten av ditt skript. Du behöver det här objektet för att köra de flesta Visuellt innehåll funktions anrop.

### <a name="find-the-subscription-key-and-endpoint"></a>Hitta prenumerations nyckeln och slut punkten

Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din prenumerations nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 

Skapa variabler för din Visuellt innehåll prenumerations nyckel och slut punkt. Klistra in prenumerations nyckeln och slut punkten i följande kod där det anges. Din Visuellt innehåll-slutpunkt har formuläret `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Kom ihåg att ta bort prenumerations nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

Därefter börjar du lägga till kod för att utföra olika OCR-åtgärder.

> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i OCR go SDK.

|Name|Beskrivning|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Den här klassen krävs för alla Visuellt innehåll funktioner, till exempel bild analys och text läsning. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Den här typen innehåller resultatet av en batch-Läs åtgärd. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med OCR-klientprogrammet för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har [skapat miljövariabler](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din visuellt innehåll nyckel och slut punkt, med namnet respektive `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` .

Skapa en `main` funktion och Lägg till följande kod i den för att instansiera en klient med din slut punkt och nyckel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#read-printed-and-handwritten-text) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

OCR-tjänsten kan läsa synlig text i en bild och konvertera den till en tecken ström. Koden i det här avsnittet definierar en funktion, `RecognizeTextReadAPIRemoteImage` som använder-klient objekt för att identifiera och extrahera utskrift eller handskriven text i bilden.

Lägg till exempel bild referensen och funktions anropet i din `main` funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Du kan också Extrahera text från en lokal avbildning. Se [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) -metoderna, till exempel **BatchReadFileInStream**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier som involverar lokala avbildningar.

### <a name="call-the-read-api"></a>Anropa Read API

Definiera den nya funktionen för att läsa text `RecognizeTextReadAPIRemoteImage` . Lägg till koden nedan, som anropar **BatchReadFile** -metoden för den aktuella avbildningen. Den här metoden returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i avbildningen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Hämta Läs resultat

Hämta sedan det åtgärds-ID som returnerades från **BatchReadFile** -anropet och Använd det med **GetReadOperationResult** -metoden för att fråga tjänsten efter åtgärds resultat. Följande kod kontrollerar åtgärden vid en sekunds intervall tills resultatet returneras. Den skriver sedan ut de extraherade text data till-konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Visa Läs resultat

Lägg till följande kod för att parsa och Visa hämtade text data och slutför funktions definitionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Jag](?success=read-printed-handwritten-text#run-the-application) [fick ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text) med att läsa texten

## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `go run` kommandot.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du installerar klient biblioteket för OCR och använder Read API. Läs sedan mer om API-funktionerna för läsning.

> [!div class="nextstepaction"]
>[Anropa Read API](../../Vision-API-How-to-Topics/call-read-api.md)

* [Översikt över OCR](../../overview-ocr.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
