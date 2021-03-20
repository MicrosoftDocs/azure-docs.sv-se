---
title: Initierare CodePackages i Service Fabric
description: Beskriver CodePackages för initierare i Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258969"
---
# <a name="initializer-codepackages"></a>Initierare för CodePackages

Från och med version 7,1 stöder Service Fabric **initierare CodePackages** för [behållare][containers-introduction-link] och [körbara gäst][guest-executables-introduction-link] program. Initierare CodePackages ger möjlighet att utföra initieringar i ServicePack-området innan andra CodePackages börjar köras. Deras förhållande till ett ServicePack är detsamma som en [SetupEntryPoint][setup-entry-point-link] är för en CodePackage.

Innan du fortsätter med den här artikeln rekommenderar vi att du får bekanta dig med [Service Fabric program modellen][application-model-link] och [Service Fabric värd modell][hosting-model-link].

> [!NOTE]
> Initierare CodePackages stöds för närvarande inte för tjänster som skrivits med hjälp av [Reliable Services][reliable-services-link] programmerings modell.
 
## <a name="semantics"></a>Semantik

En initierare CodePackage förväntas köras för att **slutföras klart (slutkod 0)**. En misslyckad initierare CodePackage startas om tills den har slutförts. Flera initierare CodePackages tillåts och **körs i** **turordning**, **i en angiven ordning** innan andra CodePackages i servicepack börjar köras.

## <a name="specifying-initializer-codepackages"></a>Ange initierare CodePackages
Du kan markera en CodePackage som initierare genom att ange attributet **initierare** till **Sant** i ServiceManifest. När det finns flera initierare-CodePackages kan deras ordning för körning anges via attributet **ExecOrder** . **ExecOrder** måste vara ett icke-negativt heltal och är bara giltigt för CodePackages för initierare. CodePackages för initierare med lägre värden för **ExecOrder** körs först. Om **ExecOrder** inte har angetts för en initierare CodePackage antas standardvärdet 0. Den relativa körnings ordningen för initieraren CodePackages med samma värde för **ExecOrder** har inte angetts.

Följande ServiceManifest-kodfragment beskriver tre CodePackages två av som har marker ATS som initierare. När detta ServicePack aktive ras körs *InitCodePackage0* först eftersom det har det lägsta värdet för **ExecOrder**. Vid lyckad slut för ande (slutkod 0) av *InitCodePackage0* körs *InitCodePackage1* . Slutligen körs *WorkloadCodePackage* vid lyckad slut för ande av *InitCodePackage1*.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Slutför exempel med hjälp av initierare CodePackages

Nu ska vi titta på ett komplett exempel med hjälp av initierare CodePackages.

> [!IMPORTANT]
> I följande exempel förutsätter vi att du skapar [Windows container-program med hjälp av Service Fabric och Docker][containers-getting-started-link].
>
> Det här exemplet hänvisar till mcr.microsoft.com/windows/nanoserver:1809. Windows Server-behållare är inte kompatibla i alla versioner av ett värd operativ system. Mer information finns i [kompatibilitet med Windows container version](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Följande ServiceManifest.xml bygger på ServiceManifest-kodfragmentet som beskrivs ovan. *InitCodePackage0*, *InitCodePackage1* och *WorkloadCodePackage* är CodePackages som representerar behållare. Vid aktivering körs *InitCodePackage0* först. Den loggar ett meddelande till en fil och avslutas. Sedan körs *InitCodePackage1* som också loggar ett meddelande till en fil och avslutas. Slutligen börjar *WorkloadCodePackage* köras. Den loggar också ett meddelande till en fil och matar ut innehållet i filen till **STDOUT** och pingar sedan för alltid.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Följande ApplicationManifest.xml beskriver ett program baserat på ServiceManifest.xml som beskrivs ovan. Observera att den anger samma **volym** montering för alla behållare, d.v.s. **C:\WorkspaceOnHost** monteras på **C:\WorkspaceOnContainer** på alla tre behållare. Netto resultatet är att alla behållare skriver till samma loggfil i den ordning som de är aktiverade.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
När ServicePack har Aktiver ATS bör innehållet i **C:\WorkspaceOnHost\log.txt** vara följande.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för relaterad information.

* [Service Fabric och behållare.][containers-introduction-link]
* [Service Fabric och körbara gäst program.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
