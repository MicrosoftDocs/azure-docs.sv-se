---
title: Säkerhetskopiera och återställa Service Fabric
description: Konceptuell dokumentation för Service Fabric säkerhets kopiering och återställning, en tjänst för att konfigurera säkerhets kopiering av pålitliga tillstånds känsliga tjänster och Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791619"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Säkerhetskopiera och återställa Reliable Services och Reliable Actors
Azure Service Fabric är en plattform med hög tillgänglighet som replikerar tillstånd över flera noder för att upprätthålla denna hög tillgänglighet.  Även om en nod i klustret Miss lyckas, fortsätter tjänsterna att vara tillgängliga. Även om den här inbyggda redundansen från plattformen kan vara tillräcklig för vissa, i vissa fall är det önskvärt att tjänsten säkerhetskopierar data (till ett externt lager).

> [!NOTE]
> Det är viktigt att säkerhetskopiera och återställa dina data (och testa att det fungerar som förväntat) så att du kan återställa från data förlust scenarier.
> 

> [!NOTE]
> Microsoft rekommenderar att du använder [Periodisk säkerhets kopiering och återställning](service-fabric-backuprestoreservice-quickstart-azurecluster.md) för att konfigurera data säkerhets kopiering av pålitliga tillstånds känsliga tjänster och Reliable Actors. 
> 


En tjänst kan till exempel vilja säkerhetskopiera data för att skydda från följande scenarier:

- I händelse av permanent förlust av ett helt Service Fabric-kluster.
- Permanent förlust av en majoritet av en tjänstepartitions repliker
- Administrativa fel där tillstånden oavsiktligt tas bort eller skadas. Detta kan till exempel inträffa om en administratör med tillräcklig behörighet tar bort tjänsten felaktigt.
- Buggar i tjänsten som orsakar datafel. Detta kan till exempel inträffa när en tjänst kods uppgradering börjar skriva felaktiga data till en tillförlitlig samling. I sådana fall kan både koden och data måste återställas till ett tidigare tillstånd.
- Data bearbetning offline. Det kan vara praktiskt att ha offline-bearbetning av data för Business Intelligence som sker separat från tjänsten som genererar data.

Funktionen säkerhets kopiering/återställning gör det möjligt för tjänster som bygger på Reliable Services API att skapa och återställa säkerhets kopior. De säkerhets kopierings-API: er som tillhandahålls av plattformen tillåter säkerhets kopiering av en tjänstepartitions tillstånd, utan att blockera Läs-eller Skriv åtgärder. Med återställnings-API: erna kan en tjänstepartitions tillstånd återställas från en vald säkerhets kopia.

## <a name="types-of-backup"></a>Typer av säkerhets kopiering
Det finns två alternativ för säkerhets kopiering: fullständig och stegvis.
En fullständig säkerhets kopiering är en säkerhets kopia som innehåller alla data som krävs för att återskapa replikens status: kontroll punkter och alla logg poster.
Eftersom den har kontroll punkter och loggen kan en fullständig säkerhets kopia återställas separat.

Problemet med fullständig säkerhets kopiering uppstår när kontroll punkterna är stora.
En replik som har 16 GB tillstånd har till exempel kontroll punkter som ger cirka 16 GB.
Om vi har ett mål för återställnings punkter på fem minuter måste repliken säkerhets kopie ras var femte minut.
Varje gången de säkerhetskopieras måste de kopiera 16 GB kontroll punkter, förutom 50 MB (konfigurerbara med `CheckpointThresholdInMB` ) av loggar.

![Exempel på fullständig säkerhets kopiering.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Lösningen på det här problemet är stegvisa säkerhets kopior, där säkerhets kopiering bara innehåller de ändrade logg posterna sedan den senaste säkerhets kopieringen.

![Exempel på stegvis säkerhets kopiering.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Eftersom stegvisa säkerhets kopieringar endast är ändringar sedan den senaste säkerhets kopieringen (inte innehåller kontroll punkter), tenderar de att vara snabbare, men de kan inte återställas på egen hand.
För att återställa en stegvis säkerhets kopia krävs hela säkerhets kopierings kedjan.
En säkerhets kopierings kedja är en kedja av säkerhets kopior som börjar med en fullständig säkerhets kopiering och följt av ett antal sammanhängande stegvisa säkerhets kopieringar.

## <a name="backup-reliable-services"></a>Säkerhets kopierings Reliable Services
Tjänste författaren har fullständig kontroll över när de ska göra säkerhets kopior och där säkerhets kopiorna ska lagras.

För att starta en säkerhets kopiering måste tjänsten anropa den ärvda medlems funktionen `BackupAsync` .  
Säkerhets kopieringar kan bara göras från primära repliker, och de kräver att Skriv status beviljas.

Som det visas nedan, `BackupAsync` tar ett `BackupDescription` objekt, där det går att ange en fullständig eller stegvis säkerhets kopia, samt en callback-funktion `Func<< BackupInfo, CancellationToken, Task<bool>>>` som anropas när säkerhetskopieringsmappen har skapats lokalt och är redo att flyttas ut till en extern lagrings plats.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Begäran om att göra en stegvis säkerhets kopiering kan inte utföras `FabricMissingFullBackupException` . Detta undantag anger att någon av följande saker händer:

- repliken har aldrig tagit en fullständig säkerhets kopia eftersom den har blivit primär,
- några av logg posterna sedan den senaste säkerhets kopieringen har trunkerats eller
- repliken klarade `MaxAccumulatedBackupLogSizeInMB` gränsen.

Användare kan öka sannolikheten för att kunna utföra stegvisa säkerhets kopieringar genom att konfigurera `MinLogSizeInMB` eller `TruncationThresholdFactor` .
Om du ökar värdena ökar disk användningen per replik.
Mer information finns i [Reliable Services konfiguration](service-fabric-reliable-services-configuration.md)

`BackupInfo` innehåller information om säkerhets kopieringen, inklusive platsen för mappen där körningen sparade säkerhets kopian ( `BackupInfo.Directory` ). Motringningsfunktionen kan flyttas `BackupInfo.Directory` till en extern lagrings plats eller till en annan plats.  Den här funktionen returnerar också en bool som visar om det gick att flytta säkerhetskopieringsmappen till dess målplats.

Följande kod visar hur `BackupCallbackAsync` metoden kan användas för att överföra säkerhets kopian till Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

I föregående exempel `ExternalBackupStore` är exempel klassen som används för att gränssnitt med Azure Blob Storage, och `UploadBackupFolderAsync` är metoden som komprimerar mappen och placerar den i Azure Blob Store.

Tänk på följande:

  - Det kan bara finnas en säkerhets kopierings åtgärd per replik vid en viss tidpunkt. Mer än ett `BackupAsync` anrop i taget utlöses `FabricBackupInProgressException` för att begränsa synlighetssekvensnummer säkerhets kopieringar till ett.
  - Om en replik växlar över medan en säkerhets kopiering pågår kanske säkerhets kopieringen inte har slutförts. När redundansväxlingen är klar är det därför tjänstens ansvar att starta om säkerhets kopieringen genom att anropa `BackupAsync` vid behov.

## <a name="restore-reliable-services"></a>Återställ Reliable Services
I allmänhet är de fall då du kan behöva utföra en återställnings åtgärd i en av följande kategorier:

  - Tjänstepartitionen förlorade data. Till exempel kan disken för två av tre repliker för en partition (inklusive den primära repliken) skadas eller rensas. Den nya primära servern kan behöva återställa data från en säkerhets kopia.
  - Hela tjänsten förloras. En administratör tar till exempel bort hela tjänsten och därmed måste tjänsten och data återställas.
  - Tjänsten replikerade skadade program data (till exempel på grund av ett program fel). I så fall måste tjänsten uppgraderas eller återställas för att ta bort orsaken till skadan och data som inte är skadade måste återställas.

Även om många metoder är möjliga, erbjuder vi några exempel på hur `RestoreAsync` du kan använda för att återställa från ovanstående scenarier.

## <a name="partition-data-loss-in-reliable-services"></a>Data förlust för partitioner i Reliable Services
I det här fallet identifierar körningen automatiskt data förlust och anropar API: `OnDataLossAsync` et.

Tjänst författaren måste utföra följande för att återställa:

  - Åsidosätt den virtuella Bask lass metoden `OnDataLossAsync` .
  - Hitta den senaste säkerhets kopian på den externa plats som innehåller tjänstens säkerhets kopior.
  - Ladda ned den senaste säkerhets kopian (och expandera säkerhets kopian till säkerhetskopieringsmappen om den var komprimerad).
  - `OnDataLossAsync`Metoden ger en `RestoreContext` . Anropa `RestoreAsync` API: et på den angivna `RestoreContext` .
  - Returnera True om återställningen lyckades.

Följande är ett exempel på en implementering av `OnDataLossAsync` metoden:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` skickades till `RestoreContext.RestoreAsync` anropet innehåller en medlem som kallas `BackupFolderPath` .
När du återställer en enda fullständig säkerhets kopia `BackupFolderPath` ska du ange den lokala sökvägen till den mapp som innehåller din fullständiga säkerhets kopia.
När du återställer en fullständig säkerhets kopia och ett antal stegvisa säkerhets kopior `BackupFolderPath` bör du ställa in den lokala sökvägen till mappen som inte bara innehåller den fullständiga säkerhets kopian, utan även alla de stegvisa säkerhets kopiorna.
`RestoreAsync` anrop kan utlösa `FabricMissingFullBackupException` om den `BackupFolderPath` tillhandahållna inte innehåller en fullständig säkerhets kopia.
Det kan också utlösa `ArgumentException` om `BackupFolderPath` har en bruten kedja av stegvisa säkerhets kopior.
Om den till exempel innehåller den fullständiga säkerhets kopian, den första stegvisa och den tredje säkerhets kopian, men inte den andra stegvisa säkerhets kopian.

> [!NOTE]
> RestorePolicy är inställt på säker som standard.  Det innebär att `RestoreAsync` API: et Miss fungerar med ArgumentException om den identifierar att säkerhetskopieringsmappen innehåller ett tillstånd som är äldre än eller lika med det tillstånd som finns i den här repliken.  `RestorePolicy.Force` kan användas för att hoppa över den här säkerhets kontrollen. Detta anges som en del av `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Borttagen eller förlorad tjänst
Om en tjänst tas bort måste du först återskapa tjänsten innan du kan återställa data.  Det är viktigt att skapa tjänsten med samma konfiguration, till exempel partitionerings schema, så att data kan återställas sömlöst.  När tjänsten är igång måste API: et för att återställa data ( `OnDataLossAsync` ovan) anropas på varje partition av den här tjänsten. Ett sätt att uppnå detta är genom att använda [FabricClient. TestManagementClient. StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) på varje partition.  

I det här läget är implementeringen samma som scenariot ovan. Varje partition måste återställa den senaste relevanta säkerhets kopian från den externa lagrings platsen. Ett villkor är att partitions-ID: t nu kan ha ändrats, eftersom körningen skapar partitions-ID: n dynamiskt. Därför måste tjänsten lagra lämplig partitionsinformation och tjänst namn för att identifiera rätt senaste säkerhets kopiering för att återställa från för varje partition.

> [!NOTE]
> Du bör inte använda `FabricClient.ServiceManager.InvokeDataLossAsync` på varje partition för att återställa hela tjänsten, eftersom det kan skada klustrets tillstånd.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikering av skadade program data
Om den nyligen distribuerade program uppgraderingen har ett fel, kan det leda till att data skadas. En program uppgradering kan till exempel börja uppdatera varje telefonnummer post i en tillförlitlig ord lista med ett ogiltigt rikt nummer.  I det här fallet kommer de ogiltiga telefonnumren att replikeras eftersom Service Fabric inte känner till vilken typ av data som lagras.

Det första du ska göra när du har identifierat ett sådant egregious-fel som orsakar skadade data är att låsa tjänsten på program nivå och, om möjligt, uppgradera till den version av program koden som inte har något fel.  Men även efter att tjänst koden har åtgärd ATS kan data fortfarande vara skadade, och därför kan data behöva återställas.  I sådana fall kanske det inte räcker att återställa den senaste säkerhets kopieringen eftersom de senaste säkerhets kopieringarna kan vara skadade.  Därför måste du hitta den senaste säkerhets kopian som gjordes innan data skadades.

Om du inte är säker på vilka säkerhets kopior som är skadade kan du distribuera ett nytt Service Fabric kluster och återställa säkerhets kopior av berörda partitioner precis som i scenariot "borttagen eller förlorad tjänst".  Börja återställa säkerhets kopiorna från den senaste till minst för varje partition. När du har hittat en säkerhets kopia som inte har skadan flyttar du/tar bort alla säkerhets kopior av den här partitionen som var nyare (än den säkerhets kopian). Upprepa den här processen för varje partition. När `OnDataLossAsync` anropas på partitionen i produktions klustret blir den senaste säkerhets kopian som finns i den externa lagrings platsen den som har hämtats av ovanstående process.

Nu kan stegen i avsnittet "borttagen eller förlorad tjänst" användas för att återställa tjänstens tillstånd till tillstånd innan fel söknings koden skadar status.

Tänk på följande:

  - När du återställer finns det en risk att säkerhets kopian som återställs är äldre än partitionens tillstånd innan data förlorades. Därför bör du bara återställa som en sista utväg för att återställa så mycket data som möjligt.
  - Strängen som representerar sökvägen till mappen för säkerhets kopian och sökvägen till filerna i säkerhetskopieringsmappen får innehålla fler än 255 tecken, beroende på sökvägen till FabricDataRoot och program typ namnet. Detta kan orsaka att vissa .NET-metoder, t `Directory.Move` . ex `PathTooLongException` ., genererar undantag. En lösning är att anropa API-API: er direkt, t `CopyFile` . ex..

## <a name="back-up-and-restore-reliable-actors"></a>Säkerhetskopiera och återställa Reliable Actors


Reliable Actors Framework skapas ovanpå Reliable Services. ActorService som är värd för aktören är en tillstånds känslig, tillförlitlig tjänst. Därför är alla säkerhets kopierings-och återställnings funktioner som är tillgängliga i Reliable Services också tillgängliga för Reliable Actors (förutom beteenden som är State Provider-Specific). Eftersom säkerhets kopieringarna görs per partition kommer tillstånd för alla aktörer i den partitionen att säkerhets kopie ras (och återställningen är liknande och sker per partition). Om du vill utföra säkerhets kopiering/återställning bör tjänst ägaren skapa en anpassad aktörs tjänst klass som härleds från ActorService-klassen och sedan göra säkerhets kopiering/återställning liknande Reliable Services enligt beskrivningen ovan i föregående avsnitt.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

När du skapar en anpassad aktörs tjänst klass måste du registrera det även när du registrerar aktören.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Standard-providern för Reliable Actors är `KvsActorStateProvider` . Stegvis säkerhets kopiering är inte aktiverat som standard för `KvsActorStateProvider` . Du kan aktivera stegvis säkerhets kopiering genom att skapa `KvsActorStateProvider` med lämplig inställning i dess konstruktor och sedan skicka den till ActorService-konstruktorn som visas i följande kodfragment:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

När den stegvisa säkerhets kopieringen har Aktiver ATS kan en stegvis säkerhets kopiering Miss Miss FabricMissingFullBackupException av en av följande orsaker och du måste göra en fullständig säkerhets kopia innan du tar de stegvisa säkerhets kopieringarna:

  - Repliken har aldrig tagit en fullständig säkerhets kopia sedan den blev primär.
  - Några av logg posterna trunkerades sedan den senaste säkerhets kopieringen utfördes.

När stegvis säkerhets kopiering är aktiverat `KvsActorStateProvider` använder inte cirkulär buffert för att hantera logg poster och trunkerar den regelbundet. Om ingen säkerhets kopia utförs av användaren under en period på 45 minuter trunkerar systemet automatiskt logg posterna. Intervallet kan konfigureras genom att ange `logTruncationIntervalInMinutes` i `KvsActorStateProvider` konstruktorn (liknar när du aktiverar stegvis säkerhets kopiering). Logg posterna kan också bli trunkerade om den primära repliken behöver skapa en annan replik genom att skicka alla data.

När du gör en återställning från en säkerhets kopierings kedja som liknar Reliable Services, ska BackupFolderPath innehålla under kataloger med en under katalog som innehåller fullständig säkerhets kopiering och andra under kataloger som innehåller stegvisa säkerhets kopior. Återställnings-API: et genererar FabricException med lämpligt fel meddelande om verifieringen av säkerhets kopierings kedjan Miss lyckas. 

> [!NOTE]
> `KvsActorStateProvider` alternativet RestorePolicy. Safe ignoreras för närvarande. Stöd för den här funktionen planeras i en kommande version.
> 

## <a name="testing-back-up-and-restore"></a>Testa säkerhets kopiering och återställning
Det är viktigt att se till att viktiga data säkerhets kopie ras och kan återställas från. Detta kan göras genom att anropa `Start-ServiceFabricPartitionDataLoss` cmdleten i PowerShell som kan orsaka data förlust i en viss partition för att testa om säkerhets kopierings-och återställnings funktionerna för data fungerar som förväntat.  Det är också möjligt att program mässigt anropa data förlust och även återställa från händelsen.

> [!NOTE]
> Du hittar ett exempel på implementering av säkerhets kopierings-och återställnings funktioner i Web Reference-appen på GitHub. Mer information hittar du i `Inventory.Service` tjänsten.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Under huven: Mer information om säkerhets kopiering och återställning
Här är mer information om säkerhets kopiering och återställning.

### <a name="backup"></a>Backup
Den tillförlitliga tillstånds hanteraren ger möjlighet att skapa konsekventa säkerhets kopieringar utan att blockera Läs-och skriv åtgärder. För att göra det använder den en kontroll punkt och en logg beständig mekanism.  Den tillförlitliga tillstånds hanteraren tar suddiga (lätta) kontroll punkter vid vissa tidpunkter för att minska trycket från transaktions loggen och förbättra återställnings tiderna.  När `BackupAsync` anropas instruerar den tillförlitliga tillstånds hanteraren alla pålitliga objekt att kopiera sina senaste Checkpoint-filer till en lokal mapp för säkerhets kopiering.  Sedan kopierar den pålitliga tillstånds hanteraren alla logg poster från "starta" till den senaste logg posten till mappen säkerhets kopia.  Eftersom alla logg poster upp till den senaste logg posten ingår i säkerhets kopieringen och den Reliable State Manager bevarar loggen för Skriv åtgärder, garanterar den pålitliga tillstånds hanteraren att alla transaktioner som har genomförts ( `CommitAsync` har returnerats) ingår i säkerhets kopian.

Alla transaktioner som checkar in efter `BackupAsync` har anropats kan vara i säkerhets kopian.  När den lokala säkerhetskopieringsmappen har fyllts av plattformen (dvs. den lokala säkerhets kopian slutförs av körningen) anropas tjänstens återanrop för säkerhets kopiering.  Det här återanropet ansvarar för att flytta säkerhetskopieringsmappen till en extern plats, till exempel Azure Storage.

### <a name="restore"></a>Återställ
Den tillförlitliga tillstånds hanteraren ger möjlighet att återställa från en säkerhets kopia med hjälp av `RestoreAsync` API: et.  
`RestoreAsync`Metoden `RestoreContext` kan endast anropas inuti `OnDataLossAsync` metoden.
Bool returnerade av `OnDataLossAsync` anger om tjänsten återställde sitt tillstånd från en extern källa.
Om `OnDataLossAsync` värdet är true, kommer Service Fabric att återskapa alla andra repliker från den här primära servern. Service Fabric säkerställer att repliker som kommer att ta emot `OnDataLossAsync` samtal först över gång till den primära rollen men inte beviljas Läs status eller Skriv status.
Detta innebär att för StatefulService-implementerare, `RunAsync` anropas inte förrän åtgärden har `OnDataLossAsync` slutförts.
Sedan startas `OnDataLossAsync` den nya primära servern.
Innan en tjänst har slutfört det här API: et (genom att returnera true eller false) och slutför den relevanta omkonfigurationen, så kommer API: et att behållas en i taget.

`RestoreAsync` först släpps alla befintliga tillstånd i den primära repliken som den anropades. Sedan skapar den pålitliga tillstånds hanteraren alla pålitliga objekt som finns i säkerhetskopieringsmappen. Sedan instrueras de tillförlitliga objekten att återställa från sina kontroll punkter i säkerhetskopieringsmappen. Slutligen återställer den pålitliga tillstånds hanteraren sin egen status från logg posterna i säkerhetskopieringsmappen och utför återställningen. Som en del av återställnings processen spelas åtgärder som börjar på den "Start punkten" som har allokerade logg poster i säkerhetskopieringsmappen om till de tillförlitliga objekten. Det här steget säkerställer att det återställda läget är konsekvent.

## <a name="next-steps"></a>Nästa steg
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services snabb start](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services meddelanden](service-fabric-reliable-services-notifications.md)
  - [Reliable Services konfiguration](service-fabric-reliable-services-configuration.md)
  - [Referens för utvecklare för tillförlitliga samlingar](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Regelbunden säkerhetskopiering och återställning i Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
