---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Installera och Använd REST SDK för Phoenix Query Server i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/01/2020
ms.openlocfilehash: 5ce459e2820cfd2ea5cacb036f5d6885c9c5afa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933361"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>REST SDK för Apache Phoenix Query Server

[Apache Phoenix](https://phoenix.apache.org/) är ett stort parallellt Relations databas lager med öppen källkod på [Apache HBase](apache-hbase-overview.md). Med Phoenix kan du använda SQL-liknande frågor med HBase via SSH-verktyg som [SQLLine](apache-hbase-query-with-phoenix.md). Phoenix tillhandahåller också en HTTP-server som kallas Phoenix Query Server (PQS), en tunn klient som stöder två transport metoder för klient kommunikation: JSON-och Protocol-buffertar. Protokoll buffertar är standardmekanismen och erbjuder effektivare kommunikation än JSON.

Den här artikeln beskriver hur du använder PQS REST SDK för att skapa tabeller, upsert-rader individuellt och i bulk och välja data med hjälp av SQL-uttryck. I exemplen används [Microsoft .net driv rutin för Apache Phoenix-frågegrupp](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Detta SDK bygger på [Apache Calcites Avatica](https://calcite.apache.org/avatica/) -API: er, som uteslutande använder protokoll buffertar för serialiserings formatet.

Mer information finns i [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Installera SDK:n

Microsoft .NET driv rutin för Apache Phoenix-frågegrupp tillhandahålls som ett NuGet-paket, som kan installeras från Visual Studio **NuGet Package Manager-konsolen** med följande kommando:

```console
Install-Package Microsoft.Phoenix.Client
```

## <a name="instantiate-new-phoenixclient-object"></a>Instansiera nytt PhoenixClient-objekt

Om du vill börja använda biblioteket instansierar du ett nytt `PhoenixClient` objekt och skickar `ClusterCredentials` det som innehåller `Uri` till klustret och klustrets Apache Hadoop användar namn och lösen ord.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Ersätt kluster namn med ditt HDInsight HBase-kluster namn och användar namn och lösen ord med de Hadoop-autentiseringsuppgifter som anges när klustret skapas. Standard användar namnet för Hadoop är **admin**.

## <a name="generate-unique-connection-identifier"></a>Generera unik anslutnings identifierare

Om du vill skicka en eller flera begär anden till PQS måste du inkludera ett unikt anslutnings-ID för att associera förfrågningarna med anslutningen.

```csharp
string connId = Guid.NewGuid().ToString();
```

Varje exempel anropar först ett anrop till `OpenConnectionRequestAsync` metoden och skickar det unika anslutnings-ID: t. Definiera `ConnectionProperties` och, och `RequestOptions` skicka sedan dessa objekt och den genererade anslutnings identifieraren till- `ConnectionSyncRequestAsync` metoden. Med PQS-objektet ser du till att `ConnectionSyncRequest` både klienten och servern har en konsekvent vy över databas egenskaperna.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest och dess ConnectionProperties

För att anropa  `ConnectionSyncRequestAsync` , skicka ett `ConnectionProperties` objekt.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Här följer några egenskaper av intresse:

| Egenskap | Beskrivning |
| -- | -- |
| AutoCommit | Ett booleskt värde som anger om `autoCommit` är aktiverat för Phoenix-transaktioner. |
| ReadOnly | Ett booleskt värde som anger om anslutningen är skrivskyddad. |
| TransactionIsolation | Ett heltal som anger nivån för transaktions isolering enligt JDBC-specifikationen – se följande tabell.|
| Katalog | Namnet på katalogen som ska användas vid hämtning av anslutnings egenskaper. |
| Schema | Namnet på det schema som ska användas vid hämtning av anslutnings egenskaper. |
| IsDirty | Ett booleskt värde som anger om egenskaperna har ändrats. |

Här följer `TransactionIsolation` värdena:

| Isolerings värde | Beskrivning |
| -- | -- |
| 0 | Transaktioner stöds inte. |
| 1 | Skadade läsningar, icke-repeterbara läsningar och fiktiva läsningar kan uppstå. |
| 2 | Felaktiga läsningar förhindras, men icke-repeterbara läsningar och fiktiva läsningar kan uppstå. |
| 4 | Skadade läsningar och icke-repeterbara läsningar förhindras, men fiktiva läsningar kan uppstå. |
| 8 | Skadade läsningar, icke-repeterbara läsningar och fiktiva läsningar har förhindrats. |

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase, precis som andra RDBMS, lagrar data i tabeller. Phoenix använder standard-SQL-frågor för att skapa nya tabeller, medan du definierar primär nyckel och kolumn typer.

I det här exemplet och alla senare exempel använder du det instansierade `PhoenixClient` objektet som definieras i [instansiera ett nytt PhoenixClient-objekt](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

I föregående exempel skapas en ny tabell med namnet `Customers` med hjälp av `IF NOT EXISTS` alternativet. `CreateStatementRequestAsync`Anropet skapar en ny instruktion i Avitica-servern (pqs). `finally`Blocket stänger de returnerade `CreateStatementResponse` `OpenConnectionResponse` objekten och.

## <a name="insert-data-individually"></a>Infoga data individuellt

I det här exemplet visas en enskild data infogning som hänvisar till en `List<string>` samling med förkortningar för amerikanskt tillstånd och distrikt:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tabellens `StateProvince` kolumn värde kommer att användas i en senare Select-åtgärd.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Strukturen för att köra en INSERT-instruktion liknar att skapa en ny tabell. I slutet av `try` blocket bekräftas transaktionen explicit. I det här exemplet upprepas en Infoga transaktion 300 gånger. I följande exempel visas en effektivare batch-infogande process.

## <a name="batch-insert-data"></a>Infoga data i batch

Följande kod är nästan identisk med koden för att infoga data individuellt. I det här exemplet används `UpdateBatch` objektet i ett anrop till `ExecuteBatchRequestAsync` , i stället för upprepade anrop `ExecuteRequestAsync` med ett för berett uttryck.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

I en test miljö tog en separat infogning av 300 nya poster i ungefär 2 minuter. Däremot infogar du 300-poster som en batch som bara kräver 6 sekunder.

## <a name="select-data"></a>Välj data

Det här exemplet visar hur du återanvänder en anslutning för att köra flera frågor:

1. Markera alla poster och hämta sedan återstående poster efter att standard Max värdet på 100 har returnerats.
2. Använd ett SELECT-uttryck med totalt antal rader för att hämta det enkla skalära resultatet.
3. Kör en SELECT-instruktion som returnerar det totala antalet kunder per stat eller område.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Utmatningen av `select` satserna bör vara följande resultat:

```output
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Nästa steg

* [Apache Phoenix i HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Använda Apache HBase REST SDK](apache-hbase-rest-sdk.md)
