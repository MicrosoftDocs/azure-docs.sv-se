---
title: Ansluta Azure Functions till Azure Cosmos DB med Visual Studio Code
description: Lär dig hur du ansluter Azure Functions till ett Azure Cosmos DB konto genom att lägga till en utgående bindning i Visual Studio Code-projektet.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023256"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Ansluta Azure Functions till Azure Cosmos DB med Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du använder Visual Studio Code för att ansluta [Azure Cosmos DB](../cosmos-db/introduction.md) till funktionen som du skapade i föregående snabb starts artikel. Den utgående bindning som du lägger till i den här funktionen skriver data från HTTP-begäran till ett JSON-dokument som lagras i en Azure Cosmos DB-behållare. 

::: zone pivot="programming-language-csharp"
Innan du börjar måste du slutföra snabb starten [: skapa en C#-funktion i Azure med Visual Studio Code](create-first-function-vs-code-csharp.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Innan du börjar måste du slutföra snabb starten [: skapa en JavaScript-funktion i Azure med Visual Studio Code](create-first-function-vs-code-node.md). Om du redan har rensat resurser i slutet av den artikeln går du igenom stegen igen för att återskapa Function-appen och relaterade resurser i Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Konfigurera din miljö

Innan du börjar ska du se till att installera [tillägget Azure-databaser](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) för Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Skapa ditt Azure Cosmos DB-konto

> [!IMPORTANT]
> [Azure Cosmos DB server](../cosmos-db/serverless.md) lös är nu tillgängligt i för hands versionen. Det här förbruknings läget gör Azure Cosmos DB ett starkt alternativ för Server lös arbets belastningar. Om du vill använda Azure Cosmos DB i Server lös läge väljer du **Server** lös som **kapacitets läge** när du skapar ditt konto.

1. Logga in på [Azure Portal](https://portal.azure.com/) i ett nytt webbläsarfönster.

2. Klicka på **Skapa en resurs** > **Databaser** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Fönstret Databaser på Azure Portal" border="true":::

3. På sidan **skapa Azure Cosmos DB konto** anger du inställningarna för ditt nya Azure Cosmos DB-konto. 
 
    Inställning|Värde|Beskrivning
    ---|---|---
    Prenumeration|*Din prenumeration*|Välj den Azure-prenumeration där du skapade din Funktionsapp i [föregående artikel](./create-first-function-vs-code-csharp.md).
    Resursgrupp|*Din resursgrupp*|Välj den resurs grupp där du skapade Funktionsapp i [föregående artikel](./create-first-function-vs-code-csharp.md).
    Kontonamn|*Ange ett unikt namn*|Ange ett unikt namn som identifierar ditt Azure Cosmos DB-konto.<br><br>Konto namnet får bara innehålla gemena bokstäver, siffror och bindestreck (-) och måste vara mellan 3 och 31 tecken långt.
    API|Core (SQL)|Välj **Core (SQL)** om du vill skapa en dokument databas som du kan fråga med hjälp av en SQL-syntax. [Läs mer om Azure Cosmos DB SQL API](../cosmos-db/introduction.md).|
    Location|*Välj den region som är närmast din plats*|Välj en geografisk plats som värd för ditt Azure Cosmos DB-konto. Använd den plats som är närmast dig eller dina användare får snabbast åtkomst till dina data.
    Kapacitetsläge|Server lös eller allokerat data flöde|Välj **Server** lös om du vill skapa ett konto i [Server](../cosmos-db/serverless.md) fritt läge. Välj **tillhandahållet data flöde** för att skapa ett konto i ett [allokerat data flödes](../cosmos-db/set-throughput.md) läge.<br><br>Välj **Server** lös om du vill komma igång med Azure Cosmos dB.

4. Klicka på **Granska + skapa**. Du kan hoppa över avsnittet **Nätverk** och **Taggar**. 

5. Granska sammanfattningsinformationen och klicka på **Skapa**. 

6. Vänta tills din nya Azure Cosmos DB har skapats och välj sedan **gå till resurs**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Skapandet av Azure Cosmos DBs kontot har slutförts" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Skapa en Azure Cosmos DB databas och container

Välj **datautforskaren** och sedan **ny behållare** från ditt Azure Cosmos DB konto. Skapa en ny databas med namnet *min-Database*, en ny behållare med namnet *My-container* och välj `/id` som [partitionsnyckel](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Skapa en ny Azure Cosmos DB behållare från Azure Portal" border="true":::

## <a name="update-your-function-app-settings"></a>Uppdatera inställningarna för Function-appen

I [föregående snabb starts artikel](./create-first-function-vs-code-csharp.md)skapade du en Function-app i Azure. I den här artikeln uppdaterar du Funktionsapp för att skriva JSON-dokument i Azure Cosmos DB-behållaren som du har skapat ovan. Om du vill ansluta till ditt Azure Cosmos DB-konto måste du lägga till dess anslutnings sträng i dina AppData. Sedan hämtar du den nya inställningen till din local.settings.jspå filen så att du kan ansluta till ditt Azure Cosmos DB-konto när du kör lokalt.

1. Leta upp det Azure Cosmos DB konto som du nyss skapade i Visual Studio Code. Högerklicka på namnet och välj **Kopiera anslutnings sträng**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Kopiera Azure Cosmos DB anslutnings sträng" border="true":::

1. Tryck på <kbd>F1</kbd> för att öppna kommando paletten och Sök sedan efter och kör kommandot `Azure Functions: Add New Setting...` .

1. Välj den Function-app som du skapade i föregående artikel. Ange följande information i meddelanderutorna:

    + **Ange ett nytt inställnings namn för appen**: typ `CosmosDbConnectionString` .

    + **Ange värde för "CosmosDbConnectionString"**: klistra in anslutnings strängen för ditt Azure Cosmos DB konto, som kopierades tidigare.

1. Tryck på <kbd>F1</kbd> igen för att öppna kommando-paletten och Sök sedan efter och kör kommandot `Azure Functions: Download Remote Settings...` . 

1. Välj den Function-app som du skapade i föregående artikel. Välj **Ja om** du vill skriva över de befintliga lokala inställningarna. 

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en Azure Cosmos DB utgående bindning måste du ha motsvarande tillägg för bindningar installerade innan du kör projektet. 

::: zone pivot="programming-language-csharp"

Med undantag för HTTP-och timer-utlösare implementeras bindningar som tilläggs paket. Kör följande [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) kommando i terminalfönstret för att lägga till lagrings tilläggs paketet i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Ditt projekt har kon figurer ATS för att använda [tilläggs](functions-bindings-register.md#extension-bundles)paket, som automatiskt installerar en fördefinierad uppsättning tilläggs paket. 

Användnings paket för tillägg aktive ras i host.jspå filen i roten för projektet, som visas på följande sätt:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Nu kan du lägga till Azure Cosmos DB utgående bindning i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I functions kräver varje typ av bindning en `direction` , `type` , och en unik `name` för att definieras i function.jspå filen. Hur du definierar dessa attribut beror på språket i din Function-app.

::: zone pivot="programming-language-csharp"

I ett C#-klass biblioteks projekt definieras bindningarna som binding-attribut i funktions metoden. Den *function.js* filen som krävs av Functions genereras sedan automatiskt baserat på dessa attribut.

Öppna projekt filen *HttpExample. cs* och Lägg till följande parameter i `Run` metod definitionen:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut`Parametern är en IAsyncCollector <T> -typ som representerar en samling JSON-dokument som ska skrivas till din Azure Cosmos DB-behållare när funktionen har slutförts. Vissa attribut anger namnet på behållaren och namnet på den överordnade databasen. Anslutnings strängen för ditt Azure Cosmos DB konto anges av `ConnectionStringSettingAttribute` .

Definitionen av Run-metoden bör nu se ut så här:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Binding-attribut definieras direkt i function.jsi filen. Beroende på bindnings typen kan ytterligare egenskaper krävas. [Konfigurationen för Azure Cosmos DB utdata](./functions-bindings-cosmosdb-v2-output.md#configuration) beskriver de fält som krävs för en Azure Cosmos DB utgående bindning. Tillägget gör det enkelt att lägga till bindningar till function.jsi filen. 

Om du vill skapa en bindning högerklickar du på (Ctrl + klicka på macOS) `function.json` filen i mappen HttpTrigger och väljer **Lägg till bindning...**. Följ anvisningarna för att definiera följande bindnings egenskaper för den nya bindningen:

| Prompt | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindnings riktning** | `out` | Bindningen är en utgående bindning. |
| **Välj bindning med riktningen ut** | `Azure Cosmos DB` | Bindningen är en Azure Cosmos DB bindning. |
| **Namnet som används för att identifiera den här bindningen i din kod** | `outputDocument` | Namn som identifierar den bindnings parameter som refereras till i din kod. |
| **Cosmos DB databasen där data ska skrivas** | `my-database` | Namnet på den Azure Cosmos DB databasen som innehåller mål behållaren. |
| **Databas samling där data ska skrivas** | `my-container` | Namnet på den Azure Cosmos DB-behållare där JSON-dokumenten ska skrivas. |
| **Om värdet är true skapas Cosmos DB-databasen och -samlingen** | `false` | Mål databasen och behållaren finns redan. |
| **Välj inställning från "local.setting.jspå"** | `CosmosDbConnectionString` | Namnet på en program inställning som innehåller anslutnings strängen för det Azure Cosmos DB kontot. |
| **Partitionsnyckel (valfritt)** | *lämna tomt* | Krävs endast när den utgående bindningen skapar behållaren. |
| **Samlingsdataflöde (valfritt)** | *lämna tomt* | Krävs endast när den utgående bindningen skapar behållaren. |

En bindning läggs till `bindings` i matrisen i function.jspå, vilket bör se ut så här:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

::: zone pivot="programming-language-csharp"  

Lägg till kod som använder `documentsOut` objektet utgående bindning för att skapa ett JSON-dokument. Lägg till den här koden innan metoden returnerar.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

I det här läget bör din funktion se ut så här:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Lägg till kod som använder `outputDocument` objektet utgående bindning på `context.bindings` för att skapa ett JSON-dokument. Lägg till den här koden före `context.res` instruktionen.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

I det här läget bör din funktion se ut så här:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Som i föregående artikel trycker du på <kbd>F5</kbd> för att starta programmet för Function-appen och kärn verktyg. 

1. Med kärn verktyg som körs går du till **Azure: Functions** -avsnittet. Under **funktioner**, expanderar du **lokala projekt**  >  **funktioner**. Högerklicka (Ctrl-klicka på Mac) `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Kör funktionen nu från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på RETUR.  
 
1. När ett svar har returnerats trycker du på <kbd>CTRL + C</kbd> för att stoppa Core tools.

### <a name="verify-that-a-json-document-has-been-created"></a>Verifiera att ett JSON-dokument har skapats

1. Gå tillbaka till ditt Azure Cosmos DB-konto på Azure Portal och välj **datautforskaren**.

1. Expandera din databas och behållare och välj **objekt** för att lista de dokument som skapats i din behållare.

1. Kontrol lera att ett nytt JSON-dokument har skapats av utgående bindning.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Verifiera att ett nytt dokument har skapats i Azure Cosmos DB container" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Distribuera om och verifiera den uppdaterade appen

1. I Visual Studio Code, trycker du på F1 för att öppna kommando paletten. I paletten kommando söker du efter och väljer `Azure Functions: Deploy to function app...` .

1. Välj den Function-app som du skapade i den första artikeln. Eftersom du omdistribuerar projektet till samma app väljer du **distribuera** för att ignorera varningen om att skriva över filer.

1. När distributionen är klar kan du återigen använda funktionen **Kör funktion nu...** för att utlösa funktionen i Azure.

1. Kontrol [lera återigen de dokument som skapats i Azure Cosmos DB container](#verify-that-a-json-document-has-been-created) för att kontrol lera att utgående bindningen igen genererar ett nytt JSON-dokument.

## <a name="clean-up-resources"></a>Rensa resurser

I Azure refererar *resurserna* till Function-appar, funktioner, lagrings konton och så vidare. De är grupperade i *resurs grupper* och du kan ta bort allt i en grupp genom att ta bort gruppen.

Du skapade resurser för att slutföra de här snabbstarterna. Det är möjligt att du debiteras för de här resurserna beroende på din [kontostatus](https://azure.microsoft.com/account/) och dina [servicepriser](https://azure.microsoft.com/pricing/). Om du inte behöver resurserna längre så visar vi hur du tar bort dem här:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva JSON-dokument till en Azure Cosmos DB behållare. Nu kan du lära dig mer om hur du utvecklar funktioner med Visual Studio Code:

+ [Utveckla Azure Functions med Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions utlösare och bindningar](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Exempel på kompletta funktions projekt i C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exempel på kompletta funktions projekt i Java Script](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript-guide för utvecklare](functions-reference-node.md)  
::: zone-end  