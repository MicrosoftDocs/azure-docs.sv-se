---
title: Vanliga frågor och svar om Cognitive Services behållare
titleSuffix: Azure Cognitive Services
description: Vanliga frågor och svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 71b57eae1a66e6966f61123e638c4790410ef445
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862536"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Cognitive Services containers

## <a name="general-questions"></a>Allmänna frågor

**F: Vad är tillgängligt?**

**A:** Med Azure Cognitive Services-behållare kan utvecklare använda samma intelligenta API: er som är tillgängliga i Azure, men med [fördelarna](../cognitive-services-container-support.md#features-and-benefits) med skapa behållare. Vissa behållare är tillgängliga som en gated Preview-version som kan kräva att ett program får åtkomst. Andra behållare är offentligt tillgängliga som en icke-grind för hands version eller är allmänt tillgängliga. Du hittar en fullständig lista över behållare och deras tillgänglighet i stöd för [behållare i Azure Cognitive Services](../cognitive-services-container-support.md) -artikeln. Du kan också Visa behållare i [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**F: finns det någon skillnad mellan Cognitive Services molnet och behållarna?**

**A:** Cognitive Services behållare är ett alternativ till Cognitive Services molnet. Behållare erbjuder samma funktioner som motsvarande moln tjänster. Kunder kan distribuera behållare lokalt eller i Azure. Kärn tekniken för AI, pris nivåer, API-nycklar och API-signatur är samma som för behållaren och motsvarande moln tjänster. Här är [funktionerna och fördelarna](../cognitive-services-container-support.md#features-and-benefits) med att välja behållare över deras moln tjänst motsvarande.

**F: Hur gör jag för att åtkomst och användning av en gated Preview-behållare?**

**A:** Tidigare var gated Preview-behållare värdbaserade på `containerpreview.azurecr.io` lagrings platsen. Från och med september 22 2020 är dessa behållare värdbaserade på Microsoft-Container Registry och om du laddar ned dem behöver du inte använda kommandot Docker login. Du kan köra en port för förhands granskning om din Azure-resurs skapades med det godkända ID: t för Azure-prenumerationen. Du kommer inte att kunna köra behållaren om din Azure-prenumeration inte har godkänts efter att du fyllt i [formuläret för begäran](https://aka.ms/csgate).


**F: kommer behållare att vara tillgängliga för alla Cognitive Services och vilka är nästa uppsättning behållare som vi ska förvänta oss?**

**A:** Vi vill göra fler Cognitive Services tillgängliga som container erbjudanden. Kontakta din lokala Microsoft-konto Manager för att få uppdateringar om nya behållar versioner och andra Cognitive Servicess meddelanden.

**F: Vad är Service-Level avtalet (SLA) för Cognitive Services behållare?**

**A:** Cognitive Services behållare har inget service avtal (SLA).

Cognitive Services behållar konfiguration av resurser styrs av kunder, så Microsoft erbjuder inte något service avtal för allmän tillgänglighet (GA). Kunderna är kostnads fria att distribuera behållare lokalt, vilket innebär att de definierar värd miljöerna.

> [!IMPORTANT]
> Mer information om Cognitive Services Service-Level avtal [finns på vår SLA-sida](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/).

**F: finns de här behållarna tillgängliga i suveräna moln?**

**A:** Alla är inte bekanta med termen "suveräna moln", så vi börjar med att definiera:

> Det "suveräna molnet" består av [Azure Government](../../azure-government/documentation-government-welcome.md), [Azure Germany](../../germany/germany-welcome.md)och [Azure Kina 21Vianet](/azure/china/overview-operations) -moln.

Tyvärr stöds *inte* Cognitive Services-behållare internt i de suveräna molnen. Behållarna kan köras i dessa moln, men de kommer att hämtas från det offentliga molnet och måste skicka användnings data till den offentliga slut punkten.

### <a name="versioning"></a>Versionshantering

**F: Hur uppdateras behållare till den senaste versionen?**

**A:** Kunderna kan välja när de behållare som de har distribuerat ska uppdateras. Behållare kommer att markeras med standard [Docker-Taggar](https://docs.docker.com/engine/reference/commandline/tag/) , till exempel `latest` för att ange den senaste versionen. Vi uppmuntrar kunderna att hämta den senaste versionen av behållare när de släpps, check [Azure Container Registry Webhooks](../../container-registry/container-registry-webhook.md) för att få information om hur du får ett meddelande när en avbildning uppdateras.
 
**F: vilka versioner kommer att stödjas?**

**A:** Den aktuella och sista huvud versionen av behållaren kommer att stödjas. Vi uppmuntrar dock kunderna att hålla sig uppdaterade för att få den senaste tekniken.
 
**F: Hur uppdateras versioner?**

**A:** Huvud versions ändringar indikerar att det finns en brytande ändring i API-signaturen. Vi förväntar dig att detta kommer att sammanfalla med större versions ändringar av motsvarande kognitiva tjänst moln erbjudande. Lägre versions ändringar indikerar fel korrigeringar, modell uppdateringar eller nya funktioner som inte gör en större ändring i API-signaturen.

## <a name="technical-questions"></a>Tekniska frågor

**F: Hur ska jag köra Cognitive Services-behållare på IoT-enheter?**

**A:** Om du inte har en tillförlitlig Internet anslutning eller om du vill spara pengar på bandbredd. Eller om du har krav på låg latens eller hanterar känsliga data som behöver analyseras på plats, [Azure IoT Edge med Cognitive Services-containrar](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/) ger dig konsekvens med molnet.

**F: är de här behållarna kompatibla med OpenShift?** 

Vi testar inte behållare med OpenShift, men normalt bör Cognitive Services behållare köras på alla plattformar som stöder Docker-avbildningar. Om du använder OpenShift rekommenderar vi att du kör behållarna som `root-user` .

**F: Hur gör jag för att ge feedback om produkter och funktions rekommendationer?**

**A:** Kunderna uppmuntras att vara indelade [offentligt och att rösta på andra](https://cognitive.uservoice.com/) som har gjort samma där potentiella problem överlappar varandra. Användar röst verktyget kan användas för både produkt feedback och funktions rekommendationer.

**F: vilka status meddelanden och fel returneras av Cognitive Services behållare?**

**A:** I följande tabell visas en lista över status meddelanden och fel.

|Status  | Beskrivning  |
|---------|---------|
| `Valid` | Din API-nyckel är giltig, ingen åtgärd krävs. |
| `Invalid` |   Din API-nyckel är ogiltig. Du måste ange en giltig API-nyckel för att köra behållaren. Hitta din API-nyckel och tjänst region i avsnittet **nycklar och slut punkt** för Azure Cognitive Services-resursen i Azure Portal. |
| `Mismatch` | Du har angett en API-nyckel eller en slut punkt för en annan typ av kognitiva tjänst resurser. Hitta din API-nyckel och tjänst region i avsnittet **nycklar och slut punkt** för din Azure Cognitive Services-resurs. |
| `CouldNotConnect` | Behållaren kunde inte ansluta till fakturerings slut punkten. Kontrol lera `Retry-After` värdet och vänta tills den här perioden har slutförts innan du gör ytterligare förfrågningar. |
| `OutOfQuota` | API-nyckeln är utanför kvoten. Du kan antingen uppgradera pris nivån eller vänta tills ytterligare kvot görs tillgänglig. Hitta din nivå i avsnittet **pris nivå** i din Azure-tjänst resurs, i Azure Portal. |
| `BillingEndpointBusy` | Fakturerings slut punkten är upptagen för tillfället. Kontrol lera `Retry-After` värdet och vänta tills den här perioden har slutförts innan du gör ytterligare förfrågningar. |
| `ContainerUseUnauthorized` | Den angivna API-nyckeln har inte behörighet att använda med den här behållaren. Du använder förmodligen en gated-behållare, så se till att ditt Azure-prenumerations-ID godkänns genom att skicka en [online-begäran](https://aka.ms/csgate). |
| `Unknown` | Servern kan för närvarande inte bearbeta fakturerings begär Anden. |


**F: Vem kontaktar jag för support?**

**A:** Kund support kanaler är desamma som Cognitive Services Cloud-erbjudandet. Alla Cognitive Services-behållare innehåller loggnings funktioner som hjälper oss och community Support-kunder. Mer support finns i följande alternativ.

### <a name="customer-support-plan"></a>Kund support avtal

Kunderna bör hänvisa till support [avtalet för Azure](https://azure.microsoft.com/support/plans/) för att se vem som ska kontaktas.

### <a name="azure-knowledge-center"></a>Azures kunskaps Center

Kunden är kostnads fri att utforska [Azures kunskaps Center](https://azure.microsoft.com/resources/knowledge-center/) för att få svar på frågor och support ärenden.

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) är en fråge-och svars plats för professionella och amatör programmerare.

Utforska följande taggar för potentiella frågor och svar som passar dina behov.

* [Azure Cognitive Services](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft kognitivt](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**F: Hur fungerar faktureringen?**

**A:** Kunderna debiteras baserat på förbrukning, som liknar Cognitive Services molnet. Behållarna måste konfigureras för att skicka Mät data till Azure, och transaktioner faktureras därefter. Resurser som används i värdbaserade och lokala tjänster kommer att läggas till i en enda kvot med utgångs punkt i priser och beräknas mot båda användningarna. Mer information finns på sidan med priser för motsvarande erbjudande.

* [Avvikelseidentifiering][ad-containers-billing]
* [Visuellt innehåll][cv-containers-billing]
* [Ansiktsigenkänning][fa-containers-billing]
* [Formigenkänning][fr-containers-billing]
* [Language Understanding (LUIS)][lu-containers-billing]
* [Speech Service API][sp-containers-billing]
* [Textanalys][ta-containers-billing]

> [!IMPORTANT]
> Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information till Microsoft.
 
**F: Vad är den aktuella support garantin för behållare?**

**A:** Det finns ingen garanti för för hands versionerna. Microsofts standard garanti för företags program vara gäller när behållare är formellt presenterade som allmän tillgänglighet (GA).
 
**F: Vad händer med att Cognitive Services behållare när Internet anslutningen förloras?**

**A:** Cognitive Services behållare är *inte licensierade* att köras utan att vara anslutna till Azure för mätning. Kunder måste göra det möjligt för behållarna att kommunicera med mätnings tjänsten hela tiden.

**F: hur länge kan behållaren köras utan att vara ansluten till Azure?**

**A:** Cognitive Services behållare är *inte licensierade* att köras utan att vara anslutna till Azure för mätning. Kunder måste göra det möjligt för behållarna att kommunicera med mätnings tjänsten hela tiden.
 
**F: Vad är den aktuella maskin varan som krävs för att köra dessa behållare?**

**A:** Cognitive Services behållare är x64-baserade behållare som kan köra valfri kompatibel Linux-nod, virtuell dator och Edge-enhet som stöder x64 Linux Docker-behållare. Alla kräver processor processorer. De minsta och rekommenderade konfigurationerna för varje behållar erbjudande är tillgängliga nedan:

* [Avvikelseidentifiering][ad-containers-recommendations]
* [Visuellt innehåll][cv-containers-recommendations]
* [Ansiktsigenkänning][fa-containers-recommendations]
* [Formigenkänning][fr-containers-recommendations]
* [Language Understanding (LUIS)][lu-containers-recommendations]
* [Speech Service API][sp-containers-recommendations]
* [Textanalys][ta-containers-recommendations]
 
**F: finns de här behållarna som stöds i Windows?**

**A:** Cognitive Services behållare är Linux-behållare, men det finns vissa stöd för Linux-behållare i Windows. Mer information om Linux-behållare i Windows finns i [Docker-dokumentationen](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/).
 
**F: Hur gör jag för att identifiera behållarna?**

**A:** Cognitive Services behållare finns på olika platser, till exempel Azure Portal, Docker Hub och Azure Container Registration. De senaste behållar platserna finns i [behållar avbildningar](container-image-tags.md).

**F: Hur jämför Cognitive Services containers med AWS och Google-erbjudanden?**

**A:** Microsoft är den första moln leverantören att flytta sina förtränade AI-modeller i behållare med enkel fakturering per transaktion som även om kunderna använder en moln tjänst. Microsoft anser att ett hybrid moln ger kunderna mer valmöjligheter.

**F: vilka certifieringar för regelefterlevnad har behållare?**

**A:** Kognitiva tjänst behållare har inga certifieringar för regelefterlevnad

**F: vilka regioner är Cognitive Services behållare tillgängliga i?**

**A:** Behållare kan köras var som helst i vilken region som helst, men de behöver en nyckel och för att kunna ringa tillbaka till Azure för mätning. Alla regioner som stöds för moln tjänsten stöds för container mätar anropet.

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations