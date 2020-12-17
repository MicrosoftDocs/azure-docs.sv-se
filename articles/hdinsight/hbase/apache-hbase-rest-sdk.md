---
title: Använda HBase .NET SDK – Azure HDInsight
description: Använd HBase .NET SDK för att skapa och ta bort tabeller och för att läsa och skriva data.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 9b5693ddef5e512b0a95c87a700fd12acd4b5fae
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654650"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Använd .NET SDK för Apache HBase

[Apache HBase](apache-hbase-overview.md) tillhandahåller två primära alternativ för att arbeta med dina data: [Apache Hive frågor och anrop till HBASE RESTful-API](apache-hbase-tutorial-get-started-linux.md). Du kan arbeta direkt med REST API med hjälp av `curl` kommandot eller ett liknande verktyg.

För C#-och .NET-program tillhandahåller [Microsoft HBASE rest Client library för .net](https://www.nuget.org/packages/Microsoft.HBase.Client/) ett klient bibliotek ovanpå HBase-REST API.

## <a name="install-the-sdk"></a>Installera SDK:n

HBase .NET SDK tillhandahålls som ett NuGet-paket, som kan installeras från Visual Studio **NuGet Package Manager-konsolen** med följande kommando:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Instansiera ett nytt HBaseClient-objekt

Om du vill använda SDK instansierar du ett nytt `HBaseClient` objekt, skickar i `ClusterCredentials` består av `Uri` till klustret och Hadoop-användarnamnet och lösen ordet.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Ersätt kluster namn med ditt HDInsight HBase-kluster namn och användar namn och lösen ord med de Apache Hadoop autentiseringsuppgifter som angavs när klustret skapades. Standard användar namnet för Hadoop är **admin**.

## <a name="create-a-new-table"></a>Skapa en ny tabell

HBase lagrar data i tabeller. En tabell består av en *Rowkey*, den primära nyckeln och en eller flera grupper av kolumner som kallas *kolumn serier*. Data i varje tabell fördelas vågrätt med ett Rowkey intervall i *regioner*. Varje region har en start-och slut nyckel. En tabell kan ha en eller flera regioner. När data i tabellen växer, delar HBase upp stora regioner i mindre regioner. Regioner lagras i *region servrar* där en region Server kan lagra flera regioner.

Data lagras fysiskt i *HFiles*. En enda HFile innehåller data för en tabell, en region och en kolumn serie. Rader i HFile lagras sorterade på Rowkey. Varje HFile har ett *B + träd* -index för att hämta rader snabbare.

Om du vill skapa en ny tabell anger du en `TableSchema` kolumn och. Följande kod kontrollerar om tabellen "RestSDKTable" redan finns, om inte tabellen skapas.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Den här nya tabellen innehåller serier med två kolumner, T1 och T2. Eftersom kolumn familjer lagras separat i olika HFiles, är det klokt att ha en separat kolumn serie för data som används ofta. I följande exempel på [infoga data](#insert-data) läggs kolumner till i T1 Column-serien.

## <a name="delete-a-table"></a>Ta bort en tabell

Ta bort en tabell:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Infoga data

Om du vill infoga data anger du en unik rad nyckel som rad-ID. Alla data lagras i en `byte[]` matris. I följande kod definieras och läggs `title` `director` kolumnerna,, och `release_date` till i T1 Column-serien, eftersom de här kolumnerna används ofta. `description` `tagline` Kolumnerna och läggs till i kolumn familjen T2. Du kan partitionera dina data i kolumn familjer efter behov.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementerar [Cloud BigTable](https://cloud.google.com/bigtable/), så data formatet ser ut som följande bild:

![Apache HBase-exempel data utdata](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Välj data

Om du vill läsa data från en HBase-tabell skickar du tabell namnet och rad nyckeln till- `GetCellsAsync` metoden för att returnera `CellSet` .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

I det här fallet returnerar koden bara den första matchande raden, eftersom det bara ska finnas en rad för en unik nyckel. Det returnerade värdet ändras till `string` format från `byte[]` matrisen. Du kan också konvertera värdet till andra typer, till exempel ett heltal för filmens utgivnings datum:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Skanna över rader

HBase använder `scan` för att hämta en eller flera rader. Det här exemplet begär flera rader i batchar med 10 och hämtar data vars nyckel värden är mellan 25 och 35. När du har hämtat alla rader tar du bort skannern för att rensa resurserna.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Nästa steg

* [Kom igång med ett Apache HBase-exempel i HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Bygg ett program från slut punkt till slut punkt med [Analysera Twitter-sentiment i real tid med Apache HBase](./apache-hbase-tutorial-get-started-linux.md)