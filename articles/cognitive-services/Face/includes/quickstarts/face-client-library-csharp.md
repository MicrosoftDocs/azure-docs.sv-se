---
title: Snabb start för .NET-klient bibliotek
description: Kom igång med ansikts klient biblioteket för .NET med den här snabb starten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: c243668f4ca1569ad05567649ad4a2498888847c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88602393"
---
Kom igång med ansikts klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd ansikts klient biblioteket för .NET för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicks bild för datamigrering](#take-a-snapshot-for-data-migration)

[Referens dokumentation](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Exempel](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Förutsättningar

* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* När du har fått en nyckel och slut punkt [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en, med namnet respektive `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE. 

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `face-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```dotnetcli
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using` direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

I programmets `Main` metod skapar du variabler för resursens Azure-slutpunkt och nyckel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

I program katalogen installerar du ansikts klient biblioteket för .NET med följande kommando:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i klient biblioteket Face .NET:

|Name|Beskrivning|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Den här klassen hanterar ögonblicks bild funktionen. Du kan använda den för att tillfälligt Spara alla dina molnbaserade ansikts data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

Kodfragmenten nedan visar hur du gör följande uppgifter med ansikts klient biblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicks bild för datamigrering](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din ansikts nyckel och slut punkt, med namnet `FACE_SUBSCRIPTION_KEY` och `FACE_ENDPOINT` .

I en ny metod instansierar du en klient med din slut punkt och nyckel. Skapa ett **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** -objekt med din nyckel och Använd den med slut punkten för att skapa ett **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** -objekt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Du vill förmodligen anropa den här metoden i- `Main` metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Deklarera hjälp programs fält

Följande fält behövs för flera av de ansikts åtgärder som du kommer att lägga till senare. Definiera följande URL-sträng i roten för din klass. Den här URL: en pekar på en mapp med exempel bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definiera strängar för att peka på olika typer av igenkännings modeller. Senare kan du ange vilken igenkännings modell som du vill använda för ansikts igenkänning. Se [Ange en igenkännings modell](../../Face-API-How-to-Topics/specify-recognition-model.md) för information om de här alternativen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Lägg till följande metod anrop till din **main** -metod. Du definierar metoden härnäst. Den sista identifiera-åtgärden tar ett **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** -objekt, en bild-URL och en igenkännings modell.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Hämta identifierade ansikts objekt

I nästa kodblock `DetectFaceExtract` identifierar metoden ansikten i tre av bilderna på den angivna URL: en och skapar en lista över **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** -objekt i program minnet. Listan med **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** -värden anger vilka funktioner som ska extraheras. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visa identifierade ansikts data

Resten av `DetectFaceExtract` metoden tolkar och skriver ut attributets data för varje identifierad ansikte. Varje attribut måste anges separat i det ursprungliga API-anropet för ansikts igenkänning (i **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** -listan). Följande kod bearbetar alla attribut, men du behöver troligen bara använda ett eller några få.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar en enda identifierad ansikte (källa) och söker efter matchningar i en uppsättning andra ansikten (mål). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

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

Identifiera-åtgärden tar en bild av en person (eller flera personer) och söker efter identiteten för varje ansikte i bilden. Den jämför alla identifierade ansikte till en **PersonGroup**, en databas med olika **person** objekt vars ansikts funktioner är kända. Du måste först skapa och träna en **PersonGroup** för att kunna identifiera åtgärden

### <a name="create-and-train-a-person-group"></a>Skapa och träna en person grupp

Följande kod skapar en **PersonGroup** med sex olika **person** objekt. Den associerar varje **person** med en uppsättning exempel bilder, och sedan är det tågen att identifiera varje person genom ansikts egenskaper. **Person** -och **PersonGroup** -objekt används i åtgärder för att verifiera, identifiera och gruppera.

#### <a name="create-persongroup"></a>Skapa PersonGroup

Deklarera en sträng variabel i roten av klassen för att representera ID: t för den **PersonGroup** som du skapar.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Lägg till följande kod i en ny metod. Den här metoden kommer att utföra identifierings åtgärden. Det första kod blocket associerar namnen på personer med sina exempel bilder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Lägg sedan till följande kod för att skapa ett **person** objekt för varje person i ord listan och Lägg till ansikts data från lämpliga avbildningar. Varje **person** objekt är associerat med samma **PersonGroup** via dess unika ID-sträng. Kom ihåg att skicka variablerna `client` , `url` och `RECOGNITION_MODEL1` till den här metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Träna PersonGroup

När du har extraherat ansikts data från dina avbildningar och sorterat dem i olika **person** objekt måste du träna **PersonGroup** för att identifiera de visuella funktioner som är associerade med vart och ett av dess **person** objekt. Följande kod anropar metoden för asynkrona **tåg** och avsöker resultaten, och skriver ut statusen till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Den här **person** gruppen och dess associerade **person** objekt är nu redo att användas i åtgärderna verifiera, identifiera eller gruppera.

### <a name="get-a-test-image"></a>Hämta en test avbildning

Observera att koden för att [skapa och träna en person grupp](#create-and-train-a-person-group) definierar en variabel `sourceImageFileName` . Den här variabeln motsvarar käll bilden &mdash; för den bild som innehåller personer som ska identifieras.

### <a name="identify-faces"></a>Identifiera ansikten

Följande kod tar käll avbildningen och skapar en lista över alla ansikten som identifierats i bilden. Det här är de ansikten som kommer att identifieras mot **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Nästa kodfragment anropar åtgärden **IdentifyAsync** och skriver ut resultatet till-konsolen. Här försöker tjänsten matcha varje ansikte från käll avbildningen till en **person** i den aktuella **PersonGroup**. Detta avslutar din identifierings metod.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicks bild för datamigrering

Med funktionen ögonblicks bilder kan du flytta dina sparade ansikts data, till exempel en utbildad **PersonGroup**, till en annan Azure Cognitive Services Face-prenumeration. Du kanske vill använda den här funktionen om du till exempel har skapat ett **PersonGroup** -objekt med en kostnads fri prenumeration och vill migrera det till en betald prenumeration. Se [migrera dina ansikts data](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en översikt över ögonblicks bilds funktionen.

I det här exemplet ska du migrera **PersonGroup** som du skapade i [skapa och träna en person grupp](#create-and-train-a-person-group). Du kan antingen slutföra det avsnittet först eller skapa egna data konstruktioner att migrera.

### <a name="set-up-target-subscription"></a>Konfigurera mål prenumeration

Först måste du ha en andra Azure-prenumeration med en ansikts resurs. Du kan göra detta genom att följa stegen i avsnittet [Konfigurera inställningar](#setting-up) . 

Definiera sedan följande variabler i `Main` program metoden. Du måste skapa nya miljövariabler för prenumerations-ID: t för ditt Azure-konto, samt nyckel, slut punkt och prenumerations-ID för ditt nya (mål) konto. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

I det här exemplet ska du deklarera en variabel för ID: t för målet **PersonGroup** &mdash; objektet som tillhör den nya prenumerationen, som du kopierar dina data till.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Autentisera mål klient

Lägg sedan till koden för att autentisera din sekundära ansikts prenumeration.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Använd en ögonblicks bild

Resten av ögonblicks bild åtgärderna måste äga rum inom en asynkron metod. 

1. Det första steget är att **ta** ögonblicks bilden, som sparar din ursprungliga prenumerations ansikts data till en tillfällig moln plats. Den här metoden returnerar ett ID som du använder för att fråga efter status för åtgärden.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Fråga sedan ID: t tills åtgärden har slutförts.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Använd sedan **Apply** -åtgärden för att skriva dina ansikts data till mål prenumerationen. Den här metoden returnerar också ett ID-värde.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Fråga sedan det nya ID: t tills åtgärden har slutförts.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Slutligen avslutar du try/catch-blocket och slutför-metoden.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

Det nya **PersonGroup** -objektet ska nu ha samma data som det ursprungliga, och det bör vara tillgängligt från din nya (mål) Azure Face-prenumeration.

## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabb starten och du vill ta bort den kör du följande kod i programmet:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definiera borttagnings metoden med följande kod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Om du har migrerat data med hjälp av ögonblicks bild funktionen i den här snabb starten måste du dessutom ta bort **PersonGroup** som sparats till mål prenumerationen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts biblioteket för .NET för att utföra grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
