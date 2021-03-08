---
title: Snabb start för .NET-klient bibliotek
description: Använd ansikts klient biblioteket för .NET för att identifiera ansikten, hitta liknande (ansikts sökning efter bild), identifiera ansikten (ansikts igenkännings sökning) och migrera dina ansikts data.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: b06a35274082e09ec0973c5123b39935a93e354d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445517"
---
Kom igång med ansikts igenkänning med ansikts klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd ansikts klient biblioteket för .NET för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa en person grupp](#create-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)

[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Exempel](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Förutsättningar


* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Skapa ett nytt .NET Core-program med Visual Studio. 

### <a name="install-the-client-library"></a>Installera klient biblioteket 

När du har skapat ett nytt projekt installerar du klient biblioteket genom att högerklicka på projekt lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version** och söker efter `Microsoft.Azure.CognitiveServices.Vision.Face` . Välj version `2.6.0-preview.1` och **Installera** sedan. 

#### <a name="cli"></a>[CLI](#tab/cli)

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `face-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*. 

```console
dotnet new console -n face-quickstart
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

I program katalogen installerar du ansikts klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), som innehåller kod exemplen i den här snabb starten.


Från projekt katalogen öppnar du filen *program.cs* och lägger till följande `using` direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

I programmets **program** klass skapar du variabler för resursens nyckel och slut punkt.


> [!IMPORTANT]
> Gå till Azure-portalen. Om den ansikts resurs som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du kommer att implementera dessa senare.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i klient biblioteket Face .NET:

|Name|Beskrivning|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |

## <a name="code-examples"></a>Kodexempel

Kodfragmenten nedan visar hur du gör följande uppgifter med ansikts klient biblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa en person grupp](#create-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du en klient med din slut punkt och nyckel. Skapa ett **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** -objekt med din nyckel och Använd den med slut punkten för att skapa ett **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** -objekt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Deklarera hjälp programs fält

Följande fält behövs för flera av de ansikts åtgärder som du kommer att lägga till senare. Definiera följande URL-sträng i roten i **program** klassen. Den här URL: en pekar på en mapp med exempel bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

I **huvud** metoden definierar du strängar för att peka på olika typer av igenkännings modeller. Senare kan du ange vilken igenkännings modell som du vill använda för ansikts igenkänning. Se [Ange en igenkännings modell](../../Face-API-How-to-Topics/specify-recognition-model.md) för information om de här alternativen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

### <a name="get-detected-face-objects"></a>Hämta identifierade ansikts objekt

Skapa en ny metod för att identifiera ansikten. `DetectFaceExtract`Metoden bearbetar tre av avbildningarna på den angivna URL: en och skapar en lista över **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** -objekt i program minnet. Listan med **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** -värden anger vilka funktioner som ska extraheras. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Du kan också identifiera ansikten i en lokal bild. Se [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) -metoderna, till exempel **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Visa identifierade ansikts data

Resten av `DetectFaceExtract` metoden tolkar och skriver ut attributets data för varje identifierad ansikte. Varje attribut måste anges separat i det ursprungliga API-anropet för ansikts igenkänning (i **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** -listan). Följande kod bearbetar alla attribut, men du behöver troligen bara använda ett eller några få.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar ett enda identifierat ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar (ansikts sökning efter bild). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

### <a name="detect-faces-for-comparison"></a>Identifiera ytor för jämförelse

Börja med att definiera en andra ansikts identifierings metod. Du måste identifiera ansikten i bilder innan du kan jämföra dem, och den här identifierings metoden är optimerad för jämförelse åtgärder. Den extraherar inte detaljerade ansikts attribut som i avsnittet ovan och använder en annan igenkännings modell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Hitta matchningar

Följande metod identifierar ansikten i en uppsättning mål bilder och i en enda käll bild. Sedan jämförs de och hittar du alla mål bilder som liknar käll bilden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Skriv ut matchningar

Följande kod skriver ut matchnings informationen i-konsolen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Identifiera-åtgärden tar en bild av en person (eller flera personer) och söker efter identiteten för varje ansikte i bilden (ansikts igenkännings sökning). Den jämför alla identifierade ansikte till en **PersonGroup**, en databas med olika **person** objekt vars ansikts funktioner är kända. Du måste först skapa och träna en **PersonGroup** för att kunna identifiera åtgärden

### <a name="create-a-person-group"></a>Skapa en person grupp

Följande kod skapar en **PersonGroup** med sex olika **person** objekt. Den associerar varje **person** med en uppsättning exempel bilder, och sedan är det tågen att identifiera varje person genom ansikts egenskaper. **Person** -och **PersonGroup** -objekt används i åtgärder för att verifiera, identifiera och gruppera.

Deklarera en sträng variabel i roten av klassen för att representera ID: t för den **PersonGroup** som du skapar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Lägg till följande kod i en ny metod. Den här metoden kommer att utföra identifierings åtgärden. Det första kod blocket associerar namnen på personer med sina exempel bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Observera att den här koden definierar en variabel `sourceImageFileName` . Den här variabeln motsvarar käll bilden &mdash; för den bild som innehåller personer som ska identifieras.

Lägg sedan till följande kod för att skapa ett **person** objekt för varje person i ord listan och Lägg till ansikts data från lämpliga avbildningar. Varje **person** objekt är associerat med samma **PersonGroup** via dess unika ID-sträng. Kom ihåg att skicka variablerna `client` , `url` och `RECOGNITION_MODEL1` till den här metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Du kan också skapa en **PersonGroup** från lokala avbildningar. Se [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) -metoderna, till exempel **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Träna PersonGroup

När du har extraherat ansikts data från dina avbildningar och sorterat dem i olika **person** objekt måste du träna **PersonGroup** för att identifiera de visuella funktioner som är associerade med vart och ett av dess **person** objekt. Följande kod anropar metoden för asynkrona **tåg** och avsöker resultaten, och skriver ut statusen till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Ansikts-API körs på en uppsättning fördefinierade modeller som är statiska efter typ (modellens prestanda kommer inte att regress eller förbättras när tjänsten körs). Resultaten som modellen genererar kan ändras om Microsoft uppdaterar modellens Server del utan att migrera till en helt ny modell version. Om du vill dra nytta av en nyare version av en modell kan du träna om din **PersonGroup**, ange den nya modellen som en parameter med samma registrerings avbildningar.

Den här **person** gruppen och dess associerade **person** objekt är nu redo att användas i åtgärderna verifiera, identifiera eller gruppera.

### <a name="identify-faces"></a>Identifiera ansikten

Följande kod tar käll avbildningen och skapar en lista över alla ansikten som identifierats i bilden. Det här är de ansikten som kommer att identifieras mot **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Nästa kodfragment anropar åtgärden **IdentifyAsync** och skriver ut resultatet till-konsolen. Här försöker tjänsten matcha varje ansikte från käll avbildningen till en **person** i den aktuella **PersonGroup**. Detta avslutar din identifierings metod.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

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

Om du har skapat en **PersonGroup** i den här snabb starten och du vill ta bort den kör du följande kod i programmet:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definiera borttagnings metoden med följande kod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts klient biblioteket för .NET för att utföra grundläggande ansikts igenkännings aktiviteter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
