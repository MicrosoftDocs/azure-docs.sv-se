---
title: Snabb start för ansikts python-klient bibliotek
description: Den här snabb starten hjälper dig att komma igång med ansikts klient biblioteket för python för att identifiera, hitta liknande, identifiera, verifiera och mycket annat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 8ec32c7bd0d977ea18e4ff66dc616c300851a633
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505902"
---
Kom igång med ansikts klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd ansikts klient biblioteket för python för att:

* Identifiera ansikten i en bild
* Hitta liknande ansikten
* Skapa och träna en person grupp
* Identifiera ett ansikte
* Verifiera ansikten
* Ta en ögonblicks bild för datamigrering

[Referens dokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Exempel](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Krav

* [Python 3.x](https://www.python.org/)
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* När du har fått en nyckel och slut punkt [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln och slut punkten, med namnet `FACE_SUBSCRIPTION_KEY` respektive `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurera
 
### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt Python-skript &mdash; *QuickStart-File.py*, till exempel. Öppna den sedan i önskat redigerings program eller IDE och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Skapa sedan variabler för resursens Azure-slutpunkt och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Du kan installera klient biblioteket med:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i ansikts python-klient biblioteket.

|Namn|Beskrivning|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Den här klassen hanterar ögonblicks bilds funktionerna. Du kan använda den för att tillfälligt Spara alla dina molnbaserade ansikts data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med ansikts klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Verifiera ansikten](#verify-faces)
* [Ta en ögonblicks bild för datamigrering](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din ansikts nyckel, med namnet `FACE_SUBSCRIPTION_KEY` .

Instansiera en klient med din slut punkt och nyckel. Skapa ett [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Följande kod identifierar en ansikts i en fjärran sluten avbildning. Den skriver ut identifierat ansiktes ID till-konsolen och lagrar den också i program minnet. Sedan identifierar den ansikten i en bild med flera personer och skriver ut sina ID: n till konsolen. Genom att ändra parametrarna i [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) -metoden kan du returnera annan information med varje [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) för fler identifierings scenarier.

### <a name="display-and-frame-faces"></a>Visa och inrama ansikten

Följande kod matar ut den angivna bilden till visning och ritar rektanglar runt ansikten med hjälp av egenskapen DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![En ung kvinna med den röd rektangel ritad runt ansiktet](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar ett enda identifierat ansikte och söker efter matchningar i en uppsättning andra ansikten. När en matchning hittas, skrivs Rectangle-koordinaterna för den matchade ytan till-konsolen. 

### <a name="find-matches"></a>Hitta matchningar

Kör först koden i avsnittet ovan ([identifiera ansikten i en bild](#detect-faces-in-an-image)) för att spara en referens till ett enda ansikte. Kör sedan följande kod för att få referenser till flera ansikten i en grupp bild.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Lägg sedan till följande kodblock för att hitta förekomster av den första ytan i gruppen. Se [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) -metoden för att lära dig hur du ändrar det här beteendet.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Skriv ut matchningar

Använd följande kod för att skriva ut matchnings informationen till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Skapa och träna en person grupp

Följande kod skapar en **PersonGroup** med tre olika **person** objekt. Den associerar varje **person** med en uppsättning exempel bilder, och sedan kan IT-tågen identifiera varje person. 

### <a name="create-persongroup"></a>Skapa PersonGroup

För att gå igenom det här scenariot måste du spara följande avbildningar till rot katalogen i projektet: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Den här gruppen med bilder innehåller tre uppsättningar ansikts bilder som motsvarar tre olika personer. Koden definierar tre **person** objekt och associerar dem med bildfiler som börjar med `woman` , `man` och `child` .

När du har konfigurerat dina avbildningar definierar du en etikett överst i skriptet för **PersonGroup** -objektet som du skapar.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Lägg sedan till följande kod längst ned i skriptet. Den här koden skapar en **PersonGroup** och tre **person** objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Tilldela ansikten till personer

Följande kod sorterar bilderna efter prefix, identifierar ansikten och tilldelar ansikten till varje **person** objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Träna PersonGroup

När du har tilldelat ansikten måste du träna **PersonGroup** så att den kan identifiera de visuella funktioner som är associerade med var och en av dess **person** objekt. Följande kod anropar metoden för asynkrona **tåg** och avsöker resultatet, och skriver ut statusen till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Följande kod tar en bild med flera ansikten och söker efter identiteten för varje person i avbildningen. Den jämför alla identifierade ansikte till en **PersonGroup**, en databas med olika **person** objekt vars ansikts funktioner är kända.

> [!IMPORTANT]
> Om du ska kunna köra det här exemplet måste du först köra koden i [skapa och träna en person grupp](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Hämta en test avbildning

Följande kod söker i roten i projektet efter en bild _test-image-person-group.jpg_ och identifierar ansikten i bilden. Du kan hitta avbildningen med de avbildningar som används för **PersonGroup** -hantering: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifiera ansikten

**Identifiera** -metoden tar en matris med identifierade ansikten och jämför dem med en **PersonGroup**. Om den kan matcha ett identifierat ansikte till en **person**sparas resultatet. Den här koden skriver ut detaljerade matchnings resultat till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verifiera ansikten

Verifierings åtgärden tar ett ansikts-ID och antingen ett annat ansikts-ID eller ett **person** objekt och avgör om de tillhör samma person.

Följande kod identifierar ansikten i två käll avbildningar och verifierar sedan dem mot en ansikts som identifieras från en mål avbildning.

### <a name="get-test-images"></a>Hämta test bilder

Följande kod block deklarerar variabler som pekar på käll-och mål avbildningarna för verifierings åtgärden.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Identifiera ansikten för verifiering

Följande kod identifierar ansikten i käll-och mål bilderna och sparar dem i variabler.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Få verifierings resultat

Följande kod jämför var och en av käll avbildningarna till mål avbildningen och skriver ut ett meddelande som anger om de tillhör samma person.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicks bild för datamigrering

Med funktionen ögonblicks bilder kan du flytta dina sparade ansikts data, till exempel en utbildad **PersonGroup**, till en annan Azure Cognitive Services Face-prenumeration. Du kanske vill använda den här funktionen om du till exempel har skapat ett **PersonGroup** -objekt med en kostnads fri prenumeration och nu vill migrera den till en betald prenumeration. Se [migrera dina ansikts data](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en bred översikt över ögonblicks bilds funktionen.

I det här exemplet ska du migrera **PersonGroup** som du skapade i [skapa och träna en person grupp](#create-and-train-a-person-group). Du kan antingen slutföra det avsnittet först eller använda dina egna data konstruktioner.

### <a name="set-up-target-subscription"></a>Konfigurera mål prenumeration

Först måste du ha en andra Azure-prenumeration med en ansikts resurs. Du kan göra detta genom att följa stegen i avsnittet [Konfigurera inställningar](#setting-up) . 

Skapa sedan följande variabler längst upp i skriptet. Du måste också skapa nya miljövariabler för prenumerations-ID: t för ditt Azure-konto, samt nyckel, slut punkt och prenumerations-ID för ditt nya (mål) konto. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autentisera mål klient

Senare i skriptet sparar du det aktuella klient objekt som käll klienten och autentiserar sedan ett nytt klient objekt för mål prenumerationen. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Använd en ögonblicks bild

Resten av ögonblicks bild åtgärderna sker i en asynkron funktion. 

1. Det första steget är att **ta** ögonblicks bilden, som sparar din ursprungliga prenumerations ansikts data till en tillfällig moln plats. Den här metoden returnerar ett ID som du använder för att fråga efter status för åtgärden.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Fråga sedan ID: t tills åtgärden har slutförts.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Den här koden använder `wait_for_operation` funktionen, som du bör definiera separat:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Gå tillbaka till den asynkrona funktionen. Använd åtgärden **tillämpa** för att skriva dina ansikts data till mål prenumerationen. Den här metoden returnerar också ett ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Använd sedan `wait_for_operation` funktionen för att fråga efter ID tills åtgärden har slutförts.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

När du har slutfört de här stegen kan du komma åt dina data konstruktioner från din nya (mål) prenumeration.

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabb starten och du vill ta bort den kör du följande kod i skriptet:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Om du har migrerat data med hjälp av ögonblicks bild funktionen i den här snabb starten måste du också ta bort **PersonGroup** som sparats till mål prenumerationen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts biblioteket för python för att utföra grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
