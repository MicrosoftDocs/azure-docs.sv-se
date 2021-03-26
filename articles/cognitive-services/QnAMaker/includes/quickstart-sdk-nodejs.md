---
title: 'Snabb start: QnA Maker klient bibliotek för Node.js'
description: Den här snabb starten visar hur du kommer igång med QnA Maker klient biblioteket för Node.js.
ms.topic: quickstart
ms.date: 06/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0d0522be53ec9b1008f18725308c91a19ee24156
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609509"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Använd QnA Maker klient biblioteket för Node.js för att:

* Skapa en kunskaps databas
* Uppdatera en kunskaps databas
* Publicera en kunskaps databas
* Hämta slut punkts nyckel för förutsägelse körning
* Vänta på aktivitet som körs
* Hämta en kunskaps databas
* Få ett svar från en kunskaps bank
* Ta bort kunskaps bas

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Använd QnA Maker klient biblioteket för Node.js för att:

* Skapa en kunskaps databas
* Uppdatera en kunskaps databas
* Publicera en kunskaps databas
* Vänta på aktivitet som körs
* Hämta en kunskaps databas
* Få ett svar från en kunskaps bank
* Ta bort kunskaps bas

[Referens dokumentation](/javascript/api/@azure/cognitiveservices-qnamaker/)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker)  |  [Node.js exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org).
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och resurs. När den har distribuerats väljer **du gå till resurs**.
    * Du behöver nyckel-och resurs namnet från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du kommer att klistra in nyckeln och resurs namnet i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org).
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och slut punkt.
    * Obs: se till att markera kryss rutan **hanterad** .
    * När du har distribuerat QnA Maker resurser väljer **du gå till resurs**. Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

---

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

Kör `npm init -y` kommandot för att skapa ett Node-program med en `package.json` fil.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Installera följande NPM-paket:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/cognitiveservices-qnamaker-runtime
npm install @azure/ms-rest-js
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Installera följande NPM-paket:

```console
npm install @azure/cognitiveservices-qnamaker
npm install @azure/ms-rest-js
```

---

Appens `package.json` fil uppdateras med beroenden.

Skapa en fil med namnet index.js och importera följande bibliotek:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Dependencies](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Dependencies)]

---

Skapa en variabel för resursens Azure-nyckel och resurs namn.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

- Vi använder prenumerations nyckeln och redigerings nyckeln interchangably. Om du vill ha mer information om redigerings nyckeln följer du [nycklar i QNA Maker](../concepts/azure-resources.md?tabs=v1#keys-in-qna-maker).

- Värdet för QNA_MAKER_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Gå till Azure Portal och hitta den QnA Maker resurs som du skapade i kraven. Klicka på sidan **nycklar och slut punkt** , under **resurs hantering** för att hitta redigerings nyckeln (prenumeration) och QNA Maker slut punkten.

 ![QnA Maker redigerings slut punkt](../media/keys-endpoint.png)

- Värdet för QNA_MAKER_RUNTIME_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.azurewebsites.net` . Gå till Azure Portal och hitta den QnA Maker resurs som du skapade i kraven. Klicka på sidan **Exportera mall** under **Automation** för att hitta runtime-slutpunkten.

 ![QnA Maker runtime-slutpunkt](../media/runtime-endpoint.png)
   
- För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel ger [Azure Key Vault](../../../key-vault/general/overview.md) säker nyckel lagring.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

- Vi använder prenumerations nyckeln och redigerings nyckeln interchangably. Om du vill ha mer information om redigerings nyckeln följer du [nycklar i QNA Maker](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).

- Värdet för QNA_MAKER_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . Gå till Azure Portal och hitta den QnA Maker resurs som du skapade i kraven. Klicka på sidan **nycklar och slut punkt** , under **resurs hantering** för att hitta redigerings nyckeln (prenumeration) och QNA Maker slut punkten.

 ![QnA Maker redigerings slut punkt](../media/keys-endpoint.png)
 
- För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel ger [Azure Key Vault](../../../key-vault/general/overview.md) säker nyckel lagring.

[!code-javascript[Set the resource key and resource name](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=Resourcevariables)]

---

## <a name="object-models"></a>Objekt modeller

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[QNA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) använder två olika objekt modeller:
* **[QnAMakerClient](#qnamakerclient-object-model)** är objektet för att skapa, hantera, publicera och ladda ned kunskaps listan.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** är objektet för att fråga kunskaps basen med GenerateAnswer-API: et och skicka nya föreslagna frågor med hjälp av träna API (som en del av den [aktiva inlärningen](../how-to/use-active-learning.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[QNA Maker](/javascript/api/@azure/cognitiveservices-qnamaker/) använder följande objekt modell:
* **[QnAMakerClient](#qnamakerclient-object-model)** är objektet för att skapa, hantera, publicera, ladda ned och fråga i kunskaps frågan.

---

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objektmodellen

Redigerings QnA Makers klienten är ett [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) -objekt som autentiserar till Azure med hjälp av dina autentiseringsuppgifter, som innehåller din nyckel.

När klienten har skapats använder du [kunskaps](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient#knowledgebase) basen för att skapa, hantera och publicera din kunskaps bas.

Hantera din kunskaps bas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligt vis ett JSON-objekt som indikerar status. För långvariga åtgärder är svaret åtgärds-ID. Anropa metoden [client. Operations. getDetails](/javascript/api/@azure/cognitiveservices-qnamaker/operations#getdetails-string--msrest-requestoptionsbase-) med ÅTGÄRDS-ID för att fastställa [status för begäran](/javascript/api/@azure/cognitiveservices-qnamaker/operation).

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objektmodellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Förutsägelse QnA Maker-klienten är ett QnAMakerRuntimeClient-objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din förutsägelse körnings nyckel som returneras från klient anropet för [redigering. EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys#getkeys-msrest-requestoptionsbase-) efter att kunskaps listan har publicerats.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

En QnA Maker hanterad resurs kräver inte att QnAMakerRuntimeClient-objektet används. I stället anropar du [generateAnswer](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#generateAnswer_string__QueryDTO__msRest_RequestOptionsBase_) direkt i [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) -objektet.

---

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med QnA Maker klient biblioteket för .NET:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

* [Autentisera redigerings klienten](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Skapa en kunskaps bas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Hämta en kunskaps bas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta körnings nyckel för fråga](#get-query-runtime-key)
* [Hämta status för en åtgärd](#get-status-of-an-operation)
* [Autentisera klienten för frågekörningen-körning](#authenticate-the-runtime-for-generating-an-answer)
* [Generera ett svar från kunskaps basen](#generate-an-answer-from-the-knowledge-base)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

* [Autentisera redigerings klienten](#authenticate-the-client-for-authoring-the-knowledge-base)
* [Skapa en kunskaps bas](#create-a-knowledge-base)
* [Uppdatera en kunskapsbas](#update-a-knowledge-base)
* [Hämta en kunskaps bas](#download-a-knowledge-base)
* [Publicera en kunskapsbas](#publish-a-knowledge-base)
* [Ta bort en kunskapsbas](#delete-a-knowledge-base)
* [Hämta status för en åtgärd](#get-status-of-an-operation)
* [Generera ett svar från kunskaps basen](#generate-an-answer-from-the-knowledge-base)

---

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>Autentisera klienten för att redigera kunskaps basen

Instansiera en klient med din slut punkt och nyckel. Skapa ett ServiceClientCredentials-objekt med din nyckel och Använd den med slut punkten för att skapa ett [QnAMakerClient](/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient) -objekt.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto) -objektet från tre källor:

* För **redaktionellt innehåll** använder du [QnADTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadto) -objektet.
    * Om du vill använda metadata och Uppföljnings anvisningar använder du redigerings kontexten eftersom dessa data läggs till på den enskilda QnA-ihopparningen.
* För **filer** använder du [FileDTO](/javascript/api/@azure/cognitiveservices-qnamaker/filedto) -objektet. FileDTO innehåller fil namnet och den offentliga URL: en för att komma åt filen.
* För **URL: er** använder du en lista med strängar som representerar offentliga tillgängliga URL: er.

Steget Skapa innehåller också egenskaper för kunskaps listan:
* `defaultAnswerUsedForExtraction` – vad som returneras när inget svar hittas
* `enableHierarchicalExtraction` – Skapa automatiskt prompt-relationer mellan extraherade QnA-par
* `language` – När du skapar den första kunskaps listan för en resurs ställer du in språket som ska användas i Azure Search indexet.

Anropa [create](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#create-createkbdto--servicecallback-operation--) -metoden med kunskaps bas informationen. Kunskaps bas informationen är i princip ett JSON-objekt.

När metoden Create returnerar skickar du det returnerade åtgärds-ID: t till [wait_for_operation](#get-status-of-an-operation) -metoden för att avsöka efter status. Metoden wait_for_operation returnerar när åtgärden har slutförts. Parsa `resourceLocation` huvud värde för den returnerade åtgärden för att hämta det nya kunskaps bas-ID: t.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Create knowledgebase](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=CreateKBMethod)]

---

Se till att inkludera [`wait_for_operation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna skapa en kunskaps bas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att skicka i kunskaps bas-ID: t och en [UpdateKbOperationDTO](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto) som innehåller objekt för att [lägga till](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#add), [Uppdatera](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#update)och [ta bort](/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto#deleteproperty) DTO till [uppdaterings](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#update-string--updatekboperationdto--msrest-requestoptionsbase-) metoden. DTO är också i princip JSON-objekt. Använd metoden [wait_for_operation](#get-status-of-an-operation) för att avgöra om uppdateringen har slutförts.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=UpdateKBMethod)]

---

Se till att inkludera [`wait_for_operation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna uppdatera en kunskaps bas.

## <a name="download-a-knowledge-base"></a>Hämta en kunskaps bas

Använd [nedladdnings](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#download-string--models-environmenttype--msrest-requestoptionsbase-) metoden för att ladda ned databasen som en lista över [QnADocumentsDTO](/javascript/api/@azure/cognitiveservices-qnamaker/qnadocumentsdto). Detta motsvarar _inte_ QNA Maker portalens export från **inställnings** sidan eftersom resultatet av den här metoden inte är en TSV-fil.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Download a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen med [publicerings](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#publish-string--msrest-requestoptionsbase-) metoden. Detta tar den aktuella sparade och utbildade modellen som refereras till av kunskaps bas-ID: t och publicerar den på en slut punkt. Kontrol lera HTTP-svarskod för att verifiera att publiceringen lyckades.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=PublishKB)]

---

## <a name="query-a-knowledge-base"></a>Köra frågor mot en kunskapsbas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

### <a name="get-query-runtime-key"></a>Hämta körnings nyckel för fråga

När en kunskaps databas har publicerats behöver du frågans körnings nyckel för att fråga körnings miljön. Detta är inte samma nyckel som används för att skapa det ursprungliga klient objektet.

Använd metoden [EndpointKeys. getKeys](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeys) för att hämta klassen [EndpointKeysDTO](/javascript/api/@azure/cognitiveservices-qnamaker/endpointkeysdto) .

Använd någon av de nyckel egenskaper som returnerades i objektet för att fråga kunskaps listan.

[!code-javascript[Get query runtime key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GetQueryEndpointKey)]

### <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autentisera körningen för att skapa ett svar

Skapa en QnAMakerRuntimeClient för att söka i kunskaps basen för att skapa ett svar eller träna från aktiv inlärning.

[!code-javascript[Authenticate the runtime](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=AuthorizationQuery)]

Använd QnAMakerRuntimeClient för att få svar från kunskapen eller för att skicka nya föreslagna frågor till kunskaps basen för [aktiv inlärning](../concepts/active-learning-suggestions.md).

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskaps basen

Generera ett svar från en publicerad kunskaps bas med hjälp av metoden RuntimeClient. Runtime. generateAnswer. Den här metoden godkänner kunskaps bas-ID: t och [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Få åtkomst till ytterligare egenskaper för QueryDTO, till exempel Top och context, som ska användas i din Chat-robot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

### <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskaps basen

Generera ett svar från en publicerad kunskaps bas med hjälp av metoden QnAMakerClient. kunskaps. generateAnswer. Den här metoden godkänner kunskaps bas-ID: t och [QueryDTO](/javascript/api/@azure/cognitiveservices-qnamaker/querydto). Få åtkomst till ytterligare egenskaper för QueryDTO, till exempel Top och context, som ska användas i din Chat-robot.

[!code-javascript[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=GenerateAnswer)]

---

Detta är ett enkelt exempel som frågar kunskaps basen. Om du vill förstå avancerade fråge scenarier kan du läsa mer i [exempel på andra frågor](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskaps basen med metoden [Delete](/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase#deletemethod-string--msrest-requestoptionsbase-) med en parameter i kunskaps bas-ID: t.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt lång tid i stället för att vänta på att processen ska slutföras, returneras en [åtgärd](/javascript/api/@azure/cognitiveservices-qnamaker/operations) . Använd [Åtgärds-ID: t](/javascript/api/@azure/cognitiveservices-qnamaker/operation#operationid) från åtgärden att söka (med logiken för omprövning) för att fastställa statusen för den ursprungliga metoden.

_DelayTimer_ -anropet i följande kodblock används för att simulera omprövnings logiken. Ersätt detta med din egen omprövnings logik.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/preview-sdk/quickstart.js?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node index.js` kommandot från program katalogen.

```console
node index.js
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/preview-sdk/quickstart.js).

---
