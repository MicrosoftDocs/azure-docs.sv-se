---
title: Vad är Speech Service?
titleSuffix: Azure Cognitive Services
description: 'Tal tjänsten är arbetskonton av tal-till-text, text till tal och tal översättning till en enda Azure-prenumeration. Lägg till tal till program, verktyg och enheter med tal-SDK, tal enheter SDK eller REST API: er.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: ddb61a79e6af402b06fe8c18df8c62f9287437a3
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209722"
---
# <a name="what-is-the-speech-service"></a>Vad är Speech Service?

Tal tjänsten är arbetskonton av tal-till-text, text till tal och tal översättning till en enda Azure-prenumeration. Det är enkelt att aktivera program, verktyg och enheter med [tal-CLI](spx-overview.md), [tal-SDK](./speech-sdk.md), [tal enheter SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [tal Studio](https://speech.microsoft.com/)eller [REST-API: er](#reference-docs).

> [!IMPORTANT]
> Tal tjänsten har ersatt API för Bing-taligenkänning och Translator Speech. Se avsnittet _migrering_ för instruktioner för migrering.

Följande funktioner är en del av tal tjänsten. Använd länkarna i den här tabellen för att lära dig mer om vanliga användnings fall för varje funktion, eller Sök i API-referensen.

| Tjänst | Funktion | Beskrivning | SDK | REST |
|---------|---------|-------------|-----|------|
| [Tal till text](speech-to-text.md) | Tal till text i real tid | Tal till text omvandlar eller översätter ljud strömmar eller lokala filer till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Använd tal-till-text med [language Understanding (Luis)](../luis/index.yml) för att härleda användar avsikter från inställt tal och agera på röst kommandon. | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Batch-tal-till-text](batch-transcription.md) | Med tal-till-text i batch kan du använda asynkrona tal-till-text-avskrifter av stora mängder tal ljud data som lagras i Azure Blob Storage. Förutom att konvertera tal ljud till text, tillåter batch-tal-till-text också diarization och sentiment-analys. | Inga | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Konversation med flera enheter](multi-device-conversation.md) | Anslut flera enheter eller klienter i en konversation för att skicka tal-eller textbaserade meddelanden, med enkel support för avskrift och översättning| Ja | Inga |
| | [Konversations avskrift](./conversation-transcription.md) | Aktiverar tal igenkänning i real tid, högtalar-ID och diarization. Det är perfekt för att skriva över person möten med möjlighet att skilja på högtalare. | Ja | Inga |
| | [Skapa Custom Speech modeller](#customize-your-speech-experience) | Om du använder tal-till-text för igenkänning och avskriftering i en unik miljö kan du skapa och träna anpassade ljud-, språk-och uttals modeller för att hantera omgivande brus eller branschspecifika vokabulär. | Inga | [Ja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Text till tal](text-to-speech.md) | Text till tal | Text till tal konverterar inmatad text till mänskligt syntetiskt tal med [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). Använd neurala-röster, som är mänskliga röster som drivs av djup neurala nätverk. Se [språk stöd](language-support.md). | [Ja](./speech-sdk.md) | [Ja](#reference-docs) |
| | [Skapa anpassade röster](#customize-your-speech-experience) | Skapa anpassade röst teckensnitt som är unika för ditt varumärke eller din produkt. | Inga | [Ja](#reference-docs) |
| [Talöversättning](speech-translation.md) | Talöversättning | Tal översättning möjliggör real tids översättning av tal till dina program, verktyg och enheter i real tid. Använd den här tjänsten för tal-till-tal-och tal-till-text-översättning. | [Ja](./speech-sdk.md) | Inga |
| [Röstassistenter](voice-assistants.md) | Röstassistenter | Röst assistenter som använder röst tjänsten ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Röst assistents tjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistent implementering som använder bot Framework: s direkta rad tal kanal eller den integrerade anpassade kommando tjänsten för att slutföra uppgiften. | [Ja](voice-assistants.md) | Inga |
| [Talarigenkänning](speaker-recognition-overview.md) | Identifiering av högtalar & | Tjänsten Talarigenkänning tillhandahåller algoritmer som verifierar och identifierar högtalare genom sina unika röst egenskaper. Talarigenkänning används för att besvara frågan "Vem talar?". | Ja | [Ja](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Prova Speech Service kostnadsfritt

För följande steg behöver du både ett Microsoft-konto och ett Azure-konto. Om du inte har en Microsoft-konto kan du registrera dig för en kostnads fri avgift på [Microsoft-konto portalen](https://account.microsoft.com/account). Välj **Logga in med Microsoft** och välj sedan **skapa en Microsoft-konto** när du uppmanas att logga in. Följ stegen för att skapa och verifiera din nya Microsoft-konto.

När du har en Microsoft-konto går du till [sidan för Azure-registrering](https://azure.microsoft.com/free/ai/), väljer **Starta kostnads fritt** och skapar ett nytt Azure-konto med hjälp av en Microsoft-konto. Här är en video om [hur du registrerar dig för ett kostnads fritt Azure-konto](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> När du registrerar dig för ett kostnads fritt Azure-konto levereras det med $200 i service kredit som du kan använda mot en betald röst tjänst prenumeration, som är giltig i upp till 30 dagar. Dina Azure-tjänster inaktive ras när din kredit tar slut eller upphör att gälla i slutet av 30 dagar. Om du vill fortsätta att använda Azure-tjänsterna måste du uppgradera ditt konto. Mer information finns i [uppgradera ditt kostnads fria Azure-konto](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> Tal tjänsten har två service nivåer: kostnads fri (F0) och prenumeration (S0) som har olika begränsningar och fördelar. Om du använder den kostnads fria, låga tal Service nivån kan du behålla den här kostnads fria prenumerationen även efter att din kostnads fria utvärderings version eller service kredit upphör att gälla. Mer information finns i [Cognitive Services priss igenkännings tjänst](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Skapa Azure-resursen

Så här lägger du till en tjänst resurs (kostnads fri eller betald) till ditt Azure-konto:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

1. Välj **skapa en resurs** längst upp till vänster i portalen. Om du inte ser **skapa en resurs** kan du alltid hitta den genom att välja menyn komprimerad i det övre vänstra hörnet på skärmen.

1. Skriv "tal" i rutan Sök i det **nya** fönstret och tryck på RETUR.

1. I Sök resultaten väljer du **tal**.

   ![tal Sök Resultat](media/index/speech-search.png)

1. Välj **skapa** och sedan:

   - Ange ett unikt namn för den nya resursen. Namnet hjälper dig att skilja mellan flera prenumerationer som är kopplade till samma tjänst.
   - Välj den Azure-prenumeration som den nya resursen är kopplad till för att avgöra hur avgifterna faktureras. Här är introduktionen om [hur du skapar en Azure-prenumeration](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) i Azure Portal.
   - Välj den [region](regions.md) där resursen ska användas. Azure är en global moln plattform som är allmänt tillgänglig i många regioner i hela världen. Välj en region som är närmast dig eller där ditt program körs för att få bästa möjliga prestanda. Röst tjänsten tillgänglighet skiljer sig från olika regioner. Se till att du skapar din resurs i en region som stöds. Se [region stöd för tal tjänster](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Välj antingen en kostnads fri (F0) eller betald (S0) pris nivå. Om du vill ha fullständig information om priser och användnings kvoter för varje nivå väljer du **Visa fullständig pris information** eller mer information om [priser för tal tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Begränsningar för resurser finns i [Azure Cognitive Services-gränser](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Skapa en ny resurs grupp för den här tal prenumerationen eller tilldela prenumerationen till en befintlig resurs grupp. Med resurs grupper kan du hålla dina olika Azure-prenumerationer organiserade.
   - Välj **Skapa**. Detta tar dig till distributions översikten och visar meddelanden om distributions förlopp.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Det tar en stund att distribuera den nya tal resursen. 

### <a name="find-keys-and-region"></a>Hitta nycklar och region

Följ dessa steg om du vill hitta nycklar och region för en slutförd distribution:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Microsoft-konto.

2. Välj **alla resurser** och välj namnet på din Cognitive Services-resurs.

3. I den vänstra rutan, under **resurs hantering**, väljer du **nycklar och slut punkt**.

Varje prenumeration har två nycklar. Du kan använda någon av nycklarna i ditt program. Om du vill kopiera eller klistra in en nyckel i kod redigeraren eller på en annan plats väljer du kopierings knappen bredvid varje nyckel, växlar fönster så att urklipps innehållet klistras in på önskad plats.

Kopiera dessutom `LOCATION` värdet, vilket är ditt regions-ID (t. ex. `westus`, `westeurope` ) för SDK-anrop.

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt – till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="complete-a-quickstart"></a>Slutför en snabb start

Vi erbjuder snabb starter i de flesta populära programmeringsspråk, som var och en har utformats för att lära dig grundläggande design mönster, och du kan köra kod på mindre än 10 minuter. Se följande lista för snabb starten för varje funktion.

* [Snabbstart för tal-till-text](get-started-speech-to-text.md)
* [Snabbstart för text till tal](get-started-text-to-speech.md)
* [Snabbstart för talöversättning](./get-started-speech-translation.md)
* [Snabbstart för avsiktsigenkänning](./get-started-intent-recognition.md)
* [Snabb start för högtalar igenkänning](./get-started-speaker-recognition.md)

När du har fått chansen att komma igång med tal tjänsten kan du prova våra självstudier som visar hur du kan lösa olika scenarier.

- [Självstudie: identifiera avsikter från tal med talet SDK och LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Självstudie: röst aktivera din robot med talet SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Självstudie: Bygg en kolv-app för att översätta text, analysera sentiment och syntetisera översatt text till tal, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Hämta exempelkod

Exempel kod finns på GitHub för tal tjänsten. De här exemplen beskriver vanliga scenarier som att läsa ljud från en fil eller ström, kontinuerlig och enkel igenkänning och arbeta med anpassade modeller. Använd dessa länkar om du vill visa SDK: er och REST-exempel:

- [Exempel på tal-till-text-text till tal och tal översättning (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-avskrifts exempel (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Anpassa din tal upplevelse

Röst tjänsten fungerar bra med inbyggda modeller, men du kanske vill anpassa och justera upplevelsen för din produkt eller miljö ytterligare. Anpassnings alternativ sträcker sig från akustisk modell justering till unika röst teckensnitt för ditt varumärke.

Andra produkter erbjuder tal modeller som är justerade för speciella syfte, t. ex. sjukvård eller försäkring, men som är tillgängliga för alla lika. Anpassning i Azure tal blir en del av *din unika* konkurrens för del som inte är tillgänglig för någon annan användare eller kund. Med andra ord är dina modeller privata och anpassade efter ditt användnings fall.

| Tjänst för taligenkänning | Plattform | Beskrivning |
| -------------- | -------- | ----------- |
| Tal till text | [Custom Speech](https://aka.ms/customspeech) | Anpassa tal igenkännings modeller till dina behov och tillgängliga data. Kringgå hinder för tal igenkänning, till exempel tal format, vokabulär och bakgrunds ljud. |
| Text till tal | [Anpassad röst](https://aka.ms/customvoice) | Skapa en identifierbar, unik röst för dina text-till-tal-appar med dina tillgängliga taldata. Du kan finjustera röst utmatningarna ytterligare genom att justera en uppsättning röst parametrar. |

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuera lokalt med Docker-behållare

[Använd tal tjänst behållare](speech-container-howto.md) för att distribuera API-funktioner lokalt. Med dessa Docker-behållare kan du ta tjänsten närmare dina data för efterlevnad, säkerhet eller andra drift orsaker. Tal tjänsten erbjuder följande behållare:

* Standard tal till text
* Custom Speech till text
* Standard text till tal
* Neurala text till tal
* Anpassad text till tal (för hands version)
* Tal Språkidentifiering (förhands granskning)

## <a name="reference-docs"></a>Referens dokument

- [Speech SDK](./speech-sdk.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: tal till text](rest-speech-to-text.md)
- [REST API: text till tal](rest-text-to-speech.md)
- [REST API: batch-avskrift och anpassning](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med tal till text](./get-started-speech-to-text.md) 
>  [Kom igång med text till tal](get-started-text-to-speech.md)