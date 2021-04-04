---
title: RunToCompletion semantik i Service Fabric
description: Beskriver RunToCompletion-semantik i Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000274"
---
# <a name="runtocompletion"></a>RunToCompletion

Från och med version 7,1 stöder Service Fabric semantiska **RunToCompletion** för [behållare][containers-introduction-link] och [körbara gäst][guest-executables-introduction-link] program. Dessa semantik gör det möjligt för program och tjänster som slutför en aktivitet och avslutas, i motsats till, som alltid kör program och tjänster.

Innan du fortsätter med den här artikeln rekommenderar vi att du får bekanta dig med [Service Fabric program modellen][application-model-link] och [Service Fabric värd modell][hosting-model-link].

> [!NOTE]
> RunToCompletion-semantik stöds för närvarande inte för tjänster som skrivits med hjälp av [Reliable Services][reliable-services-link] programmerings modell.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion semantik och specifikation
RunToCompletion-semantiken kan anges som en **ExecutionPolicy** när [du importerar ServiceManifest][application-and-service-manifests-link]. Den angivna principen ärvs av alla CodePackages som omfattar ServiceManifest. Följande ApplicationManifest.xml-kodfragment innehåller ett exempel.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** tillåter följande två attribut:
* **Typ:** **RunToCompletion** är för närvarande det enda tillåtna värdet för det här attributet.
* **Starta om:** Det här attributet anger den princip för omstart som tillämpas på CodePackages som omfattar ServicePack, vid ett haveri. En CodePackage som avslutas med en **slutkod som inte är noll** anses ha misslyckats. Tillåtna värden för det här attributet är **onFailure** och **aldrig** med **onFailure** som standard.

När en princip för omstart har angetts till **onFailure**, om någon CodePackage Miss lyckas **(slutkod som inte är noll)**, startas den om, med Server delar mellan upprepade fel. När en princip för omstart har ställts in på **Never**, om något CodePackage Miss lyckas, markeras distributions status för DeployedServicePackage som **misslyckad** men andra CodePackages tillåts fortsätta att köras. Om alla CodePackages som utgörs av ServicePack som har körts till slutförd **(slutkod 0)**, markeras distributions status för DeployedServicePackage som **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Slutför exempel med RunToCompletion-semantik

Nu ska vi titta på ett komplett exempel med RunToCompletion-semantik.

> [!IMPORTANT]
> I följande exempel förutsätter vi att du skapar [Windows container-program med hjälp av Service Fabric och Docker][containers-getting-started-link].
>
> Det här exemplet hänvisar till mcr.microsoft.com/windows/nanoserver:1809. Windows Server-behållare är inte kompatibla i alla versioner av ett värd operativ system. Mer information finns i [kompatibilitet med Windows container version](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Följande ServiceManifest.xml beskriver ett ServicePack som består av två CodePackages, som representerar behållare. *RunToCompletionCodePackage1* loggar bara ett meddelande i **STDOUT** och avslutas. *RunToCompletionCodePackage2* pingar loopback-adressen för en stund och avslutas sedan med slut koden **0**, **1** eller **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Följande ApplicationManifest.xml beskriver ett program baserat på ServiceManifest.xml som beskrivs ovan. Den anger **RunToCompletion** - **ExecutionPolicy** för *WindowsRunToCompletionServicePackage* med en omstarts princip för **onFailure**. Vid aktivering av *WindowsRunToCompletionServicePackage* startas dess komponent-CodePackages. *RunToCompletionCodePackage1* bör avslutas när den första aktiveringen är klar. *RunToCompletionCodePackage2* kan dock Miss Missing **(slutkod som inte är noll)**, vilket innebär att den startas om sedan principen för omstart är **onFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Fråga distributions status för en DeployedServicePackage
Distributions status för en DeployedServicePackage kan frågas från PowerShell med [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] eller från C# med hjälp av [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (sträng, URI, sträng)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Att tänka på när du använder RunToCompletion-semantik

Följande punkter måste anges för det aktuella RunToCompletion-stödet.
* Dessa semantik stöds endast för [behållare][containers-introduction-link] och [körbara gäst][guest-executables-introduction-link] program.
* Uppgraderings scenarier för program med RunToCompletion-semantik är inte tillåtna. Användarna bör ta bort och återskapa dessa program, om det behövs.
* Redundansväxling kan orsaka att CodePackages körs igen efter att den slutförts, på samma nod eller andra noder i klustret. Exempel på redundansväxling är, noden startas om och Service Fabric körnings uppgraderingar på en nod.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
