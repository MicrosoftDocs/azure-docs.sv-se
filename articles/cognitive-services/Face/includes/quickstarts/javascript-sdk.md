---
title: Snabb start för front skript för klient bibliotek
description: Använd ansikts klient biblioteket för Java Script för att identifiera ansikten, hitta liknande (ansikts sökning efter bild), identifiera ansikten (ansikts igenkännings sökning) och migrera dina ansikts data.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 16797a5bdb5ef5f2b5660a33e1788b8824f1ad8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722573"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Snabb start: ansikts klient bibliotek för Java Script

Kom igång med ansikts igenkänning med ansikts klient biblioteket för Java Script. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd ansikts klient bibliotek för Java Script för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa en person grupp](#create-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-face/?view=azure-node-latest)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Exempel](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den senaste versionen av [Node.js](https://nodejs.org/en/)
* När du har en Azure-prenumeration kan du [skapa en ansikts resurs](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
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

Installera `ms-rest-azure` och `azure-cognitiveservices-face` NPM-paketen:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Appens `package.json` fil kommer att uppdateras med beroenden.

Skapa en fil med namnet `index.js` och importera följande bibliotek:

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](), som innehåller kod exemplen i den här snabb starten.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Skapa variabler för resursens Azure-slutpunkt och nyckel. 

> [!IMPORTANT]
> Gå till Azure-portalen. Om den ansikts resurs som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) artikeln Cognitive Services.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i klient biblioteket Face .NET:

|Namn|Beskrivning|
|---|---|
|[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient?view=azure-node-latest) | Den här klassen representerar ditt tillstånd att använda ansikts tjänsten och du behöver den för alla ansikts funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. |
|[Ansiktsigenkänning](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest)|Den här klassen hanterar de grundläggande identifierings-och igenkännings aktiviteter som du kan göra med människo ansikten. |
|[DetectedFace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/detectedface?view=azure-node-latest)|Den här klassen representerar alla data som upptäcktes från ett enskilt ansikte i en bild. Du kan använda den för att hämta detaljerad information om FACET.|
|[FaceList](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/facelist?view=azure-node-latest)|Den här klassen hanterar molnbaserade **FaceList** -konstruktioner, som lagrar en stor uppsättning ansikten. |
|[PersonGroupPerson](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest)| Den här klassen hanterar molnbaserade **person** konstruktioner, som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest)| Den här klassen hanterar de **PersonGroup** -konstruktioner i molnet som lagrar en uppsättning med utvalda **person** objekt. |

## <a name="code-examples"></a>Kodexempel

Kodfragmenten nedan visar hur du gör följande uppgifter med ansikts klient biblioteket för .NET:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa en person grupp](#create-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), som innehåller kod exemplen i den här snabb starten.

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en klient med din slut punkt och nyckel. Skapa ett **[ApiKeyCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest)** -objekt med din nyckel och Använd den med slut punkten för att skapa ett **[FaceClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceclient?view=azure-node-latest)** -objekt.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Deklarera globala värden och hjälp funktion

Följande globala värden krävs för flera av ansikts åtgärder som du kommer att lägga till senare.

URL: en pekar till en mapp med exempel bilder. UUID: t fungerar som både namn och ID för den PersonGroup som du kommer att skapa.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

Du ska använda följande funktion för att vänta på att PersonGroup-utbildningen ska slutföras.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

### <a name="get-detected-face-objects"></a>Hämta identifierade ansikts objekt

Skapa en ny metod för att identifiera ansikten. `DetectFaceExtract`Metoden bearbetar tre av avbildningarna på den angivna URL: en och skapar en lista över **[DetectedFace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/detectedface?view=azure-node-latest)** -objekt i program minnet. Listan med **[FaceAttributeType](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceattributetype?view=azure-node-latest)** -värden anger vilka funktioner som ska extraheras. 

`DetectFaceExtract`Metoden tolkar och skriver ut attributets data för varje identifierad ansikte. Varje attribut måste anges separat i det ursprungliga API-anropet för ansikts igenkänning (i **[FaceAttributeType](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/faceattributetype?view=azure-node-latest)** -listan). Följande kod bearbetar alla attribut, men du behöver troligen bara använda ett eller några få.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> Du kan också identifiera ansikten i en lokal bild. Se [ansikts](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest) metoder som [DetectWithStreamAsync](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar ett enda identifierat ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar (ansikts sökning efter bild). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

### <a name="detect-faces-for-comparison"></a>Identifiera ytor för jämförelse

Börja med att definiera en andra ansikts identifierings metod. Du måste identifiera ansikten i bilder innan du kan jämföra dem, och den här identifierings metoden är optimerad för jämförelse åtgärder. Den extraherar inte detaljerade ansikts attribut som i avsnittet ovan och använder en annan igenkännings modell.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Hitta matchningar

Följande metod identifierar ansikten i en uppsättning mål bilder och i en enda käll bild. Sedan jämförs de och hittar du alla mål bilder som liknar käll bilden. Slutligen skrivs matchnings informationen ut till-konsolen.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Identifiera ett ansikte

[Identifiera](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/face?view=azure-node-latest#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) -åtgärden tar en bild av en person (eller flera personer) och söker efter identiteten för varje ansikte i bilden (ansikts igenkännings sökning). Den jämför alla identifierade ansikte till en [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest), en databas med olika [person](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/person?view=azure-node-latest) objekt vars ansikts funktioner är kända. Du måste först skapa och träna en [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest)för att kunna identifiera åtgärden.

### <a name="add-faces-to-person-group"></a>Lägg till ansikten i person gruppen

Skapa följande funktion för att lägga till ansikten i [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-person-group"></a>Vänta på utbildning av person grupp

Skapa följande hjälp funktion för att vänta på att person gruppen Slutför utbildningen.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-person-group"></a>Skapa en person grupp

Följande kod:
- Skapar en [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup?view=azure-node-latest)
- Lägger till ansikten i person gruppen genom att anropa `AddFacesToPersonGroup` , som du definierade tidigare.
- Tågen person gruppen.
- Identifierar ansikten i person gruppen.

Den här **person** gruppen och dess associerade **person** objekt är nu redo att användas i åtgärderna verifiera, identifiera eller gruppera.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Du kan också skapa en **PersonGroup** från lokala avbildningar. Se [PersonGroupPerson](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest) -metoderna, till exempel [AddFaceFromStream](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroupperson?view=azure-node-latest#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Huvudtillg

Slutligen skapar du `main` funktionen och anropar den.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts klient biblioteket för Java Script för att utföra åtgärder för ansikts igenkänning. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens (Java Script)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js).
