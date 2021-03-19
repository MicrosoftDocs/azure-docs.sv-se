---
title: Vad är nytt i API för textanalys
titleSuffix: Text Analytics - Azure Cognitive Services
description: Den här artikeln innehåller information om nya versioner och funktioner för Azure Cognitive Services Textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: a2b001d34d265c8e7246b03875c32168f2c5c962
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598906"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Vad är nytt i API:et för textanalys?

API för textanalys uppdateras regelbundet. Den här artikeln innehåller information om nya versioner och funktioner för att hålla dig uppdaterad med den senaste utvecklingen.

## <a name="march-2021"></a>Mars 2021

### <a name="general-api-updates"></a>Allmänna API-uppdateringar
* Version av New API v 3.1 – för hands version 4 som innehåller 
   * Ändringar i avyttrandet av utryckets JSON-svars text: 
      * `aspects` är nu `targets` och `opinions` är nu `assessments` . 
   * Ändringar i JSON-svars texten för det värdbaserade webb-API: t för Textanalys för hälso tillstånd: 
      * Det `isNegated` booleska namnet för ett identifierat enhets objekt för negation är inaktuellt och ersätts av kontroll av kontroll.
      * En ny egenskap `role` som heter är nu en del av den extraherade relationen mellan ett attribut och en entitet samt relationen mellan entiteter.  Detta lägger till information till den identifierade Relations typen.
   * Enhets länkning är nu tillgängligt som en asynkron aktivitet i `/analyze` slut punkten.
   * En ny `pii-categories` parameter är nu tillgänglig i `/pii` slut punkten.
      * Med den här parametern kan du ange Välj PII-entiteter och de som inte stöds som standard för inspråket.
* Uppdaterade klient bibliotek, som inkluderar asynkron analys och Textanalys för hälso åtgärder. Du kan hitta exempel på GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v 3.1 – för hands version. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Textanalys för hälso uppdateringar

* En ny modell version `2021-03-01` för `/health` slut punkten och den lokala behållaren som tillhandahåller
    * Ett namnbyte på `Gene` enhets typen till `GeneOrProtein` .
    * En ny `Date` entitetstyp.
    * Kontroll av identifiering som ersätter negation identifiering (endast tillgängligt i API v 3.1 – för hands version. 4).
    * En ny önskad `name` egenskap för länkade entiteter som normaliseras från olika Ontologies-och kodnings system (endast tillgängligt i API v 3.1 – för hands version. 4). 
* En ny behållar avbildning med tagg `3.0.015370001-onprem-amd64` och den nya modell versionen `2021-03-01` har frigjorts till behållar förhands gransknings databasen. 
* Textanalys för hälso behållar avbildningen kommer att flyttas till en ny databas nästa månad.  Se efter om det finns en e-postkommunikation på platsen för dess nya hem.
> [!div class="nextstepaction"]
> [Läs mer om Textanalys för hälsa](how-tos/text-analytics-for-health.md)
>

### <a name="text-analytics-resource-portal-update"></a>Uppdatera Textanalys resurs Portal
* **Bearbetade text poster** är nu tillgängliga som ett mått i avsnittet **övervakning** för din Textanalys-resurs i Azure Portal.  

## <a name="february-2021"></a>Februari 2021

* `2021-01-15`Modell versionen för PII-slutpunkten i [namngiven entitet igenkänning](how-tos/text-analytics-how-to-entity-linking.md) v 3.1 – för hands version. x, som innehåller 
  * Utökat stöd för 9 nya språk
  * Förbättrad AI-kvalitet för namngivna enhets kategorier för språk som stöds.
* Pris nivåerna S0 till S4 dras tillbaka den 8 mars 2021. Om du har en befintlig Textanalys resurs med hjälp av pris nivån S0 till S4, bör du uppdatera den för att använda [pris nivån](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)standard (S).
* [Behållaren för språk identifiering](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) är nu allmänt tillgänglig.
* v 2.1 i API: et dras tillbaka. 

## <a name="january-2021"></a>Januari 2021

* `2021-01-15`Modell versionen för [namngiven entitets igenkänning](how-tos/text-analytics-how-to-entity-linking.md) v3. x, som ger 
  * Expanderat språk stöd för [flera allmänna enhets kategorier](named-entity-types.md). 
  * Förbättrad AI-kvalitet för allmänna enhets kategorier för alla v3-språk som stöds. 

* `2021-01-05`Modell versionen för [språk identifiering](how-tos/text-analytics-how-to-language-detection.md), vilket ger ytterligare [språk stöd](language-support.md?tabs=language-detection).

Dessa modell versioner är för närvarande inte tillgängliga i regionen USA, östra. 

> [!div class="nextstepaction"]
> [Lär dig mer om den nya NER-modellen](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>December 2020

* [Uppdaterad pris](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) information för API för textanalys.

## <a name="november-2020"></a>November 2020

* En [ny slut punkt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) med API för textanalys v 3.1 – för hands version. 3 för den nya asynkrona [analys-API: n](how-tos/text-analytics-how-to-call-api.md?tabs=analyze), som stöder BATCHBEARBETNING för ner, PII och extraherings åtgärder för nyckel fraser.
* En [ny slut punkt](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) med API för textanalys v 3.1 – för hands version. 3 för nya asynkrona [textanalys för](how-tos/text-analytics-for-health.md) hälsovärdbaserade API: er med stöd för batchbearbetning.
* De båda nya funktionerna i listan ovan är bara tillgängliga i följande regioner `West US 2` : `East US 2` , `Central US` , `North Europe` och `West Europe` .
* Portugisiska (Brasilien) `pt-BR` stöds nu i [Attitydanalys](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x, från och med modell version `2020-04-01` . Den lägger till i det befintliga `pt-PT` stödet för portugisiska.
* Uppdaterade klient bibliotek, som inkluderar asynkron analys och Textanalys för hälso åtgärder. Du kan hitta exempel på GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v 3.1 – för hands version. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Oktober 2020

* Stöd för hindi för Attitydanalys v3. x, från och med modell version `2020-04-01` . 
* Modell version `2020-09-01` för v3/languages-slutpunkten som lägger till förbättrade språk identifierings-och precisions förbättringar.
* v3-tillgänglighet i Central Indien och Förenade Arabemiraten Nord.

## <a name="september-2020"></a>September 2020

### <a name="general-api-updates"></a>Allmänna API-uppdateringar

* Version av en ny URL för den offentliga för hands versionen av Textanalys v 3.1 som stöder uppdateringar av följande namngivna enhets igenkänning v3-slut punkter: 
    * `/pii` slut punkten innehåller nu den nya `redactedText` egenskapen i svars-JSON där identifierade PII-enheter i indatamängden ersätts av ett `*` för varje tecken i dessa entiteter.
    * `/linking` slut punkten innehåller nu `bingID` egenskapen i svars-JSON för länkade entiteter.
* Följande Textanalys för hands version av API-slutpunkter har dragits tillbaka den 4 september 2020:
    * v 2.1 – för hands version
    * v3.0-förhandsversion
    * v 3.0 – för hands version. 1
    
> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v 3.1 – för hands version. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Textanalys för uppdateringar av hälso container

Följande uppdateringar gäller endast september-versionen av Textanalys för hälso container.
* En ny behållar avbildning med tagg `1.1.013530001-amd64-preview` med den nya modell versionen `2020-09-03` har lanserats till behållar förhands gransknings databasen. 
* Den här modell versionen ger förbättringar av enhets igenkänning, förkortnings identifiering och tids fördröjnings förbättringar.

> [!div class="nextstepaction"]
> [Läs mer om Textanalys för hälsa](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Augusti 2020

### <a name="general-api-updates"></a>Allmänna API-uppdateringar

* Modell version `2020-07-01` för v3 `/keyphrases` - `/pii` och `/languages` slut punkter som lägger till:
    * Ytterligare myndigheter och landsspecifika [entiteter](named-entity-types.md?tabs=personal) för namngiven enhets igenkänning.
    * Stöd för norska och turkiska i Attitydanalys v3.
* Ett HTTP 400-fel kommer nu att returneras för v3 API-begäranden som överskrider [gränserna](concepts/data-limits.md)för publicerade data. 
* Slut punkter som returnerar en förskjutning stöder nu den valfria `stringIndexType` parametern, som justerar de returnerade `offset` och `length` värdena så att de matchar ett [sträng index schema](concepts/text-offsets.md)som stöds.

### <a name="text-analytics-for-health-container-updates"></a>Textanalys för uppdateringar av hälso container

Följande uppdateringar gäller endast augusti-versionen av Textanalys endast för hälso container.

* Ny modell version för Textanalys för hälso tillstånd: `2020-07-24`
* Ny URL för att skicka Textanalys för hälso tillstånd: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (Observera att det krävs en rensning av webbläsarens cacheminne för att kunna använda webb programmet demo som ingår i den här nya behållar avbildningen)

Följande egenskaper i JSON-svaret har ändrats:

* `type` har bytt namn till `category` 
* `score` har bytt namn till `confidenceScore`
* Entiteter i `category` fältet i JSON-utdata är nu i Pascal-fall. Följande entiteter har bytt namn:
    * `EXAMINATION_RELATION` har bytt namn till `RelationalOperator` .
    * `EXAMINATION_UNIT` har bytt namn till `MeasurementUnit` .
    * `EXAMINATION_VALUE` har bytt namn till `MeasurementValue` .
    * `ROUTE_OR_MODE` har bytt namn `MedicationRoute` .
    * Relations enheten `ROUTE_OR_MODE_OF_MEDICATION` har bytt namn till `RouteOfMedication` .

Följande entiteter har lagts till:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Relations extrahering
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Läs mer om Textanalys för hälso container](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Juli 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Textanalys för hälso container – för hands version av offentlig gated

Behållaren Textanalys för hälso tillstånd finns nu i för hands versionen av den offentliga övervisningen, vilket gör att du kan extrahera information från ostrukturerad engelsk text i kliniska dokument som: patient insugnings formulär, läkares anteckningar, forsknings handlingar och utsläpps sammanfattningar. För närvarande debiteras du inte för Textanalys för användning av hälso containern.

Behållaren erbjuder följande funktioner:

* Igenkänning av namngiven enhet
* Relations extrahering
* Länkning av entitet
* Negation

## <a name="may-2020"></a>Maj 2020

### <a name="text-analytics-api-v3-general-availability"></a>API för textanalys v3 allmän tillgänglighet

Text Analysis API v3 är nu allmänt tillgänglig med följande uppdateringar:

* Modell version `2020-04-01`
* Nya [data begränsningar](concepts/data-limits.md) för varje funktion
* [Språk stöd](language-support.md) för [Attitydanalys (sa) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) har uppdaterats
* Separat slut punkt för att länka entiteten 
* Ny "Address"-kategori i [namngiven entitets igenkänning (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nya under Kategorier i NER v3:
   * Plats-geografisk
   * Plats-strukturellt
   * Organisation – börs kurs
   * Organisation – medicin
   * Organisation – idrotts
   * Event-kultur
   * Händelse – naturlig
   * Händelse-idrotts

Följande egenskaper i JSON-svaret har lagts till:
   * `SentenceText` i Attitydanalys
   * `Warnings` för varje dokument 

Namnen på följande egenskaper i JSON-svaret har ändrats, i tillämpliga fall:

* `score` har bytt namn till `confidenceScore`
    * `confidenceScore` har två decimal punkter av precision. 
* `type` har bytt namn till `category`
* `subtype` har bytt namn till `subcategory`

> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>API för textanalys v 3.1 offentlig för hands version
   * Ny Attitydanalys funktion – [Utanser utvinning](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nytt personligt ( `PII` ) domän filter för skyddad hälso information ( `PHI` ).

> [!div class="nextstepaction"]
> [Läs mer om för hands versionen av API för textanalys v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-stöd för API för textanalys v3 offentlig för hands version

Som en del av den [enhetliga Azure SDK-versionen](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)är API för TEXTANALYS v3 SDK nu tillgängligt som en offentlig för hands version för följande programmeringsspråk:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Namngiven enhets igenkänning v3 offentlig för hands version

Ytterligare entitetstyper är nu tillgängliga i den namngivna enhets igenkännings tjänsten (NER) v3 offentlig för hands version eftersom vi expanderar identifieringen av allmänna och personliga informations enheter i text. Den här uppdateringen introducerar [modell versionen](concepts/model-versioning.md) `2020-02-01` , som innehåller:

* Igenkänning av följande allmänna entitetstyper (endast engelska):
    * PersonType
    * Produkt
    * Händelse
    * GPE (politisk entitet) som en undertyp under plats
    * Kvalifikation

* Igenkänning av följande entitets typer för personlig information (endast engelska):
    * Person
    * Organisation
    * Ålder som underordnad kvantitet
    * Datum som en undertyp under DateTime
    * E-post 
    * Telefonnummer (endast USA)
    * URL
    * IP-adress

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) för att identifiera entitets typer för personlig information (endast engelska)

* Separata slut punkter för [entitets igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) och [entitet länkning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Modell version](concepts/model-versioning.md) `2019-10-01` , som innehåller:
    * Utökad identifiering och kategorisering av entiteter som finns i text. 
    * Igenkänning av följande nya enhets typer:
        * Telefonnummer
        * IP-adress

Enhets länkning stöder engelska och spanska. Språk stöd för NER varierar beroende på enhets typen.

#### <a name="sentiment-analysis-v3-public-preview"></a>Attitydanalys v3 offentlig för hands version

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) för att analysera sentiment.
* [Modell version](concepts/model-versioning.md) `2019-10-01` , som innehåller:

    * Betydande förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning.
    * Automatisk etikettering för olika sentiment i text.
    * Sentiment analys och utdata på en dokument-och menings nivå. 

Det stöder engelska ( `en` ), japanska ( `ja` ), kinesiska (förenklad) `zh-Hans` , kinesiska (traditionell), `zh-Hant` franska (), `fr` italienska ( `it` ), spanska ( `es` ), nederländska ( `nl` ), portugisiska () och tyska (), `pt` `de` och är tillgängliga i följande regioner: `Australia East` , `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,, och. 

> [!div class="nextstepaction"]
> [Läs mer om Attitydanalys v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)  
* [Exempel på användarscenarier](text-analytics-user-scenarios.md)
* [Attitydanalys](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Språk identifiering](how-tos/text-analytics-how-to-language-detection.md)
* [Enhets igenkänning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrahering av nyckel fraser](how-tos/text-analytics-how-to-keyword-extraction.md)
