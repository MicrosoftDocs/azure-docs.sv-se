---
title: 'Snabb start: Azure-hanterings klient bibliotek för Node.js'
description: I den här snabb starten ska du komma igång med Azures hanterings klient bibliotek för Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 41f6c8e260968eacd04249b3f887d4865907df0d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879588"
---
[Referens dokumentation](/javascript/api/@azure/arm-cognitiveservices/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript-krav

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).
* Den aktuella versionen av [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```

Skapa en fil med namnet _index.js_ innan du fortsätter.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera följande NPM-paket:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Appens `package.json` fil kommer att uppdateras med beroenden.

### <a name="import-libraries"></a>Importera bibliotek

Öppna _index.js_ -skriptet och importera följande bibliotek.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till följande fält i roten i skriptet och fyll i deras värden med hjälp av tjänstens huvud namn som du skapade och din Azure konto information.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Lägg sedan till följande `quickstart` funktion för att hantera programmets huvud arbete. Det första blocket med kod skapar ett **CognitiveServicesManagementClient** -objekt med hjälp av de variabler för autentisering som du angav ovan. Det här objektet krävs för alla dina Azure-hanterings åtgärder.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Funktioner för att anropa hantering

Lägg till följande kod i slutet av din `quickstart` funktion för att lista tillgängliga resurser, skapa en exempel resurs, lista dina ägda resurser och ta sedan bort exempel resursen. Du definierar dessa funktioner i nästa steg.

## <a name="create-a-cognitive-services-resource-nodejs"></a>Skapa en Cognitive Services resurs (Node.js)

Använd funktionen **skapa** för att skapa och prenumerera på en ny Cognitive Services-resurs. Den här funktionen lägger till en ny fakturerbar resurs i resurs gruppen som du skickar. När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats. Följande funktion tar alla dessa argument och skapar en resurs.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Välj en tjänst och en pris nivå

När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du använder denna och annan information som parametrar när du skapar resursen. Följande funktion visar en lista över tillgängliga kognitiva tjänst typer.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visa dina resurser

Använd följande funktion om du vill visa alla resurser under ditt Azure-konto (i alla resurs grupper):

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Ta bort en resurs

Följande funktion tar bort den angivna resursen från den angivna resurs gruppen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Kör programmet

Lägg till följande kod längst ned i skriptet för att anropa din huvud `quickstart` funktion med fel hantering.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Sedan kör du programmet med kommandot i konsol fönstret `node` .

```console
node index.js
```

## <a name="see-also"></a>Se även

* Se **[autentisera begär anden till Azure Cognitive Services](../../authentication.md)** om hur du säkert arbetar med Cognitive Services.
* Se **[Vad är Azure Cognitive Services?](../../what-are-cognitive-services.md)** för att hämta en lista över olika kategorier i Cognitive Services.
* Se den **[naturliga språk supporten](../../language-support.md)** om du vill se en lista över naturliga språk som Cognitive Services stöder.
* Se **[använda Cognitive Services som behållare](../../cognitive-services-container-support.md)** för att förstå hur du använder Cognitive Services lokal.
* Se **[planera och hantera kostnader för Cognitive Services](../../plan-manage-costs.md)** för att beräkna kostnaden för att använda Cognitive Services.
* Mer information om hanterings-SDK finns i **[referens dokumentationen för Azure Management SDK](/javascript/api/@azure/arm-cognitiveservices/)** .
