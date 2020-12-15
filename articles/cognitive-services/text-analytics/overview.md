---
title: Text utvinning och analys med API för textanalys Azure-Cognitive Services
titleSuffix: Azure Cognitive Services
description: Lär dig mer om text utvinning med API för textanalys. Använd den för sentiment-analys, språk identifiering och andra former av naturlig språk bearbetning.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/17/2020
ms.author: aahi
keywords: text utvinning, sentiment-analys, text analys
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 2d8ebe87cdf1d11ae4d9c9050ba1ac924089aa33
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505621"
---
# <a name="what-is-the-text-analytics-api"></a>Vad är API för textanalys?

API för textanalys är en molnbaserad tjänst som tillhandahåller NLP-funktioner (Natural Language Processing) för text utvinning och text analys, inklusive: sentiment analys, uppläggnings utvinning, extrahering av nyckel fraser, språk identifiering och identifiering av namngivna enheter.

API:et är en del av [Azure Cognitive Services](../index.yml), en samling maskininlärnings- och AI-algoritmer i molnet för dina utvecklingsprojekt. Du kan använda dessa funktioner med [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)eller [klient biblioteket](quickstarts/client-libraries-rest-api.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

## <a name="sentiment-analysis"></a>Attitydanalys

Använd [sentiment-analys](how-tos/text-analytics-how-to-sentiment-analysis.md) och ta reda på vad man tycker om ditt varumärke eller ditt ämne genom att lansera texten för LED trådar om positiv eller negativ sentiment. 

Funktionen ger sentiment-etiketter (till exempel "negativa", "neutral" och "positiv") baserat på de högsta förtroende poängen som tjänsten har hittat på en mening och dokument nivå. Den här funktionen returnerar även konfidens resultat mellan 0 och 1 för varje dokument & meningar i den för positiva, neutrala och negativa sentiment. Du kan också köra tjänsten lokalt [med en behållare](how-tos/text-analytics-how-to-install-containers.md).

Från och med v 3.1-förhands granskningen är utgångs punkt en funktion i Attitydanalys. Den här funktionen är även känd som Aspect-baserad Attitydanalys i naturlig språk bearbetning (NLP) och ger mer detaljerad information om de åsikter som rör aspekter (till exempel attributen för produkter eller tjänster) i text.

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Använd [extrahering av nyckel fraser](how-tos/text-analytics-how-to-keyword-extraction.md) för att snabbt identifiera huvud begreppen i text. I texten "livsmedlet var Delicious och det var en fantastiskt anställd", kommer Extrahering av diskussionsämne att returnera de viktigaste punkterna: "mat" och "underbara personal".

## <a name="language-detection"></a>Språkidentifiering

Språk identifiering kan [identifiera språket som en inmatad text är skriven i](how-tos/text-analytics-how-to-language-detection.md) och rapportera en enda språkkod för varje dokument som skickas på begäran i en mängd olika språk, varianter, dialekter och vissa regionala/kulturella språk. Språk koden är kopplad till en säkerhets poäng.

## <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet

Med den namngivna enhets igenkänningen (NER) kan du [identifiera och kategorisera entiteter](how-tos/text-analytics-how-to-entity-linking.md) i din text som personer, platser, organisationer, kvantiteter och välkända entiteter också känns igen och länkas till mer information på webben.

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

[Använd textanalys behållare](how-tos/text-analytics-how-to-install-containers.md) för att distribuera API-funktioner lokalt. Med dessa Docker-behållare kan du ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker. Textanalys erbjuder följande behållare:

* sentiment-analys
* extrahering av nyckel fraser (för hands version)
* språk identifiering (för hands version)
* Textanalys för hälsa (för hands version)

## <a name="asynchronous-operations"></a>Asynkrona åtgärder

Med `/analyze` slut punkten kan du använda urvals funktioner i API för textanalys [asynkront](how-tos/text-analytics-how-to-call-api.md), till exempel extrahering av ner och nyckel fraser.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

Arbetsflödet är enkelt: du skickar data för analys och hanterar utdata i din kod. Analysverktyg förbrukas i befintligt skick, utan någon ytterligare konfiguration eller anpassning.

1. [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för textanalys. Sedan [hämtar du nyckeln](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som genereras för att du ska kunna autentisera dina begär Anden.

2. [Formulera en begäran](how-tos/text-analytics-how-to-call-api.md#json-schema) som innehåller dina data som rå ostrukturerad text, i JSON.

3. Publicera begäran till den slut punkt som upprättats vid registreringen, Lägg till önskad resurs: sentiment analys, extrahering av nyckel fraser, språk identifiering eller identifiering av namngiven entitet.

4. Strömma eller lagra svaret lokalt. Beroende på begäran är resultatet attitydpoäng, en samling extraherade nyckelfraser eller en språkkod.

Utdata returneras som ett enda JSON-dokument, med resultat för varje textdokument du har publicerat, baserat på ID. Du kan därefter analysera, visualisera eller kategorisera resultatet i användbara insikter.

Data lagras inte på ditt konto. Åtgärder som utförs av API för textanalys är tillståndslösa, vilket betyder att texten du anger bearbetas och resultaten returneras omedelbart.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Textanalys för flera programmerings miljö nivåer

Du kan börja använda API för textanalys i dina processer, även om du inte har mycket erfarenhet av programmering. Använd de här självstudierna för att lära dig hur du kan använda API: t för att analysera text på olika sätt för att passa din erfarenhets nivå. 

* Minimal programmering krävs:
    * [Extrahera information i Excel med Textanalys och energi automatisering](tutorials/extract-excel-information.md)
    * [Använd API för textanalys och MS Flow för att identifiera sentiment av kommentarer i en Yammer-grupp](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integrera Power BI med API för textanalys för att analysera feedback från kunder](tutorials/tutorial-power-bi-key-phrases.md)
* Programmerings upplevelse rekommenderas:
    * [Sentimentanalys på strömmade data med hjälp av Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Bygg en kolv-app för att översätta text, analysera sentiment och syntetiskt tal](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Språk som stöds

Det här avsnittet har flyttats till en separat artikel för bättre synlighet. Referera till [språk som stöds i API för textanalys](./language-support.md) för det här innehållet.

<a name="data-limits"></a>

## <a name="data-limits"></a>Databegränsningar

Alla av slutpunkterna för API för textanalys accepterar råtextdata. Mer information finns i artikeln [data begränsningar](concepts/data-limits.md) .

## <a name="unicode-encoding"></a>Unicode-kodning

API för textanalys använder Unicode-kodning för textrepresentation och beräkningar av antal tecken. Begäranden kan skickas i både UTF-8 och UTF-16 utan mätbara skillnader i antal tecken. Unicode-kodpunkter används som heuristik för teckenlängd och anses motsvara för databegränsningar för textanalys. Om du använder [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) för att hämta antalet bokstäver använder du samma metod som vi använder för att mäta data storleken.

## <a name="next-steps"></a>Nästa steg

+ [Skapa en Azure-resurs](../cognitive-services-apis-create-account.md) för textanalys för att få en nyckel och slut punkt för dina program.

+ Använd [snabb](quickstarts/client-libraries-rest-api.md) start för att börja skicka API-anrop. Lär dig hur du skickar text, väljer en analys och visar resultat med minimal kod.

+ Se [vad som är nytt i API för textanalys](whats-new.md) för information om nya versioner och funktioner.

+ Lär dig mer om den här [självstudien om sentiment analys](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) med hjälp av Azure Databricks.

+ Kolla in vår lista med blogg inlägg och fler videor om hur du använder API för textanalys med andra verktyg och tekniker på sidan för den [externa & community-innehåll](text-analytics-resource-external-community.md).