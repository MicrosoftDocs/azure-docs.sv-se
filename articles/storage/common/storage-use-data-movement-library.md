---
title: Överföra data med data flyttnings biblioteket för .NET
titleSuffix: Azure Storage
description: Använd biblioteket för data förflyttning för att flytta eller kopiera data till eller från blob-och fil innehåll. Kopiera data till Azure Storage från lokala filer eller kopiera data inom eller mellan lagrings konton. Migrera enkelt dina data till Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023422"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Överföra data med dataflyttbiblioteket

Biblioteket för Azure Storage data förflyttning är ett bibliotek med öppen källkod mellan olika plattformar som har utformats för hög prestanda överföring, hämtning och kopiering av blobbar och filer. Data flyttnings biblioteket innehåller praktiska metoder som inte är tillgängliga i Azure Storage klient biblioteket för .NET. Dessa metoder ger möjlighet att ställa in antalet parallella åtgärder, spåra överförings förlopp, enkelt återuppta en avbruten överföring och mycket mer.

Det här biblioteket använder också .NET Core, vilket innebär att du kan använda det när du skapar .NET-appar för Windows, Linux och macOS. Mer information om .NET Core finns i [.net Core-dokumentationen](https://dotnet.github.io/). Det här biblioteket fungerar även för traditionella .NET Framework-appar för Windows.

Det här dokumentet visar hur du skapar ett .NET Core-konsolprogram som körs på Windows, Linux och macOS och utför följande scenarier:

- Ladda upp filer och kataloger till Blob Storage.
- Definiera antalet parallella åtgärder vid överföring av data.
- Spåra data överförings förlopp.
- Återuppta avbruten data överföring.
- Kopiera filen från URL till Blob Storage.
- Kopiera från Blob Storage till Blob Storage.

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio Code](https://code.visualstudio.com/)
- Ett [Azure Storage-konto](storage-account-create.md)

## <a name="setup"></a>Konfiguration

1. Besök [installations guiden för .net Core](https://www.microsoft.com/net/core) för att installera .net Core. När du väljer din miljö väljer du kommando rads alternativet.
2. Skapa en katalog för projektet från kommando raden. Navigera till den här katalogen och skriv sedan `dotnet new console -o <sample-project-name>` för att skapa ett C#-konsol projekt.
3. Öppna den här katalogen i Visual Studio Code. Det här steget kan snabbt utföras via kommando raden genom att skriva `code .` i Windows.
4. Installera [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) från Visual Studio Code Marketplace. Starta om Visual Studio Code.
5. Nu bör du se två prompter. Ett är för att lägga till "nödvändiga till gångar för att skapa och felsöka". Klicka på Ja. En annan prompt är för att återställa olösta beroenden. Klicka på Återställ.
6. Ändra `launch.json` under `.vscode` för att använda extern Terminal som en konsol. Den här inställningen bör läsas som `"console": "externalTerminal"`
7. Med Visual Studio Code kan du felsöka .NET Core-program. Tryck `F5` på för att köra programmet och kontrol lera att installationen fungerar. Du bör se "Hello World!" skrivs ut till-konsolen.

## <a name="add-the-data-movement-library-to-your-project"></a>Lägg till data flyttnings biblioteket i projektet

1. Lägg till den senaste versionen av data flyttnings biblioteket i `dependencies` avsnittet i `<project-name>.csproj` filen. Vid tidpunkten för skrivning skulle den här versionen vara `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. En prompt ska visa för att återställa projektet. Klicka på knappen Återställ. Du kan också återställa projektet från kommando raden genom att skriva kommandot `dotnet restore` i rot katalogen för projekt katalogen.

Ändra `<project-name>.csproj` :

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurera Skeleton för ditt program

Det första vi gör är att ställa in "Skeleton"-koden för vårt program. I den här koden uppmanas vi att ange ett lagrings konto namn och en konto nyckel och använder dessa autentiseringsuppgifter för att skapa ett `CloudStorageAccount` objekt. Det här objektet används för att interagera med vårt lagrings konto i alla överförings scenarier. Koden meddelar också oss att välja den typ av överförings åtgärd som vi vill köra.

Ändra `Program.cs` :

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Ladda upp en lokal fil till en BLOB

Lägg till metoderna `GetSourcePath` och `GetBlob` för att `Program.cs` :

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Ändra `TransferLocalFileToAzureBlob` metoden:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

I den här koden uppmanas vi att ange sökvägen till en lokal fil, namnet på en ny eller befintlig behållare och namnet på en ny blob. `TransferManager.UploadAsync`Metoden utför uppladdningen med hjälp av den här informationen.

Tryck `F5` på för att köra programmet. Du kan kontrol lera att överföringen har skett genom att visa ditt lagrings konto med [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ange antalet parallell åtgärder

En funktion som erbjuds av data flyttnings biblioteket är möjligheten att ange antalet parallella åtgärder för att öka data överförings data flödet. Som standard anger data flyttnings biblioteket antalet parallell åtgärder till 8 * antalet kärnor på datorn.

Tänk på att många parallella åtgärder i en miljö med låg bandbredd kan överbelasta nätverks anslutningen och förhindra att åtgärder fullständigt slutförs. Du måste experimentera med den här inställningen för att avgöra vad som fungerar bäst baserat på din tillgängliga nätverks bandbredd.

Nu ska vi lägga till kod som gör det möjligt för oss att ange antalet parallella åtgärder. Nu ska vi lägga till kod som visar hur lång tid det tar för överföringen att slutföras.

Lägg till en `SetNumberOfParallelOperations` metod för att `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändra `ExecuteChoice` metoden som ska användas `SetNumberOfParallelOperations` :

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Ändra `TransferLocalFileToAzureBlob` metoden för att använda en timer:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Spåra överförings förlopp

Det är bra att veta hur lång tid det tog för data att överföras. Att kunna se förloppet för överföringen *under* överförings åtgärden skulle dock vara ännu bättre. För att uppnå det här scenariot måste vi skapa ett `TransferContext` objekt. `TransferContext`Objektet kommer i två former: `SingleTransferContext` och `DirectoryTransferContext` . Den tidigare är för att överföra en enda fil och den senare för att överföra en katalog med filer.

Lägg till metoderna `GetSingleTransferContext` och `GetDirectoryTransferContext` för att `Program.cs` :

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Ändra `TransferLocalFileToAzureBlob` metoden som ska användas `GetSingleTransferContext` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Återuppta en avbruten överföring

En annan praktisk funktion som erbjuds av data flytt biblioteket är möjligheten att återuppta en avbruten överföring. Nu ska vi lägga till kod som gör det möjligt för oss att tillfälligt avbryta överföringen genom att skriva `c` och sedan återuppta överföringen 3 sekunder senare.

Ändra `TransferLocalFileToAzureBlob` :

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Fram till nu har vårt `checkpoint` värde alltid angetts till `null` . Om vi avbryter överföringen hämtar vi den senaste kontroll punkten för vår överföring och använder sedan den nya kontroll punkten i vår överförings kontext.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Överföra en lokal katalog till Blob Storage

Det skulle bli Disappointing om data flyttnings biblioteket bara kan överföra en fil i taget. Som tur är, detta är inte fallet. Med data flyttnings biblioteket kan du överföra en katalog med filer och alla dess under kataloger. Nu ska vi lägga till kod som gör det möjligt för oss att göra just det.

Lägg först till-metoden `GetBlobDirectory` i `Program.cs` :

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Ändra sedan `TransferLocalDirectoryToAzureBlobDirectory` :

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Det finns några skillnader mellan den här metoden och metoden för att ladda upp en enskild fil. Vi använder nu `TransferManager.UploadDirectoryAsync` och metoden som `getDirectoryTransferContext` vi skapade tidigare. Dessutom tillhandahåller vi ett `options` värde för vår överförings åtgärd, vilket gör att vi kan ange att vi vill inkludera under kataloger i vår uppladdning.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiera en fil från URL till en BLOB

Nu ska vi lägga till kod som gör det möjligt för oss att kopiera en fil från en URL till en Azure-blob.

Ändra `TransferUrlToAzureBlob` :

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ett viktigt användnings fall för den här funktionen är när du behöver flytta data från en annan moln tjänst (t. ex. AWS) till Azure. Så länge du har en URL som ger dig åtkomst till resursen kan du enkelt flytta resursen till Azure-blobbar med hjälp av- `TransferManager.CopyAsync` metoden. Den här metoden introducerar också en ny boolesk parameter. Om du anger den här parametern `true` anges att vi vill göra en asynkron kopia på Server sidan. Om du anger den här parametern som `false` en synkron kopiering, innebär det att resursen laddas ned till den lokala datorn först och sedan överförs till Azure-blobben. Synkron kopiering är dock för närvarande bara tillgänglig för kopiering från en Azure Storage resurs till en annan.

## <a name="copy-a-blob"></a>Kopiera en BLOB

En annan funktion som tillhandahålls unikt av data flyttnings biblioteket är möjligheten att kopiera från en Azure Storage resurs till en annan.

Ändra `TransferAzureBlobToAzureBlob` :

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

I det här exemplet anger vi den booleska parametern i `TransferManager.CopyAsync` till `false` för att indikera att vi vill göra en synkron kopia. Det innebär att resursen laddas ned till den lokala datorn först och sedan överförs till Azure-blobben. Alternativet för synkron kopiering är ett bra sätt att se till att kopierings åtgärden har en konsekvent hastighet. Hastigheten för en asynkron kopia på Server sidan är däremot beroende av den tillgängliga nätverks bandbredden på servern, som kan variera. Synkron kopiering kan dock generera ytterligare utgående kostnad jämfört med asynkron kopia. Den rekommenderade metoden är att använda synkron kopia i en virtuell Azure-dator som finns i samma region som ditt käll lagrings konto för att undvika utgångs kostnader.

Programmet för data flytt har slutförts. [Det fullständiga kod exemplet finns på GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Nästa steg

[Azure Storage referens dokumentation för data flytt bibliotek](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
