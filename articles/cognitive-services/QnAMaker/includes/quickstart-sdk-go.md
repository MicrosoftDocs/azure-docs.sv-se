---
title: 'Snabb start: QnA Maker klient bibliotek för go'
description: Den här snabb starten visar hur du kommer igång med QnA Maker klient biblioteket för go.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 39df309a4c4c9c1db181a6081afe00498d449957
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731025"
---
Använd QnA Maker klient bibliotek för att gå till:

* Skapa en kunskaps databas
* Uppdatera en kunskaps databas
* Publicera en kunskaps databas
* Hämta slut punkts nyckel för förutsägelse körning
* Vänta på aktivitet som körs
* Hämta en kunskaps databas
* Få svar
* Ta bort kunskaps bas

[Referens (redigering)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker)  |  [Referens (Runtime)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime)  |  [Biblioteks käll kod (redigering)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker)  |  [Biblioteks käll kod (Runtime)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Kör](https://golang.org/)
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och slut punkt. När den har distribuerats väljer **du gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-go-application"></a>Skapa ett nytt Go-program

Skapa en ny fil med namnet `kb_sample.go` och importera följande bibliotek.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

Skapa variabler för resursens Azure-slutpunkt och nyckel.

> [!IMPORTANT]
> Gå till Azure Portal och hitta nyckeln och slut punkten för den QnA Maker resurs som du skapade i kraven. De kommer att finnas på resursens **nyckel-och slut punkts** sida under **resurs hantering**.
> Du behöver hela nyckeln för att skapa din kunskaps databas. Du behöver bara resurs namnet från slut punkten. Formatet är `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`.
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel ger [Azure Key Vault](../../../key-vault/general/overview.md) säker nyckel lagring.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>Objekt modeller

QnA Maker använder två olika objekt modeller:
* **[QnAMakerClient](#qnamakerclient-object-model)** är objektet för att skapa, hantera, publicera och ladda ned kunskaps listan.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** är objektet för att fråga kunskaps basen med GenerateAnswer-API: et och skicka nya föreslagna frågor med hjälp av träna API (som en del av den [aktiva inlärningen](../how-to/use-active-learning.md)).

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objektmodellen

Använd [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient) för att skapa, uppdatera, publicera, ladda ned och ta bort din kunskaps bas. De här åtgärderna beskrivs i följande avsnitt.

Använd [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient) för att kontrol lera statusen för långvariga åtgärder som att skapa och uppdatera kunskaps baser.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objektmodellen

När du har publicerat din kunskaps bas använder du [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) för att fråga din kunskaps bas. Den här åtgärden beskrivs i [fråga kunskaps basen](#query-the-knowledge-base).

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO) -objektet från tre källor:

* För **redaktionellt innehåll** använder du [QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO) -objektet.
    * Om du vill använda metadata och Uppföljnings anvisningar använder du redigerings kontexten eftersom dessa data läggs till på den enskilda QnA-ihopparningen.
* För **filer** använder du [FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO) -objektet. FileDTO innehåller fil namnet och den offentliga URL: en för att komma åt filen.
* För **URL: er** använder du en lista med strängar som representerar offentliga tillgängliga URL: er.

Anropa metoden [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create) och skicka sedan `operationId` egenskapen för den returnerade åtgärden till [getDetails](#get-status-of-an-operation) -metoden för att söka efter status.

Den sista raden i följande kod returnerar kunskaps bas-ID: t.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att anropa [Update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update) och skicka i kunskaps bas-ID: t och ett [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO) -objekt. Objektet i sin tur kan innehålla:
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [uppdatera](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

Skicka `operationId` egenskapen för den returnerade åtgärden till [getDetails](#get-status-of-an-operation) -metoden för att söka efter status.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>Hämta en kunskaps bas

Använd [nedladdnings](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download) metoden för att ladda ned databasen som en lista över [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO). Detta motsvarar _inte_ QNA Maker portalens export från **inställnings** sidan eftersom resultatet av den här metoden inte är en TSV-fil.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen med [publicerings](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish) metoden. Detta tar den aktuella sparade och utbildade modellen som refereras till av kunskaps bas-ID: t och publicerar den på en slut punkt.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>Fråga kunskaps basen

När en kunskaps bas linje har publicerats behöver du körnings slut punkts nyckeln för att fråga efter kunskapen. Detta är inte samma som den prenumerations nyckel som används för att skapa redigerings klienten.

Skapa en [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient) och Använd [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys) -metoden för att hämta ett [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO) -objekt.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

Skapa en [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) och anropa [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer) -metoden för att fråga kunskaps basen.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

Detta är ett enkelt exempel på att fråga en kunskaps bas. Om du vill förstå avancerade fråge scenarier kan du läsa mer i [exempel på andra frågor](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskaps basen med metoden [Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete) med en parameter i kunskaps bas-ID: t.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt lång tid i stället för att vänta på att processen ska slutföras, returneras en [åtgärd](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation) . Använd åtgärds-ID: t från åtgärden att söka (med logiken för omprövning) för att fastställa statusen för den ursprungliga metoden.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>Hantera fel

Följande kod visar hur du hanterar fel som kan returneras av SDK: s funktioner.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>Kör programmet

Här är den huvudsakliga metoden för programmet.

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

Kör programmet med kommandot go i snabb starts filen.

```console
go run kb_sample.go
```

Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/QnAMaker/sdk/kb_sample.go)
