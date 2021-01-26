---
title: Azure Service Fabric-distribution med PowerShell
description: Lär dig mer om att ta bort och distribuera program i Azure Service Fabric och hur du utför dessa åtgärder i PowerShell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: d428a51c0bc224ca8706403ae176d46f1db82a32
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785455"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Distribuera och ta bort program med PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

När en [program typ har paketerats][10]är den redo för distribution till ett Azure Service Fabric-kluster. Distributionen omfattar följande tre steg:

1. Ladda upp programpaketet till avbildnings arkivet.
2. Registrera program typen med relativ sökväg för avbildnings arkivet.
3. Skapa program instansen.

När det distribuerade programmet inte längre behövs kan du ta bort program instansen och dess program typ. För att helt ta bort ett program från klustret, omfattar följande steg:

1. Ta bort (eller ta bort) den program instans som körs.
2. Avregistrera program typen om du inte längre behöver den.
3. Ta bort programpaketet från avbildnings arkivet.

Om du använder Visual Studio för att distribuera och felsöka program i ditt lokala utvecklings kluster hanteras alla föregående steg automatiskt via ett PowerShell-skript.  Det här skriptet finns i mappen *scripts* i programprojektet. Den här artikeln innehåller en bakgrund för vad skriptet gör så att du kan utföra samma åtgärder utanför Visual Studio. 

Ett annat sätt att distribuera ett program är att använda extern etablering. Programpaketet kan [paketeras som `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) och överföras till en extern lagrings plats. I det här fallet behövs inte överföring till avbildnings arkivet. Distributionen behöver följande steg:

1. Överför `sfpkg` till en extern lagrings plats. Den externa lagrings platsen kan vara vilken butik som helst som exponerar en REST-http-eller HTTPS-slutpunkt.
2. Registrera program typen med hjälp av den externa nedladdnings-URI: n och information om program typen.
2. Skapa program instansen.

Ta bort program instanserna för rensning och avregistrera program typen. Eftersom paketet inte har kopierats till avbildnings arkivet finns det ingen tillfällig plats att rensa. Etableringen från det externa arkivet är tillgängligt från och med Service Fabric version 6,1.

>[!NOTE]
> Visual Studio har för närvarande inte stöd för extern etablering.

 

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Innan du kör PowerShell-kommandon i den här artikeln ska du alltid starta med hjälp av [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) för att ansluta till Service Fabric klustret. Kör följande för att ansluta till det lokala utvecklings klustret:

```powershell
Connect-ServiceFabricCluster
```

Exempel på hur du ansluter till ett fjärran slutet kluster eller ett kluster som skyddas med Azure Active Directory, X509-certifikat eller Windows Active Directory finns i [ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Ladda upp programpaketet

När du överför programpaketet placeras det på en plats som är tillgänglig för interna Service Fabric-komponenter.
Använd cmdleten [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) om du vill verifiera program paketet lokalt.

Kommandot [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) överför programpaketet till klustrets avbildnings arkiv.

Anta att du skapar och paketerar ett program med namnet mina *program* i Visual Studio 2015. Som standard är program typs namnet som anges i ApplicationManifest.xml "MyApplicationType".  Programpaketet, som innehåller det nödvändiga applikations manifestet, tjänst manifesten och kod/config/data paket, finns i *C:\Users \<username\> \Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*. 

Följande kommando visar innehållet i programpaketet:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Om applikations paketet är stort och/eller har många filer kan du [Komprimera det](service-fabric-package-apps.md#compress-a-package). Komprimeringen minskar storleken och antalet filer.
Detta resulterar i snabbare registrering och avregistrering av program typen. Uppladdnings tiden kan vara långsammare, särskilt om du tar med tiden att komprimera paketet. 

Om du vill komprimera ett paket använder du samma [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) -kommando. Komprimering kan göras separat från överföring, med hjälp av `SkipCopy` flaggan eller tillsammans med uppladdnings åtgärden. Det går inte att använda komprimering på komprimerade paket.
Om du vill expandera ett komprimerat paket använder du samma [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) -kommando med `UncompressPackage` växeln.

Följande cmdlet komprimerar paketet utan att kopiera det till avbildnings arkivet. Paketet innehåller nu zippade filer för- `Code` och- `Config` paketen. Programmet och tjänst manifesten är inte zippade eftersom de behövs för många interna åtgärder (t. ex. paket delning, namn på program typ och versions extrahering för vissa valideringar). Genom att zippa upp manifesten skulle dessa åtgärder bli ineffektiva.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

För stora programpaket tar komprimeringen tid. Använd en fast SSD-enhet för bästa resultat. Komprimerings tiderna och storleken på det komprimerade paketet skiljer sig också beroende på paket innehållet.
Här är till exempel komprimerings statistik för vissa paket, som visar ursprunglig och komprimerad paket storlek, med komprimerings tiden.

|Ursprunglig storlek (MB)|Antal filer|Komprimerings tid|Komprimerad paket storlek (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

När ett paket har komprimerats kan det överföras till ett eller flera Service Fabric-kluster efter behov. Distributions mekanismen är densamma för komprimerade och okomprimerade paket. Komprimerade paket lagras som sådana i kluster avbildnings arkivet. Paketen är okomprimerade på noden innan programmet körs.


I följande exempel överförs paketet till avbildnings arkivet i en mapp med namnet "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Om du inte anger parametern *-ApplicationPackagePathInImageStore* kopieras programpaketet till mappen "debug" i avbildnings arkivet.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** identifierar automatiskt rätt anslutnings sträng för avbildnings lagring om PowerShell-sessionen är ansluten till ett Service Fabric-kluster. För Service Fabric versioner som är äldre än 5,6 måste argumentet **-ImageStoreConnectionString** uttryckligen anges.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Cmdleten **Get-ImageStoreConnectionStringFromClusterManifest** , som är en del av modulen Service Fabric SDK PowerShell, används för att hämta anslutnings strängen för avbildnings lagret.  Importera SDK-modulen genom att köra:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Se [förstå anslutnings strängen för avbildnings arkivet](service-fabric-image-store-connection-string.md) för kompletterande information om avbildnings lagret och anslutnings strängen för avbildnings arkivet.
>
>
>

Den tid det tar att ladda upp ett paket varierar beroende på flera faktorer. Några av dessa faktorer är antalet filer i paketet, paket storleken och fil storlekarna. Nätverks hastigheten mellan käll datorn och Service Fabric-klustret påverkar också överförings tiden. Standard tids gränsen för [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) är 30 minuter.
Beroende på de beskrivna faktorerna kan du behöva öka tids gränsen. Om du komprimerar paketet i kopierings anropet måste du också överväga komprimerings tiden.



## <a name="register-the-application-package"></a>Registrera programpaketet

Den program typ och version som har deklarerats i applikations manifestet blir tillgängliga när programpaketet registreras. Systemet läser paketet som laddades upp i föregående steg, verifierar paketet, bearbetar paket innehållet och kopierar det bearbetade paketet till en intern system plats.  

Kör cmdleten [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) för att registrera program typen i klustret och gör den tillgänglig för distribution:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrera programpaketet som kopierats till avbildnings arkivet

När ett paket tidigare har kopierats till avbildnings arkivet anger register åtgärden den relativa sökvägen i avbildnings arkivet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" är den mapp i avbildnings arkivet där programpaketet finns. Program typen med namnet "MyApplicationType" och version "1.0.0" (båda finns i applikations manifestet) är nu registrerad i klustret.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrera programpaketet som kopierats till en extern lagrings plats

Från och med Service Fabric version 6,1, etablera stöd för att ladda ned paketet från en extern lagrings plats. Nedladdnings-URI: n representerar sökvägen till [ `sfpkg` programpaketet](service-fabric-package-apps.md#create-an-sfpkg) där programpaketet kan hämtas med hjälp av http-eller HTTPS-protokoll. Paketet måste ha överförts tidigare till den här externa platsen. URI: n måste tillåta Läs åtkomst så Service Fabric kan ladda ned filen. `sfpkg`Filen måste ha fil namns tillägget. sfpkg. Etablerings åtgärden ska innehålla information om program typ, som finns i applikations manifestet.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Kommandot [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) returnerar bara när systemet har registrerat programpaketet. Hur lång tid registreringen tar beror på programmets storlek och innehåll. Vid behov kan parametern **-TimeoutSec** användas för att ange en längre tids gräns (standard-timeout är 60 sekunder).

Om du har ett stort programpaket eller om du råkar ut för timeout använder du parametern **-async** . Kommandot returnerar när klustret accepterar register kommandot. Register åtgärden fortsätter efter behov.
Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) visar en lista över program typ versioner och deras registrerings status. Du kan använda det här kommandot för att avgöra när registreringen är färdig.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Ta bort ett program paket från avbildnings arkivet

Om ett paket har kopierats till avbildnings arkivet bör du ta bort det från den tillfälliga platsen när programmet har registrerats. Om du tar bort program paket från avbildnings arkivet frigörs system resurser. Att behålla oanvända programpaket använder disk lagring och leder till problem med program prestanda.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Skapa programmet

Du kan skapa en instans av ett program från alla program typ versioner som har registrerats med hjälp av cmdleten [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) . Namnet på varje program måste börja med schemat *"Fabric:"* och måste vara unikt för varje program instans. Alla standard tjänster som definieras i program manifestet för mål program typen skapas också.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Flera program instanser kan skapas för en specifik version av en registrerad program typ. Varje program instans körs i isolering med sin egen arbets katalog och process.

Om du vill se vilka namngivna appar och tjänster som körs i klustret kör du cmdletarna [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) och [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice) :

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Ta bort ett program

När en program instans inte längre behövs kan du ta bort den permanent med namnet med hjälp av cmdleten [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) . [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) tar automatiskt bort alla tjänster som tillhör programmet och tar permanent bort alla tjänst tillstånd. 

> [!WARNING]
> Den här åtgärden kan inte ångras och det går inte att återställa program tillstånd.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Avregistrera en program typ

När en viss version av en program typ inte längre behövs bör du avregistrera program typen med hjälp av [unregister-ServiceFabricApplicationType-](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) cmdleten. Genom att avregistrera oanvända program typer frigörs lagrings utrymme som används av avbildnings arkivet genom att filerna för program typen tas bort. När du avregistrerar en program typ tas inte det programpaket som kopierats till avbildnings arkivets tillfälliga plats bort, om kopieringen till avbildnings lagrings platsen användes. En program typ kan avregistreras så länge inga program instansieras mot den och inga väntande program uppgraderingar refererar till den.

Kör [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) för att se de program typer som för närvarande är registrerade i klustret:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Kör [unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) för att avregistrera en specifik program typ:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Felsökning

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage ber om en ImageStoreConnectionString

Den Service Fabric SDK-miljön bör redan ha rätt standardinställningar. Men om det behövs ska ImageStoreConnectionString för alla kommandon matcha det värde som Service Fabric-klustret använder. Du hittar ImageStoreConnectionString i kluster manifestet som hämtats med hjälp av kommandona [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) och Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Cmdleten **Get-ImageStoreConnectionStringFromClusterManifest** , som är en del av modulen Service Fabric SDK PowerShell, används för att hämta anslutnings strängen för avbildnings lagret.  Importera SDK-modulen genom att köra:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString finns i kluster manifestet:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Se [förstå anslutnings strängen för avbildnings arkivet](service-fabric-image-store-connection-string.md) för kompletterande information om avbildnings lagret och anslutnings strängen för avbildnings arkivet.

### <a name="deploy-large-application-package"></a>Distribuera stort programpaket

Problem: [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) nådde tids gränsen för ett stort programpaket (GB GB).
Prova:
- Ange en större tids gräns för kommandot [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) med `TimeoutSec` parameter. Som standard är tids gränsen 30 minuter.
- Kontrol lera nätverks anslutningen mellan käll datorn och klustret. Om anslutningen är långsam bör du överväga att använda en dator med en bättre nätverks anslutning.
Om klient datorn finns i en annan region än klustret, bör du överväga att använda en klient dator i en närmare eller samma region som klustret.
- Kontrol lera om du träffar extern begränsning. Om till exempel bild arkivet har kon figurer ATS för att använda Azure Storage kan uppladdning vara begränsad.

Problem: uppladdnings paketet har slutförts, men timeout för [register ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . Pröva
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar det till avbildnings arkivet.
Komprimeringen minskar storleken och antalet filer, vilket i sin tur minskar mängden trafik och arbete som Service Fabric måste utföra. Uppladdnings åtgärden kan vara långsammare (särskilt om du tar med komprimerings tiden), men registrera och avregistrera program typen är snabbare.
- Ange en större tids gräns för [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) med `TimeoutSec` parametern.
- Ange `Async` växel för [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). Kommandot returnerar när klustret accepterar kommandot och registreringen av program typen fortsätter asynkront. Därför behöver du inte ange en högre tids gräns i det här fallet. Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) visar en lista över alla program typ versioner som har registrerats och deras registrerings status. Du kan använda det här kommandot för att avgöra när registreringen är färdig.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Distribuera programpaket med många filer

Problem: [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) tids gräns för ett programpaket med många filer (ordning på tusental).
Prova:
- [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar det till avbildnings arkivet. Komprimeringen minskar antalet filer.
- Ange en större tids gräns för [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) med `TimeoutSec` parametern.
- Ange `Async` växel för [register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). Kommandot returnerar när klustret accepterar kommandot och registreringen av program typen fortsätter asynkront.
Därför behöver du inte ange en högre tids gräns i det här fallet. Kommandot [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) visar en lista över alla program typ versioner som har registrerats och deras registrerings status. Du kan använda det här kommandot för att avgöra när registreringen är färdig.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Nästa steg

[Paketera ett program](service-fabric-package-apps.md)

[Service Fabric program uppgradering](service-fabric-application-upgrade.md)

[Introduktion till Service Fabric hälsa](service-fabric-health-introduction.md)

[Diagnostisera och felsöka en Service Fabric-tjänst](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modellera ett program i Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
