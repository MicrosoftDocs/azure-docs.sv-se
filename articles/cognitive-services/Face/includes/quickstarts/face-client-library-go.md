---
title: Snabb start för klient bibliotek-go
description: Kom igång med ansikts klient biblioteket för go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: bb8f80baac5ce49db612a7d3a599d727b824bffc
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505899"
---
Kom igång med ansikts klient biblioteket för go. Följ de här stegen för att installera biblioteket och prova våra exempel för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd klient biblioteket för ansikts tjänsten för att gå till:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicks bild för datamigrering](#take-a-snapshot-for-data-migration)

[Referens dokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  [SDK-hämtning](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Krav

* Den senaste versionen av [Go](https://golang.org/dl/)
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* När du har fått en nyckel och slut punkt [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln och slut punkten, med namnet `FACE_SUBSCRIPTION_KEY` respektive `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-go-project-directory"></a>Skapa en go-projektfil

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt, med namnet `my-app` och navigerar till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbets ytan kommer att innehålla tre mappar:

* **src** – den här katalogen kommer att innehålla källkod och paket. Alla paket som är installerade med `go get` kommandot finns i den här mappen.
* **pkg** – den här katalogen kommer att innehålla de kompilerade go-paket-objekten. De här filerna har ett `.a` fil namns tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när du kör `go install` .

> [!TIP]
> Läs mer om strukturen för en Go-arbetsyta i [språk dokumentationen för go](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om hur du ställer in `$GOPATH` och `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Installera klient biblioteket för go

Installera sedan klient biblioteket för Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Skapa ett Go-program

Skapa sedan en fil i **src** -katalogen med namnet `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Öppna `sample-app.go` i önskad IDE-eller text redigerare. Lägg sedan till paket namnet och importera följande bibliotek:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Härnäst ska du börja lägga till kod för att utföra olika ansikts service åtgärder.

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i ansikts service go-klientcertifikatet.

|Namn|Beskrivning|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Den här klassen hanterar ögonblicks bild funktionen. Du kan använda den för att tillfälligt Spara alla dina molnbaserade ansikts data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

Dessa kod exempel visar hur du utför grundläggande uppgifter med klient biblioteket för ansikts tjänsten för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicks bild för datamigrering](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE] 
> Den här snabb starten förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din ansikts nyckel och din slut punkt, med namnet respektive `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .

Skapa en **huvud** funktion och Lägg till följande kod i den för att instansiera en klient med din slut punkt och nyckel. Du skapar ett **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** -objekt med din nyckel och använder det med slut punkten för att skapa ett **[klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** objekt. Den här koden instansierar också ett kontext objekt, vilket krävs för att skapa klient objekt. Den definierar också en fjärran sluten plats där några av exempel bilderna i den här snabb starten hittas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Lägg till följande kod i **main** -metoden. Den här koden definierar en fjärran sluten exempel bild och anger vilka ansikts funktioner som ska extraheras från avbildningen. Den anger också vilken AI-modell som ska användas för att extrahera data från identifierade ansikte. Se [Ange en igenkännings modell](../../Face-API-How-to-Topics/specify-recognition-model.md) för information om de här alternativen. Slutligen fungerar **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** -metoden på avbildningen och sparar resultatet i program minnet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visa identifierade ansikts data

Nästa kodblock tar det första elementet i matrisen med **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** -objekt och skriver ut dess attribut till-konsolen. Om du använde en avbildning med flera ansikten bör du iterera igenom matrisen i stället.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar en enda identifierad ansikte (källa) och söker efter matchningar i en uppsättning andra ansikten (mål). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

### <a name="detect-faces-for-comparison"></a>Identifiera ytor för jämförelse

Spara först en referens till den ansikte som du identifierade i avsnittet [identifiera ansikten i en bild](#detect-faces-in-an-image) . Den här ytan är källan.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ange sedan följande kod för att identifiera en uppsättning ansikten i en annan bild. Dessa ansikten är målet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Hitta matchningar

I följande kod används metoden **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** för att hitta alla mål ansikten som matchar källans ansikte.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Skriv ut matchningar

Följande kod skriver ut matchnings informationen i-konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Skapa och träna en person grupp

För att gå igenom det här scenariot måste du spara följande avbildningar till rot katalogen i projektet: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Den här gruppen med bilder innehåller tre uppsättningar med bilder med en bild som motsvarar tre olika personer. Koden definierar tre **PersonGroup person** objekt och associerar dem med bildfiler som börjar med `woman` , `man` och `child` .

### <a name="create-persongroup"></a>Skapa PersonGroup

När du har laddat ned dina avbildningar lägger du till följande kod längst ned i **huvud** metoden. Den här koden autentiserar ett **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** -objekt och använder det för att definiera en ny **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Skapa PersonGroup-personer

Nästa kod block autentiserar en **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** och använder den för att definiera tre nya **PersonGroup person** objekt. Dessa objekt representerar en enskild person i en uppsättning avbildningar.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Tilldela ansikten till personer

Följande kod sorterar bilderna efter prefixet, identifierar ansikten och tilldelar ansikten till varje respektive **PersonGroup person** -objekt, baserat på bild filens namn.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Träna PersonGroup

När du har tilldelat ansikten tränar du **PersonGroup** så att den kan identifiera de visuella funktioner som är associerade med vart och ett av dess **person** objekt. Följande kod anropar metoden för asynkrona **tåg** och avsöker resultatet, och skriver ut statusen till-konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Följande kod tar en bild med flera ansikten och söker efter identiteten för varje person i avbildningen. Den jämför alla identifierade ansikte till en **PersonGroup**, en databas med olika **person** objekt vars ansikts funktioner är kända.

> [!IMPORTANT]
> Om du ska kunna köra det här exemplet måste du först köra koden i [skapa och träna en person grupp](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Hämta en test avbildning

Följande kod söker i roten i projektet efter en avbildning _test-image-person-group.jpg_ och läser in den i program minnet. Du kan hitta avbildningen i samma lagrings platsen som de bilder som används i [skapa och träna en person grupp](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Identifiera käll ansikten i test avbildningen

Nästa kodblock använder normal ansikts igenkänning på test bilden för att hämta alla ansikten och spara dem i en matris.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifiera ansikten

**[Identifiera](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** -metoden tar matrisen med identifierade ansikten och jämför dem med den angivna **PersonGroup** (definieras och tränas i det tidigare avsnittet). Om den kan matcha ett identifierat ansikte till en **person** i gruppen sparas resultatet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Den här koden skriver sedan ut detaljerade matchnings resultat till-konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verifiera ansikten

Verifierings åtgärden tar ett ansikts-ID och antingen ett annat ansikts-ID eller ett **person** objekt och avgör om de tillhör samma person.

Följande kod identifierar ansikten i två käll bilder och verifierar sedan var och en av dem mot en ansikte som upptäcks från en mål avbildning.

### <a name="get-test-images"></a>Hämta test bilder

Följande kod block deklarerar variabler som pekar på mål-och käll avbildningarna för verifierings åtgärden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Identifiera ansikten för verifiering

Följande kod identifierar ansikten i käll-och mål bilderna och sparar dem i variabler.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Få verifierings resultat

Följande kod jämför var och en av käll avbildningarna till mål avbildningen och skriver ut ett meddelande som anger om de tillhör samma person.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicks bild för datamigrering

Med funktionen ögonblicks bilder kan du flytta dina sparade ansikts data, till exempel en utbildad **PersonGroup**, till en annan Azure Cognitive Services Face-prenumeration. Du kan använda den här funktionen om du till exempel har skapat ett **PersonGroup** -objekt med en kostnads fri prenumeration och nu vill migrera den till en betald prenumeration. Se [migrera dina ansikts data](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en bred översikt över ögonblicks bilds funktionen.

I det här exemplet ska du migrera **PersonGroup** som du skapade i [skapa och träna en person grupp](#create-and-train-a-person-group). Du kan antingen slutföra det avsnittet först eller använda dina egna data konstruktioner.

### <a name="set-up-target-subscription"></a>Konfigurera mål prenumeration

Först måste du ha en andra Azure-prenumeration med en ansikts resurs. Du kan göra detta genom att upprepa stegen i avsnittet [Konfigurera](#setting-up) . 

Skapa sedan följande variabler längst upp i **huvud** metoden. Du måste också skapa nya miljövariabler för prenumerations-ID: t för ditt Azure-konto, samt nyckel, slut punkt och prenumerations-ID för ditt nya (mål) konto.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Lägg sedan till ditt prenumerations-ID i en matris för nästa steg.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autentisera mål klient

Senare i skriptet, sparar du det ursprungliga klient objektet som käll klient och autentiserar sedan ett nytt klient objekt för mål prenumerationen. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Ta en ögonblicksbild

Nästa steg är att ta ögonblicks bilden med **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, som sparar din ursprungliga prenumerations ansikts data på en tillfällig moln plats. Den här metoden returnerar ett ID som du använder för att fråga efter status för åtgärden.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Fråga sedan ID: t tills åtgärden har slutförts.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Använd ögonblicks bilden

Använd åtgärden **[tillämpa](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** för att skriva dina nyligen överförda ansikts data till mål prenumerationen. Den här metoden returnerar också ett ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Fråga sedan detta ID igen tills åtgärden har slutförts.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

När du har slutfört de här stegen kan du komma åt dina ansikts data-konstruktioner från din nya (mål) prenumeration.

## <a name="run-the-application"></a>Kör programmet

Kör programmet Go med `go run [arguments]` kommandot från program katalogen.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabb starten och du vill ta bort den anropar du **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** -metoden. Om du har migrerat data med hjälp av ögonblicks bild funktionen i den här snabb starten måste du också ta bort **PersonGroup** som sparats till mål prenumerationen.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts biblioteket för att gå till grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
