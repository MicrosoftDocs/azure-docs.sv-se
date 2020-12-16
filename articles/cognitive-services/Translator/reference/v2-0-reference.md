---
title: Translator v 2.0
titleSuffix: Azure Cognitive Services
description: Referens dokumentation för Translator v 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: 25dda63c6450040e396de9ee0d3fb0a459416343
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606836"
---
# <a name="translator-v20"></a>Translator v 2.0

> [!IMPORTANT]
> Den här versionen av Translator är inaktuell. [Visa dokumentationen för version 3 av Translator](v3-0-reference.md).

Version 2 av Translator kan integreras sömlöst i appar, webbplatser, verktyg eller andra lösningar för att tillhandahålla användar upplevelser med flera språk. Du kan använda den på valfri maskin varu plattform och med alla operativ system för att utföra översättning av språk och andra språkbaserade uppgifter, t. ex. text språk identifiering och text till tal, enligt bransch standarder. Mer information finns i [Translator](../translator-info-overview.md).

## <a name="getting-started"></a>Komma igång
För att få åtkomst till Translator måste du [Registrera dig för Microsoft Azure](../translator-how-to-signup.md).

## <a name="authentication"></a>Autentisering 
Alla anrop till Translator kräver en prenumerations nyckel för autentisering. API: et stöder tre metoder för autentisering:

- En åtkomsttoken. Använd prenumerations nyckeln för att skapa en åtkomsttoken genom att göra en POST-begäran till Autentiseringstjänsten. Mer information finns i dokumentationen för token service. Skicka åtkomsttoken till Translator-tjänsten med hjälp av `Authorization` rubriken eller `access_token` Frågeparametern. Åtkomsttoken är giltig i 10 minuter. Hämta en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomsttoken för upprepade begär Anden under 10 minuter.
- En prenumerations nyckel som används direkt. Skicka din prenumerations nyckel som ett värde i `Ocp-Apim-Subscription-Key` rubriken som ingår i din begäran till Translator. När du använder prenumerations nyckeln direkt behöver du inte anropa tjänsten token Authentication för att skapa en åtkomsttoken.
- En [Azure Cognitive Services-prenumeration med flera tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/). Med den här metoden kan du använda en enskild hemlig nyckel för att autentisera begär Anden för flera tjänster.
När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringsscheman med din begäran. Den första rubriken skickar den hemliga nyckeln. I den andra rubriken anges den region som är kopplad till din prenumeration:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Regionen krävs för API-prenumerationen för flera service text. Den region du väljer är den enda region som du kan använda för text översättning när du använder prenumerations nyckeln för flera tjänster. Det måste vara samma region som du valde när du registrerade dig för en prenumeration på flera tjänster på Azure Portal.

De tillgängliga regionerna är,,,,,,,,,,,, `australiaeast` `brazilsouth` `canadacentral` `centralindia` `centraluseuap` ,, `eastasia` `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` och `westus2` .

Din prenumerations nyckel och åtkomsttoken är hemligheter som ska döljas i vyn.

## <a name="profanity-handling"></a>Hantering av svordomar
Översättnings tjänsten behåller vanligt vis svordomar som finns i källan. Graden av svordomar och kontexten som gör ord svordomar olika beroende på kulturen. Det går att öka eller minska antalet svordomar på mål språket.

Om du vill förhindra svordomar i översättningen även om den är i käll texten, kan du använda alternativet för att filtrera efter svordomar för de metoder som har stöd för det. Med alternativet kan du välja om du vill se hur svordomar tas bort eller markeras med lämpliga taggar, eller om du vill tillåta svordomarna i målet. De godkända värdena för `ProfanityAction` är `NoAction` (standard), `Marked` och `Deleted` .


|ProfanityAction    |Åtgärd |Exempel källa (japanska)  |Exempel översättning (engelska)  |
|:--|:--|:--|:--|
|Noaction   |Standard. Samma som att inte ställa in alternativet. Svordomar skickas från källa till mål. |彼はジャッカスです från en omfattande |Han är en Jackass.  |
|Klassificera |Svordomar ord omges av XML-taggar \<profanity> och \</profanity> . |彼はジャッカスです från en omfattande   |Han är en \<profanity> Jackass \</profanity> .  |
|Borttagen    |Svordomar ord tas bort från utdata utan ersättning. |彼はジャッカスです från en omfattande |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Undanta innehåll från översättning
När du översätter innehåll med taggar som HTML ( `contentType=text/html` ) är det ibland användbart att undanta ett särskilt innehåll från översättningen. Du kan använda attributet `class=notranslate` för att ange innehåll som ska finnas kvar på det ursprungliga språket. I följande exempel `div` översätts inte innehållet i det första elementet, men innehållet i det andra `div` elementet kommer att översättas.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>Hämta/translate

### <a name="implementation-notes"></a>Implementerings anteckningar
Översätter en text sträng från ett språk till ett annat.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Translate` .

**RETUR värde:** En sträng som representerar den översatta texten.

Om du tidigare har använt `AddTranslation` eller `AddTranslationArray` för att ange en översättning med en klassificering på 5 eller högre för samma käll mening, `Translate` returnerar bara det bästa alternativet som är tillgängligt för ditt system. "Samma käll mening" betyder exakt samma (100% matchning), förutom versaler, blank steg, tagg värden och interpunktion i slutet av en mening. Om ingen klassificering lagras med en klassificering på 5 eller högre blir det returnerade resultatet den automatiska översättningen av Microsoft Translator.

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning    |Parameter typ|datatyp|
|:--|:--|:--|:--|:--|
|undanta  |saknas    |Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|text|saknas   |Krävs. En sträng som representerar texten som ska översättas. Texten får inte innehålla mer än 10 000 tecken.|DocumentDB|sträng|
|Från|saknas   |Valfritt. En sträng som representerar språk koden för den text som ska översättas. Till exempel en för engelska.|DocumentDB|sträng|
|på|saknas |Krävs. En sträng som representerar koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|Innehålls|saknas    |Valfritt. Formatet på texten som översätts. Format som stöds är `text/plain` (standard) och  `text/html` . Alla HTML-element måste vara välformulerade, kompletta element.|DocumentDB|sträng|
|category|saknas   |Valfritt. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|Auktorisering|saknas  |Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas  |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-translatearray"></a>PUBLICERA/TranslateArray

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar översättningar för flera käll texter.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` .

Här är formatet för begär ande texten:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Dessa element finns i `TranslateArrayRequest` :


* `AppId`: Obligatoriskt. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `AppId` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .
* `From`Valfritt. En sträng som representerar språk koden för den text som ska översättas. Om det här fältet lämnas tomt kommer svaret att innehålla resultatet av automatisk språk identifiering.
* `Options`Valfritt. Ett `Options` objekt som innehåller följande värden. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: Formatet på texten som ska översättas. De format som stöds är `text/plain` (standard), `text/xml` och `text/html` . Alla HTML-element måste vara välformulerade, kompletta element.
    - `ProfanityAction`: Anger hur svordomar hanteras, vilket förklaras tidigare. Godkända värden är `NoAction` (standard), `Marked` och `Deleted` .
    - `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
    - `Uri`: Filtrera resultat efter denna URI. Standard: `all`.
    - `User`: Filtrera resultat efter den här användaren. Standard: `all`.
* `Texts`: Obligatoriskt. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 2 000.
* `To`: Obligatoriskt. En sträng som representerar koden för det språk som texten ska översättas till.

Du kan utelämna valfria element. Element som är direkt underordnade `TranslateArrayRequest` måste anges i alfabetisk ordning.

`TranslateArray`Metoden accepterar `application/xml` eller `text/xml` för `Content-Type` .

**RETUR värde:** En `TranslateArrayResponse` matris. Var och en `TranslateArrayResponse` har följande element:

* `Error`: Anger ett fel om ett inträffar. Annars anges värdet null.
* `OriginalSentenceLengths`: En matris med heltal som anger längden på varje mening i käll texten. Längden på matrisen anger antalet meningar.
* `TranslatedText`: Den översatta texten.
* `TranslatedSentenceLengths`: En matris med heltal som anger längden på varje mening i den översatta texten. Längden på matrisen anger antalet meningar.
* `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Returnerar samma innehåll som begäran.

Här är formatet för svars texten:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Svars klass (status 200)
Ett lyckat svar innehåller en matris med `TranslateArrayResponse` matriser i det format som beskrivs ovan.

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|saknas  |Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret. Vanliga fel är: <ul><li>Mat ris element får inte vara tomt.</li><li>Ogiltig kategori.</li><li>Från-språket är ogiltigt.</li><li>Till språk är ogiltigt.</li><li>Begäran innehåller för många element.</li><li>Från-språket stöds inte.</li><li>To-språket stöds inte.</li><li>Översättnings förfrågan har för mycket data.</li><li>HTML har felaktigt format.</li><li>För många strängar skickades i översättnings förfrågan.</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-getlanguagenames"></a>PUBLICERA/GetLanguageNames

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar egna namn för de språk som skickats in som parameter `languageCodes` , som är lokaliserade till det skickade `locale` språket.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` .

Begär ande texten innehåller en sträng mat ris som representerar de ISO 639-1-språkkoder för vilka du kan hämta de egna namnen. Här är ett exempel:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**RETUR värde:** En sträng mat ris som innehåller språk namn som stöds av Translator-tjänsten och som är lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller språk namn som stöds av Translator-tjänsten och som lokaliseras till det begärda språket.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|locale|saknas |Krävs. En sträng som representerar något av följande, som används för att lokalisera språk namn: <ul><li>Kombinationen av en ISO 639 2-bokstav med gemener, som är associerad med ett språk och en ISO 3166 2-bokstav med versaler i under kultur kod. <li>En ISO 639-kultur med gemener.|DocumentDB|sträng|
|Auktorisering|saknas  |Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas  |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>Hämta/GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar en lista med språk koder som representerar språk som stöds av översättnings tjänsten.  `Translate` och `TranslateArray` kan översätta mellan två av dessa språk.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` .

**RETUR värde:** En sträng mat ris som innehåller de språk koder som stöds av Translator-tjänsten.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller de språk koder som stöds av Translator-tjänsten.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|Auktorisering|saknas  |Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>Hämta/GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar de språk som är tillgängliga för tal syntes.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` .

**RETUR värde:** En sträng mat ris som innehåller de språk koder som stöds för tal syntes av tjänsten Translator.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller de språk koder som stöds för tal syntes av tjänsten Translator.

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-speak"></a>Hämta/Speak

### <a name="implementation-notes"></a>Implementerings anteckningar
Returnerar en WAV-eller MP3-ström av den inskickade texten som talas på det önskade språket.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Speak` .

**RETUR värde:** En WAV-eller MP3-ström av den inskickade texten som talas på det önskade språket.

### <a name="response-class-status-200"></a>Svars klass (status 200)

binary

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|text|saknas   |Krävs. En sträng som innehåller en eller flera meningar som ska läsas upp för data strömmen på det angivna språket. Texten får inte överstiga 2 000 tecken.|DocumentDB|sträng|
|language|saknas   |Krävs. En sträng som representerar språk koden som stöds för det språk som texten ska mätas i. Koden måste vara en av de koder som returneras av metoden `GetLanguagesForSpeak` .|DocumentDB|sträng|
|format|saknas|Valfritt. En sträng som anger ID för innehålls typ. För närvarande,  `audio/wav` och `audio/mp3` är tillgängliga. Standardvärdet är `audio/wav`.|DocumentDB|sträng|
|alternativ|saknas    |Valfritt. En sträng som anger egenskaper för det syntetiskt tal:<ul><li>`MaxQuality` och `MinSize` anger kvaliteten på ljud signalen. `MaxQuality` ger högsta kvalitet. `MinSize` ger den minsta fil storleken. Standardvärdet är  `MinSize` .</li><li>`female` och `male` Ange önskat kön för rösten. Standardvärdet är `female`. Använd det lodräta strecket ( <code>\|</code> ) för att inkludera flera alternativ. Till exempel `MaxQuality|Male`.</li></li></ul>  |DocumentDB|sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas  |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-detect"></a>Hämta/detect

### <a name="implementation-notes"></a>Implementerings anteckningar
Identifierar språket för en del av texten.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Detect` .

**RETUR värde:** En sträng som innehåller en språk kod på två tecken för texten.

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas  |Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|text|saknas|Krävs. En sträng som innehåller text vars språk ska identifieras. Texten får inte överstiga 10 000 tecken.|DocumentDB|  sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key  |saknas    |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|


## <a name="post-detectarray"></a>PUBLICERA/DetectArray

### <a name="implementation-notes"></a>Implementerings anteckningar

Identifierar språken i en sträng mat ris. Identifierar oberoende språket för varje enskilt mat ris element och returnerar ett resultat för varje rad i matrisen.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` .

Här är formatet för begär ande texten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Texten får inte överstiga 10 000 tecken.

**RETUR värde:** En sträng mat ris som innehåller en språk kod på två tecken för varje rad i den angivna matrisen.

Här är formatet för svars texten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svars klass (status 200)
`DetectArray` lyckades. Returnerar en sträng mat ris som innehåller en språk kod på två tecken för varje rad i den angivna matrisen.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma.  Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-addtranslation"></a>Hämta/AddTranslation

### <a name="implementation-notes"></a>Implementerings anteckningar

> [!IMPORTANT]
> **Utfasnings anteckning:** Den här metoden accepterar inte nya menings bidrag efter den 31 januari 2018. Du får ett fel meddelande. Se meddelandet om ändringar i samarbets översättnings ramverket (CTF).

Lägger till en översättning till översättnings minnet.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` .

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: program: XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp   |
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|originalText|saknas|Krävs. En sträng som innehåller den text som ska översättas. Den maximala längden på strängen är 1 000 tecken.|DocumentDB|sträng|
|translatedText|saknas |Krävs. En sträng som innehåller text som har översatts till mål språket. Den maximala längden på strängen är 2 000 tecken.|DocumentDB|sträng|
|Från|saknas   |Krävs. En sträng som representerar språk koden för textens ursprungliga språk. Till exempel en för engelska och de för tyska.|DocumentDB|sträng|
|på|saknas|Krävs. En sträng som representerar språk koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|rating|saknas |Valfritt. Ett heltal som representerar strängens kvalitets betyg. Värdet är mellan-10 och 10. Standard är 1.|DocumentDB|heltal|
|Innehålls|saknas    |Valfritt. Formatet på texten som översätts. De format som stöds är `text/plain` och `text/html` . Alla HTML-element måste vara välformulerade, kompletta element.    |DocumentDB|sträng|
|category|saknas|Valfritt. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|användare|saknas|Krävs. En sträng som används för att spåra upphovs mannen för överföringen.|DocumentDB|sträng|
|URI|saknas|Valfritt. En sträng som innehåller översättningens innehålls plats.|DocumentDB|sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma.  Autentiseringstoken:  `"Bearer" + " " + "access_token"` .  |sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410|`AddTranslation` stöds inte längre.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-addtranslationarray"></a>PUBLICERA/AddTranslationArray

### <a name="implementation-notes"></a>Implementerings anteckningar

> [!IMPORTANT]
> **Utfasnings anteckning:** Den här metoden accepterar inte nya menings bidrag efter den 31 januari 2018. Du får ett fel meddelande. Se meddelandet om ändringar i samarbets översättnings ramverket (CTF).

Lägger till en matris med översättningar i översättnings minnet. Den här metoden är en mat ris version av `AddTranslation` .

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` .

Här är formatet för begär ande texten:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Dessa element finns i `AddtranslationsRequest` :

* `AppId`: Obligatoriskt. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `AppId` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .
* `From`: Obligatoriskt. En sträng som innehåller språk koden för käll språket. Måste vara ett av de språk som returnerades av `GetLanguagesForTranslate` metoden.
* `To`: Obligatoriskt. En sträng som innehåller språk koden för mål språket. Måste vara ett av de språk som returnerades av `GetLanguagesForTranslate` metoden.
* `Translations`: Obligatoriskt. En matris med översättningar som ska läggas till i översättnings minnet. Varje översättning måste innehålla `OriginalText` , `TranslatedText` och `Rating` . Den maximala storleken för varje `OriginalText` och `TranslatedText` är 1 000 tecken. Summan av alla `OriginalText` och `TranslatedText` element får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 100.
* `Options`: Obligatoriskt. En uppsättning alternativ, inklusive `Category` ,, `ContentType` `Uri` och `User` . `User` måste anges. `Category`, `ContentType` , och `Uri` är valfritt. De angivna elementen måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svars klass (status 200)
`AddTranslationArray` metoden lyckades. 

Efter den 31 januari 2018 kommer menings bidrag inte att accepteras. Tjänsten svarar med felkoden 410.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma.  Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410    |`AddTranslation` stöds inte längre.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-breaksentences"></a>Hämta/BreakSentences

### <a name="implementation-notes"></a>Implementerings anteckningar
Delar upp en del av texten i meningar och returnerar en matris som innehåller längden på varje mening.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` .

**RETUR värde:** En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

heltal

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas  |Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB| sträng|
|text|saknas   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|language   |saknas    |Krävs. En sträng som representerar språk koden för inmatad text.|DocumentDB|sträng|
|Auktorisering|saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .   |sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas|Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500|Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-gettranslations"></a>PUBLICERA/GetTranslations

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar en matris med översättningar för ett angivet språk par från Store och MT-motorn. `GetTranslations` skiljer sig från `Translate` i så att den returnerar alla tillgängliga översättningar.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` .

Bröd texten i begäran inkluderar det valfria `TranslationOptions` objektet, som har det här formatet:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

`TranslateOptions`Objektet innehåller värdena i följande lista. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.

* `Category`: En sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
* `ContentType`: Det enda alternativ som stöds och standardvärdet är `text/plain` .
* `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (Skift läges känsliga). Standardvärdet är `false` , som bara returnerar ett alternativ. Genom att ställa in flaggan kan du `true` skapa artificiella alternativ som är helt integrerade med CTF (Collaborative Translation Framework). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några översättningar i CTF genom att lägga till artificiella alternativ från den *n*-bästa listan över avkodaren.
    - Drivande. Klassificeringen tillämpas så här: 
         - Den bästa automatiska översättningen har en klassificering på 5.
       - Alternativen från CTF reflekterar behörigheten hos granskaren. De sträcker sig från-10 till + 10.
       - De automatiskt genererade översättnings alternativen (*n*-Best) har en klassificering på 0 och en matchnings grad på 100.
    - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som det värde som anges i `maxTranslations` , men det kan vara lägre.
    - Språk par. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språk par som stöds av Microsoft Translator.
* `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* `Uri`: Filtrera resultat efter denna URI. Om inget värde anges är standardvärdet `all` .
* `User`: Filtrera resultat efter den här användaren. Om inget värde anges är standardvärdet `all` .

Begäran `Content-Type` ska vara `text/xml` .

**RETUR värde:** Här är svars formatet:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Det här svaret innehåller ett- `GetTranslationsResponse` element som innehåller följande värden:

* `Translations`: En matris med matchningar som hittas, lagras i `TranslationMatch` objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av den ursprungliga texten (fuzzy Matching). Översättningarna sorteras: 100% matchar först och fuzzy matchar härnäst.
* `From`: Om metoden inte anger något `From` språk kommer det här värdet att komma från automatisk språk identifiering. Annars är det angivet `From` språk.
* `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Innehåller värdet som anges i `TranslateOptions` parametern.

`TranslationMatch`Objektet består av följande värden:

* `Error`: Felkoden, om det uppstår ett fel för en angiven indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära inmatad text som matchar den ursprungliga texten som finns i butiken. Systemet matchar inmatade meningar mot butiken, inklusive inexakta matchningar. Värdet returnerade intervall från 0 till 100, där 0 inte är något likhets värde och 100 är en exakt Skift läges känslig matchning.
* `MatchedOriginalText`: Ursprunglig text som har matchats för det här resultatet. Det här värdet returneras endast om den matchade ursprungliga texten skiljer sig från inmatad text. Den används för att returnera käll texten för en Fuzzy-matchning. Värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: Anger utfärdaren för den person som fattar kvalitets beslutet. Maskin översättnings resultat har en klassificering på 5. Anonymt tillhandahållna översättningar har vanligt vis en klassificering från 1 till 4. Översättning av auktoritativt tillhandahållna i allmänhet har en klassificering från 6 till och med 10.
* `Count`: Antalet gånger som denna översättning med den här klassificeringen har marker ATS. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.

### <a name="response-class-status-200"></a>Svars klass (status 200)
Ett `GetTranslationsResponse` objekt i det format som beskrivs ovan.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. `Authorization` `Ocp-Apim-Subscription-Key` Lämna fältet tomt om eller-rubriken används `appid` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .|DocumentDB|sträng|
|text|saknas|Krävs. En sträng som representerar texten som ska översättas. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|Från|saknas|Krävs. En sträng som representerar språk koden för den text som ska översättas.|DocumentDB|sträng|
|på |saknas    |Krävs. En sträng som representerar språk koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|maxTranslations|saknas|Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.|DocumentDB|heltal|
|Auktorisering| saknas|Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma. Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sträng|  sidhuvud|
|Ocp-Apim-Subscription-Key|saknas  |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info` .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>PUBLICERA/GetTranslationsArray

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar flera käll texter för olika översättnings kandidater.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` .

Här är formatet för begär ande texten:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` innehåller följande element:

* `AppId`: Obligatoriskt. `Authorization`Lämna fältet tomt om rubriken används `AppId` . Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"` .
* `From`: Obligatoriskt. En sträng som representerar språk koden för den text som ska översättas.
* `MaxTranslations`: Obligatoriskt. Ett heltal som representerar det maximala antalet översättningar som ska returneras.
* `Options`Valfritt. Ett `Options` objekt som innehåller följande värden. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: Det enda alternativ som stöds och standardvärdet är `text/plain` .
    - `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (Skift läges känsliga). Standardvärdet är `false` , som bara returnerar ett alternativ. Att ställa in flaggan så att det `true` går att skapa artificiella alternativ i översättning, helt integrerat med CTF (Collaborative Translation Framework). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några alternativ i CTF genom att lägga till artificiella alternativ från den *n*-bästa listan över avkodaren.
        - Klassificeringen tillämpas på följande sätt:
          - Den bästa automatiska översättningen har en klassificering på 5.
          - Alternativen från CTF reflekterar behörigheten hos granskaren. De sträcker sig från-10 till + 10.
          - De automatiskt genererade översättnings alternativen (*n*-Best) har en klassificering på 0 och en matchnings grad på 100.
        - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som det värde som anges i `maxTranslations` , men det kan vara lägre.
        - Språk par. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språk par som stöds av Microsoft Translator.
* `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* `Uri`: Filtrera resultat efter denna URI. Om inget värde anges är standardvärdet `all` .
* `User`: Filtrera resultat efter den här användaren. Om inget värde anges är standardvärdet `all` .
* `Texts`: Obligatoriskt. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 10.
* `To`: Obligatoriskt. En sträng som representerar språk koden för det språk som texten ska översättas till.

Du kan utelämna valfria element. Element som är direkt underordnade `GetTranslationsArrayRequest` måste anges i alfabetisk ordning.

Begäran `Content-Type` ska vara `text/xml` .

**RETUR värde:** Här är svars formatet:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Varje- `GetTranslationsResponse` element innehåller följande värden:

* `Translations`: En matris med matchningar som hittas, lagras i `TranslationMatch` objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av den ursprungliga texten (fuzzy Matching). Översättningarna sorteras: 100% matchar först och fuzzy matchar härnäst.
* `From`: Om metoden inte anger något `From` språk kommer det här värdet att komma från automatisk språk identifiering. Annars är det angivet `From` språk.
* `State`: Användar tillstånd som hjälper till att korrelera begäran och svar. Innehåller värdet som anges i `TranslateOptions` parametern.

`TranslationMatch`Objektet innehåller följande värden:
* `Error`: Felkoden, om det uppstår ett fel för en angiven indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära inmatad text som matchar den ursprungliga texten som finns i butiken. Systemet matchar inmatade meningar mot butiken, inklusive inexakta matchningar. Värdet returnerade intervall från 0 till 100, där 0 inte är något likhets värde och 100 är en exakt Skift läges känslig matchning.
* `MatchedOriginalText`: Ursprunglig text som har matchats för det här resultatet. Det här värdet returneras endast om den matchade ursprungliga texten skiljer sig från inmatad text. Den används för att returnera käll texten för en Fuzzy-matchning. Värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: Anger utfärdaren för den person som fattar kvalitets beslutet. Maskin översättnings resultat har en klassificering på 5. Anonymt tillhandahållna översättningar har vanligt vis en klassificering från 1 till 4. Auktoritärt tillhandahållna översättningar har vanligt vis en klassificering från 6 till och med 10.
* `Count`: Antalet gånger som denna översättning med den här klassificeringen har marker ATS. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.


### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering  |saknas    |Krävs om både `appid` fältet och `Ocp-Apim-Subscription-Key` rubriken lämnas tomma.  Autentiseringstoken:  `"Bearer" + " " + "access_token"` .|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|saknas  |Krävs om både `appid` fältet och `Authorization` rubriken lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet  `X-MS-Trans-Info` .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Translator v3](../migrate-to-v3.md)