---
title: Snabb start för ansikts python-klient bibliotek
description: Använd ansikts klient biblioteket för python för att identifiera ansikten, hitta liknande (ansikts sökning efter bild) och identifiera ansikten (ansikts igenkännings sökning).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/10/2020
ms.author: pafarley
ms.openlocfilehash: 7fc1822c219c6c881e3d788f6b0e56675cecc466
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445414"
---
Kom igång med ansikts igenkänning med ansikts klient biblioteket för python. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd ansikts klient biblioteket för python för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Verifiera ansikten](#verify-faces)

[Referens dokumentation](/python/api/azure-cognitiveservices-vision-face/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Exempel](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Din python-installation ska innehålla [pip](https://pip.pypa.io/en/stable/). Du kan kontrol lera om du har pip installerat genom `pip --version` att köra på kommando raden. Hämta pip genom att installera den senaste versionen av python.
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera
 
### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt Python-skript &mdash; *QuickStart-File.py*, till exempel. Öppna den sedan i önskat redigerings program eller IDE och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py), som innehåller kod exemplen i den här snabb starten.

Skapa sedan variabler för resursens Azure-slutpunkt och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den ansikts resurs som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i ansikts python-klient biblioteket.

|Name|Beskrivning|
|---|---|
|[FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[FaceListOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |
|[ShapshotOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations)|Den här klassen hanterar ögonblicks bilds funktionerna. Du kan använda den för att tillfälligt Spara alla dina molnbaserade ansikts data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med ansikts klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en person grupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Verifiera ansikten](#verify-faces)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en klient med din slut punkt och nyckel. Skapa ett [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [FaceClient](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Följande kod identifierar en ansikts i en fjärran sluten avbildning. Den skriver ut identifierat ansiktes ID till-konsolen och lagrar den också i program minnet. Sedan identifierar den ansikten i en bild med flera personer och skriver ut sina ID: n till konsolen. Genom att ändra parametrarna i [detect_with_url](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) -metoden kan du returnera annan information med varje [DetectedFace](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

> [!TIP]
> Du kan också identifiera ansikten i en lokal bild. Se [FaceOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations) -metoder som **detect_with_stream**.

### <a name="display-and-frame-faces"></a>Visa och inrama ansikten

Följande kod matar ut den angivna bilden till visning och ritar rektanglar runt ansikten med hjälp av egenskapen DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![En ung kvinna med den röd rektangel ritad runt ansiktet](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar ett enda identifierat ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar (ansikts sökning efter bild). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

### <a name="find-matches"></a>Hitta matchningar

Kör först koden i avsnittet ovan ([identifiera ansikten i en bild](#detect-faces-in-an-image)) för att spara en referens till ett enda ansikte. Kör sedan följande kod för att få referenser till flera ansikten i en grupp bild.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Lägg sedan till följande kodblock för att hitta förekomster av den första ytan i gruppen. Se [find_similar](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) -metoden för att lära dig hur du ändrar det här beteendet.

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

> [!TIP]
> Du kan också skapa en **PersonGroup** från fjärranslutna avbildningar som URL: en refererar till. Se [PersonGroupPersonOperations](/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations) -metoder som **add_face_from_url**.

### <a name="train-persongroup"></a>Träna PersonGroup

När du har tilldelat ansikten måste du träna **PersonGroup** så att den kan identifiera de visuella funktioner som är associerade med var och en av dess **person** objekt. Följande kod anropar metoden för asynkrona **tåg** och avsöker resultatet, och skriver ut statusen till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

> [!TIP]
> Ansikts-API körs på en uppsättning fördefinierade modeller som är statiska efter typ (modellens prestanda kommer inte att regress eller förbättras när tjänsten körs). Resultaten som modellen genererar kan ändras om Microsoft uppdaterar modellens Server del utan att migrera till en helt ny modell version. Om du vill dra nytta av en nyare version av en modell kan du träna om din **PersonGroup**, ange den nya modellen som en parameter med samma registrerings avbildningar.

## <a name="identify-a-face"></a>Identifiera ett ansikte

Identifiera-åtgärden tar en bild av en person (eller flera personer) och söker efter identiteten för varje ansikte i bilden (ansikts igenkännings sökning). Den jämför alla identifierade ansikte till en **PersonGroup**, en databas med olika **person** objekt vars ansikts funktioner är kända.

> [!IMPORTANT]
> Om du ska kunna köra det här exemplet måste du först köra koden i [skapa och träna en person grupp](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Hämta en test avbildning

Följande kod söker i roten i projektet efter en bild _test-image-person-group.jpg_ och identifierar ansikten i bilden. Du kan hitta avbildningen med de avbildningar som används för **PersonGroup** -hantering: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifiera ansikten

**Identifiera** -metoden tar en matris med identifierade ansikten och jämför dem med en **PersonGroup**. Om den kan matcha ett identifierat ansikte till en **person** sparas resultatet. Den här koden skriver ut detaljerade matchnings resultat till-konsolen.

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

## <a name="run-the-application"></a>Kör programmet

Kör appen för ansikts igenkänning från program katalogen med `python` kommandot.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabb starten och du vill ta bort den kör du följande kod i skriptet:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts klient biblioteket för python för att utföra åtgärder för ansikts igenkänning. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (python)](/python/api/azure-cognitiveservices-vision-face/)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
