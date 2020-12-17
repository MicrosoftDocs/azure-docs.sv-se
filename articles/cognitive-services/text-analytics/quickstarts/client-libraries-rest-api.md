---
title: 'Snabb start: text utvinning med Textanalys klient biblioteket'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att utföra sentiment analys med mera, med hjälp av API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: aahi
keywords: text utvinning, sentiment-analys, text analys
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: f93b7954861bf3351d2930132e1391e1f21b2a6f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655126"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Snabb start: Använd Textanalys klient bibliotek och REST API

Använd den här artikeln för att komma igång med Textanalys klient bibliotek och REST API. Följ dessa steg om du vill prova exempel kod för utvinnings text:

* Attitydanalys
* Åsikts utvinning
* Språkidentifiering
* Igenkänning av enhet
* Personlig identifiering av informations igenkänning
* Extrahering av nyckelfraser


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.
> * Om du vill använda textanalys för hälso tillstånd eller asynkrona åtgärder, se exemplen på GitHub för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.
Om du vill använda textanalys för hälso tillstånd eller asynkrona åtgärder, se exemplen på GitHub för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan.
> * Du kan också köra den här versionen av klient biblioteket för Textanalys [i webbläsaren](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. För produktions scenarier rekommenderar vi att du använder de grupperade asynkrona metoderna för prestanda och skalbarhet. Se referens dokumentationen nedan. Om du vill använda textanalys för hälso tillstånd eller asynkrona åtgärder, se exemplen på GitHub för [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) eller [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Den senaste stabila versionen av API för textanalys är `3.0` .
>    * Se till att du bara följer anvisningarna för den version som du använder.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska en lösning](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Översikt över Textanalys](../overview.md)
* [Sentiment-analys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Enhets igenkänning](../how-tos/text-analytics-how-to-entity-linking.md)
* [Identifiera språk](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Språk igenkänning](../how-tos/text-analytics-how-to-language-detection.md)
