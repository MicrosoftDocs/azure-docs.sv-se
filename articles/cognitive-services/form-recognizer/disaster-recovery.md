---
title: Vägledning för haveri beredskap för Azure formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder kopiera modell-API för att säkerhetskopiera formulär igenkännings resurser.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: b5eb776a7807f48ae6c1a0e3c5879da1f6823830
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466923"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Säkerhetskopiera och återställa formulär igenkännings modeller

När du skapar en formulär igenkännings resurs i Azure Portal anger du en region. Därefter förblir din resurs och alla dess åtgärder kopplade till just den Azure-Server regionen. Det är sällsynt, men inte omöjligt, att stöta på ett nätverks problem som träffar en hel region. Om din lösning alltid måste vara tillgänglig, bör du utforma den till att antingen redundansväxla till en annan region eller dela upp arbets belastningen mellan två eller flera regioner. Båda metoderna kräver minst två formulär igenkännings resurser i olika regioner och möjligheten att synkronisera anpassade modeller över regioner.

Kopierings-API: et aktiverar det här scenariot genom att du kan kopiera anpassade modeller från ett formulär igenkännings konto eller till andra, som kan finnas i en geografisk region som stöds. Den här guiden visar hur du använder kopierings REST API med en sväng. Du kan också använda en HTTP-begäran som Postman för att utfärda begär Anden.

## <a name="business-scenarios"></a>Affärs scenarier

Om din app eller ditt företag är beroende av användningen av en anpassad modell för formulär igenkänning rekommenderar vi att du kopierar din modell till ett annat formulär tolknings konto i en annan region. Om ett regionalt avbrott inträffar kan du sedan komma åt din modell i den region där den kopierades.

##  <a name="prerequisites"></a>Förutsättningar

1. Två formulär igenkännings Azure-resurser i olika Azure-regioner. Om du inte har dem går du till Azure Portal och <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" skapar en ny formulär igenkännings resurs " target="_blank"> skapa en ny formulär igenkännings resurs </a> .
1. Prenumerations nyckel, slut punkts-URL och prenumerations-ID för formulärets tolknings resurs. Du hittar dessa värden på resurs-fliken **Översikt** på Azure Portal.


## <a name="copy-api-overview"></a>Översikt över Copy API

Processen för att kopiera en anpassad modell består av följande steg:

1. Först utfärdar du en begäran om kopiering av begäran till mål resursen &mdash; som är den resurs som ska ta emot den kopierade modellen. Du får tillbaka URL: en för den nyligen skapade mål modellen, som kommer att ta emot de kopierade data.
1. Sedan skickar du kopierings förfrågan till käll resursen &mdash; som innehåller den modell som ska kopieras. Du får tillbaka en URL som du kan fråga efter för att följa åtgärdens förlopp.
1. Du ska använda dina käll resurs uppgifter för att ställa frågor till förlopps-URL tills åtgärden har slutförts. Du kan också fråga det nya modell-ID: t i mål resursen för att hämta statusen för den nya modellen.

## <a name="generate-copy-authorization-request"></a>Skapa begäran om Copy-auktorisering

Följande HTTP-begäran får kopiera auktorisering från mål resursen. Du måste ange slut punkten och nyckeln för mål resursen som rubriker.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Du får ett `201\Created` svar med ett `modelId` värde i bröd texten. Den här strängen är ID: t för den nyligen skapade modellen (tom). `accessToken`Krävs för API: et för att kopiera data till den här resursen och `expirationDateTimeTicks` värdet är förfallo datum för token. Spara alla tre värdena på en säker plats.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Starta kopierings åtgärd

Följande HTTP-begäran startar kopierings åtgärden på käll resursen. Du måste ange slut punkten och nyckeln för käll resursen som rubriker. Observera att URL: en för begäran innehåller modell-ID: t för den käll modell som du vill kopiera.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Bröd texten i begäran måste ha följande format. Du måste ange resurs-ID och region namn för mål resursen. Du hittar resurs-ID på fliken **Egenskaper** för resursen i Azure Portal och du kan hitta regions namnet på fliken **nycklar och slut punkt** . Du behöver också modell-ID, åtkomsttoken och förfallo värde som du kopierade från föregående steg.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> Kopierings-API: t transparent stöder funktionen [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) . Detta kräver ingen särskild behandling, men Observera att om du kopierar mellan en okrypterad resurs till en krypterad resurs måste du ta med rubriken för begäran `x-ms-forms-copy-degrade: true` . Om den här rubriken inte ingår, Miss kopie ras kopierings åtgärden och returnerar en `DataProtectionTransformServiceError` .

Du får ett `202\Accepted` svar med ett Operation-Locations huvud. Det här värdet är den URL som du använder för att följa förloppet för åtgärden. Kopiera den till en tillfällig plats för nästa steg.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Vanliga fel

|Fel|Lösning|
|:--|:--|
| 400/Felaktig begäran med `"code:" "1002"` | Indikerar ett verifierings fel eller en felaktig kopierings förfrågan. Vanliga problem är: a) ogiltig eller ändrad `copyAuthorization` nytto Last. b) utgånget värde för `expirationDateTimeTicks` token ( `copyAuhtorization` nytto lasten är giltigt i 24 timmar). c) ogiltigt eller stöds inte `targetResourceRegion` . d) ogiltig eller felaktig `targetResourceId` sträng.
|

## <a name="track-copy-progress"></a>Spåra kopierings förlopp

Spåra förloppet genom att skicka frågor till resultat-API: t för **Kopiera modell** mot käll resurs slut punkten.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Ditt svar kan variera beroende på åtgärdens status. Leta efter `"status"` fältet i JSON-texten. Om du automatiserar det här API-anropet i ett skript rekommenderar vi att du frågar åtgärden en gång i sekunden.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Vanliga fel

|Fel|Lösning|
|:--|:--|
|"fel": [{"Code": "AuthorizationError",<br>"meddelande": "auktoriseringsfel på grund av <br>auktorisations anspråk saknas eller är ogiltiga. "}]   | Inträffar när `copyAuthorization` nytto lasten eller innehållet ändras från vad som returnerades av `copyAuthorization` API: et. Se till att nytto lasten är samma exakta innehåll som returnerades från det tidigare `copyAuthorization` anropet.|
|"fel": [{"Code": "AuthorizationError",<br>"meddelande": "Det gick inte att hämta auktorisering <br>metadatatjänst. Om problemet kvarstår använder du en annan <br>mål modell att kopiera till. "}] | Anger att `copyAuthorization` nytto lasten återanvänds med en Copy-begäran. En Copy-begäran som lyckas kommer inte att tillåta ytterligare förfrågningar som använder samma `copyAuthorization` nytto Last. Om du höjer ett separat fel (som de som anges nedan) och sedan försöker kopiera igen med samma nytto nytto Last, utlöses det här felet. Lösningen är att generera en ny `copyAuthorization` nytto last och sedan utfärda kopian igen.|
|"fel": [{"Code": "DataProtectionTransformServiceError",<br>"meddelande": begäran om data överföring är inte tillåten <br>När den nedgraderas till ett mindre säkert data skydds schema. Läs dokumentationen eller kontakta tjänst administratören <br>för mer information. "}]    | Inträffar när du kopierar mellan en `AEK` aktive rad resurs till en resurs som inte `AEK` är aktive rad. För att tillåta kopiering av krypterad modell till målet som okrypterad ange `x-ms-forms-copy-degrade: true` rubrik med Copy-begäran.|
|"fel": [{"Code": "ResourceResolverError",<br>"meddelande": "Det gick inte att hämta information för kognitiv resurs med ID"... ". Se till att resursen är giltig och finns i det angivna området ' westus2 '.. "}] | Anger att den Azure-resurs som anges av `targetResourceId` inte är en giltig kognitiv resurs eller inte finns. Verifiera och utfärda kopierings förfrågan igen för att lösa problemet.|


### <a name="optional-track-the-target-model-id"></a>Valfritt Spåra mål modellens ID 

Du kan också använda API: t för att **Hämta anpassad modell** för att spåra status för åtgärden genom att fråga mål modellen. Anropa detta API med mål modell-ID: t som du kopierade i det första steget.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

I svars texten visas information om modellen. Kontrol lera `"status"` status för modellen i fältet.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>exempel kod för sväng

Följande kodfragment använder sig av sväng för att göra API-anropen beskrivs i stegen ovan. Du måste fortfarande fylla i modell-ID och prenumerations information som är specifika för dina egna resurser.

### <a name="generate-copy-authorization-request"></a>Skapa begäran om Copy-auktorisering

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Starta kopierings åtgärd

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Spåra kopierings förlopp

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du använder kopierings-API: t för att säkerhetskopiera dina anpassade modeller till en sekundär formulär igenkännings resurs. Nu ska du utforska API-referenserna för att se vad mer du kan göra med formulär igenkänning.
* [REST API referens dokumentation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
