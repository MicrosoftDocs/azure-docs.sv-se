---
title: Distribuera en befintlig körbar fil till Azure Service Fabric
description: Lär dig hur du paketerar ett befintligt program som en körbar gäst fil, så att det kan distribueras till ett Service Fabric kluster.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 72fde75e16341164106bb952d0bb66b83be744e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86259262"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Paketera och distribuera en befintlig körbar fil till Service Fabric

När du packar en befintlig körbar fil som en [gäst fil](service-fabric-guest-executables-introduction.md)kan du välja att använda en Visual Studio-projektmall eller [skapa programpaketet manuellt](#manually). Med Visual Studio skapas programpaketets struktur och manifest filen av den nya projekt mal len åt dig.

> [!TIP]
> Det enklaste sättet att paketera en befintlig Windows-fil i en tjänst är att använda Visual Studio och Linux för att använda Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Använda Visual Studio för att paketera och distribuera en befintlig körbar fil

I Visual Studio finns en Service Fabric tjänstmall som hjälper dig att distribuera en körbar gäst fil till ett Service Fabric-kluster.

1. Välj **Arkiv**  >  **nytt projekt** och skapa ett Service Fabric-program.
2. Välj **gäst-körbara filer** som tjänst mal len.
3. Klicka på **Bläddra** för att välja mappen med den körbara filen och fyll i resten av parametrarna för att skapa tjänsten.
   * *Kod pakets beteende*. Kan ställas in så att allt innehåll i din mapp kopieras till Visual Studio-projektet, vilket är användbart om den körbara filen inte ändras. Om du förväntar dig att den körbara filen ska ändras och du vill kunna hämta nya versioner dynamiskt, kan du välja att länka till mappen i stället. Du kan använda länkade mappar när du skapar programprojektet i Visual Studio. Detta länkar till käll platsen inifrån projektet, vilket gör det möjligt för dig att uppdatera den körbara gäst filen på käll platsen. Dessa uppdateringar blir en del av programpaketet när de skapas.
   * *Programmet* anger den körbara fil som ska köras för att starta tjänsten.
   * *Argument* anger de argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
   * *WorkingFolder* anger arbets katalogen för processen som ska startas. Du kan ange tre värden:
     * `CodeBase` anger att arbets katalogen ska ställas in på kod katalogen i programpaketet ( `Code` katalogen som visas i den föregående fil strukturen).
     * `CodePackage` anger att arbets katalogen ska ställas in på programmets rot ( `GuestService1Pkg` visas i föregående fil struktur).
     * `Work` anger att filerna placeras i en under katalog som kallas arbete.
4. Namnge tjänsten och klicka på **OK**.
5. Om tjänsten behöver en slut punkt för kommunikation kan du nu lägga till protokollet, porten och typen i ServiceManifest.xml-filen. Exempel: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Du kan nu använda paketet och publicera åtgärden mot ditt lokala kluster genom att felsöka lösningen i Visual Studio. När du är klar kan du publicera programmet i ett fjärran slutet kluster eller checka in lösningen till käll kontroll.
7. Läs [kontrol lera att programmet körs](#check-your-running-application) för att se hur du kan visa den körbara gäst tjänsten som körs i Service Fabric Explorer.

En exempel genom gång finns i [skapa ditt första gäst körbara program med Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Paketera flera körbara filer med Visual Studio

Du kan använda Visual Studio för att skapa ett programpaket som innehåller flera körbara gäst program. När du har lagt till den första gäst filen, högerklickar du på programprojektet och väljer **>ny Service Fabric tjänst** för att lägga till det andra körbara gäst programmet till lösningen.

> [!NOTE]
> Om du väljer att länka källan i Visual Studio-projektet och skapar Visual Studio-lösningen, ser du till att ditt programpaket är uppdaterat med ändringar i källan.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Använda Yeoman för att paketera och distribuera en befintlig körbar fil på Linux

Proceduren för att skapa och distribuera en gäst-körbar fil på Linux är detsamma som att distribuera ett csharp-eller Java-program.

1. I en terminal, skriver du in `yo azuresfguest`.
2. Namnge ditt program.
3. Namnge din tjänst och ange informationen inklusive sökvägen till den körbara filen och de parametrar som måste anropas med.

Yeoman skapar ett programpaket med rätt program-och MANIFEST-filer tillsammans med installations-och avinstallations skript.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Paketera flera körbara filer med Yeoman i Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg:

1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ange nödvändig information.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Paketera och distribuera en befintlig körbar fil manuellt

Processen för att packa en körbar gästen manuellt baseras på följande allmänna steg:

1. Skapa paket katalog strukturen.
2. Lägg till programmets kod och konfigurationsfiler.
3. Redigera tjänst manifest filen.
4. Redigera program manifest filen.

### <a name="create-the-package-directory-structure"></a>Skapa paket katalog strukturen

Du kan börja med att skapa katalog strukturen enligt beskrivningen i [Paketera en Azure Service Fabric-app](./service-fabric-package-apps.md).

### <a name="add-the-applications-code-and-configuration-files"></a>Lägg till programmets kod och konfigurationsfiler

När du har skapat katalog strukturen kan du lägga till programmets kod och konfigurationsfiler under kod-och konfigurations katalogerna. Du kan också skapa ytterligare kataloger eller under kataloger under kod-eller konfigurations katalogerna.

Service Fabric gör ett `xcopy` av innehållet i programmets rot Katalog, så det finns ingen fördefinierad struktur för att använda andra än att skapa två översta kataloger, kod och inställningar. (Du kan välja olika namn om du vill. Mer information finns i nästa avsnitt.)

> [!NOTE]
> Se till att du inkluderar alla filer och beroenden som programmet behöver. Service Fabric kopierar innehållet i programpaketet på alla noder i klustret där programmets tjänster ska distribueras. Paketet bör innehålla all kod som programmet behöver köra. Anta inte att beroendena redan har installerats.
>
>

### <a name="edit-the-service-manifest-file"></a>Redigera tjänst manifest filen

Nästa steg är att redigera tjänst manifest filen som innehåller följande information:

* Namnet på tjänst typen. Detta är ett ID som Service Fabric använder för att identifiera en tjänst.
* Det kommando som ska användas för att starta programmet (ExeHost).
* Alla skript som måste köras för att konfigurera programmet (SetupEntrypoint).

Följande är ett exempel på en `ServiceManifest.xml` fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Följande avsnitt går igenom de olika delarna av filen som du behöver uppdatera.

#### <a name="update-servicetypes"></a>Uppdatera ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Du kan välja valfritt namn som du vill använda `ServiceTypeName` . Värdet används i `ApplicationManifest.xml` filen för att identifiera tjänsten.
* Ange `UseImplicitHost="true"`. Det här attributet anger Service Fabric att tjänsten är baserad på en självständig app, vilket innebär att alla Service Fabric behöver göra är att starta den som en process och övervaka dess hälsa.

#### <a name="update-codepackage"></a>Uppdatera CodePackage
CodePackage-elementet anger platsen (och versionen) för tjänst koden.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name`Elementet används för att ange namnet på den katalog i programpaketet som innehåller tjänst koden. `CodePackage` har också `version` attributet. Detta kan användas för att ange versionen av koden och kan även användas för att uppgradera tjänst koden med hjälp av infrastrukturen för program livs cykel hantering i Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Valfritt: uppdatera SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint-elementet används för att ange alla körbara filer eller kommandofiler som ska köras innan tjänst koden startas. Det är ett valfritt steg, så det behöver inte inkluderas om det inte finns någon initiering som krävs. SetupEntryPoint körs varje gång tjänsten startas om.

Det finns bara en SetupEntryPoint, så konfigurations skript måste grupperas i en enda kommando fil om programmets konfiguration kräver flera skript. SetupEntryPoint kan köra alla typer av filer: körbara filer, batch-filer och PowerShell-cmdletar. Mer information finns i [Konfigurera SetupEntryPoint](service-fabric-application-runas-security.md).

I föregående exempel kör SetupEntryPoint en kommando fil `LaunchConfig.cmd` som finns i under `scripts` katalogen i kod katalogen (förutsatt att WorkingFolder-elementet är inställt på kodbas).

#### <a name="update-entrypoint"></a>Uppdatera start punkt

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

`EntryPoint`Elementet i tjänst manifest filen används för att ange hur tjänsten ska startas.

`ExeHost`Elementet anger den körbara filen (och argument) som ska användas för att starta tjänsten. Du kan också lägga till `IsExternalExecutable="true"` attributet för `ExeHost` att ange att programmet är en extern körbar fil utanför kod paketet. Till exempel `<ExeHost IsExternalExecutable="true">`.

* `Program` Anger namnet på den körbara fil som ska starta tjänsten.
* `Arguments` anger de argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
* `WorkingFolder` anger arbets katalogen för processen som ska startas. Du kan ange tre värden:
  * `CodeBase` anger att arbets katalogen ska ställas in på kod katalogen i programpaketet ( `Code` katalogen i föregående fil struktur).
  * `CodePackage` anger att arbets katalogen ska ställas in till roten för programpaketet ( `GuestService1Pkg` i föregående fil struktur).
    * `Work` anger att filerna placeras i en under katalog som kallas arbete.

WorkingFolder är användbart för att ange rätt arbets katalog så att relativa sökvägar kan användas av antingen programmet eller initierings skripten.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Uppdatera slut punkter och registrera dig för Naming Service för kommunikation

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

I föregående exempel `Endpoint` anger elementet de slut punkter som programmet kan lyssna på. I det här exemplet lyssnar Node.jss programmet på http på port 3000.

Dessutom kan du be Service Fabric att publicera slut punkten till Naming Service så att andra tjänster kan identifiera slut punkts adressen för den här tjänsten. På så sätt kan du kommunicera mellan tjänster som är körbara gäst program.
Adressen till den publicerade slut punkten är av typen `UriScheme://IPAddressOrFQDN:Port/PathSuffix` . `UriScheme` och `PathSuffix` är valfria attribut. `IPAddressOrFQDN` är IP-adressen eller det fullständiga domän namnet för den nod som den körbara filen ska placeras på och den beräknas åt dig.

I följande exempel visas en slut punkt som liknar `http://10.1.4.92:3000/myapp/` publicerad för tjänst instansen när tjänsten har distribuerats i Service Fabric Explorer. Eller om det är en lokal dator visas `http://localhost:3000/myapp/` .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Du kan använda dessa adresser med [omvänd proxy](service-fabric-reverseproxy.md) för att kommunicera mellan tjänster.

### <a name="edit-the-application-manifest-file"></a>Redigera program manifest filen

När du har konfigurerat `Servicemanifest.xml` filen måste du göra några ändringar i `ApplicationManifest.xml` filen för att säkerställa att rätt tjänst typ och namn används.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>Service manifest import

I `ServiceManifestImport` -elementet kan du ange en eller flera tjänster som du vill ta med i appen. Tjänsterna refereras till `ServiceManifestName` , som anger namnet på den katalog där `ServiceManifest.xml` filen finns.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurera loggning

För körbara gäst program är det bra att kunna se konsol loggar för att ta reda på om program-och konfigurations skripten visar eventuella fel.
Omdirigering av konsol kan konfigureras i `ServiceManifest.xml` filen med hjälp av- `ConsoleRedirection` elementet.

> [!WARNING]
> Använd aldrig konsolens omdirigerings policy i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. Använd *endast* detta för lokal utveckling och fel sökning.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` kan användas för att omdirigera utdata från konsolen (både STDOUT och stderr) till en arbets katalog. Detta ger möjlighet att kontrol lera att det inte finns några fel under installationen eller körningen av programmet i Service Fabric klustret.

`FileRetentionCount` anger hur många filer som sparas i arbets katalogen. Värdet 5 innebär till exempel att loggfilerna för de föregående fem körningarna lagras i arbets katalogen.

`FileMaxSizeInKb` anger den maximala storleken på loggfilerna.

Loggfiler sparas i en av tjänstens arbets kataloger. Ta reda på var filerna finns genom att använda Service Fabric Explorer för att avgöra vilken nod tjänsten körs på och vilken arbets katalog som används. Den här processen beskrivs längre fram i den här artikeln.

## <a name="deployment"></a>Distribution

Det sista steget är att [distribuera ditt program](service-fabric-deploy-remove-applications.md). Följande PowerShell-skript visar hur du distribuerar ditt program till det lokala utvecklings klustret och startar en ny Service Fabric-tjänst.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till avbildnings arkivet om paketet är stort eller har många filer. Läs mer [här](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

En Service Fabric tjänst kan distribueras i olika "konfigurationer". Den kan till exempel distribueras som en enda eller flera instanser, eller så kan den distribueras på ett sådant sätt att det finns en instans av tjänsten på varje nod i Service Fabric klustret.

`InstanceCount`Parametern för `New-ServiceFabricService` cmdleten används för att ange hur många instanser av tjänsten som ska startas i Service Fabric klustret. Du kan ställa in `InstanceCount` värdet, beroende på vilken typ av program du distribuerar. De två vanligaste scenarierna är:

* `InstanceCount = "1"`. I det här fallet distribueras bara en instans av tjänsten i klustret. Service Fabric Scheduler bestämmer vilken nod tjänsten ska distribueras på.
* `InstanceCount ="-1"`. I det här fallet distribueras en instans av tjänsten på varje nod i Service Fabric klustret. Resultatet har en (och endast en) instans av tjänsten för varje nod i klustret.

Detta är en användbar konfiguration för klient program (till exempel en REST-slutpunkt), eftersom klient program måste "ansluta" till någon av noderna i klustret för att använda slut punkten. Den här konfigurationen kan också användas när till exempel alla noder i Service Fabric-klustret är anslutna till en belastningsutjämnare. Klient trafiken kan sedan distribueras över tjänsten som körs på alla noder i klustret.

## <a name="check-your-running-application"></a>Kontrol lera programmet som körs
I Service Fabric Explorer identifierar du noden där tjänsten körs. I det här exemplet körs den på Nod1:

![Nod där tjänsten körs](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Om du navigerar till-noden och bläddrar till programmet visas viktig information om noden, inklusive dess plats på disken.

![Plats på disk](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Om du bläddrar till katalogen med hjälp av Server Explorer kan du hitta arbets katalogen och tjänstens loggfil, som visas på följande skärm bild:

![Loggens plats](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du paketerar en körbar gäst fil och distribuerar den till Service Fabric. Se följande artiklar för relaterad information och uppgifter.

* [Exempel på paketering och distribution av en körbar gäst fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till för hands versionen av packnings verktyget
* [Exempel på två körbara gäst program (C# och NodeJS) som kommunicerar via namngivnings tjänsten med REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Skapa ditt första Service Fabric-program med Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
