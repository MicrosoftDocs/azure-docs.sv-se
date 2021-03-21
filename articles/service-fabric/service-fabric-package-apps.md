---
title: Paketera en Azure Service Fabric-app
description: Lär dig mer om att paketera ett Azure Service Fabric-program och hur du förbereder distributionen till ett kluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 168e6d6dc7ab5bfeccc4e1dabc7bd50efcbe8f34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789710"
---
# <a name="package-an-application"></a>Paketera ett program

I den här artikeln beskrivs hur du paketerar ett Service Fabric-program och gör det klart för distribution.

## <a name="package-layout"></a>Paketera layout

Program manifestet, ett eller flera tjänst manifest och andra nödvändiga paketfiler måste organiseras i en specifik layout för distribution till ett Service Fabric-kluster. Exempel manifesten i den här artikeln måste ordnas i följande katalog struktur:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Mapparna heter för att matcha **namnattribut** för varje motsvarande element. Om tjänst manifestet till exempel innehåller två kod paket med namnen **koda** och **MyCodeB**, skulle två mappar med samma namn innehålla de binärfiler som krävs för varje kod paket.

## <a name="use-setupentrypoint"></a>Använd SetupEntryPoint

Vanliga scenarier för att använda **SetupEntryPoint** är när du behöver köra en körbar fil innan tjänsten startar eller så måste du utföra en åtgärd med utökade privilegier. Exempel:

* Konfigurera och initiera miljövariabler som tjänstens körbara fil behöver. Den är inte begränsad till enbart körbara filer som skrivits via Service Fabric programmerings modeller. npm.exe behöver till exempel vissa miljövariabler som har kon figurer ATS för att distribuera ett node.js-program.
* Konfigurera åtkomst kontroll genom att installera säkerhets certifikat.

Mer information om hur du konfigurerar **SetupEntryPoint** finns i [Konfigurera principen för en start punkt för tjänst konfiguration](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurera

### <a name="build-a-package-by-using-visual-studio"></a>Bygga ett paket med Visual Studio

Om du använde Visual Studio för att skapa ditt program, kan du använda kommandot *Package* för att automatiskt skapa ett paket som matchar den layout som beskrivs ovan.

Om du vill skapa ett paket högerklickar du på programprojektet i *Solution Explorer* och väljer **paket** kommandot:

![Paketera ett program med Visual Studio][vs-package-command]

När paketeringen är klar kan du hitta paketets placering i fönstret **utdata** . Förpacknings steget sker automatiskt när du distribuerar eller felsöker programmet i Visual Studio.

### <a name="build-a-package-by-command-line"></a>Bygga ett paket via kommando raden

Det är också möjligt att program mässigt Paketera upp ditt program med hjälp av `msbuild.exe` . Under huven körs Visual Studio så att resultatet är detsamma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Testa paketet

Du kan verifiera paket strukturen lokalt via PowerShell med kommandot [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .
Det här kommandot söker efter manifest tolknings problem och kontrollerar alla referenser. Detta kommando verifierar endast strukturernas struktur rätt för katalogerna och filerna i paketet.
Den verifierar inte någon kod eller ett data pakets innehåll utöver att kontrol lera att alla nödvändiga filer är tillgängliga.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Det här felet visar att *MySetup.bat* -filen som refereras i tjänst Manifestets **SetupEntryPoint** saknas i kod paketet. När den fil som saknas har lagts till skickas program verifieringen:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Om programmet har definierade [program parametrar](service-fabric-manage-multiple-environment-app-configuration.md) kan du skicka dem i [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) för korrekt verifiering.

Om du känner till klustret där programmet ska distribueras rekommenderar vi att du skickar den till- `ImageStoreConnectionString` parametern. I det här fallet verifieras även paketet mot tidigare versioner av programmet som redan körs i klustret. Till exempel kan verifieringen identifiera om ett paket med samma version, men ett annat innehåll redan har distribuerats.  

När programmet har paketerats korrekt och godkänts i valideringen bör du överväga att komprimera paketet för snabbare distributions åtgärder.

## <a name="compress-a-package"></a>Komprimera ett paket

När ett paket är stort eller har många filer kan du komprimera det för snabbare distribution. Komprimeringen minskar antalet filer och paketets storlek.
För ett komprimerat programpaket kan det ta längre tid att [Ladda upp programpaketet](service-fabric-deploy-remove-applications.md#upload-the-application-package) jämfört med att ladda upp det okomprimerade paketet, särskilt om komprimeringen görs som en del av kopian. Med komprimering går det snabbare att [Registrera](service-fabric-deploy-remove-applications.md#register-the-application-package) och [avregistrera program typen](service-fabric-deploy-remove-applications.md#unregister-an-application-type) .

Distributions mekanismen är densamma för komprimerade och okomprimerade paket. Om paketet är komprimerat lagras det som ett sådant i klustrets avbildnings Arkiv och komprimeras inte på noden innan programmet körs.
Komprimeringen ersätter det giltiga Service Fabric-paketet med den komprimerade versionen. Mappen måste tillåta Skriv behörighet. Att köra komprimering på redan komprimerade paket ger inga ändringar.

Du kan komprimera ett paket genom att köra PowerShell [-kommandot Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) med `CompressPackage` switch. Du kan expandera paketet med samma kommando, med hjälp av `UncompressPackage` switch.

Följande kommando komprimerar paketet utan att kopiera det till avbildnings arkivet. Du kan kopiera ett komprimerat paket till ett eller flera Service Fabric kluster, efter behov, med hjälp av [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) utan `SkipCopy` flaggan.
Paketet innehåller nu zippade filer för `code` `config` paketen, och `data` . Program manifestet och tjänst manifesten är inte zippade eftersom de behövs för många interna åtgärder. Till exempel, paket delning, program typs namn och versions extrahering för vissa valideringar behöver du åtkomst till manifesten. Genom att zippa upp manifesten skulle dessa åtgärder bli ineffektiva.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Du kan också komprimera och kopiera paketet med [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) i ett enda steg.
Om paketet är stort, anger du en hög tillräckligt lång tids gräns för att tillåta tiden för både paket komprimering och uppladdning till klustret.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internt beräknar Service Fabric kontroll summor för programpaketen för verifiering. När du använder komprimering beräknas kontroll summorna på de zippade versionerna av varje paket. Om du skapar ett nytt zip från samma programpaket skapas olika kontroll summor. Använd [diff-etablering](service-fabric-application-upgrade-advanced.md)för att förhindra verifierings fel. Med det här alternativet ska du inte inkludera de oförändrade paketen i den nya versionen. Referera istället till dem direkt från det nya tjänst manifestet.

Om differentierings etableringen inte är ett alternativ och du måste inkludera paketen genererar du nya versioner för `code` -, `config` -och- `data` paketen för att undvika felaktig kontroll summa. Att generera nya versioner för ej ändrade paket är nödvändigt när ett komprimerat paket används, oavsett om den tidigare versionen använder komprimering eller inte.

Paketet är nu korrekt paketerat, verifierat och komprimerat (vid behov), så det är klart för [distribution](service-fabric-deploy-remove-applications.md) till ett eller flera Service Fabric-kluster.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprimera paket vid distribution med Visual Studio

Du kan instruera Visual Studio att komprimera paket vid distribution genom att lägga till `CopyPackageParameters` elementet i publicerings profilen och ange `CompressPackage` attributet till `true` .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Skapa en sfpkg

Från och med version 6,1 tillåter Service Fabric etablering från en extern lagrings plats.
Med det här alternativet behöver programpaketet inte kopieras till avbildnings arkivet. I stället kan du skapa en `sfpkg` och överföra den till en extern lagrings plats och sedan ange hämtnings-URI: n för att Service Fabric vid etablering. Samma paket kan tillhandahållas till flera kluster. Etableringen från den externa butiken sparar tiden som krävs för att kopiera paketet till varje kluster.

`sfpkg`Filen är ett zip som innehåller det första programpaketet och har tillägget ". sfpkg".
I zip-paketet kan programpaketet komprimeras eller expanderas. Komprimeringen av programpaketet i zip-paketet görs vid kod-, konfigurations-och data paket nivåer, som [tidigare nämnts](service-fabric-package-apps.md#compress-a-package).

För att skapa en `sfpkg` , börjar du med en mapp som innehåller det ursprungliga programpaketet, komprimerat eller inte. Använd sedan valfritt verktyg för att zippa mappen med tillägget ". sfpkg". Använd till exempel [ZipFile. CreateFromDirectory](/dotnet/api/system.io.compression.zipfile.createfromdirectory#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg`Måste överföras till det externa lagret out-of-band, utanför Service Fabric. Den externa lagrings platsen kan vara vilken butik som helst som exponerar en REST-http-eller HTTPS-slutpunkt. Under etableringen kör Service Fabric en GET-åtgärd för att ladda ned `sfpkg` programpaketet så att arkivet måste tillåta Läs åtkomst för paketet.

Om du vill etablera paketet använder du extern etablering, som kräver nedladdnings-URI och program typs information.

>[!NOTE]
> Etablering som baseras på relativ sökväg för avbildnings lager stöder för närvarande inte `sfpkg` filer. Därför `sfpkg` bör inte kopieras till avbildnings arkivet.

## <a name="next-steps"></a>Nästa steg

[Distribuera och ta bort program][10] beskriver hur du använder PowerShell för att hantera program instanser

Att [hantera program parametrar för flera miljöer][11] beskriver hur du konfigurerar parametrar och miljövariabler för olika program instanser.

[Konfigurera säkerhets principer för programmet][12] beskriver hur du kör tjänster under säkerhets principer för att begränsa åtkomsten.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
