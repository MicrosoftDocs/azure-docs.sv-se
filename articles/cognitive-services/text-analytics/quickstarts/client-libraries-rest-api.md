---
title: 'Snabbstart: Textutvinning med hjälp Textanalys klientbibliotek'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att utföra attitydanalys och mycket mer med hjälp Textanalys API från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: aahi
keywords: textutvinning, attitydanalys, textanalys
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 527936e1da385a3622358fe12efa0f87af4b3fd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765102"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Snabbstart: Använd Textanalys klientbibliotek och REST API

Använd den här artikeln för att komma igång Textanalys klientbibliotek och REST API. Följ de här stegen om du vill prova exempelkod för att utvinna text:

* Attitydanalys
* Yttrandeutvinning
* Språkidentifiering
* Igenkänning av enhet
* Identifiering av personligt identifierande information
* Extrahering av nyckelfraser


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Den senaste stabila versionen av TEXTANALYS-API:et är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och oskadlig lagring av autentiseringsuppgifter för enkelhetens skull. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.
> * Om du vill använda Textanalys för hälsotillstånd eller asynkrona åtgärder kan du se exemplen på Github för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Den senaste stabila versionen av TEXTANALYS-API:et är `3.0` .
> * Koden i den här artikeln använder synkrona metoder och oskadlig lagring av autentiseringsuppgifter för enkelhetens skull. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.
Om du vill använda Textanalys för hälsotillstånd eller asynkrona åtgärder kan du se exemplen på Github för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Den senaste stabila versionen av TEXTANALYS-API:et är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och oskadlig lagring av autentiseringsuppgifter för enkelhetens skull. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan.
> * Du kan också köra den här versionen Textanalys klientbiblioteket [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Den senaste stabila versionen av Textanalys-API:et är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och lagring av o säkra autentiseringsuppgifter för enkelhetens skull. För produktionsscenarier rekommenderar vi att du använder batchade asynkrona metoder för prestanda och skalbarhet. Se referensdokumentationen nedan. Om du vill använda Textanalys för hälsotillstånd eller asynkrona åtgärder kan du se exemplen på Github för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Den senaste stabila versionen av Textanalys-API:et är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska en lösning](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Översikt över Textanalys](../overview.md)
* [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitetsigenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språkigenkänning](../how-tos/text-analytics-how-to-language-detection.md)
