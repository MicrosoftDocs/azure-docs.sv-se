---
title: Distribuera programpaket till Compute-noder
description: Använd funktionen programpaket i Azure Batch för att enkelt hantera flera program och versioner för installation på batch Compute-noder.
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: b4c6ab99b0fa93e5fdea4256d6f232cc0fe4218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033739"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program till Compute-noder med batch-programpaket

Programpaket kan förenkla koden i din Azure Batch-lösning och göra det enklare att hantera de program som dina aktiviteter kör. Med programpaket kan du ladda upp och hantera flera versioner av programmen som dina aktiviteter kör, inklusive deras stödfiler. Du kan sedan automatiskt distribuera ett eller flera av dessa program till Compute-noderna i poolen.

API: erna för att skapa och hantera programpaket ingår i [batch Management .net](/dotnet/api/overview/azure/batch/management) -biblioteket. API: erna för att installera programpaket på en Compute-nod är en del av [batch .net](/dotnet/api/overview/azure/batch/client) -biblioteket. Jämförbara funktioner finns i de tillgängliga batch-API: erna för andra språk.

Den här artikeln förklarar hur du överför och hanterar programpaket i Azure Portal. Det visar också hur du installerar dem i en Pools datornoder med [batch .net](/dotnet/api/overview/azure/batch/client) -biblioteket.

## <a name="application-package-requirements"></a>Krav för applikations paket

Om du vill använda programpaket måste du [Länka ett Azure Storage-konto](#link-a-storage-account) till ditt batch-konto.

Det finns begränsningar för antalet program och programpaket i ett batch-konto och den maximala storleken för programpaket. Mer information finns i [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).

> [!NOTE]
> Batch-pooler som skapats före den 5 juli 2017 har inte stöd för programpaket (såvida de inte skapats efter den 10 mars 2016 med Cloud Services konfiguration). Funktionen programpaket som beskrivs här ersätter funktionen batch apps som finns i tidigare versioner av tjänsten.

## <a name="understand-applications-and-application-packages"></a>Förstå program och programpaket

I Azure Batch refererar ett *program* till en uppsättning versioner av binärfiler som kan hämtas automatiskt till Compute-noderna i poolen. Ett program innehåller ett eller flera *programpaket* som representerar olika versioner av programmet.

Varje *programpaket* är en. zip-fil som innehåller programmets binärfiler och eventuella stödfiler. Endast zip-formatet stöds.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram som visar en övergripande vy över program och programpaket.":::

Du kan ange programpaket på pool-eller aktivitets nivå.

- **Programpaket för pooler** distribueras till varje nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller avbildningas om.
  
    Poolens programpaket är lämpliga när alla noder i en pool ska köra ett jobbs aktiviteter. Du kan ange ett eller flera programpaket som ska distribueras när du skapar en pool. Du kan också lägga till eller uppdatera en befintlig Pools paket. Om du vill installera ett nytt paket i en befintlig pool måste du starta om noderna.

- **Programpaket för uppgifter** distribueras endast till en Compute-nod som har schemalagts för att köra en uppgift, precis innan aktivitetens kommando rad körs. Om det angivna programpaketet och versionen redan finns på noden omdistribueras det inte och det befintliga paketet används.
  
    Uppgifts program paket är användbara i miljöer med delade pooler, där olika jobb körs på en pool och poolen inte tas bort när ett jobb slutförs. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera data överföringen eftersom ditt program bara distribueras till noderna som kör aktiviteter.
  
    Andra scenarier som kan dra nytta av uppgifts programmets paket är jobb som kör ett stort program, men endast för några få uppgifter. Till exempel kan uppgifts program vara användbara för ett Heavyweight för bearbetnings steg eller en sammanslagnings uppgift.

Med programpaket behöver inte poolens start uppgift ange en lång lista med enskilda resursfiler som ska installeras på noderna. Du behöver inte manuellt hantera flera versioner av dina programfiler i Azure Storage eller på noderna. Och du behöver inte oroa dig för att skapa [SAS-URL: er](../storage/common/storage-sas-overview.md) för att ge åtkomst till filerna i ditt lagrings konto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem till Compute-noder.

> [!NOTE]
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om start aktiviteten överskrider den här gränsen, är det ett annat alternativ att använda programpaket. Du kan också skapa en. zip-fil som innehåller dina resursfiler, ladda upp den som en blob till Azure Storage och sedan zippa upp den från kommando raden för start aktiviteten.

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program

Du kan använda [Azure Portal](https://portal.azure.com) eller API: er för batch Management för att hantera programpaketen i batch-kontot. I följande avsnitt beskrivs hur du länkar ett lagrings konto och hur du lägger till och hanterar program och programpaket i Azure Portal.

### <a name="link-a-storage-account"></a>Länka ett lagrings konto

Om du vill använda programpaket måste du länka ett [Azure Storage-konto](accounts.md#azure-storage-accounts) till ditt batch-konto. Batch-tjänsten använder det associerade lagrings kontot för att lagra dina programpaket. Vi rekommenderar att du skapar ett lagrings konto som är specifikt för användning med ditt batch-konto.

Om du ännu inte har konfigurerat ett lagrings konto visas en varning i Azure Portal första gången du väljer **program** i batch-kontot. Om du vill länka ett lagrings konto till ditt batch-konto väljer du **lagrings konto** i **varnings** fönstret och väljer sedan **lagrings konto** igen.

När du har länkat de två kontona kan batch automatiskt distribuera paketen som lagras i det länkade lagrings kontot till dina Compute-noder.

> [!IMPORTANT]
> Du kan inte använda programpaket med Azure Storage konton som kon figurer ATS med [brand Väggs regler](../storage/common/storage-network-security.md)eller med **hierarkiskt namn område** inställt på **aktiverat**

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som block-blobbar. Du [debiteras som normal](https://azure.microsoft.com/pricing/details/storage/) för block-BLOB-data och storleken på varje paket får inte överskrida den största blockets BLOB-storlek. Mer information finns i [Azure Storage skalbarhets-och prestanda mål för lagrings konton](../storage/blobs/scalability-targets.md). För att minimera kostnaderna måste du ta hänsyn till storleken på och antalet programpaket och regelbundet ta bort inaktuella paket.

### <a name="view-current-applications"></a>Visa aktuella program

Om du vill visa programmen i batch-kontot väljer du **program** i den vänstra navigerings menyn.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Skärm bild av meny alternativet program i Azure Portal.":::

När du väljer det här meny alternativet öppnas fönstret **program** . I det här fönstret visas ID: t för varje program i ditt konto och följande egenskaper:

- **Paket**: antalet versioner som är associerade med det här programmet.
- **Standard version**: om det är tillämpligt den program version som ska installeras om ingen version anges när programmet distribueras.
- **Tillåt uppdateringar**: anger om paket uppdateringar och borttagningar tillåts.

Om du vill se [fil strukturen](files-and-directories.md) för programpaketet på en Compute-nod går du till batch-kontot i Azure Portal. Välj **pooler**. Välj sedan den pool som innehåller Compute-noden. Välj den Compute-nod där programpaketet är installerat och öppna mappen **program** .

### <a name="view-application-details"></a>Visa programinformation

Om du vill se information om ett program väljer du det i fönstret **program** . Du kan konfigurera följande inställningar för ditt program.

- **Tillåt uppdateringar**: anger om programpaket kan [uppdateras eller tas bort](#update-or-delete-an-application-package). Standardvärdet är **Ja**. Om det är inställt på **Nej** kan befintliga programpaket inte uppdateras eller tas bort, men nya programpaket versioner kan fortfarande läggas till.
- **Standard version**: det standard program paket som ska användas när programmet distribueras, om ingen version har angetts.
- **Visnings namn**: ett eget namn som din batch-lösning kan använda när den visar information om programmet. Namnet kan till exempel användas i användar gränssnittet för en tjänst som du tillhandahåller till dina kunder via batch.

### <a name="add-a-new-application"></a>Lägg till ett nytt program

Om du vill skapa ett nytt program lägger du till ett programpaket och anger ett unikt program-ID.

Välj **program** i batch-kontot och välj sedan **Lägg till**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Skärm bild av den nya processen för att skapa program i Azure Portal.":::

Ange följande information:

- **Program-ID**: ID för ditt nya program.
- **Version**: versionen för det programpaket som du överför.
- **Programpaket**:. zip-filen som innehåller programmets binärfiler och stödfiler som krävs för att köra programmet.

Det **program-ID** och den **version** som du anger måste följa dessa krav:

- På Windows-noder kan ID: t innehålla en kombination av alfanumeriska tecken, bindestreck och under streck. På Linux-noder är endast alfanumeriska tecken och under streck tillåtna.
- Får innehålla högst 64 tecken.
- Måste vara unikt inom batch-kontot.
- ID: n är Skift läges känsliga och Skift läges okänsliga.

När du är klar väljer du **Skicka**. När zip-filen har överförts till ditt Azure Storage-konto visas ett meddelande i portalen. Detta kan ta lite tid beroende på storleken på den fil som du överför och nätverks anslutningens hastighet.

### <a name="add-a-new-application-package"></a>Lägg till ett nytt programpaket

Om du vill lägga till en programpaket version för ett befintligt program väljer du programmet i avsnittet **program** i batch-kontot och väljer sedan **Lägg till**.

Som du gjorde för det nya programmet anger du **versionen** för det nya paketet, laddar upp. zip-filen i fältet **programpaket** och väljer sedan **Skicka**.

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket

Om du vill uppdatera eller ta bort ett befintligt programpaket väljer du programmet i avsnittet **program** i batch-kontot. Välj ellipsen på raden i det programpaket som du vill ändra och välj sedan den åtgärd som du vill utföra.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Skärm bild som visar uppdaterings-och borttagnings alternativen för programpaket i Azure Portal.":::

Om du väljer **Uppdatera** kan du ladda upp en ny. zip-fil. Då ersätts den tidigare. zip-filen som du laddade upp för den versionen.

Om du väljer **ta bort** uppmanas du att bekräfta borttagningen av den versionen. När du har valt **OK** tar batch bort. zip-filen från ditt Azure Storage-konto. Om du tar bort standard versionen av ett program tas **standard versions** inställningen bort för programmet.

## <a name="install-applications-on-compute-nodes"></a>Installera program på Compute-noder

Nu när du har lärt dig hur du hanterar programpaket i Azure Portal kan vi diskutera hur du distribuerar dem för att beräkna noder och köra dem med batch-uppgifter.

### <a name="install-pool-application-packages"></a>Installera poolens programpaket

Om du vill installera ett programpaket på alla Compute-noder i en pool anger du ett eller flera Programpakets referenser för poolen. De programpaket som du anger för en pool installeras på varje Compute-nod som ansluter till poolen och på alla noder som startas om eller avbildningar.

I batch .NET anger du en eller flera [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) när du skapar en ny pool eller för en befintlig pool. [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) -klassen anger ett program-ID och en version som ska installeras i en Pools datornoder.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Om distributionen av ett programpaket Miss lyckas, markerar batch-tjänsten noden som [oanvändbar](/dotnet/api/microsoft.azure.batch.computenode.state)och inga aktiviteter är schemalagda för körning på den noden. Om detta händer startar du om noden för att starta om paket distributionen. När du startar om noden aktive ras även schemaläggning igen på noden.

### <a name="install-task-application-packages"></a>Installera programpaket för uppgift

På samma sätt som en pool anger du programpaket-referenser för en aktivitet. När en aktivitet har schemalagts för körning på en nod laddas paketet ned och extraheras precis innan aktivitetens kommando rad körs. Om ett angivet paket och version redan är installerat på noden laddas inte paketet ned och det befintliga paketet används.

För att installera ett program paket för uppgift konfigurerar du egenskapen [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Kör de installerade programmen

De paket som du har angett för en pool eller aktivitet laddas ned och extraheras till en namngiven katalog i `AZ_BATCH_ROOT_DIR` noden. Batch skapar också en miljö variabel som innehåller sökvägen till den namngivna katalogen. Dina aktivitets kommando rader använder den här miljövariabeln för att referera till programmet på noden.

På Windows-noder har variabeln följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

På Linux-noder är formatet något annorlunda. Punkter (.), bindestreck (-) och nummer tecken (#) förenklas till under streck i miljö variabeln. Observera också att fallet med program-ID: t bevaras. Exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` och `version` är värden som motsvarar den program-och paket version som du har angett för distribution. Om du till exempel har angett att version 2,7 av program *blandning* ska installeras på Windows-noder, använder dina aktivitets kommando rader denna miljö variabel för att komma åt dess filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

På Linux-noder anger du miljövariabeln i det här formatet. Förenkla perioderna (.), bindestreck (-) och nummer tecken (#) till under streck och behåll fallet med program-ID: t:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

När du laddar upp ett programpaket kan du ange en standard version som ska distribueras till dina Compute-noder. Om du har angett en standard version för ett program kan du utelämna versionens suffix när du refererar till programmet. Du kan ange standard program versionen i Azure Portal i fönstret **program** , som visas i [överför och hantera program](#upload-and-manage-applications).

Om du till exempel ställer in "2,7" som standard version för program *blandning*, och dina uppgifter refererar till följande miljö variabel, körs Windows-noderna version 2,7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodfragment visar ett exempel på en uppgift kommando rad som startar standard versionen av *över gångs* programmet:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Mer information om inställningar för Compute Node-miljön finns i [miljö inställningar för aktiviteter](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool

Om en befintlig pool redan har kon figurer ATS med ett programpaket kan du ange ett nytt paket för poolen. Det innebär att du måste:

- Batch-tjänsten installerar det nyligen angivna paketet på alla nya noder som ansluter till poolen och på alla befintliga noder som startas om eller avbildningar.
- Compute-noder som redan finns i poolen när du uppdaterar paket referenserna installerar inte automatiskt det nya programpaketet. De här Compute-noderna måste startas om eller avbildningas om för att det nya paketet ska kunna tas emot.
- När ett nytt paket distribueras återspeglar de skapade miljövariablerna de nya programpaket referenserna.

I det här exemplet har den befintliga poolen version 2,7 av *över gångs* programmet som kon figurer ATS som en av dess [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Om du vill uppdatera poolens noder med version 2.76 b anger du en ny [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) med den nya versionen och genomför ändringen.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nu när den nya versionen har kon figurer ATS installerar batch-tjänsten version 2.76 b till en ny nod som ansluter till poolen. Om du vill installera 2.76 b på noderna som redan finns i poolen startar du om eller återavbildningar dem. Observera att omstartade noder behåller filer från tidigare paket distributioner.

## <a name="list-the-applications-in-a-batch-account"></a>Visa en lista över programmen i ett batch-konto

Du kan visa en lista över program och deras paket i ett batch-konto med hjälp av metoden [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) .

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Batch-REST API](/rest/api/batchservice) tillhandahåller också stöd för att arbeta med programpaket. Se till exempel elementet [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) för hur du anger paket som ska installeras och [program](/rest/api/batchservice/application) för att hämta programinformation.
- Lär dig hur du program mässigt [hanterar Azure Batch konton och kvoter med batch Management .net](batch-management-dotnet.md). Med [batch Management .net](/dotnet/api/overview/azure/batch/management) -biblioteket kan du aktivera funktioner för att skapa och ta bort konton för ditt batch-program eller-tjänst.
