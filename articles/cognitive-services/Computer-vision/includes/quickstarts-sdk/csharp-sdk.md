---
title: 'Snabb start: klient bibliotek för optiskt teckensnitts igenkänning för .NET'
description: I den här snabb starten ska du komma igång med klient biblioteket för optiskt teckensnitts igenkänning för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 410ced99b1c5053c084921edf4d9bbde1a9443c4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073524"
---
<a name="HOLTop"></a>

Använd klient biblioteket för OCR för att läsa utskrift och handskriven text från en bild.

[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Skapa ett nytt .NET Core-program med Visual Studio. 

### <a name="install-the-client-library"></a>Installera klient biblioteket 

När du har skapat ett nytt projekt installerar du klient biblioteket genom att högerklicka på projekt lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version** och söker efter `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Välj version `6.0.0-preview.1` och **Installera** sedan. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `computer-vision-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *program. cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du Visuellt innehåll klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), som innehåller kod exemplen i den här snabb starten.

Från projekt katalogen öppnar du filen *program. cs* i önskat redigerings program eller IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Hitta prenumerations nyckeln och slut punkten

Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din prenumerations nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 

I programmets **program** klass skapar du variabler för din visuellt innehåll prenumerations nyckel och slut punkt. Klistra in prenumerations nyckeln och slut punkten i följande kod där det anges. Din Visuellt innehåll-slutpunkt har formuläret `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Kom ihåg att ta bort prenumerations nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

I programmets `Main` metod lägger du till anrop för de metoder som används i den här snabb starten. Du kommer att skapa dessa senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i OCR .NET SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Den här klassen innehåller ytterligare metoder för **ComputerVisionClient**.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med OCR-klientcertifikatet för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod i **program** klassen instansierar du en klient med din slut punkt och prenumerations nyckel. Skapa ett **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** -objekt med din prenumerations nyckel och Använd den med slut punkten för att skapa ett **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** -objekt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#read-printed-and-handwritten-text) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

OCR-tjänsten kan läsa synlig text i en bild och konvertera den till en tecken ström. Mer information om text igenkänning finns i Översikt över [OCR (optisk tecken läsning)](../../overview-ocr.md) . Koden i det här avsnittet använder den senaste [visuellt innehåll SDK-versionen för Read 3,0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) och definierar en metod, `BatchReadFileUrl` som använder klient objekt för att identifiera och extrahera text i avbildningen.

> [!TIP]
> Du kan också Extrahera text från en lokal avbildning. Se [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) -metoderna, till exempel **ReadInStreamAsync**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) för scenarier som involverar lokala avbildningar.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Spara en referens till URL: en för den avbildning som du vill extrahera text från i **program** klassen. Det här kodfragmentet innehåller exempel bilder för både utskrift och handskriven text.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Anropa Read API

Definiera den nya metoden för att läsa text. Lägg till koden nedan, som anropar **ReadAsync** -metoden för den aktuella avbildningen. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i avbildningen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Hämta Läs resultat

Hämta sedan det åtgärds-ID som returnerades från **ReadAsync** -anropet och Använd det för att fråga tjänsten efter åtgärds resultat. Följande kod kontrollerar åtgärden tills resultatet returneras. Den skriver sedan ut de extraherade text data till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Visa Läs resultat

Lägg till följande kod för att parsa och Visa hämtade text data och slutför metod definitionen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Kör programmet

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Kör programmet genom att klicka på knappen **Felsök** överst i IDE-fönstret.

#### <a name="cli"></a>[CLI](#tab/cli)

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du installerar klient biblioteket för OCR och använder Read API. Läs sedan mer om API-funktionerna för läsning.

> [!div class="nextstepaction"]
>[Anropa Read API](../../Vision-API-How-to-Topics/call-read-api.md)

* [Översikt över OCR](../../overview-ocr.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
