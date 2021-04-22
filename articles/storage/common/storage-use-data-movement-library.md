---
title: Överföra data med dataflyttningsbiblioteket för .NET
titleSuffix: Azure Storage
description: Använd dataförflyttningsbiblioteket för att flytta eller kopiera data till eller från blob- och filinnehåll. Kopiera data till Azure Storage lokala filer eller kopiera data inom eller mellan lagringskonton. Migrera enkelt dina data till Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f87379f48f82757916aef0fa0d358835f48cb9a5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875943"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Överföra data med dataflyttbiblioteket

Biblioteket Azure Storage Data Movement är ett plattformsoberoende bibliotek med öppen källkod som är utformat för uppladdning, nedladdning och kopiering av blobar och filer med höga prestanda. Dataförflyttningsbiblioteket innehåller praktiska metoder som inte är tillgängliga i Azure Storage klientbibliotek för .NET. Dessa metoder ger möjlighet att ange antalet parallella åtgärder, spåra överföringsförloppet, enkelt återuppta en avbruten överföring och mycket mer.

Det här biblioteket använder också .NET Core, vilket innebär att du kan använda det när du skapar .NET-appar för Windows, Linux och macOS. Mer information om .NET Core finns i [.NET Core-dokumentationen.](https://dotnet.github.io/) Det här biblioteket fungerar även för traditionella .NET Framework för Windows.

Det här dokumentet visar hur du skapar ett .NET Core-konsolprogram som körs på Windows, Linux och macOS och utför följande scenarier:

- Ladda upp filer och kataloger till Blob Storage.
- Definiera antalet parallella åtgärder vid överföring av data.
- Spåra dataöverföringsförloppet.
- Återuppta avbruten dataöverföring.
- Kopiera filen från URL:en till Blob Storage.
- Kopiera från Blob Storage till Blob Storage.

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio Code](https://code.visualstudio.com/)
- Ett [Azure Storage-konto](storage-account-create.md)

## <a name="setup"></a>Konfiguration

1. Gå till [installationsguiden för .NET Core](https://dotnet.microsoft.com/download) för att installera .NET Core SDK. När du väljer din miljö väljer du kommandoradsalternativet.
2. Skapa en katalog för projektet från kommandoraden. Navigera till den här katalogen och skriv sedan `dotnet new console -o <sample-project-name>` för att skapa ett C#-konsolprojekt.
3. Öppna den här katalogen i Visual Studio Code. Det här steget kan snabbt utföras via kommandoraden genom att skriva `code .` i Windows.
4. Installera [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) från Visual Studio Code Marketplace. Starta Visual Studio kod.
5. Nu bör du se två uppmaningar. En är för att lägga till "nödvändiga tillgångar för att skapa och felsöka". Klicka på "Ja". En annan uppmaning är att återställa olösta beroenden. Klicka på "återställ".
6. Ändra `launch.json` under för att använda extern terminal som en `.vscode` konsol. Den här inställningen bör läsas som `"console": "externalTerminal"`
7. Visual Studio Code kan du felsöka .NET Core-program. Tryck `F5` på för att köra programmet och kontrollera att konfigurationen fungerar. Du bör se "Hello World!" skrivs ut till konsolen.

## <a name="add-the-data-movement-library-to-your-project"></a>Lägga till dataflyttningsbiblioteket i projektet

1. Lägg till den senaste versionen av dataförflyttningsbiblioteket `dependencies` i -avsnittet i `<project-name>.csproj` filen. När detta skrivs skulle den här versionen vara `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. En uppmaning bör visas för att återställa projektet. Klicka på knappen "återställ". Du kan också återställa projektet från kommandoraden genom att skriva `dotnet restore` kommandot i roten för projektkatalogen.

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

## <a name="set-up-the-skeleton-of-your-application"></a>Konfigurera stommen för ditt program

Det första vi gör är att konfigurera "skeleton"-koden för vårt program. Den här koden uppmanar oss att ange ett lagringskontonamn och en kontonyckel och använder dessa autentiseringsuppgifter för att skapa ett `CloudStorageAccount` -objekt. Det här objektet används för att interagera med vårt lagringskonto i alla överföringsscenarier. Koden uppmanar oss också att välja vilken typ av överföringsåtgärd vi vill köra.

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

## <a name="upload-a-local-file-to-a-blob"></a>Ladda upp en lokal fil till en blob

Lägg till metoderna `GetSourcePath` och `GetBlob` i `Program.cs` :

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

Den här koden uppmanar oss att ange sökvägen till en lokal fil, namnet på en ny eller befintlig container och namnet på en ny blob. Metoden `TransferManager.UploadAsync` utför uppladdningen med hjälp av den här informationen.

Tryck `F5` på för att köra programmet. Du kan kontrollera att uppladdningen har skett genom att visa ditt lagringskonto [med Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Ange antalet parallella åtgärder

En funktion som erbjuds av dataförflyttningsbiblioteket är möjligheten att ange antalet parallella åtgärder för att öka dataflödet för dataöverföring. Som standard anger dataförflyttningsbiblioteket antalet parallella åtgärder till 8 * antalet kärnor på datorn.

Tänk på att många parallella åtgärder i en miljö med låg bandbredd kan överbelasta nätverksanslutningen och faktiskt förhindra att åtgärder slutförs helt. Du måste experimentera med den här inställningen för att avgöra vad som fungerar bäst baserat på din tillgängliga nätverksbandbredd.

Nu ska vi lägga till kod som gör att vi kan ange antalet parallella åtgärder. Nu ska vi också lägga till kod som lägger tid på hur lång tid det tar för överföringen att slutföras.

Lägg till en `SetNumberOfParallelOperations` metod i `Program.cs` :

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Ändra metoden `ExecuteChoice` så att den använder `SetNumberOfParallelOperations` :

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

Ändra metoden `TransferLocalFileToAzureBlob` så att en timer används:

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

## <a name="track-transfer-progress"></a>Spåra överföringsförloppet

Att veta hur lång tid det tog för data att överföra är användbart. Men att kunna se förloppet för överföringen *under* överföringsåtgärden skulle vara ännu bättre. För att uppnå det här scenariot måste vi skapa ett `TransferContext` -objekt. -objektet `TransferContext` finns i två former: och `SingleTransferContext` `DirectoryTransferContext` . Den första är till för överföring av en enda fil och den senare används för att överföra en katalog med filer.

Lägg till metoderna `GetSingleTransferContext` och `GetDirectoryTransferContext` i `Program.cs` :

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

Ändra metoden `TransferLocalFileToAzureBlob` så att den använder `GetSingleTransferContext` :

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

En annan praktisk funktion som erbjuds av dataflyttningsbiblioteket är möjligheten att återuppta en avbruten överföring. Nu ska vi lägga till kod som gör att vi tillfälligt kan avbryta överföringen genom att skriva `c` och sedan återuppta överföringen tre sekunder senare.

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

Fram till nu har `checkpoint` vårt värde alltid angetts till `null` . Om vi avbryter överföringen hämtar vi den sista kontrollpunkten för överföringen och använder sedan den nya kontrollpunkten i överföringskontexten.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Överföra en lokal katalog till Blob Storage

Det skulle vara svårt om dataförflyttningsbiblioteket bara kunde överföra en fil i taget. Som tur är är detta inte fallet. Dataförflyttningsbiblioteket ger möjlighet att överföra en katalog med filer och alla dess underkataloger. Nu ska vi lägga till kod som gör att vi kan göra just det.

Lägg först till metoden `GetBlobDirectory` i `Program.cs` :

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

Det finns några skillnader mellan den här metoden och metoden för att ladda upp en enskild fil. Vi använder nu och `TransferManager.UploadDirectoryAsync` metoden som vi skapade `getDirectoryTransferContext` tidigare. Dessutom anger vi nu ett värde för uppladdningsåtgärden, vilket gör att vi kan ange att vi vill inkludera `options` underkataloger i uppladdningen.

## <a name="copy-a-file-from-url-to-a-blob"></a>Kopiera en fil från URL:en till en blob

Nu ska vi lägga till kod som gör att vi kan kopiera en fil från en URL till en Azure Blob.

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

Ett viktigt användningsfall för den här funktionen är när du behöver flytta data från en annan molntjänst (t.ex. AWS) till Azure. Så länge du har en URL som ger dig åtkomst till resursen kan du enkelt flytta resursen till Azure Blobs med hjälp av `TransferManager.CopyAsync` metoden . Den här metoden introducerar också en ny boolesk parameter. Om den här `true` parametern anges till anger vi att vi vill göra en asynkron kopia på serversidan. Om den här parametern anges till anger du en synkron kopia, vilket innebär att resursen först laddas ned till den lokala datorn `false` och sedan laddas upp till Azure Blob. Synkron kopia är dock för närvarande endast tillgänglig för kopiering från en Azure Storage resurs till en annan.

## <a name="copy-a-blob"></a>Kopiera en blob

En annan funktion som tillhandahålls unikt av dataflyttningsbiblioteket är möjligheten att kopiera från en Azure Storage resurs till en annan.

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

I det här exemplet anger vi den booleska parametern i till för att ange att vi `TransferManager.CopyAsync` `false` vill göra en synkron kopia. Det innebär att resursen först laddas ned till den lokala datorn och sedan laddas upp till Azure Blob. Alternativet för synkron kopiering är ett bra sätt att se till att kopieringsåtgärden har en konsekvent hastighet. Hastigheten för en asynkron kopia på serversidan är däremot beroende av serverns tillgängliga nätverksbandbredd, som kan variera. Synkron kopiering kan dock generera ytterligare utgående kostnader jämfört med asynkron kopia. Den rekommenderade metoden är att använda synkron kopiering på en virtuell Azure-dator som finns i samma region som ditt källlagringskonto för att undvika utgående kostnader.

Dataförflyttningsprogrammet är nu klart. [Det fullständiga kodexe exemplet finns på GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Nästa steg

[Azure Storage referensdokumentation för Data Movement-biblioteket](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
