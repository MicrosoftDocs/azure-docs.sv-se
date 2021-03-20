---
title: Spara utdata till Azure Storage med batch-tjänst-API
description: Lär dig hur du använder batch-tjänstens API för att spara batch-aktivitet och utskrifts data till Azure Storage.
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88936936"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Spara uppgifts data till Azure Storage med batch-tjänstens API

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Batch-tjänstens API har stöd för att spara utdata till Azure Storage för aktiviteter och jobb Manager-aktiviteter som körs på pooler med den virtuella dator konfigurationen. När du lägger till en aktivitet kan du ange en behållare i Azure Storage som mål för aktivitetens utdata. Batch-tjänsten skriver sedan alla utdata till behållaren när uppgiften har slutförts.

En fördel med att använda batch-tjänstens API för att spara Uppgiftsutdata är att du inte behöver ändra det program som aktiviteten körs på. I stället kan du spara aktivitetens utdata från samma kod som skapar uppgiften i stället för att få några ändringar i klient programmet.

> [!IMPORTANT]
> Att spara uppgifts data till Azure Storage med batch-tjänstens API fungerar inte med pooler som skapats före den [1 februari 2018](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204).

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>När ska jag använda batch-tjänstens API för att spara Uppgiftsutdata?

Azure Batch innehåller fler än ett sätt att spara Uppgiftsutdata. Att använda batch-tjänstens API är en praktisk metod som passar bäst för dessa scenarier:

- Du vill skriva kod för att spara Uppgiftsutdata från ditt klient program, utan att ändra det program som aktiviteten körs på.
- Du vill spara utdata från batch-uppgifter och jobb Manager-aktiviteter i pooler som skapats med den virtuella dator konfigurationen.
- Du vill spara utdata till en Azure Storage behållare med ett godtyckligt namn.
- Du vill spara utdata till en Azure Storage-behållare med namnet enligt [satserna i satsen för fil konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files).

Om ditt scenario skiljer sig från de som anges ovan kan du behöva överväga en annan metod. Batch-tjänstens API stöder till exempel inte strömmande utdata till Azure Storage medan aktiviteten körs. Om du vill strömma utdata kan du använda biblioteket med kommando fils konventioner, som är tillgängligt för .NET. För andra språk måste du implementera din egen lösning. Information om andra alternativ för att spara Uppgiftsutdata finns i [Spara jobb-och Uppgiftsutdata till Azure Storage](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Skapa en behållare i Azure Storage

Om du vill spara Uppgiftsutdata till Azure Storage måste du skapa en behållare som fungerar som mål för dina utdatafiler. Skapa behållaren innan du kör uppgiften, helst innan du skickar jobbet. Använd rätt Azure Storage klient bibliotek eller SDK för att skapa behållaren. Mer information om Azure Storage-API: er finns i [Azure Storage-dokumentationen](../storage/index.yml).

Om du till exempel skriver ditt program i C# använder du [Azure Storage klient biblioteket för .net](https://www.nuget.org/packages/WindowsAzure.Storage/). I följande exempel visas hur du skapar en behållare:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Hämta en signatur för delad åtkomst för behållaren

När du har skapat behållaren får du en signatur för delad åtkomst (SAS) med skriv åtkomst till behållaren. En SAS ger delegerad åtkomst till behållaren. SAS beviljar åtkomst med en angiven uppsättning behörigheter och under ett angivet tidsintervall. Batch-tjänsten behöver en SAS med Skriv behörighet för att skriva Uppgiftsutdata till behållaren. Mer information om SAS finns i [använda säkerhets associationer för delade Access-signaturer \( \) i Azure Storage](../storage/common/storage-sas-overview.md).

När du får en SAS med hjälp av Azure Storage API: er, returnerar API: et en SAS-token-sträng. Denna token-sträng innehåller alla parametrar för SAS, inklusive behörigheter och intervallet som SAS är giltig för. Om du vill använda SAS för att komma åt en behållare i Azure Storage måste du lägga till SAS-token-strängen i resurs-URI: n. Resurs-URI: n, tillsammans med den bifogade SAS-token, ger autentiserad åtkomst till Azure Storage.

I följande exempel visas hur du hämtar en skrivskyddad SAS-token-sträng för behållaren och sedan lägger till SAS i behållarens URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Ange utdatafiler för Uppgiftsutdata

Om du vill ange utdatafiler för en aktivitet skapar du en samling [indatafiler](/dotnet/api/microsoft.azure.batch.outputfile) och tilldelar den till egenskapen [CloudTask. OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) när du skapar uppgiften.

Följande C#-kod exempel skapar en uppgift som skriver slumpmässiga tal till en fil med namnet `output.txt` . I exemplet skapas en utdatafil som `output.txt` skrivs till behållaren. I exemplet skapas även utdatafiler för alla loggfiler som matchar fil mönstret `std*.txt` (_t. ex._ `stdout.txt` och `stderr.txt` ). Behållar-URL: en kräver den SAS som skapades tidigare för behållaren. Batch-tjänsten använder SAS för att autentisera åtkomsten till behållaren:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Om du använder det här exemplet med Linux ska du se till att ändra omvända snedstreck för att vidarebefordra snedstreck.

### <a name="specify-a-file-pattern-for-matching"></a>Ange ett fil mönster för matchning

När du anger en utdatafil kan du använda egenskapen [utfil. FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) för att ange ett fil mönster som ska matchas. Fil mönstret kan matcha inga filer, en enskild fil eller en uppsättning filer som skapas av uppgiften.

Egenskapen **FilePattern** stöder standardjokertecken i fil systemet, till exempel `*` (för icke-rekursiva matchningar) och `**` (för rekursiva matchningar). Kod exemplet ovan anger till exempel fil mönstret som `std*.txt` inte matchar rekursivt:

`filePattern: @"..\std*.txt"`

Om du vill ladda upp en enstaka fil anger du ett fil mönster utan jokertecken. Kod exemplet ovan anger till exempel fil mönstret som ska matchas `output.txt` :

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Ange ett överförings villkor

Egenskapen [OutputFileUploadOptions. UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tillåter villkorlig överföring av utdatafiler. Ett vanligt scenario är att ladda upp en uppsättning filer om aktiviteten lyckas och en annan uppsättning filer om den Miss lyckas. Du kanske till exempel bara vill överföra utförliga loggfiler när aktiviteten Miss lyckas och avslutas med en slutkod som inte är noll. På samma sätt kanske du bara vill överföra resultat filer om aktiviteten lyckas, eftersom filerna kan saknas eller vara ofullständiga om aktiviteten Miss lyckas.

I kod exemplet ovan anges egenskapen **UploadCondition** till **TaskCompletion**. Den här inställningen anger att filen ska överföras när aktiviteterna har slutförts, oavsett värdet för slut koden.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Andra inställningar finns i uppräkningen [OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) .

### <a name="disambiguate-files-with-the-same-name"></a>Disambiguate-filer med samma namn

Aktiviteterna i ett jobb kan producera filer som har samma namn. Till exempel `stdout.txt` skapas och `stderr.txt` skapas för varje aktivitet som körs i ett jobb. Eftersom varje aktivitet körs i ett eget sammanhang är dessa filer inte motstridiga i nodens fil system. När du laddar upp filer från flera aktiviteter till en delad behållare måste du dock disambiguate filer med samma namn.

Egenskapen [OutputFileBlobContainerDestination. Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) anger mål-BLOB eller virtuell katalog för utdatafiler. Du kan använda egenskapen **sökväg** för att namnge bloben eller den virtuella katalogen på ett sådant sätt att utdatafiler med samma namn unikt namnges i Azure Storage. Att använda aktivitets-ID i sökvägen är ett bra sätt att säkerställa unika namn och enkelt identifiera filer.

Om egenskapen **FilePattern** har angetts till ett jokertecken, överförs alla filer som matchar mönstret till den virtuella katalogen som anges av egenskapen **Path** . Om behållaren till exempel är `mycontainer` , aktivitets-ID: t `mytask` och fil mönstret är `..\std*.txt` , kommer de absoluta URI: erna till utdatafilerna i Azure Storage att likna följande:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Om egenskapen **FilePattern** är inställd på att matcha ett enda fil namn, vilket innebär att den inte innehåller jokertecken, anger värdet för egenskapen **Path** det fullständigt kvalificerade BLOB-namnet. Om du förväntar dig namn konflikter med en enda fil från flera aktiviteter tar du med namnet på den virtuella katalogen som en del av fil namnet för att disambiguate filerna. Ange till exempel egenskapen **Path** att inkludera aktivitets-ID, avgränsnings tecken (vanligt vis ett snedstreck) och fil namnet:

`path: taskId + @"/output.txt"`

De absoluta URI: erna till utdatafilerna för en uppsättning aktiviteter ser ut ungefär så här:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Mer information om virtuella kataloger i Azure Storage finns i [lista blobarna i en behållare](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnostisera fil överförings fel

Om det inte går att ladda upp utdatafiler till Azure Storage, flyttas aktiviteten till det **slutförda** läget och egenskapen [TaskExecutionInformation. FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) har angetts. Granska egenskapen **FailureInformation** för att avgöra vilket fel som har inträffat. Här är till exempel ett fel som uppstår vid fil uppladdning om behållaren inte kan hittas:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Vid varje fil uppladdning skriver batch två loggfiler till Compute-noden `fileuploadout.txt` och `fileuploaderr.txt` . Du kan kontrol lera loggfilerna för att lära dig mer om ett speciellt haveri. I de fall där fil överföringen aldrig har gjorts, till exempel eftersom själva uppgiften inte kunde köras, finns inte dessa loggfiler.

## <a name="diagnose-file-upload-performance"></a>Diagnostisera prestanda för fil uppladdning

`fileuploadout.txt`Hämtnings förloppet för fil loggar. Du kan granska den här filen om du vill veta mer om hur lång tid det tar för dina fil överföringar. Tänk på att det finns många bidrags faktorer för att ladda upp prestanda, inklusive storleken på noden, annan aktivitet på noden vid överförings tillfället, om mål behållaren finns i samma region som batch-poolen, hur många noder som ska överföras till lagrings kontot samtidigt, och så vidare.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Använd batch-tjänstens API med standard satserna för sats fil konvention

När du sparar Uppgiftsutdata med batch-tjänstens API kan du namnge din mål behållare och blobar som du vill. Du kan också välja att namnge dem enligt [satserna i satsen för fil konventioner som standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files). Standard för fil konventioner bestämmer namnen på mål behållaren och blobben i Azure Storage för en utdatafil baserat på namnet på jobbet och uppgiften. Om du använder fil konventions standarden för namngivning av utdatafiler är utdatafilerna tillgängliga för visning i [Azure Portal](https://portal.azure.com).

Om du utvecklar i C# kan du använda de metoder som är inbyggda i [biblioteket med kommando fils konventioner för .net](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Det här biblioteket skapar de korrekt namngivna behållare och blob-sökvägar åt dig. Du kan till exempel anropa API: et för att få rätt namn för behållaren, baserat på jobbets namn:

```csharp
string containerName = job.OutputStorageContainerName();
```

Du kan använda metoden [CloudJobExtensions. GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) för att returnera en URL för signaturer för delad åtkomst (SAS) som används för att skriva till behållaren. Du kan sedan skicka denna SAS till [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) -konstruktorn.

Om du utvecklar på ett annat språk än C# måste du implementera filen Conventions standard.

## <a name="code-sample"></a>Kodexempel

[PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) -exempelprojektet är ett av [Azure Batch kod exempel](https://github.com/Azure/azure-batch-samples) på GitHub. Den här Visual Studio-lösningen visar hur du använder batch-klientens bibliotek för .NET för att spara Uppgiftsutdata till varaktig lagring. Följ dessa steg om du vill köra exemplet:

1. Öppna projektet i **Visual Studio 2019**.
2. Lägg till dina autentiseringsuppgifter för batch-och lagrings **konto** i **AccountSettings. settings** i Microsoft.Azure.BatCH. Samples. common Project.
3. **Skapa** (men kör inte) lösningen. Återställ eventuella NuGet-paket om du uppmanas att göra det.
4. Använd Azure Portal för att ladda upp ett [programpaket](batch-application-packages.md) för **PersistOutputsTask**. Ta med `PersistOutputsTask.exe` och dess beroende sammansättningar i. zip-paketet, ange program-ID till "PersistOutputsTask" och programpaket versionen till "1,0".
5. **Starta** (kör) **PersistOutputs** -projektet.
6. När du uppmanas att välja den beständiga teknik som ska användas för att köra exemplet anger du **2** för att köra exemplet med batch-tjänstens API för att spara Uppgiftsutdata.
7. Om du vill kan du köra exemplet igen, ange **3** för att spara utdata med batch-tjänst-API: et och även namnge mål behållaren och blob-sökvägen enligt fil konventions standarden.

## <a name="next-steps"></a>Nästa steg

- Mer information om att spara Uppgiftsutdata med fil konventions biblioteket för .NET finns i [Spara jobb-och uppgifts data till Azure Storage med bibliotek för batch-Filkonventioner för .net](batch-task-output-file-conventions.md).
- Information om andra metoder för att bevara utdata i Azure Batch finns i [Spara jobb-och Uppgiftsutdata för att Azure Storage](batch-task-output.md).
