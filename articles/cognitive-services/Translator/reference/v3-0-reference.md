---
title: Translator V 3.0-referens
titleSuffix: Azure Cognitive Services
description: Referens dokumentation för Translator V 3.0. Version 3 av Translator tillhandahåller ett modernt JSON-baserat webb-API.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 567e28ee7f698565d6ad0020db7abdca0557f053
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650770"
---
# <a name="translator-v30"></a>Translator v 3.0

## <a name="whats-new"></a>Nyheter

Version 3 av Translator tillhandahåller ett modernt JSON-baserat webb-API. Det förbättrar användbarhet och prestanda genom att konsolidera befintliga funktioner i färre operationer och den innehåller nya funktioner.

 * Transkriberingsspråk att konvertera text på ett språk från ett skript till ett annat.
 * Översättning till flera språk i en begäran.
 * Språk identifiering, översättning och transkriberingsspråk i en begäran.
 * Ord lista för att söka efter alternativa översättningar av en term, för att hitta tillbaka-översättningar och exempel som visar termer som används i sammanhang.
 * Fler informativa språk identifierings resultat.

## <a name="base-urls"></a>Bas-URL: er

Microsoft Translator hanteras av flera data Center platser. De finns för närvarande i 10 [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/regions)områden:

* **Amerika:** Östra USA, södra centrala USA, västra centrala USA och västra USA 2 
* **Asien och Stillahavsområdet:** Södra Korea, Östra Japan, Sydostasien och östra Australien
* **Europa:** Nord Europa, Västeuropa, Schweiz, norra <sup>1, 2</sup>och Schweiz, västra <sup>1, 2</sup>

Begär anden till Microsoft Translator hanteras i de flesta fall av data centret som är närmast den plats där begäran kommer. Om ett Data Center haveri skulle Miss lyckas kan begäran dirigeras utanför Azure-geografien.

Om du vill tvinga begäran att hanteras av ett visst Azure-geografi ändrar du den globala slut punkten i API-begäran till önskad geografisk slut punkt:

|Description|Azure geografi|Bas-URL (geografisk slut punkt)|
|:--|:--|:--|
|Azure|Global (icke-regional)|   api.cognitive.microsofttranslator.com|
|Azure|USA|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asien och stillahavsområdet|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> kund med en resurs som finns i Schweiz, norra eller Schweiz, västra kan se till att deras text-API-begäranden hanteras i Schweiz. För att säkerställa att förfrågningar hanteras i Schweiz skapar du Translator-resursen i "resurs region" Schweiz, norra eller Schweiz, västra och använder sedan resursens anpassade slut punkt i API-begärandena. Exempel: om du skapar en översättare-resurs i Azure Portal med "resurs region" som "Schweiz, norra" och resurs namnet är "My-CH-n" är din anpassade slut punkt " https://my-ch-n.cognitiveservices.azure.com ". Och en exempel förfrågan att översätta är:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> Anpassad översättare är för närvarande inte tillgänglig i Schweiz.

## <a name="authentication"></a>Autentisering

Prenumerera på Translator eller [Cognitive Services multi-service](https://azure.microsoft.com/pricing/details/cognitive-services/) i Azure Cognitive Services och Använd din prenumerations nyckel (tillgänglig i Azure Portal) för att autentisera. 

Det finns tre huvuden som du kan använda för att autentisera din prenumeration. Den här tabellen beskriver hur var och en används:

|Sidhuvuden|Description|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Använd med Cognitive Services prenumeration om du skickar den hemliga nyckeln*.<br/>Värdet är Azures hemliga nyckel för din prenumeration på Translator.|
|Auktorisering|*Använd med Cognitive Services prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är Bearer-token: `Bearer <token>` .|
|OCP-APIM-Subscription-region|*Använd med Cognitive Services flera tjänst-och regional Translator-resurser.*<br/>Värdet är regionen för resursen för flera tjänst-eller regionala översättare. Det här värdet är valfritt när du använder en global translator-resurs.|

###  <a name="secret-key"></a>Hemlig nyckel
Det första alternativet är att autentisera med hjälp av- `Ocp-Apim-Subscription-Key` huvudet. Lägg till `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` rubriken i din begäran.

#### <a name="authenticating-with-a-global-resource"></a>Autentisera med en global resurs

När du använder en [Global Translator-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)måste du inkludera en rubrik för att anropa översättaren.

|Sidhuvuden|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är Azures hemliga nyckel för din prenumeration på Translator.|

Här är en exempel förfrågan om att anropa Translator med hjälp av den globala Translator-resursen

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autentisera med en regional resurs

När du använder en [lokal översättare-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Det finns två huvuden som du måste anropa Translator.

|Sidhuvuden|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är Azures hemliga nyckel för din prenumeration på Translator.|
|OCP-APIM-Subscription-region| Värdet är den region där Translator-resursen finns. |

Här är en exempel förfrågan om att anropa Translator med hjälp av den regionala Translator-resursen

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autentisering med en resurs med flera tjänster

När du använder en kognitiv tjänsts resurs för flera tjänster. På så sätt kan du använda en enskild hemlig nyckel för att autentisera begär Anden för flera tjänster. 

När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringsscheman med din begäran. Det finns två huvuden som du måste anropa Translator.

|Sidhuvuden|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är Azures hemliga nyckel för din multi-service-resurs.|
|OCP-APIM-Subscription-region| Värdet är regionen för flera tjänst resurser. |

Region krävs för multi-service text API-prenumerationen. Den region du väljer är den enda region som du kan använda för text översättning när du använder prenumerations nyckeln för flera tjänster och måste vara samma region som du valde när du registrerade dig för din prenumeration på flera tjänster via Azure Portal.

Tillgängliga regioner är,,,,,,,,,,,,,,,,,,,, `australiaeast` `brazilsouth` `canadacentral` `centralindia` `centralus` `centraluseuap` `eastasia` `eastus` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral` `northcentralus` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` och `southafricanorth` .

Om du skickar den hemliga nyckeln i frågesträngen med parametern `Subscription-Key` måste du ange regionen med Frågeparametern `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Autentisera med en åtkomsttoken
Alternativt kan du byta hemlig nyckel för en åtkomsttoken. Denna token ingår i varje begäran som `Authorization` rubrik. Om du vill hämta en autentiseringstoken gör du en `POST` begäran till följande URL:

| Resurstyp     | URL för autentiseringstjänst                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regional eller multi-service | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Här är exempel begär Anden om att hämta en token som har fått en hemlig nyckel:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

En lyckad begäran returnerar kodad åtkomsttoken som oformaterad text i svars texten. En giltig token skickas till Translator-tjänsten som en Bearer-token i auktoriseringen.

```http
Authorization: Bearer <Base64-access_token>
```

En autentiseringstoken är giltig i 10 minuter. Token ska återanvändas när du gör flera anrop till Translator. Men om programmet skickar begär anden till översättaren under en längre tid måste programmet begära en ny åtkomsttoken med jämna mellanrum (till exempel var 8: e minut).

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk

Translator-tjänsten är nu tillgänglig med Virtual Network-funktioner (VNET) i alla regioner i det offentliga Azure-molnet. Om du vill aktivera Virtual Network kan du läsa [Konfigurera Azure Cognitive Services Virtual Networks](../../cognitive-services-virtual-networks.md?tabs=portal). 

När du har aktiverat den här funktionen måste du använda den anpassade slut punkten för att anropa Translator. Du kan inte använda den globala Translator-slutpunkten ("api.cognitive.microsofttranslator.com") och du kan inte autentisera med en åtkomsttoken.

Du hittar den anpassade slut punkten när du har skapat en [översättare-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) och tillåter åtkomst från valda nätverk och privata slut punkter.

|Sidhuvuden|Description|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är Azures hemliga nyckel för din prenumeration på Translator.|
|OCP-APIM-Subscription-region| Värdet är den region där Translator-resursen finns. Det här värdet är valfritt om resursen är `global`|

Här är en exempel förfrågan om att anropa Translator med den anpassade slut punkten

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Fel

Ett standard fel svar är ett JSON-objekt med namnet/värde-paret med namnet `error` . Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En server-definierad felkod.
  * `message`: En sträng som ger en läslig representation av felet.

Till exempel skulle en kund med en kostnads fri utvärderings prenumeration få följande fel när den kostnads fria kvoten har uppnåtts:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga fel koder är:

| Kod | Description |
|:----|:-----|
| 400000| En av begärda indata är inte giltiga.|
| 400001| Parametern "scope" är ogiltig.|
| 400002| Parametern "Category" är ogiltig.|
| 400003| En språk specifikation saknas eller är ogiltig.|
| 400004| En mål skripts specifikation ("till skript") saknas eller är ogiltig.|
| 400005| En inmatad text saknas eller är ogiltig.|
| 400006| Kombinationen av språk och skript är ogiltig.|
| 400018| En identifierare för käll skript (från skript) saknas eller är ogiltig.|
| 400019| Ett av de angivna språken stöds inte.|
| 400020| Ett av elementen i matrisen med inmatad text är inte giltigt.|
| 400021| Parametern för API-versionen saknas eller är ogiltig.|
| 400023| Ett av de angivna språk paret är inte giltigt.|
| 400035| Käll språket ("från"-fältet) är inte giltigt.|
| 400036| Mål språket ("till"-fältet) saknas eller är ogiltigt.|
| 400042| Ett av de angivna alternativen ("alternativ"-fältet) är inte giltigt.|
| 400043| ID för klient spårning (ClientTraceId-fält eller X-ClientTranceId-huvud) saknas eller är ogiltigt.|
| 400050| Inmatad text är för lång. Visa [begränsningar för förfrågningar](../request-limits.md).|
| 400064| Parametern "översättning" saknas eller är ogiltig.|
| 400070| Antalet mål skript (ToScript parameter) matchar inte antalet mål språk (till parameter).|
| 400071| Värdet är inte giltigt för TextType.|
| 400072| Matrisen med inmatad text innehåller för många element.|
| 400073| Skript parametern är inte giltig.|
| 400074| Bröd texten i begäran är inte giltig JSON.|
| 400075| Kombinationen av språk par och kategori är ogiltig.|
| 400077| Maximal storlek för begäran har överskridits. Visa [begränsningar för förfrågningar](../request-limits.md).|
| 400079| Det anpassade systemet som begärdes för översättning mellan från och till språk finns inte.|
| 400080| Transkriberingsspråk stöds inte för språket eller skriptet.|
| 401000| Begäran är inte auktoriserad eftersom autentiseringsuppgifterna saknas eller är ogiltiga.|
| 401015| "De angivna autentiseringsuppgifterna är för Speech API. Den här begäran kräver autentiseringsuppgifter för text-API: et. Använd en prenumeration på Translator. "|
| 403000| Åtgärden är inte tillåten.|
| 403001| Åtgärden tillåts inte eftersom prenumerationen har överskridit den kostnads fria kvoten.|
| 405000| Metoden Request stöds inte för den begärda resursen.|
| 408001| Det begärda översättnings systemet förbereds. Försök igen om några minuter.|
| 408002| Tids gränsen för begäran överskreds i väntan på inkommande data ström. Klienten genererade inte någon begäran inom den tid då servern var beredd på att vänta. Klienten kan upprepa begäran utan ändringar vid ett senare tillfälle.|
| 415000| Content-Type-huvudet saknas eller är ogiltigt.|
| 429000, 429001, 429002| Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.|
| 500000| Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med datum/tid för felet, begärande-ID från svars huvudet X-RequestId och klient-ID: n från begär ande huvudet X-ClientTraceId.|
| 503000| Tjänsten är inte tillgänglig för tillfället. Försök igen. Om felet kvarstår rapporterar du det med datum/tid för felet, begärande-ID från svars huvudet X-RequestId och klient-ID: n från begär ande huvudet X-ClientTraceId.|

## <a name="metrics"></a>Mått 
Med mått kan du Visa information om användning och tillgänglighet för översättare i Azure Portal under mått avsnittet som visas i skärm bilden nedan. Mer information finns i [data-och plattforms mått](../../../azure-monitor/essentials/data-platform-metrics.md).

![Translator-mått](../media/translatormetrics.png)

I den här tabellen listas tillgängliga mått med en beskrivning av hur de används för att övervaka API-anrop för översättning.

| Mått | Description |
|:----|:-----|
| TotalCalls| Totalt antal API-anrop.|
| TotalTokenCalls| Totalt antal API-anrop via token service med autentiseringstoken.|
| SuccessfulCalls| Antal lyckade anrop.|
| TotalErrors| Antal anrop med fel svar.|
| BlockedCalls| Antal anrop som har överskridit frekvensen eller kvot gränsen.|
| ServerErrors| Antal anrop med internt Server fel (5XX).|
| ClientErrors| Antal anrop med fel på klient sidan (4XX).|
| Svarstid| Varaktighet för att slutföra begäran i millisekunder.|
| CharactersTranslated| Totalt antal tecken i begäran om inkommande text.|
