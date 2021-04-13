---
title: 'Snabbstart: Klientbibliotek för optisk teckenläsning för .NET'
description: I den här snabbstarten kommer du igång med klientbiblioteket för optisk teckenläsning för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327737"
---
<a name="HOLTop"></a>

Använd OCR-klientbiblioteket för att läsa tryckt och handskriven text från en bild.

[Referensdokumentation](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har din Azure-prenumeration skapar du en Visuellt innehåll-resurs för att skapa Visuellt innehåll resurs i Azure Portal för att <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll tjänsten. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Med Visual Studio skapar du ett nytt .NET Core-program. 

### <a name="install-the-client-library"></a>Installera klientbiblioteket 

När du har skapat ett nytt projekt installerar du klientbiblioteket genom att högerklicka på projektlösningen i **Solution Explorer** och välja **Hantera NuGet-paket.** I pakethanteraren som öppnas väljer du **Bläddra,** **markerar Inkludera förhandsversion** och söker efter `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Välj version `7.0.0` och sedan **Installera**. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en ny `dotnet new` konsolapp med namnet `computer-vision-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Ändra katalogen till den nyligen skapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla några varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I programkatalogen installerar du Visuellt innehåll för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), som innehåller kodexe exemplen i den här snabbstarten.

Öppna filen *Program.cs* i önskad redigerare eller IDE från projektkatalogen.

### <a name="find-the-subscription-key-and-endpoint"></a>Hitta prenumerationsnyckeln och slutpunkten

Gå till Azure-portalen. Om den Visuellt innehåll som du skapade  i avsnittet Förutsättningar har distribuerats klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar din prenumerationsnyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .** 

I programmets **Program-klass** skapar du variabler för din Visuellt innehåll prenumerationsnyckel och slutpunkt. Klistra in din prenumerationsnyckel och slutpunkt i följande kod där det anges. Slutpunkten Visuellt innehåll har formen `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Kom ihåg att ta bort prenumerationsnyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

I programmets -metod `Main` lägger du till anrop för de metoder som används i den här snabbstarten. Du kommer att skapa dessa senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Jag konfigurerade klienten som](?success=set-up-client#object-model) [jag stötte på ett problem med](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i OCR .NET SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Den här klassen behövs för alla Visuellt innehåll funktioner. Du instansierar den med din prenumerationsinformation och använder den för att göra de flesta avbildningsåtgärder.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Den här klassen innehåller ytterligare metoder för **ComputerVisionClient**.|

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med OCR-klientbiblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod i **klassen Program** instansierar du en klient med din slutpunkt och prenumerationsnyckel. Skapa ett **[ApiKeyServiceClientCredentials-objekt](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** med din prenumerationsnyckel och använd det med slutpunkten för att skapa ett **[ComputerVisionClient-objekt.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten som](?success=authenticate-client#read-printed-and-handwritten-text) [jag stötte på ett problem med](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

OCR-tjänsten kan läsa synlig text i en bild och konvertera den till en teckenström. Mer information om textigenkänning finns i [översikten över optisk teckenläsning (OCR).](../../overview-ocr.md) Koden i det här avsnittet använder den senaste Visuellt innehåll SDK-versionen för [Read 3.0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) och definierar en metod, , som använder klientobjektet för att identifiera och extrahera `BatchReadFileUrl` text i bilden.

> [!TIP]
> Du kan också extrahera text från en lokal bild. Se [ComputerVisionClient-metoder,](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) till exempel **ReadInStreamAsync**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) scenarier som rör lokala avbildningar.

### <a name="set-up-test-image"></a>Konfigurera testbild

I klassen **Program** sparar du en referens till URL:en för den bild som du vill extrahera text från. Det här kodfragmentet innehåller exempelbilder för både tryckt och handskriven text.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Anropa API:et Read

Definiera den nya metoden för att läsa text. Lägg till koden nedan, som anropar **metoden ReadAsync** för den angivna bilden. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i bilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Hämta läsresultat

Hämta sedan åtgärds-ID:t som returnerades från **ReadAsync-anropet** och använd det för att fråga tjänsten efter åtgärdsresultat. Följande kod kontrollerar åtgärden tills resultatet returneras. Den skriver sedan ut extraherade textdata till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Visa läsresultat

Lägg till följande kod för att parsa och visa hämtade textdata och slutför metoddefinitionen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Kör programmet

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Kör programmet genom att klicka **på knappen** Felsök längst upp i IDE-fönstret.

#### <a name="cli"></a>[CLI](#tab/cli)

Kör programmet från programkatalogen med `dotnet run` kommandot .

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. När du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du installerar OCR-klientbiblioteket och använder API:et Läsa. Härnäst kan du läsa mer om funktionerna i Api:et Läsa.

> [!div class="nextstepaction"]
>[Anropa Read-API:et](../../Vision-API-How-to-Topics/call-read-api.md)

* [Översikt över OCR](../../overview-ocr.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
