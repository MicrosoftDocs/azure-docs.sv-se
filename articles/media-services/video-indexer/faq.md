---
title: Vanliga frågor om Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln innehåller svar på vanliga frågor om Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 0b7f0427708eece75b5d418b68dd1c7a2e90853f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108701"
---
# <a name="video-indexer-frequently-asked-questions"></a>Vanliga frågor och svar om Video Indexer

I den här artikeln får du svar på vanliga frågor om Video Indexer.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer är en artificiell intelligens-tjänst som är en del av Microsoft Azure Media Services. Video Indexer tillhandahåller ett dirigering av flera maskin inlärnings modeller som gör att du enkelt kan extrahera djupgående insikter från en video. För att tillhandahålla avancerade och exakta insikter kan Video Indexer använda flera kanaler i videon: ljud, tal och visuell information. Video Indexer insikter kan användas på många sätt, t. ex. att förbättra möjligheterna för innehåll och tillgänglighet, skapa nya Sälj möjligheter eller skapa nya upplevelser som använder insikterna. Video Indexer tillhandahåller ett webbaserat gränssnitt för testning, konfiguration och anpassning av modeller i ditt konto. Utvecklare kan använda ett REST-baserat API för att integrera Video Indexer i produktions systemet. 

### <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Några av de åtgärder som Video Indexer kan utföra på mediafiler är:

* Identifiera och extrahera tal och identifiera högtalare.
* Identifiera och extrahera text på skärmen i en video.
* Identifiera objekt i en videofil.
* Identifiera varumärken (till exempel: Microsoft) från ljud spår och text på skärmen i en video.
* Identifiera och identifiera ansikten från en databas med kändisar och en användardefinierad databas med ansikten.
* Att extrahera ämnen som diskuteras men som inte nödvändigt vis nämns i ljud-och video innehåll.
* Skapa textning eller under texter från ljud spåret.

Mer information och mer Video Indexer funktioner finns i [Översikt](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Hur gör jag för att kom igång med Video Indexer?

Video Indexer innehåller ett kostnads fritt utvärderings erbjudande som ger dig 600 minuter i det webbaserade gränssnittet och 2 400 minuter via API: et. Du kan [Logga in på video Indexer webbaserat gränssnitt](https://www.videoindexer.ai/) och testa det åt dig själv med valfri webb identitet och utan att behöva konfigurera en Azure-prenumeration. Följ [det här enkla introduktions labbet](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) för att få bättre uppfattning om hur du använder video Indexer.

För att indexera videor och ljud flyger i stor skala kan du ansluta Video Indexer till en betald Microsoft Azure-prenumeration. Du hittar mer information om prissättning på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

Du hittar mer information om att komma igång i [komma igång](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Måste jag koda kunskaper för att använda Video Indexer?

Du kan använda det webbaserade Video Indexer-gränssnittet för att utvärdera, konfigurera och hantera ditt konto utan att **behöva koda**.  När du är redo att utveckla mer komplexa program kan du använda [video INDEXER API](https://api-portal.videoindexer.ai/) för att integrera video Indexer i dina egna program, webbplatser eller [anpassade arbets flöden med hjälp av Server lös teknik som Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) eller Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Behöver jag Machine Learning-kunskaper för att använda Video Indexer?

Nej, Video Indexer ger en integrering av flera Machine Learning-modeller i en pipeline. Indexering av en video-eller ljudfil via Video Indexer hämtar en fullständig uppsättning insikter som extraheras på en delad tids linje utan några maskin inlärnings kunskaper eller kunskaper om algoritmer som behövs på kundens del.

### <a name="what-media-formats-does-video-indexer-support"></a>Vilka Media format stöder Video Indexer?

Video Indexer stöder de flesta vanliga medie format. Mer information finns i listan [Azure Media Encoder standardformat](../latest/encode-media-encoder-standard-formats-reference.md) .

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Hur gör jag för att ladda upp en mediefil i Video Indexer och vilka är begränsningarna?

I Video Indexer webbaserad portal kan du ladda upp en mediefil med hjälp av dialog rutan fil uppladdning eller genom att peka på en URL som är direkt värd för käll filen (se [exemplet](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Alla webb adresser som är värdar för medie innehåll med en iFrame-eller inbäddnings kod fungerar inte (se [exemplet](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

Mer information finns i den här [instruktions hand boken](./upload-index-videos.md).

#### <a name="limitations"></a>Begränsningar

* Namnet på videon får inte överstiga 80 tecken.
* Om du laddar upp en video med hjälp av byte mat ris är video storleken begränsad till 2 GB (och 30 GB när du använder URL). 

En omfattande lista finns i [överförings överväganden och begränsningar](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Hur lång tid tar det Video Indexer för att extrahera insikter från media?

Hur lång tid det tar att indexera en video-eller ljudfil, både via Video Indexer-API och det Video Indexer webbaserade gränssnittet, är beroende av flera parametrar, till exempel fil längd och kvalitet, antalet insikter som finns i filen, antalet [reserverade enheter](../previous/media-services-scale-media-processing-overview.md) som är tillgängliga och om [slut punkten för direkt uppspelning](../previous/media-services-streaming-endpoints-overview.md) är aktive rad eller inte. Vi rekommenderar att du kör några testfiler med ditt eget innehåll och tar ett genomsnitt för att få en bättre uppfattning.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kan jag skapa anpassade arbets flöden för att automatisera processer med Video Indexer?

Ja, du kan integrera Video Indexer i lösningar utan server som Logic Apps, Flow och [Azure Functions](https://azure.microsoft.com/services/functions/). Du hittar mer information om [Logic app](https://azure.microsoft.com/services/logic-apps/) och [Flow](https://flow.microsoft.com/en-us/) -kopplingar för video Indexer [här](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). Du kan se vissa Automation-projekt som har utförts av partner i [video Indexer samples](https://github.com/Azure-Samples/media-services-video-indexer) lagrings platsen.

### <a name="in-which-azure-regions-is-video-indexer-available"></a>I vilka Azure-regioner är video Indexer tillgängliga?

Du kan se vilka Azure-regioner Video Indexer är tillgängliga på sidan [regioner](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) .

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Kan jag anpassa Video Indexer modeller för mitt speciella användnings fall? 

Ja. I Video Indexer kan du anpassa några av de tillgängliga modellerna så att de passar dina behov bättre. 

Vår person modell stöder till exempel färdiga 1 000 000-ytor med kändis igenkänning, men du kan också träna det för att identifiera andra ansikten som inte finns i databasen. 

Mer information finns i artiklar om att anpassa [personer](customize-person-model-overview.md), [varumärken](customize-brands-model-overview.md)och [språk](customize-language-model-overview.md) modeller. 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Kan jag redigera videor i biblioteket?

Ja. Tryck på knappen **Redigera video** från biblioteks visningen eller knappen **Öppna i redaktör** från spelarens bildskärm för att komma till fliken **projekt** . Du kan skapa ett nytt projekt och lägga till fler videor från biblioteket för att redigera dem tillsammans, när du är färdig kan du rendera videon och hämta den. 

Om du vill få insikter om den nya videon kan du indexera den med Video Indexer så visas den i biblioteket med sina insikter.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Kan jag indexera flera ljud strömmar eller kanaler?

Om det finns flera ljud strömmar tar Video Indexer den första som påträffas och bearbetar bara den här data strömmen. I alla ljud strömmar Video Indexer processer tar det olika kanaler (om de finns) och bearbetar dem tillsammans som mono. För strömmar/kanal manipulation kan du använda ffmpeg-kommandon på filen innan du indexerar den.

### <a name="what-is-the-sla-for-video-indexer"></a>Vad är service avtalet för Video Indexer?

Service avtalet för Azure Media service omfattar Video Indexer och finns på sidan med [service avtal](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) . Service avtalet gäller endast Video Indexer betalda konton och gäller inte för den kostnads fria utvärderings versionen.

## <a name="privacy-questions"></a>Sekretess frågor

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Indexeras video-och ljudfiler med Video Indexer lagrade?

Ja, om du inte tar bort filen från Video Indexer, eller om du använder Video Indexer webbplats eller API, lagras dina video-och ljudfiler. För den kostnads fria utvärderings versionen lagras video-och ljudfiler som du indexerar i Azure-regionen USA, östra. Annars lagras dina video-och ljudfiler i Azure-prenumerationens lagrings konto.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kan jag ta bort mina filer som är lagrade i Video Indexer-portalen?

Ja, du kan alltid ta bort video-och ljudfiler samt alla metadata och insikter som extraheras från dem genom att Video Indexer. När du har tagit bort en fil från Video Indexer tas filen och dess metadata och insikter permanent bort från Video Indexer. Men om du har implementerat en egen lösning för säkerhets kopiering i Azure Storage finns filen kvar i din Azure-lagring.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kan jag kontrol lera användar åtkomst till mitt Video Indexer konto?

Ja, endast konto administratörer kan bjuda in och ta bort inbjudan till sina konton, samt tilldela vem som har redigerings behörigheter och som har skrivskyddad åtkomst.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vem har till gång till mina video-och ljudfiler som har indexerats och/eller lagrats av Video Indexer och de metadata och insikter som har extraherats?

Ditt video-eller ljud innehåll som är offentligt som sekretess inställningar kan nås av alla som har länken till video-eller ljud innehållet och dess insikter. Ditt video-eller ljud innehåll som är privat som sekretess inställningar kan endast nås av användare som har bjudits in till kontot för video-eller ljud innehållet. Sekretess inställningen för ditt innehåll gäller även för metadata och insikter som Video Indexer extraheras. Du tilldelar sekretess inställningen när du laddar upp video-eller ljud filen. Du kan också ändra sekretess inställningen efter indexering.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Vilken åtkomst har Microsoft till mina video-eller ljudfiler som har indexerats och/eller lagrats av Video Indexer och de metadata och insikter som har extraherats?

Enligt [Azure Online Services-villkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (ost) har du helt eget innehåll, och Microsoft kommer bara att få åtkomst till ditt innehåll och de metadata och insikter som video Indexer extraherar från ditt innehåll enligt osten och Microsofts sekretess policy.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Är de anpassade modeller som jag skapar i mitt Video Indexer-konto tillgängligt för andra konton?

 Nej, de anpassade modeller som du skapar i ditt konto är inte tillgängliga för något annat konto. Med Video Indexer kan du för närvarande bygga anpassade [varumärken](customize-brands-model-overview.md), [språk](customize-language-model-overview.md)och [person](customize-person-model-overview.md) modeller i ditt konto. Dessa modeller är bara tillgängliga i det konto som du skapade modellerna i.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Är innehållet indexerat av Video Indexer kvar i Azure-regionen där jag använder Video Indexer?

Ja, innehållet och dess insikter lagras i Azure-regionen, såvida du inte har en manuell konfiguration i din Azure-prenumeration som använder flera Azure-regioner. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Vad är sekretess policyn för Video Indexer?

Video Indexer täcks av [Microsofts sekretess policy](https://privacy.microsoft.com/privacystatement). I sekretess policyn förklaras de personliga data som Microsoft bearbetar, hur Microsoft bearbetar dem och i vilka syfte används Microsoft. Läs mer om sekretess på [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Vilka certifieringar har Video Indexer?

Video Indexer har för närvarande SOC-certifiering. Information om hur du granskar Video Indexers certifiering finns i [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Vad är skillnaden mellan privata och offentliga videor? 

När videor laddas upp till Video Indexer kan du välja mellan två sekretess inställningar: privata och offentliga. Offentliga videor är tillgängliga för alla, inklusive anonyma och oidentifierade användare. Privata är enbart begränsade till konto medlemmar. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Jag försökte ladda upp en video som offentlig och den flaggades för olämpligt eller stötande innehåll, vad innebär det? 

När du laddar upp en video till Video Indexer görs en automatisk innehålls analys av algoritmer och modeller för att säkerställa att inget olämpligt innehåll visas offentligt. Om en video har visat sig vara misstänkt som ett explicit innehåll, går det inte att ange den som offentlig. Konto medlemmar kan dock fortfarande komma åt den som en privat video (Visa den, Hämta insikter och extraherade artefakter och utföra andra åtgärder som är tillgängliga för konto medlemmar).   

För att kunna ställa in videon för offentlig åtkomst kan du antingen: 

* Bygg ditt eget gränssnitts lager (till exempel app eller webbplats) och Använd det för att interagera med tjänsten Video Indexer. På så sätt förblir videon privat i portalen och användarna kan interagera med den via ditt gränssnitt. Du kan till exempel fortfarande få insikter eller tillåta visning av videon i ditt eget gränssnitt. 
* Begära en mänsklig granskning av innehållet, vilket skulle innebära att det inte går att ta bort begränsningen förutsatt att innehållet inte är explicit. 

    Det här alternativet kan du utforska om Video Indexer webbplats används direkt av användarna som gränssnitts lager och för offentlig (oautentiserad) visning. 

## <a name="api-questions"></a>API-frågor

### <a name="what-apis-does-video-indexer-offer"></a>Vilka API: er Video Indexer erbjudandet?

Video Indexer s API: er möjliggör indexering, extrahering av metadata, till gångs hantering, översättning, inbäddning, anpassning av modeller och mycket annat. Mer detaljerad information om hur du använder Video Indexer API finns på [video Indexer Developer-portalen](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Vilka klient-SDK: er Video Indexer erbjudandet?

Det finns för närvarande inga tillgängliga klient-SDK: er. Video Indexers teamet arbetar på SDK: er och planerar att leverera dem snart.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Hur gör jag för att kom igång med Video Indexer s API?

Följ [Självstudier: kom igång med video INDEXER API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Vad är skillnaden mellan Video Indexer API och Azure Media service v3 API?

För närvarande finns det några överlappande i funktioner som erbjuds av Video Indexer API och Azure Media service v3 API. Du hittar mer information om hur du jämför båda tjänsterna [här](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Vad är en API-åtkomsttoken och varför behöver jag det?

Video Indexer-API: et innehåller ett API för auktorisering och ett API för åtgärder. API: erna för auktorisering innehåller anrop som ger dig åtkomst till token. Varje anrop till åtgärds-API:t ska associeras med en åtkomsttoken, som matchar auktoriseringsområdet för anropet.

Åtkomsttoken krävs för att använda Video Indexer-API: er i säkerhets syfte. Detta säkerställer att alla samtal kommer från dig eller de som har åtkomst behörighet till ditt konto. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Vad är skillnaden mellan kontots åtkomst-token, token för användar åtkomst och token för video åtkomst?

* Kontonivå – med åtkomsttoken på kontonivå kan du utföra åtgärder på kontonivån eller videonivån. Du kan till exempel Ladda upp en video, lista alla videor, få video insikter.
* Användar nivå – åtkomsttoken på användar nivå gör att du kan utföra åtgärder på användar nivå. Till exempel hämta associerade konton.
* Videonivå – med åtkomsttoken på videonivå kan du utföra åtgärder på en specifik video. Till exempel få videoinsikter, ladda ned textning, få widgetar osv.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Hur ofta måste jag skaffa en ny åtkomsttoken? När upphör åtkomsttoken att gälla?

Åtkomsttoken upphör att gälla varje timme, så du måste skapa en ny åtkomsttoken varje timme. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Vad är inloggnings alternativen för att Video Indexer Developer-portalen?

Se en versions kommentar om [inloggnings information](release-notes.md#october-2020).

När du har registrerat ditt e-postkonto med en identitets leverantör kan du inte använda det här e-postkontot med en annan identitets leverantör.

## <a name="billing-questions"></a>Frågor om fakturering

### <a name="how-much-does-video-indexer-cost"></a>Hur mycket kostar Video Indexer?

Video Indexer använder en enkel pris modell enligt principen betala per användning baserat på varaktigheten för det inmatade innehållet som du indexerar. Ytterligare kostnader kan tillkomma för kodning, strömning, lagring, nätverks användning och reserverade enheter. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

### <a name="when-am-i-billed-for-using-video-indexer"></a>När faktureras jag för att använda Video Indexer?

När du skickar en video som ska indexeras definierar användaren indexeringen som video analys, ljud analys eller både och. Detta avgör vilka SKU: er som ska debiteras. Om det finns ett allvarligt fel på nivån under bearbetningen returneras en felkod som ett svar. I sådana fall sker ingen fakturering.  Ett allvarligt fel kan orsakas av ett fel i vår kod eller ett allvarligt fel i ett internt beroende som tjänsten har. Fel som felaktig identifiering eller insikts extrahering anses inte vara kritiskt och ett svar returneras. I alla fall där ett giltigt svar (inte felkoder) returneras, sker faktureringen.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Erbjuder Video Indexer en kostnads fri utvärderings version?

Ja, Video Indexer erbjuder en kostnads fri utvärderings version som ger fullständiga service-och API-funktioner. Det finns en kvot på 600 minuter för videor för webbaserade gränssnitts användare och 2 400 minuter för API-användare. 

## <a name="next-steps"></a>Nästa steg

* [Översikt](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
