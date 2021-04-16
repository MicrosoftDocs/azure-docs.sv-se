---
title: Distribuera programpaket till beräkningsnoder
description: Använd funktionen för programpaket i Azure Batch enkelt hantera flera program och versioner för installation på Batch-beräkningsnoder.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 9c4b40f0e99475fc0b19ec94a14f67af131e5f59
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389391"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Distribuera program till beräkningsnoder med Batch-programpaket

Programpaket kan förenkla koden i din Azure Batch och göra det enklare att hantera de program som dina aktiviteter kör. Med programpaket kan du ladda upp och hantera flera versioner av program som dina aktiviteter kör, inklusive deras stödfiler. Du kan sedan automatiskt distribuera ett eller flera av dessa program till beräkningsnoderna i din pool.

API:erna för att skapa och hantera programpaket ingår i [Batch Management .NET-biblioteket.](/dotnet/api/overview/azure/batch/management) API:erna för att installera programpaket på en beräkningsnod ingår i [Batch .NET-biblioteket.](/dotnet/api/overview/azure/batch/client) Jämförbara funktioner finns i tillgängliga Batch-API:er för andra språk.

Den här artikeln förklarar hur du laddar upp och hanterar programpaket i Azure Portal. Den visar också hur du installerar dem på en pools beräkningsnoder med [Batch .NET-biblioteket.](/dotnet/api/overview/azure/batch/client)

## <a name="application-package-requirements"></a>Krav för programpaket

Om du vill använda programpaket måste du [länka ett Azure Storage till](#link-a-storage-account) ditt Batch-konto.

Det finns begränsningar för antalet program och programpaket i ett Batch-konto och den maximala programpaketstorleken. Mer information finns i [Kvoter och begränsningar för Azure Batch tjänsten](batch-quota-limit.md).

> [!NOTE]
> Batch-pooler som skapats före 5 juli 2017 stöder inte programpaket (såvida de inte skapades efter den 10 mars 2016 med Cloud Services Configuration). Funktionen programpaket som beskrivs här ersätter Batch Apps-funktionen som är tillgänglig i tidigare versioner av tjänsten.

## <a name="understand-applications-and-application-packages"></a>Förstå program och programpaket

Inom Azure Batch *refererar ett* program till en uppsättning binärfiler av version som kan laddas ned automatiskt till beräkningsnoderna i poolen. Ett program innehåller ett eller flera *programpaket*, som representerar olika versioner av programmet.

Varje *programpaket* är en .zip-fil som innehåller programmets binärfiler och eventuella stödfiler. Endast ZIP-formatet stöds.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram som visar en översikt över program och programpaket.":::

Du kan ange programpaket på pool- eller uppgiftsnivå.

- **Poolprogrampaket** distribueras till varje nod i poolen. Program distribueras när en nod ansluter till en pool och när den startas om eller återställs.
  
    Poolprogrampaket är lämpliga när alla noder i en pool ska köra ett jobbs aktiviteter. Du kan ange ett eller flera programpaket som ska distribueras när du skapar en pool. Du kan också lägga till eller uppdatera en befintlig pools paket. Om du vill installera ett nytt paket i en befintlig pool måste du starta om dess noder.

- **Programpaket för** aktiviteter distribueras endast till en beräkningsnod som schemalagts för att köra en aktivitet, precis innan aktivitetens kommandorad körs. Om det angivna programpaketet och versionen redan finns på noden omdistribueras det inte och det befintliga paketet används.
  
    Uppgiftsprogrampaket är användbara i miljöer med delad pool, där olika jobb körs på en pool och poolen inte tas bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen, eftersom programmet endast distribueras till de noder som kör aktiviteter.
  
    Andra scenarier som kan dra nytta av uppgiftsprogrampaket är jobb som kör ett stort program, men endast för ett fåtal uppgifter. Uppgiftsprogram kan till exempel vara användbara för ett tungt förbearbetningssteg eller en sammanslagningsaktivitet.

Med programpaket behöver inte poolens startaktivitet ange en lång lista över enskilda resursfiler som ska installeras på noderna. Du behöver inte hantera flera versioner av dina programfiler manuellt i Azure Storage eller på noderna. Och du behöver inte bekymra dig om att generera [SAS-URL:er för](../storage/common/storage-sas-overview.md) att ge åtkomst till filerna i ditt lagringskonto. Batch fungerar i bakgrunden med Azure Storage för att lagra programpaket och distribuera dem till beräkningsnoder.

> [!NOTE]
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Om startaktiviteten överskrider den här gränsen är användning av programpaket ett annat alternativ. Du kan också skapa en ZIP-fil som innehåller dina resursfiler, ladda upp den som en blob till Azure Storage och sedan packa upp den från kommandoraden för startaktiviteten.

## <a name="upload-and-manage-applications"></a>Ladda upp och hantera program

Du kan [](https://portal.azure.com) använda Azure Portal batchhanterings-API:er för att hantera programpaketen i Batch-kontot. I följande avsnitt beskrivs hur du länkar ett lagringskonto och hur du lägger till och hanterar program och programpaket i Azure Portal.

> [!NOTE]
> Du kan definiera programvärden i [ resursenMicrosoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) för en [ARM-mall,](quick-create-template.md)men det går för närvarande inte att använda en ARM-mall för att ladda upp programpaket som ska användas i Batch-kontot. Du måste ladda upp dem till ditt länkade lagringskonto enligt [beskrivningen nedan.](#add-a-new-application)

### <a name="link-a-storage-account"></a>Länka ett lagringskonto

Om du vill använda programpaket måste du länka ett [Azure Storage till](accounts.md#azure-storage-accounts) ditt Batch-konto. Batch-tjänsten använder det associerade lagringskontot för att lagra dina programpaket. Vi rekommenderar att du skapar ett lagringskonto som är specifikt för användning med ditt Batch-konto.

Om du ännu inte har konfigurerat ett lagringskonto visas Azure Portal varning första gången du väljer **Program** i Batch-kontot. Om du vill länka ett lagringskonto till batchkontot väljer **du Lagringskonto** **i** varningsfönstret och väljer sedan **Lagringskonto** igen.

När du har länkat de två kontona kan Batch automatiskt distribuera paketen som lagras i det länkade Storage-kontot till dina beräkningsnoder.

> [!IMPORTANT]
> Du kan inte använda programpaket med Azure Storage-konton som konfigurerats med brandväggsregler [eller](../storage/common/storage-network-security.md)med **Hierarkisk namnrymd** inställt på **Aktiverad.**

Batch-tjänsten använder Azure Storage för att lagra dina programpaket som blockblobar. Du [debiteras som vanligt för](https://azure.microsoft.com/pricing/details/storage/) blockblobdata och storleken på varje paket kan inte överskrida den maximala blockblobstorleken. Mer information finns i Azure Storage [skalbarhets- och prestandamål för lagringskonton.](../storage/blobs/scalability-targets.md) För att minimera kostnaderna bör du tänka på storleken och antalet programpaket och regelbundet ta bort inaktuella paket.

### <a name="view-current-applications"></a>Visa aktuella program

Om du vill visa programmen i Batch-kontot väljer **du Program** i den vänstra navigeringsmenyn.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Skärmbild av menyalternativet Program i Azure Portal.":::

Om du väljer det här menyalternativet **öppnas fönstret** Program. I det här fönstret visas ID:t för varje program i ditt konto och följande egenskaper:

- **Paket:** Antalet versioner som är associerade med det här programmet.
- **Standardversion:** Om tillämpligt, den programversion som ska installeras om ingen version anges när programmet distribueras.
- **Tillåt uppdateringar:** Anger om paketuppdateringar och borttagningar tillåts.

Om du vill [se filstrukturen](files-and-directories.md) för programpaketet på en beräkningsnod går du till Batch-kontot i Azure Portal. Välj **Pooler.** Välj sedan den pool som innehåller beräkningsnoden. Välj den beräkningsnod där programpaketet är installerat och öppna **mappen applications.**

### <a name="view-application-details"></a>Visa programinformation

Om du vill se information om ett program väljer du det i **fönstret** Program. Du kan konfigurera följande inställningar för ditt program.

- **Tillåt uppdateringar:** Anger om programpaket kan [uppdateras eller tas bort.](#update-or-delete-an-application-package) Standardvärdet är **Ja.** Om det är **inställt** på Nej kan befintliga programpaket inte uppdateras eller tas bort, men nya programpaketversioner kan fortfarande läggas till.
- **Standardversion:** Standardprogrampaketet som ska användas när programmet distribueras, om ingen version har angetts.
- **Visningsnamn:** Ett eget namn som Batch-lösningen kan använda när den visar information om programmet. Det här namnet kan till exempel användas i användargränssnittet för en tjänst som du tillhandahåller till dina kunder via Batch.

### <a name="add-a-new-application"></a>Lägga till ett nytt program

Om du vill skapa ett nytt program lägger du till ett programpaket och anger ett unikt program-ID.

I Batch-kontot väljer du **Program och** sedan Lägg **till.**

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Skärmbild av processen för att skapa ett nytt program i Azure Portal.":::

Ange följande information:

- **Program-ID:** ID:t för det nya programmet.
- **Version**": Versionen för det programpaket som du laddar upp.
- **Programpaket:** ZIP-filen som innehåller programmets binärfiler och stödfiler som krävs för att köra programmet.

**Program-ID och** **version som** du anger måste följa dessa krav:

- På Windows-noder kan ID:t innehålla valfri kombination av alfanumeriska tecken, bindestreck och understreck. På Linux-noder tillåts endast alfanumeriska tecken och understreck.
- Får inte innehålla fler än 64 tecken.
- Måste vara unikt i Batch-kontot.
- ID:n är fallbevarande och icke-casekänsliga.

När du är klar väljer du **Skicka**. När ZIP-filen har laddats upp till ditt Azure Storage-konto visar portalen ett meddelande. Beroende på storleken på filen som du laddar upp och hastigheten på nätverksanslutningen kan det ta lite tid.

### <a name="add-a-new-application-package"></a>Lägga till ett nytt programpaket

Om du vill lägga till en programpaketversion för ett befintligt program väljer du programmet i **avsnittet Program** i Batch-kontot och väljer sedan Lägg **till**.

Precis som för det nya programmet anger du **Version** för det nya paketet, laddar upp ZIP-filen i fältet **Programpaket** och väljer sedan **Skicka.**

### <a name="update-or-delete-an-application-package"></a>Uppdatera eller ta bort ett programpaket

Om du vill uppdatera eller ta bort ett befintligt programpaket väljer du programmet **i avsnittet Program** i Batch-kontot. Välj ellipsen på raden i det programpaket som du vill ändra och välj sedan den åtgärd som du vill utföra.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Skärmbild som visar alternativen för att uppdatera och ta bort programpaket i Azure Portal.":::

Om du **väljer** Uppdatera kan du ladda upp en ny ZIP-fil. Detta ersätter den tidigare ZIP-filen som du laddade upp för den versionen.

Om du **väljer** Ta bort uppmanas du att bekräfta borttagningen av den versionen. När du har **valt OK** tar Batch bort ZIP-filen från ditt Azure Storage konto. Om du tar bort standardversionen av ett program tas **inställningen Standardversion** bort för programmet.

## <a name="install-applications-on-compute-nodes"></a>Installera program på beräkningsnoder

Nu när du har lärt dig hur du hanterar programpaket i Azure Portal kan vi diskutera hur du distribuerar dem till beräkningsnoder och kör dem med Batch-uppgifter.

### <a name="install-pool-application-packages"></a>Installera programpaket för pooler

Om du vill installera ett programpaket på alla beräkningsnoder i en pool anger du en eller flera programpaketreferenser för poolen. De programpaket som du anger för en pool installeras på varje beräkningsnod som ansluter till poolen och på alla noder som startas om eller återställs.

I Batch .NET anger du en eller flera [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) när du skapar en ny pool eller för en befintlig pool. Klassen [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) anger ett program-ID och en version som ska installeras på en pools beräkningsnoder.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

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
> Om distributionen av ett programpaket misslyckas markerar Batch-tjänsten noden [oanvändbar](/dotnet/api/microsoft.azure.batch.computenode.state)och inga aktiviteter schemaläggs för körning på noden. Om detta inträffar startar du om noden för att starta om paketdistributionen. När noden startas om aktiveras även schemaläggning på noden igen.

### <a name="install-task-application-packages"></a>Installera programpaket för aktiviteter

På samma sätt som med en pool anger du programpaketreferenser för en aktivitet. När en aktivitet schemaläggs att köras på en nod laddas paketet ned och extraheras precis innan aktivitetens kommandorad körs. Om ett angivet paket och en viss version redan är installerat på noden laddas inte paketet ned och det befintliga paketet används.

Om du vill installera ett programpaket för uppgiften konfigurerar du aktivitetens [egenskap CloudTask.ApplicationPackageReferences:](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences)

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

## <a name="execute-the-installed-applications"></a>Köra de installerade programmen

De paket som du har angett för en pool eller uppgift laddas ned och extraheras till en namngiven katalog i `AZ_BATCH_ROOT_DIR` noden. Batch skapar också en miljövariabel som innehåller sökvägen till den namngivna katalogen. Dina aktivitetskommandorader använder den här miljövariabeln när programmet refereras till på noden.

På Windows-noder har variabeln följande format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

På Linux-noder skiljer sig formatet något åt. Punkter (.), bindestreck (-) och nummertecken (#) plattas ut till understreck i miljövariabeln. Observera också att program-ID:t bevaras. Exempel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` och `version` är värden som motsvarar den program- och paketversion som du har angett för distributionen. Om du till exempel har angett att version 2.7 av program *blender* ska installeras på Windows-noder använder aktivitetskommandoraderna den här miljövariabeln för att komma åt dess filer:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

På Linux-noder anger du miljövariabeln i det här formatet. Platta ut punkter (.), bindestreck (-) och nummertecken (#) till understreck och bevara fallet med program-ID:t:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

När du laddar upp ett programpaket kan du ange en standardversion som ska distribueras till dina beräkningsnoder. Om du har angett en standardversion för ett program kan du utelämna versionssuffixet när du refererar till programmet. Du kan ange standardprogramversionen i Azure Portal programfönstret, som du ser i [Ladda upp och hantera program](#upload-and-manage-applications). 

Om du till exempel anger "2.7" som standardversion för program blender , och aktiviteterna *refererar* till följande miljövariabel, kommer dina Windows-noder att köra version 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Följande kodfragment visar en exempelaktivitetskommandorad som startar standardversionen av *blender-programmet:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Mer information om inställningar för beräkningsnodmiljön finns i [Miljöinställningar för aktiviteter.](jobs-and-tasks.md#environment-settings-for-tasks)

## <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool

Om en befintlig pool redan har konfigurerats med ett programpaket kan du ange ett nytt paket för poolen. Det innebär att du måste:

- Batch-tjänsten installerar det nyligen angivna paketet på alla nya noder som ansluter till poolen och på alla befintliga noder som startas om eller återställs.
- Beräkningsnoder som redan finns i poolen när du uppdaterar paketreferenserna installerar inte automatiskt det nya programpaketet. Dessa beräkningsnoder måste startas om eller återställs för att ta emot det nya paketet.
- När ett nytt paket distribueras återspeglar de skapade miljövariablerna de nya programpaketreferenserna.

I det här exemplet har den befintliga poolen version 2.7 av *blender-programmet* konfigurerat som en [av dess CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Om du vill uppdatera poolens noder med version 2.76b anger du en ny [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) med den nya versionen och genomför ändringen.

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

Nu när den nya versionen har konfigurerats installerar Batch-tjänsten version 2.76b på en ny nod som ansluter till poolen. Om du vill installera 2,76b på noderna som redan finns i poolen startar du om eller avbildar dem. Observera att omstartade noder behåller filer från tidigare paketdistributioner.

## <a name="list-the-applications-in-a-batch-account"></a>Lista programmen i ett Batch-konto

Du kan lista programmen och deras paket i ett Batch-konto med hjälp av [metoden ApplicationOperations.ListApplicationSummaries.](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries)

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

- [Batch-REST API](/rest/api/batchservice) också stöd för att arbeta med programpaket. Se till exempel elementet [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) för hur du anger vilka paket som ska installeras och [Program](/rest/api/batchservice/application) för hur du hämtar programinformation.
- Lär dig hur du programmässigt [hanterar Azure Batch-konton och kvoter med Batch Management .NET](batch-management-dotnet.md). Batch [Management .NET-biblioteket](/dotnet/api/overview/azure/batch/management) kan aktivera funktioner för att skapa och ta bort konton för Batch-programmet eller Batch-tjänsten.
