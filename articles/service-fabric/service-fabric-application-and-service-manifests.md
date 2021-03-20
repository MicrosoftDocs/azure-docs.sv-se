---
title: Beskriva Azure Service Fabric-appar och-tjänster
description: Beskriver hur manifest används för att beskriva Service Fabric-program och-tjänster.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86248228"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric program-och tjänst manifest
I den här artikeln beskrivs hur Service Fabric-program och-tjänster definieras och versions hantering med hjälp av ApplicationManifest.xml-och ServiceManifest.xml-filer.  Mer detaljerade exempel finns i [exempel på program-och tjänst manifest](service-fabric-manifest-examples.md).  XML-schemat för dessa MANIFEST-filer dokumenteras i [dokumentation om ServiceFabricServiceModel. xsd-schema](service-fabric-service-model-schema.md).

> [!WARNING]
> Manifest-XML-filschemat tillämpar korrekt ordning för underordnade element.  Som en del lösning öppnar du "C:\Program\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" i Visual Studio när du redigerar eller ändrar något av de Service Fabric manifesten. På så sätt kan du kontrol lera ordningen för underordnade element och ger Intelli.

## <a name="describe-a-service-in-servicemanifestxml"></a>Beskriv en tjänst i ServiceManifest.xml
Tjänst manifestet definierar tjänst typ och version. Den anger tjänstens metadata som tjänst typ, hälso egenskaper, belastnings Utjämnings mått, binärfiler för tjänster och konfigurationsfiler.  På ett annat sätt beskrivs kod, konfiguration och data paket som utgör ett tjänst paket som stöder en eller flera tjänst typer. Ett tjänst manifest kan innehålla flera kod-, konfigurations-och data paket, som kan vara versioner oberoende av varandra. Här är ett tjänst manifest för ASP.NET Core webb klient tjänst för [röstnings exempel programmet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här följer några [mer detaljerade exempel](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Versionens** attribut är ostrukturerade strängar och parsas inte av systemet. Versions-attribut används för att version av varje komponent för uppgraderingar.

**ServiceTypes** deklarerar vilka tjänst typer som stöds av **CodePackages** i det här manifestet. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. De resulterande processerna förväntas registrera de tjänst typer som stöds vid körning. Tjänst typer deklareras på manifest nivå och inte på kod paket nivå. Så när det finns flera kod paket aktive ras alla när systemet söker efter någon av de deklarerade tjänst typerna.

Den körbara filen som anges av **EntryPoint** är vanligt vis den tids krävande tjänst värden. **SetupEntryPoint** är en privilegie rad start punkt som körs med samma autentiseringsuppgifter som Service Fabric (vanligt vis *LocalSystem* -kontot) innan någon annan start punkt.  Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Den körbara filen som anges av **EntryPoint** körs när **SetupEntryPoint** har avslut ATS. Om processen någonsin avslutas eller kraschar övervakas och startas den resulterande processen om (börjar med **SetupEntryPoint**).  

Vanliga scenarier för att använda **SetupEntryPoint** är när du kör en körbar fil innan tjänsten startar eller om du utför en åtgärd med utökade privilegier. Exempel:

* Konfigurera och initiera miljövariabler som tjänstens körbara fil behöver. Detta är inte begränsat till enbart körbara filer som skrivits via Service Fabric programmerings modeller. npm.exe behöver till exempel vissa miljövariabler som har kon figurer ATS för att distribuera ett node.js-program.
* Konfigurera åtkomst kontroll genom att installera säkerhets certifikat.

Mer information om hur du konfigurerar SetupEntryPoint finns i [Konfigurera principen för en start punkt för tjänst konfiguration](service-fabric-application-runas-security.md)

**EnvironmentVariables** (inte angivet i föregående exempel) innehåller en lista över miljövariabler som har angetts för det här kod paketet. Miljövariabler kan åsidosättas i `ApplicationManifest.xml` för att ge olika värden för olika tjänst instanser. 

**DataPackage** (inte inställt i föregående exempel) deklarerar en mapp, med **namnet av namnattributet,** som innehåller godtyckligt statiska data som ska konsumeras av processen vid körnings tillfället.

**ConfigPackage** deklarerar en mapp som heter med **namnattributet,** som innehåller en *Settings.xml* -fil. Inställnings filen innehåller avsnitt av användardefinierad, nyckel värdes par inställningar som processen läser tillbaka vid körning. Om endast **ConfigPackage** - **versionen** har ändrats under en uppgradering startas inte processen som körs om. I stället meddelar ett återanrop processen att konfigurations inställningarna har ändrats så att de kan läsas in dynamiskt. Här är ett exempel på en *Settings.xml* -fil:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

En Service Fabric tjänst **slut punkt** är ett exempel på en Service Fabric resurs. En Service Fabric resurs kan deklareras/ändras utan att den kompilerade koden ändras. Åtkomst till de Service Fabric-resurser som anges i tjänst manifestet kan styras via **SecurityGroup** i applikations manifestet. När en slut punkts resurs definieras i tjänst manifestet tilldelar Service Fabric portar från det reserverade programmets port intervall när en port inte anges explicit. Läs mer om att [Ange eller åsidosätta slut punkts resurser](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Genom att definiera statiska portar ska inte överlappa med det applikations port intervall som anges i ClusterManifest. Om du anger en statisk port tilldelar du den utanför program port intervallet, annars leder det till port konflikter. Med version 6.5 CU2 kommer vi att utfärda en **hälso varning** när vi identifierar en sådan konflikt men låter distributionen fortsätta att synkroniseras med den levererade 6,5 beteendet. Vi kan dock förhindra program distributionen från nästa större versioner.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Beskriv ett program i ApplicationManifest.xml
Program manifestet beskriver till exempel program typen och versionen. Den anger tjänstens sammansättnings-metadata, till exempel stabila namn, partitionerings schema, instans antal/replikeringsräknare, säkerhets-/isolerings princip, placerings begränsningar, konfigurations åsidosättningar och komponent tjänst typer. De belastnings Utjämnings domäner som programmet placeras i beskrivs också.

Därför beskriver ett program manifest element på program nivå och refererar till ett eller flera tjänst manifest för att skapa en program typ. Här är applikations manifestet för [röstnings exempel programmet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (och här följer några [mer detaljerade exempel](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Precis som tjänst manifest är **versions** -attribut ostrukturerade strängar och parsas inte av systemet. Versionens attribut används också för att konfigurera varje komponent för uppgraderingar.

**Parametrar** definierar de parametrar som används i applikations manifestet. Värdena för dessa parametrar kan anges när programmet instansieras och kan åsidosätta program-eller tjänst konfigurations inställningar.  Standardvärdet för parametern används om värdet inte ändras när programmet instansieras. Information om hur du hanterar olika program-och tjänst parametrar för enskilda miljöer finns i [hantera program parametrar för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md).

**Service manifest import** innehåller referenser till tjänst manifest som utgör den här program typen. Ett program manifest kan innehålla flera tjänst manifest importer, och var och en kan vara versions oberoende av varandra. Importerade tjänst manifest avgör vilka tjänst typer som är giltiga inom den här program typen. I service manifest import åsidosätter du konfigurations värden i Settings.xml-och miljövariabler i ServiceManifest.xml filer. **Principer** (har inte angetts i föregående exempel) för slut punkts bindning, säkerhet och åtkomst och paket delning kan anges för importerade tjänst manifest.  Mer information finns i [Konfigurera säkerhets principer för ditt program](service-fabric-application-runas-security.md).

**DefaultServices** deklarerar tjänst instanser som skapas automatiskt när ett program instansieras mot den här program typen. Standard tjänster är bara en bekvämlighet och fungerar som vanliga tjänster i alla avseenden när de har skapats. De uppgraderas tillsammans med andra tjänster i program instansen och kan också tas bort. Ett program manifest kan innehålla flera standard tjänster.

**Certifikat** (som inte anges i föregående exempel) deklarerar de certifikat som används för att [Konfigurera https-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) eller [kryptera hemligheter i applikations manifestet](service-fabric-application-secret-management.md).

**Placerings begränsningar** är de instruktioner som definierar var tjänsterna ska köras. Dessa instruktioner är kopplade till enskilda tjänster som du väljer för en eller flera Node-egenskaper. Mer information finns i [placerings begränsningar och syntax för Node Property](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax)

**Principer** (som inte anges i föregående exempel) beskriver logg samlingen, [standard körnings-som-](service-fabric-application-runas-security.md), [hälso](service-fabric-health-introduction.md#health-policies)-och [säkerhets åtkomst](service-fabric-application-runas-security.md) principer som anges på program nivå, inklusive om tjänsten eller tjänsten har åtkomst till den Service Fabric körningen.

> [!NOTE] 
> Som standard har Service Fabric-program åtkomst till Service Fabric runtime, i form av en slut punkt som accepterar programspecifika begär Anden och miljövariabler som pekar på fil Sök vägar på värden som innehåller infrastrukturer och programspecifika filer. Överväg att inaktivera den här åtkomsten när programmet är värd för ej betrodd kod (d.v.s. kod vars härkomst är okänd eller som program ägaren inte vet vara säker för körning). Mer information finns [i rekommenderade säkerhets metoder i Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Huvud konton** (som inte anges i föregående exempel) beskriver de säkerhets objekt (användare eller grupper) som krävs för att [köra tjänster och säkra tjänst resurser](service-fabric-application-runas-security.md).  Säkerhets objekt refereras till i avsnittet **principer** .





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Nästa steg
- [Paketera ett program](service-fabric-package-apps.md) och gör det redo att distribuera det.
- [Distribuera och ta bort program](service-fabric-deploy-remove-applications.md).
- [Konfigurera parametrar och miljövariabler för olika program instanser](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurera säkerhets principer för ditt program](service-fabric-application-runas-security.md).
- [Konfigurera https-slutpunkter](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Kryptera hemligheter i applikations manifestet](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
