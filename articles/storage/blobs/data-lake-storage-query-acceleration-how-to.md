---
title: Filtrera data med hjälp Azure Data Lake Storage frågeacceleration | Microsoft Docs
description: Använd frågeacceleration för att hämta en delmängd data från ditt lagringskonto.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 756258db1c6e91002bf3a7c2bd0f71f921ce655d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769939"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Filtrera data med hjälp Azure Data Lake Storage frågeacceleration

Den här artikeln visar hur du använder frågeacceleration för att hämta en delmängd data från ditt lagringskonto. 

Med frågeacceleration kan program och analysramverk optimera databehandlingen avsevärt genom att endast hämta de data som krävs för att utföra en viss åtgärd. Mer information finns i Azure Data Lake Storage [frågeacceleration](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett **v2-lagringskonto** för generell användning. se [Skapa ett lagringskonto](../common/storage-account-create.md).

- Välj en flik för att visa eventuella SDK-specifika krav.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Inte tillämpligt

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Den här artikeln förutsätter att du har skapat ett Java-projekt med hjälp av Apache Maven. Ett exempel på hur du skapar ett projekt med apache Maven finns [i Konfigurera](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 eller högre.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Det finns inga ytterligare krav som krävs för att använda Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Aktivera frågeacceleration

Om du vill använda frågeacceleration måste du registrera funktionen för frågeacceleration i din prenumeration. När du har kontrollerat att funktionen har registrerats måste du registrera Azure Storage resursprovidern. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Steg 1: Registrera frågeaccelerationsfunktionen

Om du vill använda frågeacceleration måste du först registrera frågeaccelerationsfunktionen i din prenumeration. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Öppna ett Windows PowerShell kommandofönster.

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med mer än en prenumeration anger du din aktiva prenumeration.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

3. Registrera frågeaccelerationsfunktionen med hjälp av kommandot [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du [](/cli/azure/install-azure-cli) har installerat Azure CLI lokalt öppnar du ett kommandokonsolprogram, till exempel Windows PowerShell.

2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen till prenumerationen på lagringskontot.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

3. Registrera frågeaccelerationsfunktionen med [hjälp av kommandot az feature register.](/cli/azure/feature#az_feature_register)

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Steg 2: Kontrollera att funktionen är registrerad

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Kontrollera att registreringen är klar med kommandot [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kontrollera att registreringen är klar med kommandot [az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Steg 3: Registrera Azure Storage resursprovidern

När registreringen har godkänts måste du registrera om Azure Storage resursprovidern. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera resursprovidern med kommandot [Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider)

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Registrera resursprovidern med kommandot [az provider](/cli/azure/provider#az_provider_register) register.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Konfigurera din miljö

### <a name="step-1-install-packages"></a>Steg 1: Installera paket 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installera Az-modul version 4.6.0 eller senare.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Om du vill uppdatera från en äldre version av Az kör du följande kommando:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Öppna en kommandotolk och ändra katalog ( `cd` ) till projektmappen Till exempel:

   ```console
   cd myProject
   ```

2. Installera versionen `12.5.0-preview.6` eller senare av Azure Blob Storage-klientbiblioteket för .NET-paketet med hjälp av kommandot `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Exemplen som visas i den här artikeln parsar en CSV-fil med hjälp av [csvHelper-biblioteket.](https://www.nuget.org/packages/CsvHelper/) Om du vill använda biblioteket använder du följande kommando.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Öppna *pom.xml* i projektet i en textredigerare. Lägg till följande beroendeelement i gruppen med beroenden. 

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

Installera Azure Data Lake Storage för Python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installera Data Lake-klientbiblioteket för JavaScript genom att öppna ett terminalfönster och skriv sedan följande kommando.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Steg 2: Lägg till instruktioner

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Inte tillämpligt

#### <a name="net"></a>[.NET](#tab/dotnet)

Lägg till `using` de här isatserna överst i kodfilen.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Frågeacceleration hämtar CSV- och Json-formaterade data. Se därför till att lägga till using-instruktioner för alla CSV- eller Json-parsningsbibliotek som du väljer att använda. Exemplen som visas i den här artikeln parsar en CSV-fil med hjälp av [csvHelper-biblioteket](https://www.nuget.org/packages/CsvHelper/) som är tillgängligt på NuGet. Därför lägger vi till de `using` här isatserna överst i kodfilen.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

För att kompilera exempel som presenteras i den här artikeln måste du också lägga till `using` de här isatserna.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Lägg till `import` de här isatserna överst i kodfilen.

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

Lägg till dessa importutdrag överst i kodfilen.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Inkludera modulen `storage-blob` genom att placera den här instruktionen överst i kodfilen. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Frågeacceleration hämtar CSV- och Json-formaterade data. Se därför till att lägga till instruktioner för alla CSV- eller Json-parsningsmoduler som du väljer att använda. Exemplen som visas i den här artikeln parsar en CSV-fil med hjälp av [modulen fast-csv.](https://www.npmjs.com/package/fast-csv) Därför lägger vi till den här instruktionen överst i kodfilen.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Hämta data med hjälp av ett filter

Du kan använda SQL för att ange radfilter-predikat och kolumnprojektioner i en begäran om frågeacceleration. Följande kod frågar en CSV-fil i lagringen och returnerar alla rader med data där den tredje kolumnen matchar värdet `Hemingway, Ernest` . 

- I SQL-frågan används `BlobStorage` nyckelordet för att ange den fil som efterfrågas.

- Kolumnreferenser anges som `_N` där den första kolumnen är `_1` . Om källfilen innehåller en rubrikrad kan du referera till kolumner med det namn som anges på rubrikraden. 

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

Den asynkrona metoden skickar frågan till API:et för frågeacceleration och strömmar sedan resultaten tillbaka `BlobQuickQueryClient.QueryAsync` till programmet som ett [Stream-objekt.](/dotnet/api/system.io.stream)

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
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
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

Metoden skickar frågan till API:et för frågeacceleration och strömmar sedan tillbaka resultatet till programmet som ett -objekt som kan läsas som andra `BlobQuickQueryClient.openInputStream()` `InputStream` InputStream-objekt.

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

Det här exemplet skickar frågan till API:et för frågeacceleration och strömmar sedan resultatet tillbaka. Objektet `blob` som skickas till `queryHemingway` hjälpfunktionen är av typen [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Mer information om hur du hämtar ett [BlockBlobClient-objekt](/javascript/api/@azure/storage-blob/blockblobclient) finns i [Snabbstart: Hantera blobar med JavaScript v12 SDK i Node.js](storage-quickstart-blobs-nodejs.md).

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

## <a name="retrieve-specific-columns"></a>Hämta specifika kolumner

Du kan begränsa resultatet till en delmängd kolumner. På så sätt hämtar du bara de kolumner som behövs för att utföra en viss beräkning. Detta förbättrar programmets prestanda och minskar kostnaderna eftersom mindre data överförs över nätverket. 

Den här koden hämtar endast `BibNum` kolumnen för alla böcker i datauppsättningen. Den använder också informationen från rubrikraden i källfilen för att referera till kolumner i frågan.

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

I följande kod kombineras radfiltrering och kolumnprojektioner i samma fråga. 

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

- [Azure Data Lake Storage frågeacceleration](data-lake-storage-query-acceleration.md)
- [Referens för SQL-språk för frågeacceleration](query-acceleration-sql-reference.md)
