---
title: 'Snabb start: Azure-hanterings klient bibliotek för .NET'
description: I den här snabb starten ska du komma igång med Azures hanterings klient bibliotek för .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 7da10fbdff0b6771fcffb6cc303d5ab183df190a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948096"
---
[Referens dokumentation](/dotnet/api/overview/azure/cognitiveservices/management)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C#-krav

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program. I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `azure-management-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
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

I program katalogen installerar du Azure-hanterings klient biblioteket för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

### <a name="import-libraries"></a>Importera bibliotek

Öppna *program.cs* och Lägg till följande- `using` instruktioner överst i filen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till följande fält i roten av *program.cs* och fyll i deras värden med hjälp av tjänstens huvud namn som du skapade och din Azure konto information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Använd sedan de här värdena i **main** -metoden för att skapa ett **CognitiveServicesManagementClient** -objekt. Det här objektet krävs för alla dina Azure-hanterings åtgärder.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Anrops hanterings metoder

Lägg till följande kod i **main** -metoden för att lista tillgängliga resurser, skapa en exempel resurs, lista dina ägda resurser och ta sedan bort exempel resursen. Du definierar dessa metoder i nästa steg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Skapa en Cognitive Services resurs (C#)

Använd **create** -metoden för att skapa och prenumerera på en ny Cognitive Services-resurs. Med den här metoden läggs en ny fakturerbar resurs till i resurs gruppen som du skickar. När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats. Följande metod tar alla dessa som argument och skapar en resurs.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Välj en tjänst och en pris nivå

När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du använder denna och annan information som parametrar när du skapar resursen. Du kan hitta en lista över tillgängliga kognitiva tjänst typer genom att anropa följande metod i skriptet:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visa dina resurser

Använd följande metod om du vill visa alla resurser under ditt Azure-konto (i alla resurs grupper):

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Ta bort en resurs

Följande metod tar bort den angivna resursen från den angivna resurs gruppen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run
```

## <a name="see-also"></a>Se även

* [Dokumentation om Azure Management SDK-referens](/dotnet/api/overview/azure/cognitiveservices/management)
* [Vad är Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Autentisera begär anden till Azure Cognitive Services](../../authentication.md)
* [Skapa en ny resurs med Azure Portal](../../cognitive-services-apis-create-account.md)
