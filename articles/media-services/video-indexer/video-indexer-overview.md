---
title: Vad är Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över tjänsten Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: 06f5e19718445f44dd2302faf280f083cce0774f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98783809"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Vad är Azure Media Services Video Indexer?

[!INCLUDE [regulation](./includes/regulation.md)]

Video Indexer (VI) är Azure Media Services AI-lösningen och en del av Azure Cognitive Services varumärket. Video Indexer ger möjlighet att extrahera djupgående insikter (utan behov av data analys eller kodnings kunskaper) med hjälp av maskin inlärnings modeller baserade på flera kanaler (röst, Vocals, visualisering). Du kan anpassa och träna modeller ytterligare. Tjänsten möjliggör djupgående sökning, minskar drifts kostnaderna, ger nya Sälj möjligheter och skapar nya användar upplevelser för stora Arkiv av videor (med låga ingångs hinder).

Om du vill börja extrahera insikter med Video Indexer måste du skapa ett konto och ladda upp videor. När du laddar upp videor till Video Indexer, analyserar den både visuella objekt och ljud genom att köra olika AI-modeller. När Video Indexer analyserar videon extraheras de insikter som extraheras av AI-modellerna.

När du skapar ett Video Indexer-konto och ansluter det till Media Services, lagras medie-och metadatafiler i Azure Storage-kontot som är kopplat till det Media Services kontot. Mer information finns i [skapa ett video Indexer-konto som är anslutet till Azure](connect-to-azure.md).

Följande diagram är en illustration och inte en teknisk förklaring av hur Video Indexer fungerar i Server delen.

![Flödes diagram för Azure Media Services Video Indexer](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer och du får inte använda Video Indexer eller någon Azure-tjänst på ett sätt som strider mot andras rättigheter eller som kan vara skadliga för andra.

Innan du laddar upp en video/bild till Video Indexer måste du ha alla de rättigheter som krävs för att använda videon/bilden, inklusive, vid behov enligt lag, alla nödvändiga samspelade individer (om de finns) i videon/bilden, för användning, bearbetning och lagring av data i Video Indexer och Azure. Vissa jurisdiktioner kan införa särskilda juridiska krav för insamling, online-bearbetning och lagring av vissa kategorier av data, t. ex. Bio metriska data. Innan du använder Video Indexer och Azure för bearbetning och lagring av data som omfattas av särskilda juridiska krav, måste du säkerställa efterlevnaden av sådana juridiska krav som kan gälla för dig.

Om du vill veta mer om efterlevnad, sekretess och säkerhet i Video Indexer går du till Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). För Microsofts sekretess krav, data hantering och bevarande praxis, inklusive hur du tar bort dina data, kan du läsa Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("ost") och [data bearbetnings tillägget](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Genom att använda Video Indexer samtycker du till att vara kopplad till OST-, DPA-och sekretess policyn.

## <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Video Indexer insikter kan tillämpas på många olika scenarier:

* *Djupgående sökning*: Använd de insikter som extraheras från videon för att förbättra Sök upplevelsen i ett video bibliotek. Att indexera talade ord och ansikten kan till exempel göra det möjligt att söka efter moment i en video där en person har ekrar på vissa ord eller när två personer setts tillsammans. Sök baserat på sådana insikter från videor är tillämpligt på nyhets myndigheter, utbildnings institut,-sändningar, nöjes innehålls ägare, företags-LOB-appar och i allmänhet till alla branscher som har ett video bibliotek som användare behöver söka mot.
* *Skapa innehåll*: skapa trailrar, markera rullar, innehåll i sociala medier eller nyhets klipp baserat på insikter video Indexer extraherar från ditt innehåll. Nyckel bild rutor, scener och tidsstämplar för visning av personer och etiketter gör processen mycket smidigare och enklare, och gör att du kan komma åt de delar av videon du behöver för det innehåll som du skapar.
* *Hjälpmedel*: om du vill göra ditt innehåll tillgängligt för personer med funktions nedsättning eller om du vill att innehållet ska distribueras till olika regioner med olika språk kan du använda avskriften och översättningen från video Indexer på flera språk.
* *Monetarisering*: video Indexer kan hjälpa till att öka värdet för videor. Till exempel kan branscher som förlitar sig på AD-intäkter (nyhets medier, sociala medier osv.) leverera relevanta annonser genom att använda de extraherade insikterna som ytterligare signaler till AD-servern.
* *Innehålls moderator*: Använd text-och visuella innehålls redigerings modeller för att hålla dina användare skyddade från olämpligt innehåll och kontrol lera att innehållet som du publicerar matchar din organisations värden. Du kan automatiskt blockera vissa videor eller Varna dina användare om innehållet.
* *Rekommendationer*: video insikter kan användas för att förbättra användarnas engagemang genom att markera relevant video moment till användare. Genom att tagga varje video med ytterligare metadata kan du rekommendera användare de mest relevanta videor och markera de delar av videon som ska matcha deras behov.

## <a name="features"></a>Funktioner

I följande lista visas de insikter som du kan hämta från dina videor med Video Indexer video-och ljud modeller:

### <a name="video-insights"></a>Video insikter

* **Ansiktsigenkänning**: Identifierar och grupperar ansikten som visas i videon.
* **Kändis-identifiering**: video Indexer identifierar automatiskt över 1 000 000 kändisar, till exempel världs ledare, aktörer, actresses, tävlande, forskare, företag och teknik ledare över hela världen. Data om dessa kändisar kan också hittas på olika webbplatser (IMDB, Wikipedia och så vidare).
* **Kontobaserad ansiktsidentifiering**: Video Indexer tränar en modell för ett visst konto. Den identifierar sedan ansikten i videon baserat på den tränade modellen. Mer information finns i [Anpassa en person modell från video Indexer webbplats](customize-person-model-with-website.md) och [Anpassa en person modell med video Indexer API](customize-person-model-with-api.md).
* Rendering **av miniatyrer för ansikten** ("bästa ansikte"): identifierar automatiskt den bästa infångade ytan i varje grupp av ansikten (baserat på kvalitet, storlek och front position) och extraherar den som en bild till gång.
* **Visuell text igenkänning** (OCR): extraherar text som visas visuellt i videon.
* **Moderering av visuellt innehåll**: Identifierar vuxna och/eller vågade visuella objekt.
* **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
* **Scen segmentering**: anger när en scens ändringar i videon baseras på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande bilder, som är semantiskt relaterade.
* **Bild identifiering**: anger när en bild ändras i video utifrån visuella tips. En bild är en serie med ramar som tas från samma rörelse-och bildkamera. Mer information finns i [scener, bilder och nyckel bild rutor](scenes-shots-keyframes.md).
* **Identifiering av svarta bildrutor**: Identifierar svarta bildrutor som visas i videon.
* **Extrahering av bildrutor**: Identifierar stabila nyckelbilder i en video.
* **Rullande krediter**: identifierar början och slutet av de rullande eftertexterna i slutet av TV-program och filmer.
* Igenkänning av **animerade tecken** (för hands version): identifiering, gruppering och igenkänning av tecken i animerat innehåll via integration med [Cognitive Services anpassad vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Mer information finns i [animerat teckensnitts identifiering](animated-characters-recognition.md).
* **Identifiering av redaktionell text typ**: tagga dum par baserat på deras typ (t. ex. wide Store, medie tagning, närbild, Extreme closes, två bilder, flera personer, utomhus och inomhus). Mer information finns i [identifiering av redaktionella typer av tagningar](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Ljud insikter

* **Ljud avskrift**: konverterar tal till text på 12 språk och tillåter tillägg. Språken som stöds är engelska, spanska, franska, tyska, italienska, mandarin, japanska, arabiska, ryska och portugisiska, hindi och koreanska.
* **Automatisk språkidentifiering**: Identifierar automatiskt dominant talat språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, mandarin, japanska, ryska och portugisiska. Om språket inte säkert kan identifieras förutsätter Video Indexer att det talade språket är engelska. Mer information finns i [Modell för språkidentifiering](language-identification-model.md).
* **Tal identifiering och avskriftering i flera språk**: identifierar automatiskt det talade språket i olika segment från ljud. Tjänsten skickar varje segment av mediefilen som ska transkriberas, och sedan kombineras transkriptionerna till en enda transkription. Mer information finns i [Identifiera och transkribera innehåll på olika språk automatiskt](multi-language-identification-transcription.md).
* **Textning**: Skapar textning i tre format: VTT, TTML och SRT.
* **Två kanal bearbetning**: identifierar automatiskt separata avskrifter och sammanfogningar på en enda tids linje.
* **Brus minskning**: tar bort ljud-eller brus inspelnings telefoner (baserat på Skype-filter).
* **Avskrifts anpassning** (CRI: er): anpassad tal från tågen till text modeller för att skapa branschspecifika avskrifter. Mer information finns i [Anpassa en språk modell från video Indexer webbplats](customize-language-model-with-website.md) och [Anpassa en språk modell med video Indexer-API: er](customize-language-model-with-api.md).
* **Uppräkning av högtalare**: Maps och förstår vilken talare som är eker för ord och när. Sexton högtalare kan identifieras i en enda ljudfil.
* **Högtalar statistik**: tillhandahåller statistik för talares tal förhållande.
* **Moderering av textinnehåll**: Identifierar stötande text i ljudavskriften.
* **Ljud effekter**: identifierar ljud effekter som hand claps, tal och tystnad.
* **Känslo-identifiering**: identifierar känslor baserat på tal (vad som sägs) och röst ton (hur det är det). Känslo kan vara glädje, ledsenhet, ilska eller frukt.
* **Översättning**: Skapar översättningar av ljudavskriften till 54 olika språk.

### <a name="audio-and-video-insights-multi-channels"></a>Ljud-och video insikter (flera kanaler)

Vid indexering av en kanal blir del resultat för dessa modeller tillgängliga.

* **Extrahering av nyckelord**: extraherar nyckelord från tal och visuell text.
* **Extrahering av namngivna enheter**: extraherar varumärken, platser och personer från tal och visuell text via naturlig språk bearbetning (NLP).
* **Ämnesinferens**: Skapar inferens av huvudämnen från avskrifter. IPTC-taxonomin på andra nivån ingår.
* **Artefakter**: Extraherar en omfattande uppsättning ”nästa detaljnivå”-artefakter för de olika modellerna.
* **Attitydanalys**: Identifierar positiva, negativa och neutrala attityder i tal och visuell text.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hur kan jag komma igång med Video Indexer?

Du kan komma åt Video Indexer-funktioner på tre sätt:

* Video Indexer Portal: en lättanvänd lösning som gör att du kan utvärdera produkten, hantera kontot och anpassa modeller.

    Mer information om portalen finns i [Kom igång med video Indexer webbplats](video-indexer-get-started.md).  

* API-integrering: alla Video Indexers funktioner är tillgängliga via en REST API, vilket gör att du kan integrera lösningen i dina appar och din infrastruktur.

    Information om hur du kommer igång som utvecklare finns i [använda Video Indexer REST API](video-indexer-use-apis.md).

* Inbäddnings bara widget: gör att du kan bädda in Video Indexer insikter, spelare och redigerings upplevelser i din app.

    Mer information finns i [bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Om du använder webbplatsen läggs insikterna till som metadata och visas i portalen. Om du använder API: er är insikterna tillgängliga som en JSON-fil.

## <a name="supported-browsers"></a>Webbläsare som stöds

I följande lista visas de webbläsare som stöds och som du kan använda för Video Indexer webbplats och för dina appar som bäddar in widgetarna. I listan visas även den lägsta webb läsar versionen som stöds:

- Edge, version: 16
- Firefox, version: 54
- Chrome, version: 58
- Safari, version: 11
- Opera, version: 44
- Opera Mobile, version: 59
- Android-webbläsare, version: 81
- Samsung-webbläsare, version: 7
- Chrome för Android, version: 87
- Firefox för Android, version: 83

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Kom igång med video Indexer webbplats](video-indexer-get-started.md).
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md).
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).
