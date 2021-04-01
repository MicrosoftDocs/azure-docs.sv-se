---
title: Exempel på Reliable Services-program manifest
description: Lär dig hur du konfigurerar inställningar för program-och tjänst manifest för ett Reliable Services Service Fabric-program.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701106"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exempel på Reliable Services-program och tjänstmanifest
Följande är exempel på program-och tjänst manifest för ett Service Fabric program med en ASP.NET Core webb klient del och en tillstånds känslig backend-server. Syftet med dessa exempel är att visa vilka inställningar som är tillgängliga och hur de används. Dessa program-och tjänst manifest är baserade på [Service Fabric .net-snabb starts](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) manifest.

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [resurs styrning](service-fabric-resource-governance.md), [kör en tjänst som ett lokalt administratörs konto](service-fabric-application-runas-security.md), [använder en standard princip för alla service kod paket](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [skapar användar-och grupp objekt](service-fabric-application-runas-security.md), delar ett data paket mellan tjänst instanser, [åsidosätter tjänstens slut punkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService-tjänst manifest| [Kör ett skript när tjänsten startas](service-fabric-run-script-at-service-startup.md), [definiera en HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService-tjänst manifest| [Deklarera ett konfigurations paket](service-fabric-application-and-service-manifests.md), [deklarera ett data paket](service-fabric-application-and-service-manifests.md), [Konfigurera en slut punkt](service-fabric-service-manifest-resources.md)| 

Se [program manifest](#application-manifest-elements)element, [VotingWeb tjänst manifest element](#votingweb-service-manifest-elements)och [VotingData tjänst manifest element](#votingdata-service-manifest-elements) för mer information om specifika XML-element.

## <a name="application-manifest"></a>Programmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

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
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb-tjänst manifest

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
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData-tjänst manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Program manifest element
### <a name="applicationmanifest-element"></a>ApplicationManifest-element
I den här artikeln beskrivs program typen och versionen. En eller flera tjänst manifest för komponent tjänsterna refereras till för att skapa en program typ. Konfigurations inställningar för komponent tjänsterna kan åsidosättas med hjälp av parametriserade program inställningar. Standard tjänster, tjänstmallar, huvud konton, principer, konfiguration av diagnostik och certifikat kan också deklareras på program nivå. Mer information finns i [ApplicationManifest-element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parameter element
Deklarerar de parametrar som används i det här applikations manifestet. Värdet för dessa parametrar kan anges när programmet instansieras och kan användas för att åsidosätta program-eller tjänst konfigurations inställningar. Mer information finns i [elementet Parameters](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameter element
En program parameter som ska användas i det här manifestet. Parametervärdet kan ändras när programmet instansieras, eller om inget värde anges används standardvärdet. Mer information finns i [parameter element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>Service manifest import-element
Importerar ett tjänst manifest som skapats av tjänste utvecklaren. Ett tjänst manifest måste importeras för varje komponent tjänst i programmet. Konfigurations åsidosättningar och principer kan deklareras för tjänst manifestet. Mer information finns i [service manifest import-element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef-element
Importerar tjänst manifestet efter referens. För närvarande måste tjänst manifest filen (ServiceManifest.xml) finnas i build-paketet. Mer information finns i [ServiceManifestRef-element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides-element
Anger åsidosättningar av resurser för slut punkter som har deklarerats i tjänst manifest resurser. Mer information finns i [ResourceOverrides-element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Slut punkts element
Slut punkt (er) som ska åsidosättas. Mer information finns i avsnittet [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Slut punkts element
Slut punkten, som deklarerats i tjänst manifestet, för att åsidosätta. Mer information finns i [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Princip element
Beskriver principer (slut punkts bindning, paket delning, kör som och säkerhets åtkomst) som ska tillämpas på det importerade tjänst manifestet. Mer information finns i [princip element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definierar den resurs styrnings princip som tillämpas på hela tjänst paketets nivå. Mer information finns i [ServicePackageResourceGovernancePolicy-element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-element
Anger resurs gränser för en codepackage. Mer information finns i [ResourceGovernancePolicy-element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy-element
Anger om en kod, en konfiguration eller ett data paket ska delas mellan tjänst instanser av samma tjänst typ. Mer information finns i [PackageSharingPolicy-element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy-element
Ger åtkomst behörighet till ett huvud konto på en resurs (till exempel en slut punkt) som definierats i ett tjänst manifest. Normalt är det mycket användbart att kontrol lera och begränsa åtkomsten till tjänster till olika resurser för att minimera säkerhets riskerna. Detta är särskilt viktigt när programmet byggs från en samling tjänster från en Marketplace som har utvecklats av olika utvecklare. Mer information finns i [SecurityAccessPolicy-element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>Runas policy-element
Anger det lokala användare eller lokala system konto som ett service kod paket ska köras som. Domän konton stöds på Windows Server-distributioner där Azure Active Directory är tillgängligt. Som standard körs program under det konto som Fabric.exe processen körs under. Program kan också köras som andra konton, vilket måste deklareras i avsnittet säkerhets objekt. Om du använder en RunAs-princip för en tjänst och tjänst manifestet deklarerar slut punkts resurser med HTTP-protokollet måste du också ange en SecurityAccessPolicy för att säkerställa att portarna som tilldelas dessa slut punkter är korrekt åtkomst – kontroll för det användar konto för RunAs som tjänsten kör under. För en HTTPS-slutpunkt måste du också definiera en EndpointBindingPolicy för att ange namnet på det certifikat som ska returneras till klienten. Mer information finns i [runas policy-element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices-element
Deklarerar tjänst instanser som skapas automatiskt när ett program instansieras mot den här program typen. Mer information finns i [DefaultServices-element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Tjänst element
Deklarerar en tjänst som skapas automatiskt när programmet instansieras. Mer information finns i [tjänst element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService-element
Definierar en tillstånds känslig tjänst. Mer information finns i [StatefulService-element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService-element
Definierar en tillstånds lös tjänst. Mer information finns i [StatelessService-element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Huvud objekts element
Beskriver säkerhets objekt (användare, grupper) som krävs för att det här programmet ska kunna köra tjänster och säkra resurser. Säkerhets objekt refereras till i avsnittet principer. Mer information finns i avsnittet [huvud element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Grupp element
Deklarerar en uppsättning grupper som säkerhets objekt, som kan refereras till i principer. Grupper är användbara om det finns flera användare för olika tjänst start punkter och de måste ha vissa gemensamma behörigheter som är tillgängliga på grupp nivå. Mer information finns i [elementet Groups](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Grupp element
Deklarerar en grupp som säkerhets objekt, som kan refereras till i principer. Mer information finns i [grupp element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Medlemskaps element
 Mer information finns i [medlemskaps element](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup-element
 Mer information finns i [SystemGroup-element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Användar element
Deklarerar en uppsättning användare som säkerhets objekt, som kan refereras till i principer. Mer information finns i [användar element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Användar element
Deklarerar en användare som säkerhets objekt, som kan refereras till i principer. Mer information finns i [användar element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf-element
Användare kan läggas till i alla befintliga medlemskaps grupper, så att de kan ärva alla egenskaper och säkerhets inställningar för medlemskaps gruppen. Medlemskaps gruppen kan användas för att skydda externa resurser som behöver nås av olika tjänster eller samma tjänst (på en annan dator). Mer information finns i [elementet memberOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup-element
System gruppen som användaren ska läggas till i.  System gruppen måste definieras i avsnittet grupper. Mer information finns i [SystemGroup-element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Grupp element
Gruppen att lägga till användaren i.  Gruppen måste definieras i avsnittet grupper. Mer information finns i [grupp element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Princip element
Beskriver principerna (logg insamling, standard körnings-som, hälso tillstånd och säkerhets åtkomst) som ska tillämpas på program nivå. Mer information finns i [princip element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy-element
Ange ett standard användar konto för alla service kods paket som inte har någon angiven runas policy definierad i avsnittet service manifest import. Mer information finns i [DefaultRunAsPolicy-element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Manifest element för VotingWeb-tjänst
### <a name="servicemanifest-element"></a>ServiceManifest-element
I den här artikeln beskrivs tjänst typen och versionen. Den innehåller en lista över oberoende uppgraderings bara kod, konfiguration och data paket som tillsammans skapar ett tjänst paket för att stödja en eller flera tjänst typer. Resurser, diagnostikinställningar och tjänstens metadata, till exempel tjänst typ, hälso egenskaper och belastnings Utjämnings mått, anges också. Mer information finns i [ServiceManifest-element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes-element
Definierar vilka tjänst typer som stöds av en CodePackage i det här manifestet. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. Tjänst typer deklareras på manifest nivå och inte på kod paket nivå. Mer information finns i [ServiceTypes-element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beskriver en tillstånds lös tjänst typ. Mer information finns i [StatelessServiceType-element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage-element
Beskriver ett kod paket som stöder en definierad tjänst typ. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. De resulterande processerna förväntas registrera de tjänst typer som stöds vid körning. När det finns flera kod paket aktive ras alla när systemet söker efter en av de deklarerade tjänst typerna. Mer information finns i [CodePackage-element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint-element
En privilegie rad start punkt som som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligt vis kontot NETWORKSERVICE) före någon annan start punkt. Den körbara filen som anges av EntryPoint är vanligt vis den tids krävande tjänst värden. Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Mer information finns i [SetupEntryPoint-element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost-element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program element
Namnet på den körbara filen.  Till exempel "MySetup.bat" eller "MyServiceHost.exe". Mer information finns i [program element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Argument element
 Mer information finns i [argument element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder-element
Arbets katalogen för processen i kod paketet på den klusternod där programmet har distribuerats. Du kan ange tre värden: arbete (standard), CodePackage eller kodbas. CodeBase anger att arbets katalogen har angetts till den katalog där EXE definieras i kod paketet. CodePackage anger att arbets katalogen ska vara roten i kod paketet oavsett var EXE-filen definieras i katalogen med kod paketet. Arbets katalogen anges till en unik mapp som skapas på noden.  Den här mappen är samma för hela program instansen. Som standard anges arbets katalogen för alla processer i programmet till mappen program arbete. Det är här som processerna kan skriva data. Att skriva data i kod paketet eller kodbasen rekommenderas inte eftersom dessa mappar kan delas mellan olika program instanser och kan tas bort. Mer information finns i [WorkingFolder-element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection-element

> [!WARNING]
> Använd inte konsolomdirigering i ett produktions program, Använd den endast för lokal utveckling och fel sökning. Omdirigerar konsol utdata från start skriptet till en utdatafil i programmappen med namnet "log" på klusternoden där programmet distribueras och körs. Mer information finns i [ConsoleRedirection-element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint-element
Den körbara filen som anges av EntryPoint är vanligt vis den tids krävande tjänst värden. Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslut ATS. Den resulterande processen övervakas och startas om (börjar med SetupEntryPoint) om den skulle stängas av eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost-element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp som heter med namnattributet, under PackageRoot som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt med användardefinierade, nyckel värdes par inställningar som processen kan läsa tillbaka vid körning. Om endast ConfigPackage-versionen har ändrats under en uppgradering startas inte processen som körs om. I stället meddelar ett återanrop processen att konfigurations inställningarna har ändrats så att de kan läsas in dynamiskt. Mer information finns i [ConfigPackage-element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurs element
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomst till dessa resurser styrs via avsnitten principer och principer i applikations manifestet. Mer information finns i avsnittet [Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slut punkts element
Definierar slut punkter för tjänsten. Mer information finns i avsnittet [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slut punkts element
Slut punkten, som deklarerats i tjänst manifestet, för att åsidosätta. Mer information finns i [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Manifest element för VotingData-tjänst
### <a name="servicemanifest-element"></a>ServiceManifest-element
I den här artikeln beskrivs tjänst typen och versionen. Den innehåller en lista över oberoende uppgraderings bara kod, konfiguration och data paket som tillsammans skapar ett tjänst paket för att stödja en eller flera tjänst typer. Resurser, diagnostikinställningar och tjänstens metadata, till exempel tjänst typ, hälso egenskaper och belastnings Utjämnings mått, anges också. Mer information finns i [ServiceManifest-element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes-element
Definierar vilka tjänst typer som stöds av en CodePackage i det här manifestet. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. Tjänst typer deklareras på manifest nivå och inte på kod paket nivå. Mer information finns i [ServiceTypes-element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType-element
Beskriver en tillstånds känslig tjänst typ. Mer information finns i [StatefulServiceType-element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage-element
Beskriver ett kod paket som stöder en definierad tjänst typ. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. De resulterande processerna förväntas registrera de tjänst typer som stöds vid körning. När det finns flera kod paket aktive ras alla när systemet söker efter en av de deklarerade tjänst typerna. Mer information finns i [CodePackage-element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint-element
Den körbara filen som anges av EntryPoint är vanligt vis den tids krävande tjänst värden. Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslut ATS. Den resulterande processen övervakas och startas om (börjar med SetupEntryPoint) om den skulle stängas av eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost-element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program element
Namnet på den körbara filen.  Till exempel "MySetup.bat" eller "MyServiceHost.exe". Mer information finns i [program element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder-element
Arbets katalogen för processen i kod paketet på den klusternod där programmet har distribuerats. Du kan ange tre värden: arbete (standard), CodePackage eller kodbas. CodeBase anger att arbets katalogen har angetts till den katalog där EXE definieras i kod paketet. CodePackage anger att arbets katalogen ska vara roten i kod paketet oavsett var EXE-filen definieras i katalogen med kod paketet. Arbets katalogen anges till en unik mapp som skapas på noden.  Den här mappen är samma för hela program instansen. Som standard anges arbets katalogen för alla processer i programmet till mappen program arbete. Det är här som processerna kan skriva data. Att skriva data i kod paketet eller kodbasen rekommenderas inte eftersom dessa mappar kan delas mellan olika program instanser och kan tas bort. Mer information finns i [WorkingFolder-element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp som heter med namnattributet, under PackageRoot som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt med användardefinierade, nyckel värdes par inställningar som processen kan läsa tillbaka vid körning. Om endast ConfigPackage-versionen har ändrats under en uppgradering startas inte processen som körs om. I stället meddelar ett återanrop processen att konfigurations inställningarna har ändrats så att de kan läsas in dynamiskt. Mer information finns i [ConfigPackage-element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage-element
Deklarerar en mapp som heter med namnattributet, under PackageRoot som innehåller statiska datafiler som ska användas av processen vid körning. Service Fabric kommer att återvinna alla EXEs och DLLHOSTs som anges i värd-och support paketen när något av de data paket som anges i tjänst manifestet uppgraderas. Mer information finns i [DataPackage-element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurs element
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomst till dessa resurser styrs via avsnitten principer och principer i applikations manifestet. Mer information finns i avsnittet [Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slut punkts element
Definierar slut punkter för tjänsten. Mer information finns i avsnittet [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slut punkts element
Slut punkten, som deklarerats i tjänst manifestet, för att åsidosätta. Mer information finns i [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

