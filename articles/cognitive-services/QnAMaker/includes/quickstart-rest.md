---
title: 'Snabb start: Använd spiral & REST för att hantera kunskaps bas – QnA Maker'
description: 'Den här snabb starten visar hur du skapar, publicerar och frågar din kunskaps bas med hjälp av REST-API: er.'
ms.date: 1/22/2021
ms.topic: quickstart
ms.openlocfilehash: d4c832c4a96ee1419b9e2f0accc1b86e9394ee02
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701304"
---
[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

* Den aktuella versionen av [sväng](https://curl.haxx.se/). Flera kommando rads växlar används i snabb starterna, som anges i [dokumentationen om vändning](https://curl.haxx.se/docs/manpage.html).
* Du måste ha en [QNA Maker-resurs](../How-To/set-up-qnamaker-service-azure.md)för att kunna använda nyckeln och resurs namnet. Du har angett resurs **namnet** när en resurs skapas och sedan skapades nyckeln åt dig. Resurs namnet används som under domän för din slut punkt. Om du vill hämta din nyckel och resurs namn väljer du **snabb start** för resursen i Azure Portal. Resurs namnet är den första under domänen i slut punkts-URL: en:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> I följande BASH-exempel används `\` linje fortsättnings symbolen. Använd det här alternativet om du använder en annan rad fortsättnings bokstav i en konsol eller Terminal.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

* Den aktuella versionen av [sväng](https://curl.haxx.se/). Flera kommando rads växlar används i snabb starterna, som anges i [dokumentationen om vändning](https://curl.haxx.se/docs/manpage.html).
* Du måste ha en [QNA Maker-resurs](../How-To/set-up-qnamaker-service-azure.md)för att kunna använda nyckeln och resurs namnet. Du har angett resurs **namnet** när en resurs skapas och sedan skapades nyckeln åt dig. Resurs namnet används som under domän för din slut punkt. Om du vill hämta din nyckel och resurs namn väljer du **snabb start** för resursen i Azure Portal. Resurs namnet är den första under domänen i slut punkts-URL: en:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> I följande BASH-exempel används `\` linje fortsättnings symbolen. Använd det här alternativet om du använder en annan rad fortsättnings bokstav i en konsol eller Terminal.

---

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Om du vill skapa en kunskaps bas med REST-API: er och svänger måste du ha följande information:

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|JSON som beskriver kunskaps basen|`-d` EntryPointName|[Exempel](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) på JSON|
|Storlek på JSON i byte|`-h` parameter för `Content-Size` rubrik||

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt egna resurs namn, resurs nyckel och JSON-värden och storleken på JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Ett spiral svar från QnA Maker innehåller `operationId` , vilket krävs för att [Hämta status för åtgärden](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)


Om du vill skapa en kunskaps bas med REST-API: er och svänger måste du ha följande information:

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|JSON som beskriver kunskaps basen|`-d` EntryPointName|[Exempel](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) på JSON|
|Storlek på JSON i byte|`-h` parameter för `Content-Size` rubrik||

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt egna resurs namn, resurs nyckel och JSON-värden och storleken på JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

Ett spiral svar från QnA Maker innehåller `operationId` , vilket krävs för att [Hämta status för åtgärden](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Hämta status för åtgärd

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

När du skapar en kunskaps bas, eftersom åtgärden är asynkron, innehåller svaret information för att fastställa statusen.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|Åtgärds-ID|URL-väg|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och åtgärds-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Det typografiska svaret innehåller status. Om åtgärds statusen har slutförts `resourceLocation` innehåller kunskaps bas-ID: t.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)


När du skapar en kunskaps bas, eftersom åtgärden är asynkron, innehåller svaret information för att fastställa statusen.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|Åtgärds-ID|URL-väg|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och åtgärds-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Det typografiska svaret innehåller status. Om åtgärds statusen har slutförts `resourceLocation` innehåller kunskaps bas-ID: t.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

---

## <a name="publish-knowledge-base"></a>Publicera kunskapsbas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Innan du frågar i kunskaps basen måste du:
* Publicera kunskapsbas
* Hämta slut punkts nyckeln för körning

Den här uppgiften publicerar kunskaps basen. Hämtning av körnings slut punkts nyckeln är en [separat uppgift](#get-published-knowledge-bases-runtime-endpoint-key).

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Svars statusen är 204 utan resultat. Använd `-v` kommando rads parametern för att Visa utförliga utdata för kommandot vändning. Detta kommer att innehålla HTTP-statusen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Innan du frågar i kunskaps basen måste du:
* Publicera kunskapsbas
* Hämta slut punkts nyckeln för körning

Den här uppgiften publicerar kunskaps basen. Hämtning av körnings slut punkts nyckeln är en [separat uppgift](#get-published-knowledge-bases-runtime-endpoint-key).

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Svars statusen är 204 utan resultat. Använd `-v` kommando rads parametern för att Visa utförliga utdata för kommandot vändning. Detta kommer att innehålla HTTP-statusen.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Hämta slut punkts nyckel för publicerad Knowledge Base-körning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Innan du frågar i kunskaps basen måste du:
* Publicera kunskapsbas
* Hämta slut punkts nyckeln för körning

Den här aktiviteten hämtar runtime-slutpunktens slut punkts nyckel. Publicering av kunskaps basen är en [separat uppgift](#publish-knowledge-base).

Runtime-slutpunkts nyckeln är samma nyckel för alla kunskaps banker som använder QnA Maker-resursen.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


I svarsmeddelandet ingår körnings slut punkts nycklar. Använd bara en av nycklarna när du frågar om du vill få ett svar från kunskaps basen.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Innan du frågar i kunskaps basen måste du:
* Publicera kunskapsbas
* Hämta slut punkts nyckeln för körning

Den här aktiviteten hämtar runtime-slutpunktens slut punkts nyckel. Publicering av kunskaps basen är en [separat uppgift](#publish-knowledge-base).

Runtime-slutpunkts nyckeln är samma nyckel för alla kunskaps banker som använder QnA Maker-resursen.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


I svarsmeddelandet ingår körnings slut punkts nycklar. Använd bara en av nycklarna när du frågar om du vill få ett svar från kunskaps basen.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>Fråga efter svar från publicerad kunskaps bas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Att få ett svar från kunskapen görs från en annan körning än att hantera kunskaps basen. Eftersom det är en separat körnings miljö måste du autentisera med en körnings nyckel.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker körnings nyckel|`-h` parameter för `Authorization` rubrik|Nyckeln är en del av en sträng som innehåller ordet `Endpointkey ` . Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON som beskriver fråga|`-d` EntryPointName|[Begär ande text parametrar](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) och [exempel](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) på JSON|
|Storlek på JSON i byte|`-h` parameter för `Content-Size` rubrik||

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Ett lyckat svar innehåller det främsta svaret tillsammans med annan information som ett klient program, till exempel en chatt-robot, måste visa ett svar för användaren.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Att få ett svar från kunskapen görs från en annan körning än att hantera kunskaps basen. Eftersom det är en separat körnings miljö måste du autentisera med en körnings nyckel.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON som beskriver fråga|`-d` EntryPointName|[Begär ande text parametrar](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) och [exempel](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) på JSON|
|Storlek på JSON i byte|`-h` parameter för `Content-Size` rubrik||

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Ett lyckat svar innehåller det främsta svaret tillsammans med annan information som ett klient program, till exempel en chatt-robot, måste visa ett svar för användaren.


---

## <a name="delete-knowledge-base"></a>Ta bort kunskaps bas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

När du är färdig med kunskaps basen tar du bort den.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Svars statusen är 204 utan resultat. Använd `-v` kommando rads parametern för att Visa utförliga utdata för kommandot vändning. Detta kommer att innehålla HTTP-statusen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

När du är färdig med kunskaps basen tar du bort den.

|Information|konfiguration av sväng|Syfte|
|--|--|--|
|QnA Maker resurs namn|URL|används för att skapa URL|
|QnA Maker resurs nyckel|`-h` parameter för `Ocp-Apim-Subscription-Key` rubrik|Autentisera till QnA Maker-tjänst|
|Kunskaps bas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot vänd körs från ett BASH-gränssnitt. Redigera det här kommandot med ditt eget resurs namn, resurs nyckel och kunskaps bas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Svars statusen är 204 utan resultat. Använd `-v` kommando rads parametern för att Visa utförliga utdata för kommandot vändning. Detta kommer att innehålla HTTP-statusen.

---

## <a name="additional-resources"></a>Ytterligare resurser

* [Redigering](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) Referens dokumentation
* [Körning](/rest/api/cognitiveservices/qnamaker4.0/runtime) Referens dokumentation
* [Exempel på BASH-skript med hjälp av sväng](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)