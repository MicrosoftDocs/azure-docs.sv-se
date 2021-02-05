---
title: 'Snabb start: QnA Maker klient bibliotek för .NET'
description: Den här snabb starten visar hur du kommer igång med QnA Maker-klient biblioteket för .NET. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.  Med QnA Maker kan du driva en tjänst för frågor och svar från halvstrukturerat innehåll som Vanliga frågor och svar-dokument, URL:er och produkthandböcker.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: d853f4729d126590df711554f60efe6d9f2194cc
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569598"
---
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Använd QnA Maker klient bibliotek för .NET för att:

 * Skapa en kunskaps databas
 * Uppdatera en kunskaps databas
 * Publicera en kunskaps databas
 * Hämta slut punkts nyckel för förutsägelse körning
 * Vänta på aktivitet som körs
 * Hämta en kunskaps databas
 * Få ett svar från en kunskaps bank
 * Ta bort en kunskaps databas

[Referens dokumentation](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/2.0.1)  |  [C#-exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Använd QnA Maker klient bibliotek för .NET för att:

 * Skapa en kunskaps databas
 * Uppdatera en kunskaps databas
 * Publicera en kunskaps databas
 * Vänta på aktivitet som körs
 * Hämta en kunskaps databas
 * Få ett svar från en kunskaps bank
 * Ta bort en kunskaps databas

[Referens dokumentation](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1)  |  [C#-exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

---

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Förutsättningar

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och resurs namn. När den har distribuerats väljer **du gå till resurs**.
    * Du behöver nyckel-och resurs namnet från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du kommer att klistra in nyckeln och resurs namnet i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* När du har en Azure-prenumeration kan du skapa en [QNA Maker resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) i Azure Portal för att hämta din redigerings nyckel och slut punkt.
    * Obs: se till att markera kryss rutan **hanterad** .
    * När du har distribuerat QnA Maker resurser väljer **du gå till resurs**. Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för QnA Maker. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

---

## <a name="setting-up"></a>Konfigurera

### <a name="visual-studio-ide"></a>Visual Studio IDE

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Skapa ett .NET Core-program i Visual Studio och installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra** och söker efter `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Välj version `2.0.1` och **Installera** sedan.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Skapa ett .NET Core-program i Visual Studio och installera klient biblioteket genom att högerklicka på lösningen i **Solution Explorer** och välja **Hantera NuGet-paket**. I paket hanteraren som öppnas väljer du **Bläddra**, markerar **ta med för hands version** och söker efter `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker` . Välj version `3.0.0-preview.1` och **Installera** sedan.

---

### <a name="cli"></a>CLI

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `qna-maker-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*.

```console
dotnet new console -n qna-maker-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

I program katalogen installerar du QnA Maker klient biblioteket för .NET med följande kommando:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.1
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs), som innehåller kod exemplen i den här snabb starten.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs), som innehåller kod exemplen i den här snabb starten.

---

### <a name="using-directives"></a>Använda direktiv

Från projekt katalogen öppnar du filen *program.cs* och lägger till följande `using` direktiv:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

---

### <a name="subscription-key-and-resource-endpoints"></a>Prenumerations nyckel och resurs slut punkter

I program `Main` metoden lägger du till variabler och kod, som visas i följande avsnitt, för att använda vanliga uppgifter i den här snabb starten.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

> [!IMPORTANT]
> Gå till Azure Portal och hitta nyckeln och slut punkten för den QnA Maker resurs som du skapade i kraven. De kommer att finnas på resursens **nyckel-och slut punkts** sida under **resurs hantering**. Vi använder prenumerations nyckeln och redigerings nyckeln interchangably. Om du vill ha mer information om redigerings nyckeln följer du [nycklar i QNA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v1#keys-in-qna-maker).

- Skapa miljövariabler med namnet QNA_MAKER_SUBSCRIPTION_KEY, QNA_MAKER_ENDPOINT och QNA_MAKER_RUNTIME_ENDPOINT för att lagra dessa värden.
- Värdet för QNA_MAKER_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- Värdet för QNA_MAKER_RUNTIME_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.azurewebsites.net` .
- För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel ger [Azure Key Vault](../../../key-vault/general/overview.md) säker nyckel lagring.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

> [!IMPORTANT]
> Gå till Azure Portal och hitta nyckeln och slut punkten för den QnA Maker resurs som du skapade i kraven. De kommer att finnas på resursens **nyckel-och slut punkts** sida under **resurs hantering**. Vi använder prenumerations nyckeln och redigerings nyckeln interchangably. Om du vill ha mer information om redigerings nyckeln följer du [nycklar i QNA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/azure-resources?tabs=v2#keys-in-qna-maker-managed-preview).

- Skapa miljövariabler med namnet QNA_MAKER_SUBSCRIPTION_KEY och QNA_MAKER_ENDPOINT för att lagra dessa värden.
- Värdet för QNA_MAKER_ENDPOINT har formatet `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` . 
- För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel ger [Azure Key Vault](../../../key-vault/general/overview.md) säker nyckel lagring.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

---


## <a name="object-models"></a>Objekt modeller

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[QNA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) använder två olika objekt modeller:
* **[QnAMakerClient](#qnamakerclient-object-model)** är objektet för att skapa, hantera, publicera och ladda ned kunskaps listan.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** är objektet för att fråga kunskaps basen med GenerateAnswer-API: et och skicka nya föreslagna frågor med hjälp av träna API (som en del av den [aktiva inlärningen](../concepts/active-learning-suggestions.md)).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[QNA Maker](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) använder följande objekt modell:
* **[QnAMakerClient](#qnamakerclient-object-model)** är objektet för att skapa, hantera, publicera, ladda ned och fråga i kunskaps frågan.

---

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient-objektmodellen

Redigerings QnA Makers klienten är ett [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

När klienten har skapats använder du [kunskaps bas](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) egenskapen för att skapa, hantera och publicera din kunskaps bas.

Hantera din kunskaps bas genom att skicka ett JSON-objekt. För omedelbara åtgärder returnerar en metod vanligt vis ett JSON-objekt som indikerar status. För långvariga åtgärder är svaret åtgärds-ID. Anropa [klienten. Operations. GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) -metoden med åtgärds-ID för att fastställa [status för begäran](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype).

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient-objektmodellen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Förutsägelse QnA Maker-klienten är ett [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din förutsägelse körnings nyckel, returnerad från klient anropet, `client.EndpointKeys.GetKeys` efter att kunskaps gruppen publicerats.

Använd [GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) -metoden för att få ett svar från frågans Runtime.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

En QnA Maker hanterad resurs kräver inte att **QnAMakerRuntimeClient** -objektet används. I stället anropar du [QnAMakerClient. Knowledge Base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase). [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) -metod.

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

Instansiera ett klient objekt med din nyckel och Använd den med din resurs för att skapa en slut punkt för att skapa en [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) med din slut punkt och nyckel. Skapa ett [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) -objekt.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

---

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

En kunskaps bas lagrar fråge-och svars par för [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) -objektet från tre källor:

* För **redaktionellt innehåll** använder du [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) -objektet.
    * Om du vill använda metadata och Uppföljnings anvisningar använder du redigerings kontexten eftersom dessa data läggs till på den enskilda QnA-ihopparningen.
* För **filer** använder du [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) -objektet. FileDTO innehåller fil namnet och den offentliga URL: en för att komma åt filen.
* För **URL: er** använder du en lista med strängar som representerar offentliga tillgängliga URL: er.

Steget Skapa innehåller också egenskaper för kunskaps listan:
* `defaultAnswerUsedForExtraction` – vad som returneras när inget svar hittas
* `enableHierarchicalExtraction` – Skapa automatiskt prompt-relationer mellan extraherade QnA-par
* `language` – När du skapar den första kunskaps listan för en resurs ställer du in språket som ska användas i Azure Search indexet.

Anropa metoden [CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) och skicka sedan det returnerade åtgärds-ID: t till [MonitorOperation](#get-status-of-an-operation) -metoden för att avsöka efter status.

Den sista raden i följande kod returnerar kunskaps bas-ID: t från svaret från MonitorOperation.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

---

Se till att inkludera [`MonitorOperation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna skapa en kunskaps bas.

## <a name="update-a-knowledge-base"></a>Uppdatera en kunskapsbas

Du kan uppdatera en kunskaps bas genom att skicka i kunskaps bas-ID: t och en [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto) som innehåller objekt för att [lägga till](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [Uppdatera](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate)och [ta bort](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO till [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) -metoden. Använd [MonitorOperation](#get-status-of-an-operation) -metoden för att avgöra om uppdateringen har slutförts.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

---

Se till att inkludera [`MonitorOperation`](#get-status-of-an-operation) funktionen, som refereras till i ovanstående kod, för att kunna uppdatera en kunskaps bas.

## <a name="download-a-knowledge-base"></a>Hämta en kunskaps bas

Använd metoden [DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) för att ladda ned databasen som en lista över [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto). Detta motsvarar _inte_ QNA Maker portalens export från **inställnings** sidan eftersom resultatet av den här metoden inte är en fil.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

---

## <a name="publish-a-knowledge-base"></a>Publicera en kunskapsbas

Publicera kunskaps basen med [PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) -metoden. Detta tar den aktuella sparade och utbildade modellen som refereras till av kunskaps bas-ID: t och publicerar den på slut punkten. Det här är ett nödvändigt steg för att skicka frågor till din kunskaps bank.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

---

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

## <a name="get-query-runtime-key"></a>Hämta körnings nyckel för fråga

När en kunskaps databas har publicerats behöver du frågans körnings nyckel för att fråga körnings miljön. Detta är inte samma nyckel som används för att skapa det ursprungliga klient objektet.

Använd [EndpointKeys](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) -metoden för att hämta [EndpointKeysDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto) -klassen.

Använd någon av de nyckel egenskaper som returnerades i objektet för att fråga kunskaps listan.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey)]

En körnings nyckel krävs för att skicka frågor till din kunskaps bank.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>Autentisera körningen för att skapa ett svar

Skapa en [QnAMakerRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient) för att söka i kunskaps basen för att skapa ett svar eller träna från aktiv inlärning.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

Använd QnAMakerRuntimeClient för att:
* få ett svar från kunskaps gruppen
* för att skicka nya föreslagna frågor till kunskaps basen för [aktiv inlärning](../concepts/active-learning-suggestions.md).

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskaps basen

Generera ett svar från en publicerad kunskaps databas med hjälp av [RuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase). [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync) -metod. Den här metoden godkänner kunskaps bas-ID: t och [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Få åtkomst till ytterligare egenskaper för QueryDTO, till exempel [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) och [context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context) , som ska användas i din Chat-robot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

## <a name="generate-an-answer-from-the-knowledge-base"></a>Generera ett svar från kunskaps basen

Generera ett svar från en publicerad kunskaps databas med hjälp av [QnAMakerClient. Knowledge Base](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase). [GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) -metod. Den här metoden godkänner kunskaps bas-ID: t och [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto). Få åtkomst till ytterligare egenskaper för QueryDTO, till exempel [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) och [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) som ska användas i din chatt-robot.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

---

Detta är ett enkelt exempel som frågar i kunskaps frågan. Om du vill förstå avancerade fråge scenarier kan du läsa mer i [exempel på andra frågor](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).

## <a name="delete-a-knowledge-base"></a>Ta bort en kunskapsbas

Ta bort kunskaps basen med [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) -metoden med en parameter i kunskaps bas-ID: t.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]

---

## <a name="get-status-of-an-operation"></a>Hämta status för en åtgärd

Vissa metoder, till exempel skapa och uppdatera, kan ta tillräckligt lång tid i stället för att vänta på att processen ska slutföras, returneras en [åtgärd](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation) . Använd [Åtgärds-ID: t](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) från åtgärden att söka (med logiken för omprövning) för att fastställa statusen för den ursprungliga metoden.

_Loop_ och _Task. Delay_ i följande kodblock används för att simulera omprövnings logik. De bör ersättas med din egen omprövnings logik.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation)]

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

---

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `dotnet run` kommandot från program katalogen.

```dotnetcli
dotnet run
```

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/version-1)

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/version-2)

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart).

---
