---
title: Snabb start för Content Moderator python-klient bibliotek
titleSuffix: Azure Cognitive Services
description: I den här snabb starten lär du dig att komma igång med Azure Content Moderator-klient biblioteket för python. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 06ff04d8615b7ebdda07e993a3fc560d44fbf702
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104018"
---
Kom igång med Azure Content Moderator-klient biblioteket för python. Följ dessa steg om du vill installera PiPy-paketet och prova exempel koden för grundläggande uppgifter. 

Content Moderator är en AI-tjänst som låter dig hantera innehåll som är potentiellt stötande, riskfylld eller på annat sätt olämpligt. Använd den AI-drivna Content moderatoring-tjänsten för att söka igenom text, bild och videor och tillämpa innehålls flaggor automatiskt. Sedan integrerar du din app med gransknings verktyget, en online moderator miljö för ett team av mänsklig granskare. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.

Använd Content Moderator klient bibliotek för python för att:

* Måttlig text
* Använda en anpassad villkors lista
* Måttliga bilder
* Använd en anpassad avbildnings lista
* Skapa en granskning

[Referens dokumentation](/python/api/overview/azure/cognitiveservices/contentmoderator)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)  |  [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Din python-installation ska innehålla [pip](https://pip.pypa.io/en/stable/). Du kan kontrol lera om du har pip installerat genom `pip --version` att köra på kommando raden. Hämta pip genom att installera den senaste versionen av python.
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" skapar du en Content moderator resurs "  target="_blank"> skapa en Content moderator resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Content Moderator. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.


## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera Content Moderator klient biblioteket med följande kommando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt Python-skript och öppna det i önskat redigerings program eller IDE. Lägg sedan till följande- `import` instruktioner överst i filen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py), som innehåller kod exemplen i den här snabb starten.

Skapa sedan variabler för resursens slut punkts plats och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Content Moderator resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator python-klient biblioteket.

|Name|Beskrivning|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)|Den här klassen krävs för alla Content Moderator-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations)|Den här klassen innehåller funktioner för att analysera bilder för innehåll som är olämpligt för barn, personlig information eller mänskliga ansikten.|
|[TextModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
[ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations)|Den här klassen innehåller funktionerna i gransknings-API: erna, inklusive metoder för att skapa jobb, anpassade arbets flöden och mänsklig granskning.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med Content Moderator klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Måttlig text](#moderate-text)
* [Använda en anpassad villkors lista](#use-a-custom-terms-list)
* [Måttliga bilder](#moderate-images)
* [Använd en anpassad avbildnings lista](#use-a-custom-image-list)
* [Skapa en granskning](#create-a-review)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en klient med din slut punkt och nyckel. Skapa ett [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Måttlig text

I följande kod används en Content Moderator-klient för att analysera text texten och skriva ut resultatet till-konsolen. Skapa först en **text_files/-** mapp i projekt roten och Lägg till en *content_moderator_text_moderation.txt* -fil. Lägg till din egen text i filen eller Använd följande exempel text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Lägg till en referens till den nya mappen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Lägg sedan till följande kod i python-skriptet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Använda en anpassad villkors lista

Följande kod visar hur du hanterar en lista med anpassade villkor för text redigering. Du kan använda klassen [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations) för att skapa en villkors lista, hantera enskilda villkor och Visa andra text texter mot den.

### <a name="get-sample-text"></a>Hämta exempel text

Om du vill använda det här exemplet måste du skapa en **text_files/-** mapp i projekt roten och lägga till en *content_moderator_term_list.txt* -fil. Den här filen ska innehålla organisk text som ska kontrol leras mot listan med villkor. Du kan använda följande exempel text:

```
This text contains the terms "term1" and "term2".
```

Lägg till en referens till mappen om du inte redan har definierat en.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Skapa en lista

Lägg till följande kod i python-skriptet för att skapa en anpassad villkors lista och spara dess ID-värde.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definiera List information

Du kan använda en listas ID för att redigera dess namn och beskrivning.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Lägg till en term i listan

Följande kod lägger till villkoren `"term1"` och `"term2"` i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Hämta alla villkor i listan

Du kan använda List-ID: t för att returnera alla villkor i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Uppdatera List indexet

När du lägger till eller tar bort villkor från listan måste du uppdatera indexet innan du kan använda den uppdaterade listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Skärm text mot listan

Huvud funktionerna i listan med anpassade villkor är att jämföra en text text mot listan och se om det finns några matchande villkor. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Ta bort en term från en lista

Följande kod tar bort termen `"term1"` från listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Ta bort alla villkor från en lista

Använd följande kod för att ta bort en lista över alla dess villkor.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Ta bort en lista

Använd följande kod för att ta bort en anpassad villkors lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Måttliga bilder

I följande kod används en Content Moderator-klient, tillsammans med ett [ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations) -objekt, för att analysera bilder för vuxen och vågat innehåll.

### <a name="get-sample-images"></a>Hämta exempel bilder

Definiera en referens till några bilder som ska analyseras.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Lägg sedan till följande kod för att iterera igenom dina avbildningar. Resten av koden i det här avsnittet hamnar inuti den här slingan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sök efter vuxen/vågat innehåll

Följande kod kontrollerar avbildningen på den angivna URL: en för vuxen eller vågat innehåll och skriver ut resultaten till-konsolen. Mer information om vad dessa villkor innebär finns i hand boken för [bild moderator](../../image-moderation-api.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Sök efter synlig text

Följande kod kontrollerar bilden för synligt text innehåll och skriver ut resultaten till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sök efter ansikten

Följande kod kontrollerar bilden för mänskliga ansikten och skriver ut resultatet i-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Använd en anpassad avbildnings lista

Följande kod visar hur du hanterar en anpassad lista över avbildningar för bild moderator. Den här funktionen är användbar om din plattform ofta tar emot instanser av samma uppsättning avbildningar som du vill ta ut. Genom att underhålla en lista över dessa avbildningar kan du förbättra prestandan. Med klassen [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations) kan du skapa en avbildnings lista, hantera enskilda bilder i listan och jämföra andra bilder mot den.

Skapa följande textvariabler för att lagra de bild-URL: er som du kommer att använda i det här scenariot.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Detta är inte själva listan, men en informellt lista med avbildningar som ska läggas till i `add images` avsnittet i koden.


### <a name="create-an-image-list"></a>Skapa en bild lista

Lägg till följande kod för att skapa en avbildnings lista och spara en referens till dess ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Lägga till avbildningar i en lista

Följande kod lägger till alla dina avbildningar i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definiera **add_images** Helper-funktionen någon annan stans i skriptet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Hämta bilder i listan

Följande kod skriver ut namnen på alla avbildningar i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Uppdatera List information

Du kan använda List-ID: t för att uppdatera namn och beskrivning för listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Hämta List information

Använd följande kod för att skriva ut den aktuella informationen om listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Uppdatera List indexet

När du har lagt till eller tagit bort avbildningar måste du uppdatera List indexet innan du kan använda det för att visa andra bilder.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Matcha bilder mot listan

Huvud funktionen i bild listor är att jämföra nya avbildningar och se om det finns några matchningar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Ta bort en bild från listan

Följande kod tar bort ett objekt från listan. I det här fallet är det en bild som inte matchar List kategorin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Ta bort alla avbildningar från en lista

Använd följande kod för att rensa en avbildnings lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Ta bort bild listan

Använd följande kod för att ta bort en avbildnings lista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Skapa en granskning

Du kan använda Content Moderator python-klient biblioteket för att mata in innehåll i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com) så att de mänskliga moderatorerna kan granska det. Mer information om gransknings verktyget finns i [konceptuell guide för gransknings verktyget](../../review-tool-user-guide/human-in-the-loop.md).

Följande kod använder klassen [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations) för att skapa en granskning, hämta dess ID och kontrol lera informationen efter att ha tagit emot mänsklig indata via gransknings verktygets webb Portal.

### <a name="get-review-credentials"></a>Hämta granska autentiseringsuppgifter

Logga först in på gransknings verktyget och hämta ditt grupp namn. Tilldela den sedan den aktuella variabeln i koden. Alternativt kan du skapa en slut punkt för återanrop för att ta emot uppdateringar om granskningens aktivitet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Skapa en bild granskning

Lägg till följande kod för att skapa och publicera en granskning för den angivna bild-URL: en. Koden sparar en referens till gransknings-ID: t. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Visa gransknings information

Använd följande kod för att kontrol lera informationen om en specifik granskning. När du har skapat granskningen kan du gå till gransknings verktyget själv och interagera med innehållet. Information om hur du gör detta finns i [instruktionen gransknings anvisningar](../../review-tool-user-guide/review-moderated-images.md). När du är klar kan du köra den här koden igen så hämtas resultatet från gransknings processen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Om du har använt en callback-slutpunkt i det här scenariot, ska den ta emot en händelse i följande format:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder det Content Moderator python-biblioteket för att utföra redigerings uppgifter. Läs sedan mer om hur du använder avbildningen eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
>[Bild moderator koncept](../../image-moderation-api.md)
