---
title: Service Fabric program uppgradering med PowerShell
description: Den här artikeln beskriver hur du distribuerar ett Service Fabric program, ändrar koden och installerar en uppgradering med PowerShell.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792039"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Service Fabric program uppgradering med PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Den vanligaste och rekommenderade uppgraderings metoden är den övervakade löpande uppgraderingen.  Azure Service Fabric övervakar hälso tillståndet för det program som uppgraderas baserat på en uppsättning hälso principer. När en uppdaterings domän (UD) har uppgraderats utvärderar Service Fabric program hälsan och fortsätter antingen till nästa uppdaterings domän eller Miss lyckas med uppgraderingen beroende på hälso principerna.

En övervakad program uppgradering kan utföras med hjälp av hanterade eller inbyggda API: er, PowerShell, Azure CLI, Java eller REST. Anvisningar om hur du utför en uppgradering med Visual Studio finns i [uppgradera ditt program med Visual Studio](service-fabric-application-upgrade-tutorial.md).

Med Service Fabric övervakade rullande uppgraderingar kan program administratören konfigurera hälso utvärderings principen som Service Fabric använder för att avgöra om programmet är felfritt. Dessutom kan administratören konfigurera åtgärden som ska vidtas när hälso utvärderingen Miss lyckas (till exempel göra en automatisk återställning). Det här avsnittet går igenom en övervakad uppgradering för ett av SDK-exemplen som använder PowerShell. 

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s bevaras inte i en program uppgradering. För att bevara aktuella program parametrar bör användaren hämta parametrarna först och skicka dem till uppgraderings-API-anropet som nedan:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Steg 1: Bygg och distribuera exempel på visuella objekt
Bygg och publicera programmet genom att högerklicka på programprojektet, **VisualObjectsApplication** och välja kommandot **publicera** .  Mer information finns i [självstudier för Service Fabric program uppgradering](service-fabric-application-upgrade-tutorial.md).  Du kan också använda PowerShell för att distribuera ditt program.

> [!NOTE]
> Innan något av Service Fabric-kommandona kan användas i PowerShell måste du först ansluta till klustret med hjälp av `Connect-ServiceFabricCluster` cmdleten. På samma sätt antas det att klustret redan har kon figurer ATS på den lokala datorn. Se artikeln om hur [du konfigurerar din Service Fabric utvecklings miljö](service-fabric-get-started.md).
> 
> 

När du har skapat projektet i Visual Studio kan du använda PowerShell-kommandot [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) för att kopiera programpaketet till avbildnings arkiv. Använd cmdleten [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) om du vill verifiera app-paketet lokalt. Nästa steg är att registrera programmet till Service Fabric runtime med hjälp av cmdleten [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . Följande steg är att starta en instans av programmet med hjälp av cmdleten [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) .  Dessa tre steg är likvärdiga med att använda meny alternativet **distribuera** i Visual Studio.  När etableringen har slutförts bör du rensa det kopierade programpaketet från avbildnings arkivet för att minska resurserna som används.  Om en program typ inte längre krävs ska den avregistreras av samma skäl. Mer information finns i [distribuera och ta bort program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) .

Nu kan du använda [Service Fabric Explorer för att Visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet har en webb tjänst som kan navigera till i Internet Explorer genom att skriva `http://localhost:8081/visualobjects` i adress fältet.  Du bör se vissa flytande visuella objekt som flyttas runt på skärmen.  Du kan också använda [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) för att kontrol lera programmets status.

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: uppdatera det visuella objekt exemplet
Du kanske märker att de visuella objekten inte roterar med den version som distribuerades i steg 1. Vi ska uppgradera det här programmet till ett ställe där de visuella objekten också roterar.

Välj VisualObjects. ActorService-projektet i VisualObjects-lösningen och öppna filen StatefulVisualObjectActor. cs. I den filen navigerar du till metoden `MoveObject` , kommentera ut `this.State.Move()` och ta bort kommentaren `this.State.Move(true)` . Den här ändringen roterar objekten när tjänsten har uppgraderats.

Vi måste också uppdatera *ServiceManifest.xml* -filen (under PackageRoot) för projektet **VisualObjects. ActorService**. Uppdatera *CodePackage* och tjänst versionen till 2,0 och motsvarande rader i *ServiceManifest.xml* -filen.
Du kan använda alternativet Visual Studio *Edit manifest Files* när du har högerklickat på lösningen för att göra manifest filen ändringar.

När ändringarna har gjorts bör manifestet se ut ungefär så här (markerade delar visar ändringarna):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu är *ApplicationManifest.xml* -filen (som finns i **VisualObjects** -projektet under **VisualObjects** -lösningen) uppdaterad till version 2,0 av projektet **VisualObjects. ActorService** . Dessutom uppdateras program versionen till 2.0.0.0 från 1.0.0.0. *ApplicationManifest.xml* bör se ut som följande kodfragment:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Nu ska du bygga projektet genom att välja enbart **ActorService** -projektet och sedan högerklicka och välja alternativet **build** i Visual Studio. Om du väljer **återskapa alla** bör du uppdatera versionerna för alla projekt eftersom koden har ändrats. Nu ska vi paketera det uppdaterade programmet genom att högerklicka på **_VisualObjectsApplication_*_, välja menyn Service Fabric och välja _* paket**. Den här åtgärden skapar ett programpaket som kan distribueras.  Det uppdaterade programmet är redo att distribueras.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Steg 3: Bestäm hälso principer och uppgraderings parametrar
Bekanta dig med parametrarna för [program uppgradering](service-fabric-application-upgrade-parameters.md) och [uppgraderings processen](service-fabric-application-upgrade.md) för att få en god förståelse för de olika uppgraderings parametrarna, tids gränser och hälso kriterier som tillämpas. I den här genom gången anges villkoret för service Health-utvärderingen till standardvärdena (och rekommenderade), vilket innebär att alla tjänster och instanser bör vara *felfria* efter uppgraderingen.  

Vi kan dock öka *HealthCheckStableDuration* till 180 sekunder (så att tjänsterna är felfria under minst 120 sekunder innan uppgraderingen fortsätter till nästa uppdaterings domän).  Vi anger också att *UpgradeDomainTimeout* ska vara 1200 sekunder och att *UpgradeTimeout* ska vara 3000 sekunder.

Slutligen anger vi också att *UpgradeFailureAction* ska återställas. Det här alternativet kräver Service Fabric att återställa programmet till den tidigare versionen om det påträffar eventuella problem under uppgraderingen. När du startar uppgraderingen (i steg 4) så anges följande parametrar:

FailureAction = ångring

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Steg 4: Förbered program för uppgradering
Programmet är nu byggt och redo att uppgraderas. Om du öppnar ett PowerShell-fönster som administratör och skriver [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication), bör det meddela dig att det är program typen 1.0.0.0 av **VisualObjects** som har distribuerats.  

Programpaketet lagras under följande relativa sökväg där du okomprimerade Service Fabric SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Du bör hitta mappen "paket" i katalogen där programpaketet lagras. Kontrol lera tidsstämplarna för att se till att det är den senaste versionen (du kan behöva ändra Sök vägarna på lämpligt sätt också).

Nu ska vi kopiera det uppdaterade programpaketet till Service Fabric avbildnings arkiv (där programpaketen lagras av Service Fabric). Parametern *ApplicationPackagePathInImageStore* informerar Service Fabric var programpaketet kan hittas. Vi har lagt till det uppdaterade programmet i "VisualObjects \_ v2" med följande kommando (du kan behöva ändra Sök vägarna igen på lämpligt sätt).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

Nästa steg är att registrera programmet med Service Fabric, som kan utföras med kommandot [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Om föregående kommando inte lyckas är det troligt att du behöver återskapa alla tjänster. Som vi nämnt i steg 2 kan du behöva uppdatera din WebService-version också.

Vi rekommenderar att du tar bort applikations paketet när programmet har registrerats.  Om du tar bort program paket från avbildnings arkivet frigörs system resurser.  Att behålla oanvända programpaket använder disk lagring och leder till problem med program prestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Steg 5: starta program uppgraderingen
Nu är allt klart för att starta program uppgraderingen genom att använda kommandot [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Program namnet är detsamma som det beskrevs i *ApplicationManifest.xml* -filen. Service Fabric använder det här namnet för att identifiera vilket program som uppgraderas. Om du ställer in timeout-värdet för kort kan du stöta på ett fel meddelande som anger problemet. Läs avsnittet fel sökning eller öka tids gränsen.

Eftersom program uppgraderingen fortsätter kan du övervaka den med Service Fabric Explorer eller genom att använda kommandot [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

På några minuter bör den status som du fick med hjälp av föregående PowerShell-kommando ange att alla uppdaterings domäner har uppgraderats (slutförts). Och du bör se att de visuella objekten i webbläsarfönstret har börjat rotera!

Du kan prova att uppgradera från version 2 till version 3 eller från version 2 till version 1 som en övning. Att flytta från version 2 till version 1 anses också vara en uppgradering. Spela med timeout-och hälso principer för att ge dig bekanta med dem. När du distribuerar till ett Azure-kluster måste parametrarna ställas in på rätt sätt. Det är lämpligt att ange tids gränsen på ett försiktigt sätt.

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera programmet med Visual Studio](service-fabric-application-upgrade-tutorial.md) går du igenom en program uppgradering med Visual Studio.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).
