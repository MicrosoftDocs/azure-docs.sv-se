---
title: Resurser för utvecklare – Language Understanding
description: SDK:er, REST-API:er, CLI, hjälper dig att Language Understanding (LUIS)-appar på ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787115"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST- och CLI-utvecklarresurser för Language Understanding (LUIS)

SDK:er, REST-API:er, CLI, hjälper dig att Language Understanding (LUIS)-appar på ditt programmeringsspråk. Hantera dina Azure-resurser och LUIS-förutsägelser.

## <a name="azure-resource-management"></a>Azure-resurshantering

Använd Azure Cognitive Services Management för att skapa, redigera, lista och ta bort Language Understanding eller Cognitive Service-resursen.

Hitta referensdokumentation baserat på verktyget:

* [Azure CLI](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding redigerings- och förutsägelsebegäranden

Tjänsten Language Understanding nås från en Azure-resurs som du behöver skapa. Det finns två resurser:

* Använd **redigeringsresursen** för träning för att skapa, redigera, träna och publicera.
* Använd **förutsägelsen** för körning för att skicka användarens text och ta emot en förutsägelse.

Läs mer om [V3-förutsägelseslutpunkten](luis-migration-api-v3.md).

Använd [Cognitive Services exempelkod för](https://github.com/Azure-Samples/cognitive-services-quickstart-code) att lära dig och använda de vanligaste uppgifterna.

### <a name="rest-specifications"></a>REST-specifikationer

[LUIS REST-specifikationerna,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)tillsammans med alla [Azure REST-specifikationer,](https://github.com/Azure/azure-rest-api-specs)är offentligt tillgängliga på GitHub.

### <a name="rest-apis"></a>REST API:er

Api:er för både redigering och förutsägelseslutpunkt är tillgängliga från REST API:er:

|Typ|Version|
|--|--|
|Redigering|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[förhandsversion V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Förutsägelse|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-slutpunkter

LUIS har för närvarande 2 typer av slutpunkter:

* **redigering på** träningsslutpunkten
* **frågeförutsägelse** på körningsslutpunkten.

|Syfte|URL|
|--|--|
|V2-redigering på träningsslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V3-redigering på träningsslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2-förutsägelse – alla förutsägelser på körningsslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3-förutsägelse – versionsförutsägelse för körningsslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3-förutsägelse – platsförutsägelse på körningsslutpunkt|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

I följande tabell förklaras parametrarna, som betecknas med kparenteser `{}` , i föregående tabell.

|Parameter|Syfte|
|--|--|
|`your-resource-name`|Azure-resursnamn|
|`q` eller `query`|ta emot text som skickas från klientprogram, till exempel chattrobot|
|`version`|Versionsnamn på 10 tecken|
|`slot`| `production` eller `staging`|

### <a name="rest-query-string-parameters"></a>REST-frågesträngsparametrar

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Appschema

[Appschemat](app-schema-definition.md) importeras och exporteras i `.json` ett - eller `.lu` -format.

### <a name="language-based-sdks"></a>Språkbaserade SDK:er

|Språk |Referensdokumentation|Paket|Snabbstarter|
|--|--|--|--|
|C#|[Redigering](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[Prognos](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet-redigering](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet-förutsägelse](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Frågeförutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Redigering och förutsägelse](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[Sdk](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Redigering och förutsägelse](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven-redigering](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-förutsägelse](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Redigering](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[Prognos](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM-redigering](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM-förutsägelse](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Prognos](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Redigering och förutsägelse](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Redigering](./client-libraries-rest-api.md?pivots=rest-api)<br>[Prognos](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Containers

Language Understanding (LUIS) tillhandahåller [en container](luis-container-howto.md) för att tillhandahålla lokala och inneslutna versioner av din app.

### <a name="export-and-import-formats"></a>Export- och importformat

Language Understanding ger möjlighet att hantera din app och dess modeller i JSON-format, (LUDown)-format och ett komprimerat paket `.LU` för Language Understanding containern.[](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)

Import och export av dessa format är tillgängligt från API:erna och från LUIS-portalen. Portalen tillhandahåller import och export som en del av listan appar och versioner.

## <a name="workshops"></a>Workshops

* GitHub: (Workshop) [Conversational-AI: NLU med LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Verktyg för kontinuerlig integrering

* GitHub: (förhandsversion) Utveckla [en LUIS-app med hjälp av DevOps-metoder](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) – Verktyg som stöder kontinuerlig integrering och distribution för NLU-tjänster.

## <a name="bot-framework-tools"></a>Bot Framework verktyg

Robotramverket är tillgängligt som [SDK på](https://github.com/Microsoft/botframework) flera olika språk och som en tjänst med hjälp av [Azure Bot Service](https://dev.botframework.com/).

Bot Framework innehåller [flera verktyg](https://github.com/microsoft/botbuilder-tools) för att hjälpa Language Understanding, inklusive:
* [Bot Framework emulator –](https://github.com/Microsoft/BotFramework-Emulator/releases) ett skrivbordsprogram som gör att robotutvecklare kan testa och felsöka robotar som skapats med hjälp av Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – ett integrerat utvecklingsverktyg för utvecklare och team med flera funktioner för att skapa robotar och konversationsupplevelser med Microsoft Bot Framework
* [Bot Framework exempel](https://github.com/microsoft/botbuilder-samples) – i #C, JavaScript, TypeScript och Python

## <a name="next-steps"></a>Nästa steg

* Läs mer om vanliga [HTTP-felkoder](luis-reference-response-codes.md)
* [Referensdokumentation](../../index.yml) för alla API:er och SDK:er
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) [and Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitiva containrar](../cognitive-services-container-support.md)
