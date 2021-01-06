---
title: Vad är nytt – Language Understanding (LUIS)
description: Den här artikeln uppdateras regelbundet med nyheter om Azure Cognitive Services API för Language Understanding.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 01/05/2021
ms.openlocfilehash: 4591bc26c848032d1c0f8b96720c4ec66b9b064d
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916362"
---
# <a name="whats-new-in-language-understanding"></a>Vad är nytt i Language Understanding

Läs om vad som är nytt i tjänsten. Dessa objekt innehåller viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="release-notes"></a>Viktig information

### <a name="december-2020"></a>December 2020

* Alla LUIS-användare krävs för att [migrera till en Luis Authoring-resurs](luis-migration-authoring.md)
* Nya [utvärderings slut punkter](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) som gör att du kan skicka batch-tester usting REST API och få precisions resultat för dina avsikter och entiteter. Tillgängligt från och med v 3.0 – för hands version LUIS-slutpunkten.

### <a name="june-2020"></a>Juni 2020

* [Preview 3,0-redigering](luis-migration-authoring-entities.md) Gränssnittet
    * Version 3.2.0 – för hands version. 3- [.net-NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Version 4.0.0 – för hands version. 3- [JS-NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Använda DevOps-metoder med LUIS
    * Begrepp
        * [DevOps-metoder för LUIS](luis-concept-devops-sourcecontrol.md)
        * [Kontinuerlig integrering och kontinuerliga leverans arbets flöden för LUIS-DevOps](luis-concept-devops-automation.md)
        * [Testa för LUIS-DevOps](luis-concept-devops-testing.md)
    * Så här gör du
        * [Använd DevOps-program för LUIS-utveckling med GitHub-åtgärder](luis-how-to-devops-with-github.md)
    * [Fullständig kod GitHub lagrings platsen](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>2020 maj-build

* Publicerad som **allmänt tillgänglig** (ga):
    * [Language Understanding behållare](luis-container-howto.md)
    * För hands versionen av portalen upphöjt till den [aktuella portalen](https://www.luis.ai), den [tidigare](https://previous.luis.ai) portalen är fortfarande tillgänglig
    * Nya skapande och etiketter för skapande av enheter i maskin inlärning
    * [Uppgradera processen](migrate-from-composite-entity.md) från sammansatta och enkla entiteter till Machine Learning-entiteter
    * [Ställer in](how-to-application-settings-portal.md) stöd för normaliserade Word-varianter
* Förhandsgranska redigering av API-ändringar
    * App schema 7. x för kapslade enheter för maskin inlärning
    * [Migrering till nödvändig funktion](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nya resurser för utvecklare
    * [Verktyg för kontinuerlig integrering](developer-reference-resource.md#continuous-integration-tools)
    * Workshop – lär dig metod tips för [ _naturliga language Understanding_ (NLU) med Luis](developer-reference-resource.md#workshops)
* [Kundhanterade nycklar](luis-encryption-of-data-at-rest.md) – kryptera alla data du använder i Luis genom att använda din egen nyckel
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video) – Se de nya funktionerna i Luis



### <a name="march-2020"></a>Mars 2020

* TLS 1,2 upprätthålls nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services Security](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 november 2019-antändning

* NLU- [modeller (video avancerade naturliga language Understanding) med Luis och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Förbättrad produktivitet för utvecklare
    * Allmän tillgänglighet för vår [förutsägelse slut punkt v3](luis-migration-api-v3.md).
    * Möjlighet att importera och exportera appar med `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) format. På så sätt kan du paves en effektiv CI/CD-process.
* Språk expansion
    * [Arabiska och hindi](luis-language-support.md) i offentlig för hands version.
* Fördefinierade modeller
    * [Fördefinierade domäner](luis-reference-prebuilt-domains.md) är nu allmänt tillgängliga (ga)
    * Japanska [fördefinierade entiteter](luis-reference-prebuilt-entities.md#japanese-entity-support) – ålder, valuta, tal och procent stöds inte i v3.
    * [Inbyggda italienska entiteter](luis-reference-prebuilt-entities.md#italian-entity-support) – ålder, valuta, dimension, nummer och procents ATS för en procentuell matchning har ändrats från v2.
* Förbättrad användar upplevelse i [Preview.Luis.AI Portal](https://preview.luis.ai) – förbättringar etiketting-upplevelse som möjliggör skapande och fel sökning av komplexa modeller. Testa självstudierna för Preview-portalen:
    * [Endast avsikter](tutorial-intents-only.md)
    * [Desammansättnings bar maskin inlärnings enhet](tutorial-machine-learned-entity.md)
* Förhands språk förstå funktioner – [skapa sofistikerade språk modeller](luis-concept-entity-types.md) med mindre ansträngning.
* Definiera Machine Learning-funktioner på modell nivå och aktivera modeller som ska användas som signaler till andra modeller, till exempel att använda entiteter som funktioner till avsikter och andra entiteter.
* Nya, utökade [gränser](luis-limits.md) – högre max för fras listor och total fraser, ny modell som funktions gränser
* Extrahera information från text i formatet djup hierarki-struktur, så att konversations program blir mer kraftfulla.

    ![enhets bild för maskin inlärning](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure Authoring-resurs – [migrera nu](luis-migration-authoring.md).
    * 500 appar per Azure-resurs
    * 100 versioner per app
* Turkiskt stöd för fördefinierade entiteter
* Italiensk support för datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* Uppdatera [identifierarna – text](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) till 1.2.3
    * Ålder, temperatur, dimension och valuta igenkänning i italienska.
    * Förbättra helg igenkänning på engelska för att beräkna tacksägelse datum baserade datum på ett korrekt sätt.
    * Förbättringar på franska datum/tid för att minska antalet falska positiva identifieringar av icke-datum-och icke-tidsentiteter.
    * Stöd för kalender/skola/räkenskapsår och akronymer på engelska DateRange.
    * Förbättrad telefonnummer på telefonnummer på kinesiska och japanska.
    * Förbättrat stöd för NumberRange på engelska.
    * Prestanda förbättringar.

### <a name="june-24-2019"></a>24 juni 2019

* [OrdinalV2-fördefinierad entitet](luis-reference-prebuilt-ordinal-v2.md) som stöd för sortering, till exempel nästa, föregående och sista. Endast engelsk kultur.

### <a name="may-6-2019---build-conference"></a>6 maj 2019-build-konferens

Följande funktioner släpptes på Build 2019-konferensen:

* [För hands version av v3 API migration guide](luis-migration-api-v3.md)
* [Förbättrad analys instrument panel](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md)
* [Dynamiska List enheter](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa entiteter](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Bloggar

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Videoklipp

### <a name="2019-ignite-videos"></a>2019 tänd videor

[Avancerade fysiska Language Understandings modeller (NLU) med LUIS och Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Bygg videor

[Så här använder du Azure Conversation AI för att skala din verksamhet för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Tjänstuppdateringar

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
