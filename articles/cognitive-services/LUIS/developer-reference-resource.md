---
title: Utvecklings resurser – Language Understanding
description: 'SDK: er, REST API: er, CLI, hjälper dig att utveckla Language Understanding-appar (LUIS) i ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.'
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 08a1965aadf8f50625e54d7c5e3b3f6a44c0bbd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98946168"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST-och CLI-utvecklarresurser för Language Understanding (LUIS)

SDK: er, REST API: er, CLI, hjälper dig att utveckla Language Understanding-appar (LUIS) i ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.

## <a name="azure-resource-management"></a>Resurs hantering i Azure

Använd Azure-Cognitive Services hanterings lager för att skapa, redigera, Visa och ta bort Language Understanding eller kognitiva tjänst resurser.

Hitta referens dokumentation baserat på verktyget:

* [Azure CLI](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding redigerings-och förutsägelse begär Anden

Language Understandings tjänsten nås från en Azure-resurs som du måste skapa. Det finns två resurser:

* Använd **redigerings** resursen för utbildning för att skapa, redigera, träna och publicera.
* Använd **förutsägelse** för körning för att skicka användarens text och få en förutsägelse.

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Använd [Cognitive Services exempel kod](https://github.com/Azure-Samples/cognitive-services-quickstart-code) för att lära dig och använda de vanligaste uppgifterna.

### <a name="rest-specifications"></a>REST-specifikationer

[Luis rest-specifikationerna](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), tillsammans med alla [Azure rest-specifikationer](https://github.com/Azure/azure-rest-api-specs), är offentligt tillgängliga på GitHub.

### <a name="rest-apis"></a>REST API:er

Både redigerings-och förutsägelse slut punkts-API: er är tillgängliga från REST API: er:

|Typ|Version|
|--|--|
|Redigering|[2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Förhandsgranska v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Förutsägelse|[2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-slutpunkter

LUIS har för närvarande två typer av slut punkter:

* **redigering** av utbildnings slut punkten
* fråga **förutsägelse** på körnings slut punkten.

|Syfte|URL|
|--|--|
|V2-redigering av utbildnings slut punkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3-redigering av utbildnings slut punkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2-förutsägelse – alla förutsägelser på runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 förutsägelse-versioner förutsägelse på runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 förutsägelse av förutsägelser på runtime-slutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

I följande tabell förklaras parametrarna, med klammerparenteser `{}` , i föregående tabell.

|Parameter|Syfte|
|--|--|
|`your-resource-name`|Namn på Azure-resurs|
|`q` eller `query`|uttryck text som skickats från klient programmet, till exempel chatt-robot|
|`version`|namn på 10-siffrig version|
|`slot`| `production` eller `staging`|

### <a name="rest-query-string-parameters"></a>REST-parametrar för frågesträng

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Appschema

[Appens schema](app-schema-definition.md) importeras och exporteras i ett- `.json` eller- `.lu` format.

### <a name="language-based-sdks"></a>Språkbaserade SDK: er

|Språk |Referens dokumentation|Paket|Snabbstarter|
|--|--|--|--|
|C#|[Redigering](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Förutsägelse](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet-redigering](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet förutsägelse](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Fråga förutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Redigering och förutsägelse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Redigering och förutsägelse](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven-redigering](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven förutsägelse](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Redigering](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Förutsägelse](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM-redigering](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM förutsägelse](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Förutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Redigering och förutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Förutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Containers

Language Understanding (LUIS) innehåller en [behållare](luis-container-howto.md) för att tillhandahålla lokala och befintliga versioner av din app.

### <a name="export-and-import-formats"></a>Exportera och importera format

Language Understanding ger möjlighet att hantera din app och dess modeller i ett JSON-format, `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown))-format och ett komprimerat paket för language Understanding containern.

Import och export av dessa format är tillgängligt från API: erna och från LUIS-portalen. Portalen innehåller import och export som en del av listan över appar och versioner.

## <a name="workshops"></a>Workshops

* GitHub: (Workshop) [konversational-AI: NLU med Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Verktyg för kontinuerlig integrering

* GitHub: (för hands version) [utveckla en Luis-app med hjälp av DevOps-metoder](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -verktyg som stöder kontinuerlig integrering och distribution av NLU-tjänster.

## <a name="bot-framework-tools"></a>Verktyg för bot Framework

Bot Framework är tillgängligt som [en SDK](https://github.com/Microsoft/botframework) på en rad olika språk och som en tjänst med hjälp av [Azure bot service](https://dev.botframework.com/).

Bot Framework innehåller [flera verktyg](https://github.com/microsoft/botbuilder-tools) som hjälper dig med language Understanding, inklusive:
* [Bot Framework-emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) – ett Skriv bords program som gör det möjligt för bot-utvecklare att testa och felsöka robotar som skapats med bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – ett integrerat utvecklingsverktyg för utvecklare och flera disciplin team för att bygga robotar och konversations upplevelser med Microsoft bot Framework
* [Robot Framework-exempel](https://github.com/microsoft/botbuilder-samples) – i #C, Java Script, typescript och python

## <a name="next-steps"></a>Nästa steg

* Läs mer om vanliga [http-felkoder](luis-reference-response-codes.md)
* [Referens dokumentation](../../index.yml) för alla API: er och SDK: er
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) och [Azure bot service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitiva behållare](../cognitive-services-container-support.md)
