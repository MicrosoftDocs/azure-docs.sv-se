---
title: Exempel på Azure Service Fabric container Application-manifest
description: Lär dig hur du konfigurerar inställningar för program-och tjänst manifest för ett Service Fabric program med flera behållare.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701174"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exempel på program med flera container och tjänstmanifest
Följande är exempel på program-och tjänst manifest för ett Service Fabric program med flera behållare. Syftet med dessa exempel är att visa vilka inställningar som är tillgängliga och hur de används. Dessa program-och tjänst manifest baseras på [Windows Server 2016-exempel](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) manifest för behållare.

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [Åsidosätt miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurera mappning av behållarens port-till-värd](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [Konfigurera autentisering av container register](service-fabric-get-started-containers.md#configure-container-repository-authentication), [resurs styrning](service-fabric-resource-governance.md), [Ange isolerings läge](service-fabric-get-started-containers.md#configure-isolation-mode), [Ange OS-specifika behållar avbildningar](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService-tjänst manifest](#frontendservice-service-manifest)| [Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurera en slut punkt](service-fabric-get-started-containers.md#configure-communication), skicka kommandon till behållaren, [Importera ett certifikat till en behållare](service-fabric-securing-containers.md)| 
|[BackEndService-tjänst manifest](#backendservice-service-manifest)|[Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [Konfigurera en slut punkt](service-fabric-get-started-containers.md#configure-communication), [Konfigurera volym driv rutin](service-fabric-containers-volume-logging-drivers.md)| 

Se [program manifest](#application-manifest-elements)element, [FrontEndService tjänst manifest element](#frontendservice-service-manifest-elements)och [BackEndService tjänst manifest element](#backendservice-service-manifest-elements) för mer information om specifika XML-element.

## <a name="application-manifest"></a>Programmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService-tjänst manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService-tjänst manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Princip element
Beskriver principer (slut punkts bindning, paket delning, kör som och säkerhets åtkomst) som ska tillämpas på det importerade tjänst manifestet. Mer information finns i [princip element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definierar den resurs styrnings princip som tillämpas på hela tjänst paketets nivå. Mer information finns i [ServicePackageResourceGovernancePolicy-element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-element
Anger resurs gränser för ett kod paket. Mer information finns i [ResourceGovernancePolicy-element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies-element
Anger principer för att aktivera behållar värdar. Mer information finns i [ContainerHostPolicies-element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials-element
Autentiseringsuppgifter för behållar avbildnings lagrings plats att hämta avbildningar från. Mer information finns i [RepositoryCredentials-element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding-element
Anger vilken slut punkts resurs som ska bindas till den exponerade container porten. Mer information finns i [PortBinding-element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Volym element
Anger den volym som ska bindas till behållaren. Mer information finns i [volym element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption-element
Driv rutins alternativ som ska skickas till driv rutinen. Mer information finns i [DriverOption-element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides-element
Windows Server-behållare kanske inte är kompatibla i olika versioner av operativ systemet.  Du kan ange flera OS-avbildningar per behållare och tagga dem med build-versioner av operativ systemet. Hämta build-versionen av operativ systemet genom att köra "winver" i kommando tolken i Windows. Om det underliggande operativ systemet är version 16299 (Windows Server version 1709) väljer Service Fabric behållar avbildningen som taggats med OS = "16299". En otaggade behållar avbildning antas fungera i alla versioner av operativ systemet och åsidosätter avbildningen som anges i tjänst manifestet. Mer information finns i [ImageOverrides-element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Bild element
Behållar avbildning som motsvarar OS-Build-versions numret som ska startas. Om attributet OS inte anges antas behållar avbildningen fungera i alla versioner av operativ systemet och åsidosätter avbildningen som anges i tjänst manifestet. Mer information finns i [bild element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides-element
 Mer information finns i [EnvironmentOverrides-element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Miljö variabel. Mer information finns i [EnvironmentVariable-element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef-element
Anger information om ett X509-certifikat som ska exponeras för behållar miljön. Certifikatet måste vara installerat i LocalMachine-arkivet för alla klusternoder.
När programmet startar läser körnings programmet certifikatet och genererar en PFX-fil och ett lösen ord (i Windows) eller en PEM-fil (på Linux).
PFX-filen och lösen ordet är tillgängliga i behållaren med hjälp av variablerna Certificates_ServicePackageName_CodePackageName_CertName_PFX och Certificates_ServicePackageName_CodePackageName_CertName_Password miljövariabler. PEM-filen är tillgänglig i behållaren med hjälp av variablerna Certificates_ServicePackageName_CodePackageName_CertName_PEM och Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey-miljövariabeln. Mer information finns i [CertificateRef-element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices-element
Deklarerar tjänst instanser som skapas automatiskt när ett program instansieras mot den här program typen. Mer information finns i [DefaultServices-element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Tjänst element
Deklarerar en tjänst som skapas automatiskt när programmet instansieras. Mer information finns i [tjänst element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService-element
Definierar en tillstånds lös tjänst. Mer information finns i [StatelessService-element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Manifest element för FrontEndService-tjänst
### <a name="servicemanifest-element"></a>ServiceManifest-element
I den här artikeln beskrivs tjänst typen och versionen. Den innehåller en lista över oberoende uppgraderings bara kod, konfiguration och data paket som tillsammans skapar ett tjänst paket för att stödja en eller flera tjänst typer. Resurser, diagnostikinställningar och tjänstens metadata, till exempel tjänst typ, hälso egenskaper och belastnings Utjämnings mått, anges också. Mer information finns i [ServiceManifest-element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes-element
Definierar vilka tjänst typer som stöds av en CodePackage i det här manifestet. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. Tjänst typer deklareras på manifest nivå och inte på kod paket nivå. Mer information finns i [ServiceTypes-element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beskriver en tillstånds lös tjänst typ. Mer information finns i [StatelessServiceType-element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage-element
Beskriver ett kod paket som stöder en definierad tjänst typ. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. De resulterande processerna förväntas registrera de tjänst typer som stöds vid körning. När det finns flera kod paket aktive ras alla när systemet söker efter en av de deklarerade tjänst typerna. Mer information finns i [CodePackage-element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint-element
Den körbara filen som anges av EntryPoint är vanligt vis den tids krävande tjänst värden. Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslut ATS. Den resulterande processen övervakas och startas om (börjar med SetupEntryPoint) om den skulle stängas av eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost-element
 Mer information finns i [ContainerHost-element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName-element
Lagrings platsen och avbildningen på [https://hub.docker.com](https://hub.docker.com) eller Azure Container Registry. Mer information finns i [ImageName-element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables-element
Skicka miljövariabler till din behållare eller exe-miljö.  Mer information finns i [EnvironmentVariables-element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Miljö variabel. Mer information finns i [EnvironmentVariable-element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp som heter med namnattributet, som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt med användardefinierade, nyckel värdes par inställningar som processen kan läsa tillbaka vid körning. Om endast ConfigPackage-versionen har ändrats under en uppgradering startas inte processen som körs om. I stället meddelar ett återanrop processen att konfigurations inställningarna har ändrats så att de kan läsas in dynamiskt. Mer information finns i [ConfigPackage-element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage-element
Deklarerar en mapp som heter med namnattributet, som innehåller statiska datafiler. Service Fabric kommer att återvinna alla EXEs och DLLHOSTs som anges i värd-och support paketen när något av de data paket som anges i tjänst manifestet uppgraderas. Mer information finns i [DataPackage-element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurs element
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomst till dessa resurser styrs via avsnitten principer och principer i applikations manifestet. Mer information finns i avsnittet [Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slut punkts element
Definierar slut punkter för tjänsten. Mer information finns i avsnittet [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slut punkts element
Mer information finns i [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Manifest element för BackEndService-tjänst
### <a name="servicemanifest-element"></a>ServiceManifest-element
I den här artikeln beskrivs tjänst typen och versionen. Den innehåller en lista över oberoende uppgraderings bara kod, konfiguration och data paket som tillsammans skapar ett tjänst paket för att stödja en eller flera tjänst typer. Resurser, diagnostikinställningar och tjänstens metadata, till exempel tjänst typ, hälso egenskaper och belastnings Utjämnings mått, anges också. Mer information finns i [ServiceManifest-element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes-element
Definierar vilka tjänst typer som stöds av en CodePackage i det här manifestet. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. Tjänst typer deklareras på manifest nivå och inte på kod paket nivå. Mer information finns i [ServiceTypes-element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beskriver en tillstånds lös tjänst typ. Mer information finns i [StatelessServiceType-element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage-element
Beskriver ett kod paket som stöder en definierad tjänst typ. När en tjänst instansieras mot någon av dessa tjänst typer aktive ras alla kod paket som deklarerats i det här manifestet genom att de startas. De resulterande processerna förväntas registrera de tjänst typer som stöds vid körning. När det finns flera kod paket aktive ras alla när systemet söker efter en av de deklarerade tjänst typerna. Mer information finns i [CodePackage-element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint-element
Den körbara filen som anges av EntryPoint är vanligt vis den tids krävande tjänst värden. Om du har en separat installations start punkt kan du undvika att behöva köra tjänst värden med hög behörighet under längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslut ATS. Den resulterande processen övervakas och startas om (börjar med SetupEntryPoint) om den skulle stängas av eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost-element
Mer information finns i [ContainerHost-element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName-element
Lagrings platsen och avbildningen på [https://hub.docker.com](https://hub.docker.com) eller Azure Container Registry. Mer information finns i [ImageName-element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Kommando element
Skicka en kommaavgränsad lista med kommandon till behållaren. Mer information finns i [kommandon element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables-element
Skicka miljövariabler till din behållare eller exe-miljö.  Mer information finns i [EnvironmentVariables-element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Miljö variabel. Mer information finns i [EnvironmentVariable-element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp som heter med namnattributet, som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt med användardefinierade, nyckel värdes par inställningar som processen kan läsa tillbaka vid körning. Om endast ConfigPackage-versionen har ändrats under en uppgradering startas inte processen som körs om. I stället meddelar ett återanrop processen att konfigurations inställningarna har ändrats så att de kan läsas in dynamiskt. Mer information finns i [ConfigPackage-element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurs element
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomst till dessa resurser styrs via avsnitten principer och principer i applikations manifestet. Mer information finns i avsnittet [Resources](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slut punkts element
Definierar slut punkter för tjänsten. Mer information finns i avsnittet [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slut punkts element
 Mer information finns i [slut punkts element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

