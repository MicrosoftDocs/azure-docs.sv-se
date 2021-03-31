---
title: v1 till v2 API-migrering
titleSuffix: Azure Cognitive Services
description: 'Slut punkten för version 1 och redigering Language Understanding-API: er är inaktuella. Använd den här guiden för att lära dig hur du migrerar till version 2 Endpoint och redigerar API: er.'
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 867ae2cc7567077786bb0840cd11c47b786be423
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018760"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 till v2 migration guide för LUIS-appar
[Slut punkten](https://aka.ms/v1-endpoint-api-docs) för version 1 och [redigerings](https://aka.ms/v1-authoring-api-docs) -API: er är inaktuella. Använd den här guiden för att lära dig hur du migrerar till version 2 [Endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) och [redigerar](https://go.microsoft.com/fwlink/?linkid=2092087) API: er.

## <a name="new-azure-regions"></a>Nya Azure-regioner
LUIS har nya [regioner](./luis-reference-regions.md) som finns för Luis-API: er. LUIS tillhandahåller en annan portal för region grupper. Programmet måste ha skapats i samma region som du förväntar dig att fråga. Program migrerar inte automatiskt regioner. Du exporterar appen från en region och importerar sedan till en annan för att den ska vara tillgänglig i en ny region.

## <a name="authoring-route-changes"></a>Redigera väg ändringar
Redigerings-API-vägen har ändrats från att använda **PROG** -vägen för att använda **API** -vägen.


| version | route |
|--|--|
|1|/Luis/v1.0/**PROG**/Apps|
|2|/Luis/**API**-/v2.0/Apps|


## <a name="endpoint-route-changes"></a>Ändringar i slut punkts flödet
Slut punkts-API: et har nya parametrar för frågesträng och ett annat svar. Om verbose-flaggan är sann returneras alla avsikter, oavsett poäng, i en matris med namnet names, förutom topScoringIntent.

| version | Hämta väg |
|--|--|
|1|/Luis/v1/Application? ID = {appId} &q = {q}|
|2|/luis/v2.0/apps/{appId}? q = {q} [&timezoneOffset] [&verbose] [&stavnings kontroll] [&mellanlagring] [&Bing-stavning-check-prenumeration-nyckel] [&log]|


framgångs svar för v1-slutpunkt:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 slut punkt slutfört svar:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Nyckel hantering inte längre i API
API: erna för prenumerationens slut punkts nyckel är inaktuella och returnerar 410 borta.

| version | route |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azures [slut punkts nycklar](luis-how-to-azure-subscription.md) genereras i Azure Portal. Du tilldelar nyckeln till en LUIS-app på **[publicerings](luis-how-to-azure-subscription.md)** sidan. Du behöver inte känna till det faktiska nyckelvärdet. LUIS använder prenumerations namnet för att utföra tilldelningen.

## <a name="new-versioning-route"></a>Ny versions väg
V2-modellen finns nu i en [version](luis-how-to-manage-versions.md). Ett versions namn är 10 tecken i vägen. Standard versionen är "0,1".

| version | route |
|--|--|
|1|/Luis/v1.0/**PROG**/Apps/{appId}/entities|
|2|/Luis/**API**/v2.0/Apps/{appId}/-**versioner**/{versionId}/entities|

## <a name="metadata-renamed"></a>Metadata har bytt namn
Flera API: er som returnerar LUIS metadata har nya namn.

| v1-flödets namn | v2-flödes namn |
|--|--|
|PersonalAssistantApps |assistenter|
|applicationcultures|kulturer|
|applicationdomains|domäner|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample" har bytt namn till "föreslå"
LUIS föreslår yttranden från befintliga [slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) som kan förbättra modellen. I den tidigare versionen hette det här **exemplet**. I den nya versionen ändras namnet från exempel till **föreslå**. Detta kallas för **[gransknings slut punkts yttranden](luis-how-to-review-endpoint-utterances.md)** på Luis-webbplatsen.

| version | route |
|--|--|
|1|**exempel** på/Luis/v1.0/**PROG**/Apps/{appId}/entities/{entityId}/|
|1|**exempel** på/Luis/v1.0/**PROG**/Apps/{appId}/Intents/{intentId}/|
|2|/Luis/**API**/v2.0/Apps/{appId}/-**versioner**/{versionId}/entities/{entityId}/**föreslå**|
|2|/Luis/**API**/v2.0/Apps/{appId}/-**versioner**/{versionId}/Intents/{intentId}/**föreslå**|


## <a name="create-app-from-prebuilt-domains"></a>Skapa en app från fördefinierade domäner
Fördefinierade [domäner](./howto-add-prebuilt-models.md) ger en fördefinierad domän modell. Med fördefinierade domäner kan du snabbt utveckla ditt LUIS-program för vanliga domäner. Med det här API: et kan du skapa en ny app baserat på en fördefinierad domän. Svaret är det nya appID.

|v2-väg|verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |Hämta, publicera|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>Importerar en app med 1. x till 2. x
Den exporterade 1. x-appens JSON har vissa områden som du måste ändra innan du importerar till [LUIS][LUIS] 2,0.

### <a name="prebuilt-entities"></a>Fördefinierade entiteter
De [förinställda entiteterna](./howto-add-prebuilt-models.md) har ändrats. Kontrol lera att du använder de v2 färdiga entiteterna. Detta inkluderar användning av [datetimeV2](luis-reference-prebuilt-datetimev2.md), i stället för DateTime.

### <a name="actions"></a>Åtgärder
Egenskapen åtgärder är inte längre giltig. Det ska vara tomt

### <a name="labeled-utterances"></a>Märkta yttranden
V1 tillåtna märkta yttranden för att inkludera blank steg i början eller slutet av ordet eller frasen. Blank stegen har tagits bort.

## <a name="common-reasons-for-http-response-status-codes"></a>Vanliga orsaker till status koder för HTTP-svar
Se [Luis API-svars koder](luis-reference-response-codes.md).

## <a name="next-steps"></a>Nästa steg

Använd API-dokumentationen v2 för att uppdatera befintliga REST-anrop till LUIS- [slutpunkt](https://go.microsoft.com/fwlink/?linkid=2092356) och [redigerings](https://go.microsoft.com/fwlink/?linkid=2092087) -API: er.

[LUIS]: ./luis-reference-regions.md