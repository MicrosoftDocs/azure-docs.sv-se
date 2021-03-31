---
title: Köra en tjänst under system-och lokala säkerhets konton
description: Lär dig hur du kör ett Service Fabric program under system-och lokala säkerhets konton.  Skapa säkerhets objekt och tillämpa Run-As-principen för att på ett säkert sätt köra dina tjänster.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610138"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Köra en tjänst som ett lokalt användar konto eller lokalt system konto
Genom att använda Azure Service Fabric kan du skydda program som körs i klustret under olika användar konton. Som standard körs Service Fabric-program under det konto som Fabric.exe processen körs under. Service Fabric ger också möjlighet att köra program under ett lokalt användar-eller system konto. Lokala system konto typer som stöds är **lokal användare**, **NetworkService**, **LocalService** och **LocalSystem**.  Om du kör Service Fabric på ett fristående Windows-kluster kan du köra en tjänst under [Active Directory domän konton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänst konton](service-fabric-run-service-as-gmsa.md).

I applikations manifestet definierar du de användar konton som krävs för att köra tjänster eller säkra resurser i avsnittet **säkerhets objekt** . Du kan också definiera och skapa användar grupper så att en eller flera användare kan hanteras tillsammans. Detta är användbart när det finns flera användare för olika tjänst start punkter och de behöver vanliga behörigheter på grupp nivå.  Användarna refereras sedan till i en RunAs-princip som tillämpas på en specifik tjänst eller alla tjänster i programmet. 

Som standard tillämpas RunAs-principen på huvud start punkten.  Du kan också använda en RunAs-princip för installations start punkten, om du behöver [köra vissa åtgärder med hög behörighet under ett system konto](service-fabric-run-script-at-service-startup.md)eller både huvud-och installations start punkter.  

> [!NOTE] 
> Om du använder en RunAs-princip för en tjänst och tjänst manifestet deklarerar slut punkts resurser med HTTP-protokollet måste du ange en **SecurityAccessPolicy**.  Mer information finns i [tilldela en säkerhets åtkomst princip för HTTP-och https-slutpunkter](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Köra en tjänst som lokal användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **lokal användare** -kontotyp anges i avsnittet säkerhets objekt i program manifestet skapar Service Fabric lokala användar konton på datorer där programmet distribueras. Som standard har dessa konton inte samma namn som de som anges i applikations manifestet (till exempel *Customer3* i följande program manifest exempel). De genereras i stället dynamiskt och har slumpmässiga lösen ord.

I avsnittet **runas policy** för en **service manifest import** anger du användar kontot från avsnittet **säkerhets objekt** för att köra service kod paketet.  I följande exempel visas hur du skapar en lokal användare och tillämpar en RunAs-princip på huvud start punkten:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Skapa en lokal användar grupp
Du kan skapa användar grupper och lägga till en eller flera användare i gruppen. Detta är användbart om det finns flera användare för olika tjänst start punkter och de måste ha vissa gemensamma behörigheter som är tillgängliga på grupp nivå. I följande program manifest exempel visas en lokal grupp med namnet *LocalAdminGroup* som har administratörs behörighet. Två användare, *Customer1* och *Customer2*, blir medlemmar i den här lokala gruppen. I avsnittet **service manifest import** används en runas-princip för att köra *Stateful1Pkg* -kod paketet som *Customer2*.  En annan RunAs-princip används för att köra *Web1Pkg* -kod paketet som *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Tillämpa en standard princip för alla service kod paket
Du använder avsnittet **DefaultRunAsPolicy** för att ange ett standard användar konto för alla kod paket som inte har någon angiven **runas policy** definierad. Om de flesta kod paket som anges i tjänst manifestet som används av ett program måste köras under samma användare, kan programmet bara definiera en standard-RunAs-princip med det användar kontot. I följande exempel anges att om ett kod paket inte har något angivet **runas policy** , ska kod paketet köras under den **MyDefaultAccount** -användare som anges i avsnittet säkerhets objekt.  Konto typer som stöds är lokal användare, NetworkService, LocalSystem och LocalService.  Om du använder en lokal användare eller tjänst anger du även konto namn och lösen ord.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Felsöka ett kod paket lokalt med omdirigering av konsol
Ibland är det användbart för fel söknings syfte att se konsolens utdata från en aktiv tjänst. Du kan ange en princip för omdirigering av konsol på Start punkten i tjänst manifestet, som skriver utdata till en fil. Filens utdata skrivs till programmappen som heter **log** på den klusternod där programmet distribueras och körs. 

> [!WARNING]
> Använd aldrig konsolens omdirigerings policy i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. Använd *endast* detta för lokal utveckling och fel sökning.  
> 
> 

Följande tjänst manifest exempel visar hur du aktiverar omdirigering av konsol med ett FileRetentionCount-värde:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Förstå program modellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänst manifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
