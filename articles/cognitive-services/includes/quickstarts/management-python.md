---
title: 'Snabb start: Azure-hanterings klient bibliotek för python'
description: I den här snabb starten kommer du igång med Azures hanterings klient bibliotek för python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 074df0e79313a570e83ea72239394c1aa4946fc0
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956584"
---
[Referens dokumentation](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Paket (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Python-krav

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE och navigera till ditt projekt i ett konsol fönster.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Du kan installera klient biblioteket med:

```console
pip install azure-mgmt-cognitiveservices
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

### <a name="import-libraries"></a>Importera bibliotek

Öppna python-skriptet och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till följande fält i roten i skriptet och fyll i deras värden med hjälp av tjänstens huvud namn som du skapade och din Azure konto information.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Lägg sedan till följande kod för att skapa ett **CognitiveServicesManagementClient** -objekt. Det här objektet krävs för alla dina Azure-hanterings åtgärder.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Skapa en Cognitive Services resurs (python)

Använd funktionen **skapa** för att skapa och prenumerera på en ny Cognitive Services-resurs. Den här funktionen lägger till en ny fakturerbar resurs i resurs gruppen som du skickar. När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats. Följande funktion tar alla dessa argument och skapar en resurs.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Välj en tjänst och en pris nivå

När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du använder denna och annan information som parametrar när du skapar resursen. Följande funktion visar en lista över tillgängliga kognitiva tjänst typer.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visa dina resurser

Använd följande funktion om du vill visa alla resurser under ditt Azure-konto (i alla resurs grupper):

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Ta bort en resurs

Följande funktion tar bort den angivna resursen från den angivna resurs gruppen.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Funktioner för att anropa hantering

Lägg till följande kod längst ned i skriptet för att anropa ovanstående funktioner. Den här koden visar en lista över tillgängliga resurser, skapar en exempel resurs, listar dina ägda resurser och tar sedan bort exempel resursen.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet från kommando raden med `python` kommandot.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Se även

* [Dokumentation om Azure Management SDK-referens](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Vad är Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Autentisera begär anden till Azure Cognitive Services](../../authentication.md)
* [Skapa en ny resurs med Azure Portal](../../cognitive-services-apis-create-account.md)