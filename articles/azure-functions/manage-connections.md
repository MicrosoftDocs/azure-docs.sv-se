---
title: Hantera anslutningar i Azure Functions
description: Lär dig att undvika prestanda problem i Azure Functions genom att använda statiska anslutnings klienter.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: ec16ce3e7f9793be2a012a029bcca31c9a7ea4cf
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936710"
---
# <a name="manage-connections-in-azure-functions"></a>Hantera anslutningar i Azure Functions

Funktioner i en Function-app delar resurser. Bland de delade resurserna är anslutningar: HTTP-anslutningar, databas anslutningar och anslutningar till tjänster som Azure Storage. När många funktioner körs samtidigt är det möjligt att köra slut på tillgängliga anslutningar. Den här artikeln förklarar hur du kan koda dina funktioner för att undvika att använda fler anslutningar än de behöver.

## <a name="connection-limit"></a>Anslutningsgräns

Antalet tillgängliga anslutningar är begränsat delvis eftersom en Function-App körs i en [sandbox-miljö](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). En av de begränsningar som används i den begränsade koden är en gräns för antalet utgående anslutningar, som för närvarande är 600 aktiva (totalt 1 200) anslutningar per instans. När du når den här gränsen skriver funktions körningen följande meddelande till loggarna: `Host thresholds exceeded: Connections` . Mer information finns i [funktions tjänst begränsningarna](functions-scale.md#service-limits).

Den här gränsen är per instans. När [skalnings styrenheten lägger till Function App-instanser](event-driven-scaling.md) för att hantera fler begär Anden, har varje instans en oberoende anslutnings gräns. Det innebär att det inte finns någon global anslutnings gräns och du kan ha mycket fler än 600 aktiva anslutningar över alla aktiva instanser.

Kontrol lera att du har aktiverat Application Insights för din Function-app vid fel sökning. Med Application Insights kan du visa mått för dina funktions program som körningar. Mer information finns i [Visa telemetri i Application Insights](analyze-telemetry-data.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Statiska klienter

För att undvika att rymma fler anslutningar än nödvändigt kan du återanvända klient instanser i stället för att skapa nya med varje funktions anrop. Vi rekommenderar att du använder klient anslutningar för alla språk som du kan skriva din funktion i. Till exempel kan .NET-klienter som [httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true), [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)och Azure Storage klienter hantera anslutningar om du använder en enda statisk klient.

Här följer några rikt linjer som du följer när du använder en tjänst specifik klient i ett Azure Functions program:

- Skapa *inte* en ny klient med varje funktions anrop.
- *Skapa en* enda statisk klient som varje funktion anrop kan använda.
- *Överväg att* skapa en enda statisk klient i en delad hjälp klass om olika funktioner använder samma tjänst.

## <a name="client-code-examples"></a>Klient kod exempel

Det här avsnittet visar metod tips för att skapa och använda klienter från din funktions kod.

### <a name="httpclient-example-c"></a>HttpClient-exempel (C#)

Här är ett exempel på en C#-funktions kod som skapar en statisk [httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) -instans:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

En vanlig fråga om [httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) i .net är "vill jag ta bort min klient?" I allmänhet tar du bort objekt som implementeras `IDisposable` när du är klar med dem. Men du tar inte bort en statisk klient eftersom du inte är färdig med den när funktionen avslutas. Du vill att den statiska klienten ska vara i drift för programmets varaktighet.

### <a name="http-agent-examples-javascript"></a>Exempel på HTTP-agent (Java Script)

Eftersom det ger bättre anslutnings hanterings alternativ bör du använda den inbyggda [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) klassen i stället för icke-ursprungliga metoder, till exempel `node-fetch` modulen. Anslutnings parametrarna konfigureras via alternativen i `http.agent` klassen. Detaljerade alternativ som är tillgängliga med HTTP-agenten finns i [New agent ( \[ alternativ \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Den globala `http.globalAgent` klass som används av `http.request()` har alla dessa värden inställda på sina respektive standardvärden. Det rekommenderade sättet att konfigurera anslutnings gränser i functions är att ange ett högsta antal globalt. I följande exempel anges det maximala antalet Sockets för Function-appen:

```js
http.globalAgent.maxSockets = 200;
```

 I följande exempel skapas en ny HTTP-begäran med en anpassad HTTP-agent för denna begäran:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient kod exempel (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en singleton-Azure Cosmos DB-klient för programmets livs längd](../cosmos-db/performance-tips.md#sdk-usage). I följande exempel visas ett mönster för att göra det i en funktion:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Om du arbetar med Functions v3. x måste du ha en referens till Microsoft.Azure.DocumentDB. Core. Lägg till en referens i koden:

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
Skapa också en fil med namnet "function. proj" för utlösaren och Lägg till följande innehåll:

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>CosmosClient Code-exempel (Java Script)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) ansluter till en Azure Cosmos DB-instans. Azure Cosmos DB-dokumentationen rekommenderar att du [använder en singleton-Azure Cosmos DB-klient för programmets livs längd](../cosmos-db/performance-tips.md#sdk-usage). I följande exempel visas ett mönster för att göra det i en funktion:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient-anslutningar

Funktions koden kan använda .NET Framework Data Provider för SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) för att upprätta anslutningar till en SQL Relations databas. Detta är även den underliggande providern för data ramverk som förlitar sig på ADO.NET, t. ex. [Entity Framework](/ef/ef6/). Till skillnad från [httpclient](/dotnet/api/system.net.http.httpclient) -och [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) -anslutningar implementerar ADO.net anslutningspoolen som standard. Men eftersom du fortfarande kan ta slut på anslutningar bör du optimera anslutningar till databasen. Mer information finns i [SQL Server anslutningspoolen (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Vissa data ramverk, till exempel Entity Framework, hämtar vanligt vis anslutnings strängar från avsnittet **connectionStrings** i en konfigurations fil. I det här fallet måste du uttryckligen lägga till anslutnings strängar för SQL Database i samlingen **anslutnings strängar** för dina funktioner och i [local.settings.jspå filen](functions-run-local.md#local-settings-file) i det lokala projektet. Om du skapar en instans av [SQLConnection](/dotnet/api/system.data.sqlclient.sqlconnection) i funktions koden bör du lagra värdet för anslutnings strängen i **program inställningarna** med dina andra anslutningar.

## <a name="next-steps"></a>Nästa steg

För ytterligare information om varför vi rekommenderar statiska klienter, se [felaktigt mönster för instansiering](/azure/architecture/antipatterns/improper-instantiation/).

Mer Azure Functions prestanda tips finns i [optimera prestanda och tillförlitlighet för Azure Functions](functions-best-practices.md).
