---
title: Cognitive Services och Machine Learning
titleSuffix: Azure Cognitive Services
description: Lär dig hur Azure Cognitive Services passar ihop med andra Azure-erbjudanden för maskininlärning.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 77464ccfb5b80c84f091d8e6bc491908baed6486
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955011"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services och Machine Learning

Cognitive Services tillhandahåller funktioner för maskin inlärning för att lösa allmänna problem, till exempel analys av text för känslomässig sentiment eller analys av bilder för att identifiera objekt eller ansikten. Du behöver inte ha några speciella kunskaper om maskininlärning eller datavetenskap för att använda de här tjänsterna. 

[Cognitive Services](./what-are-cognitive-services.md) är en grupp tjänster, som var och en stöder olika, generaliserade förutsägelse funktioner. Tjänsterna är indelade i olika kategorier för att hjälpa dig att hitta rätt tjänst. 

|Tjänste kategori|Syfte|
|--|--|
|[Beslut](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Skapa appar som ger rekommendationer för välinformerade och effektiva beslut.|
|[Språk](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Tillåt att dina appar behandlar naturligt språk med fördefinierade skript, utvärderar känslor och lär sig att känna igen vad användare vill.|
|[Sök](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Lägg till API:er för Bing-sökresultat i dina appar och utnyttja möjligheten att kombinera miljarder olika webbsidor, bilder, videor och nyheter med ett enda API-anrop.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konvertera tal till text och text till tal som låter naturligt. Översätt från ett språk till ett annat och aktivera talarverifiering och -igenkänning.|
|[Visuellt innehåll](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Avkänning, identifiering, textning, indexering och moderering för bilder, videor och innehåll med digitala pennanteckningar.|
||||

Använd Cognitive Services när du:

* Kan använda en generaliserad lösning.
* Få åtkomst till lösningen från ett programmerings REST API eller SDK. 

Använd en annan lösning för maskin inlärning när du:

* Du måste välja algoritmen och behöva träna på mycket information.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Machine Learning är ett koncept där du kan samla data och en algoritm för att lösa ett särskilt behov. När data och algoritmen har tränats är utdata en modell som du kan använda igen med olika data. Den tränade modellen ger insikter som baseras på nya data. 

Processen för att skapa ett Machine Learning-system kräver viss kunskap om Machine Learning eller data vetenskap.

Maskin inlärning tillhandahålls med hjälp av [Azure Machine Learning (AML)-produkter och-tjänster](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context).

## <a name="what-is-a-cognitive-service"></a>Vad är en kognitiv tjänst?

En kognitiv tjänst tillhandahåller delar av eller alla komponenter i en maskin inlärnings lösning: data, algoritm och tränad modell. Dessa tjänster är avsedda att kräva allmän kunskap om dina data utan att behöva uppleva maskin inlärning eller data vetenskap. Dessa tjänster tillhandahåller både REST API-och språkbaserade SDK: er. Därför måste du ha programmerings språk kunskap för att använda tjänsterna.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hur ser Cognitive Services och Azure Machine Learning (AML) ut?

Båda har slut målet att använda artificiell intelligens (AI) för att förbättra affärs verksamheten, men hur var och en ger dem i respektive erbjudanden är olika. 

I allmänhet är mål grupperna olika:

* Cognitive Services är för utvecklare utan maskin inlärnings miljö.
* Azure Machine Learning skräddarsys för data forskare. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Hur skiljer sig en kognitiv tjänst från maskin inlärning?

En kognitiv tjänst tillhandahåller en tränad modell åt dig. Detta ger data och en algoritm tillsammans, som är tillgänglig från en REST API eller SDK. Du kan implementera den här tjänsten inom några minuter, beroende på ditt scenario.  En kognitiv tjänst ger svar på allmänna problem, till exempel nyckel fraser i text-eller objekt identifiering i bilder. 

Machine Learning är en process som vanligt vis kräver en längre tids period för att kunna implementeras. Den här tiden används för insamling, rensning, omvandling, val av algoritm, modell utbildning och distribution för att komma till samma funktions nivå som en kognitiv tjänst. Med Machine Learning är det möjligt att ge svar på mycket specialiserade och/eller specifika problem. Maskin inlärnings problem kräver att du känner till det speciella ämnet och data om problemet, samt kunskaper om data vetenskap.

## <a name="what-kind-of-data-do-you-have"></a>Vilken typ av data har du?

Cognitive Services, som en grupp tjänster, kan kräva ingen, vissa eller alla anpassade data för den tränade modellen. 

### <a name="no-additional-training-data-required"></a>Inga ytterligare tränings data krävs

Tjänster som tillhandahåller en fullständigt utbildad modell kan behandlas som en _täckande ruta_. Du behöver inte känna till hur de fungerar eller vilka data som användes för att träna dem. Du hämtar dina data till en helt utbildad modell för att få en förutsägelse. 

### <a name="some-or-all-training-data-required"></a>Vissa eller alla utbildnings data krävs

Med vissa tjänster kan du ta med dina egna data och sedan träna en modell. På så sätt kan du utöka modellen med hjälp av tjänstens data och algoritm med dina egna data. Resultatet motsvarar dina behov. När du hämtar dina egna data kan du behöva tagga data på ett sätt som är särskilt för tjänsten. Om du till exempel tränar en modell för att identifiera blommor kan du ange en katalog med blomma-bilder tillsammans med den blommas plats i varje bild för att träna modellen. 

En tjänst kan _göra det möjligt_ att tillhandahålla data för att förbättra sina egna data. En tjänst kan _kräva_ att du tillhandahåller data. 

### <a name="real-time-or-near-real-time-data-required"></a>Real tids-eller nästan real tids data krävs

En tjänst kan behöva real tids data eller nära real tids data för att bygga en effektiv modell. Dessa tjänster bearbetar betydande mängder modell data. 

## <a name="service-requirements-for-the-data-model"></a>Tjänst krav för data modellen

Följande data kategoriserar varje tjänst enligt vilken typ av data den tillåter eller kräver.

|Kognitiv tjänst|Inga tränings data krävs|Du anger några eller alla tränings data|Data insamling i real tid eller nästan i real tid|
|--|--|--|--|
|[Avvikelseidentifiering](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Visuellt innehåll](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Ansiktsigenkänning](./Face/Overview.md)|x|x||
|[Formigenkänning](./form-recognizer/overview.md)||x||
|[Avancerad läsare](./immersive-reader/overview.md)|x|||
|[Handskriftsigenkänning](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personanpassning](./personalizer/what-is-personalizer.md)|röntgen|röntgen|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Högtalar tolk](./speech-service/speaker-recognition-overview.md)||x||
|[Tal text till tal (TTS)](speech-service/text-to-speech.md)|x|x||
|[Tal-till-text (STT)](speech-service/speech-to-text.md)|x|x||
|[Talöversättning](speech-service/speech-translation.md)|x|||
|[Textanalys](./text-analytics/overview.md)|x|||
|[Översättare](./translator/translator-info-overview.md)|x|||
|[Translator-anpassad översättare](./translator/custom-translator/overview.md)||x||

* Personanpassa behöver bara träna data som samlas in av tjänsten (som de fungerar i real tid) för att utvärdera din princip och dina data. En personanpassare behöver inte ha stora historiska data uppsättningar för utbildning i front-eller batch. 

## <a name="where-can-you-use-cognitive-services"></a>Var kan du använda Cognitive Services?
 
Tjänsterna används i alla program som kan göra REST API-eller SDK-anrop. Exempel på program är Web Sites, robotar, Virtual eller Mixed Reality, Desktop-och Mobile-program. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hur är Azure Kognitiv sökning relaterat till Cognitive Services?

[Azure kognitiv sökning](../search/search-what-is-azure-search.md) är en separat moln Sök tjänst som används för att använda Cognitive Services för att lägga till avbildning och naturlig språk bearbetning för att indexera arbets belastningar. Cognitive Services visas i Azure Kognitiv sökning via [inbyggda kunskaper](../search/cognitive-search-predefined-skills.md) som omsluter enskilda API: er. Du kan använda en kostnads fri resurs för genom gångar, men planera att skapa och koppla en [fakturerbar resurs](../search/cognitive-search-attach-cognitive-services.md) för större volymer.

## <a name="how-can-you-use-cognitive-services"></a>Hur kan du använda Cognitive Services?

Varje tjänst ger information om dina data. Du kan kombinera tjänster tillsammans med lösningar som att konvertera tal (ljud) till text, översätta texten till flera språk och sedan använda de översatta språken för att få svar från en kunskaps bas. Även om Cognitive Services kan användas för att skapa intelligenta lösningar, kan de också kombineras med traditionella Machine Learning-projekt för att komplettera modeller eller påskynda utvecklings processen. 

Cognitive Services som innehåller exporterade modeller för andra Machine Learning-verktyg:

|Kognitiv tjänst|Modell information|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|[Exportera](./Custom-Vision-Service/export-model-python.md) för Tensorflow för Android, CoreML för IOS11, ONNX för Windows ml|

## <a name="learn-more"></a>Läs mer

* [Arkitektur guide – vilka är Machine Learning-produkter på Microsoft?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Maskin inlärning – introduktion till djup inlärning jämfört med Machine Learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Nästa steg

* Skapa ett kognitivt tjänst konto i [Azure Portal](cognitive-services-apis-create-account.md) eller med [Azure CLI](./cognitive-services-apis-create-account-cli.md).
* Lär dig hur du [autentiserar](authentication.md) till en kognitiv tjänst.
* Använd [diagnostisk loggning](diagnostic-logging.md) för problem identifiering och fel sökning. 
* Distribuera en kognitiv tjänst i en Docker- [behållare](cognitive-services-container-support.md).
* Håll dig uppdaterad med [tjänst uppdateringar](https://azure.microsoft.com/updates/?product=cognitive-services).