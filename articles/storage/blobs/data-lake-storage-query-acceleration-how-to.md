---
title: Filtrera data med hjälp av Azure Data Lake Storage fråga acceleration | Microsoft Docs
description: Använd Query acceleration för att hämta en delmängd av data från ditt lagrings konto.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cc63a9787a3611f28e6466564735d029bf65b757
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968406"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrera data med hjälp av Azure Data Lake Storage fråga om acceleration

Den här artikeln visar hur du använder Query acceleration för att hämta en delmängd av data från ditt lagrings konto. 

Med hjälp av frågekörning kan program och analys ramverk dramatiskt optimera data bearbetningen genom att bara hämta de data som de behöver för att utföra en specifik åtgärd. Mer information finns i [Azure Data Lake Storage fråga om acceleration](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Krav

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **Allmänt-syfte v2-** lagrings konto. Se [skapa ett lagrings konto](../common/storage-account-create.md).

- Välj en flik för att visa alla SDK-särskilda krav.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Inte tillämpligt

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) version 8 eller senare

  - [Apache maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > I den här artikeln förutsätter vi att du har skapat ett Java-projekt med Apache Maven. Ett exempel på hur du skapar ett projekt med hjälp av Apache maven finns i [Konfigurera](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 eller senare.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Det krävs inga ytterligare krav för att använda Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Aktivera acceleration av fråga

Om du vill använda acceleration av frågor måste du registrera funktionen för frågans acceleration med din prenumeration. När du har kontrollerat att funktionen har registrerats måste du registrera Azure Storage Resource Provider. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Steg 1: registrera funktionen för frågans acceleration

Om du vill använda acceleration av frågor måste du först registrera funktionen för frågans acceleration med din prenumeration. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öppna ett Windows PowerShell-kommando fönster.

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

3. Registrera funktionen för frågans acceleration med kommandot [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på lagrings kontot.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

3. Registrera funktionen för frågans acceleration med hjälp av kommandot [AZ Feature register](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Steg 2: kontrol lera att funktionen har registrerats

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Verifiera att registreringen är klar med kommandot [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Verifiera att registreringen är klar med hjälp av kommandot [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Steg 3: registrera Azure Storage Resource Provider

När registreringen har godkänts måste du registrera Azure Storage resurs leverantören igen. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera resurs leverantören med kommandot [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Registrera resurs leverantören med kommandot [AZ Provider register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Konfigurera din miljö

### <a name="step-1-install-packages"></a>Steg 1: installera paket 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installera AZ-modulen version 4.6.0 eller högre.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Om du vill uppdatera från en äldre version av AZ kör du följande kommando:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Öppna en kommando tolk och ändra katalogen ( `cd` ) i projektmappen till exempel:

   ```console
   cd myProject
   ```

2. Installera `12.5.0-preview.6` versionen av Azure Blob Storage klient biblioteket för .net-paketet med hjälp av `dotnet add package` kommandot. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. I exemplen som visas i den här artikeln analyseras en CSV-fil med hjälp av [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -biblioteket. Använd följande kommando för att använda biblioteket.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Öppna *pom.xml* -filen för projektet i en text redigerare. Lägg till följande beroende element i gruppen med beroenden. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Installera Azure Data Lake Storage klient bibliotek för python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installera Data Lake klient bibliotek för Java Script genom att öppna ett terminalfönster och sedan skriva in följande kommando.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Steg 2: Lägg till instruktioner

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Inte tillämpligt

#### <a name="net"></a>[.NET](#tab/dotnet)

Lägg till dessa `using` uttryck överst i din kod fil.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Frågans acceleration hämtar CSV-och JSON-formaterade data. Därför bör du se till att lägga till instruktioner för alla CSV-eller JSON-parsningsfel som du väljer att använda. I exemplen som visas i den här artikeln analyseras en CSV-fil med hjälp av [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -biblioteket som finns på NuGet. Därför skulle vi lägga till dessa `using` instruktioner överst i kod filen.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

För att kunna kompilera exempel som presenteras i den här artikeln måste du också lägga till dessa `using` instruktioner.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Lägg till dessa `import` uttryck överst i din kod fil.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Lägg till dessa import uttryck längst upp i din kod fil.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Inkludera `storage-blob` modulen genom att placera den här instruktionen överst i din kod fil. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Frågans acceleration hämtar CSV-och JSON-formaterade data. Därför bör du se till att lägga till instruktioner för alla CSV-eller JSON-parsningsfel som du väljer att använda. I exemplen som visas i den här artikeln analyseras en CSV-fil med hjälp av [snabb-CSV-](https://www.npmjs.com/package/fast-csv) modulen. Vi skulle därför lägga till den här instruktionen överst i kod filen.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Hämta data med hjälp av ett filter

Du kan använda SQL för att ange rad filter-predikat och kolumn projektioner i en begäran om acceleration av frågor. Följande kod frågar en CSV-fil i Storage och returnerar alla rader med data där den tredje kolumnen matchar värdet `Hemingway, Ernest` . 

- I SQL-frågan används nyckelordet `BlobStorage` för att beteckna filen som efter frågas.

- Kolumn referenser anges som `_N` den första kolumnen `_1` . Om käll filen innehåller en rubrik rad kan du referera till kolumner med det namn som anges i rubrik raden. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Metoden async `BlobQuickQueryClient.QueryAsync` skickar frågan till API för frågans acceleration och skickar sedan tillbaka resultatet till programmet som ett [Stream](/dotnet/api/system.io.stream) -objekt.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Metoden `BlobQuickQueryClient.openInputStream()` skickar frågan till API för frågans acceleration och skickar sedan tillbaka resultatet till programmet som ett `InputStream` objekt som kan läsas som andra InputStream-objekt.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

I det här exemplet skickas frågan till API för frågans acceleration och strömmar sedan tillbaka resultatet. `blob`Objektet som har skickats till `queryHemingway` Hjälp funktionen är av typen [BlockBlobClient](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient). Mer information om hur du hämtar ett [BlockBlobClient](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient) -objekt finns i [snabb start: hantera blobbar med Java Script v12 SDK i Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Hämta vissa kolumner

Du kan begränsa resultaten till en delmängd av kolumnerna. På så sätt hämtar du bara de kolumner som behövs för att utföra en specifik beräkning. Detta förbättrar programmets prestanda och minskar kostnaden eftersom mindre data överförs över nätverket. 

Den här koden hämtar endast `BibNum` kolumnen för alla böcker i data uppsättningen. Den använder också informationen från rubrik raden i käll filen för att referera till kolumner i frågan.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

Följande kod kombinerar rad filtrering och kolumn prognoser till samma fråga. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Nästa steg

- [Azure Data Lake Storage fråga om acceleration](data-lake-storage-query-acceleration.md)
- [Språk referens för Query acceleration SQL](query-acceleration-sql-reference.md)