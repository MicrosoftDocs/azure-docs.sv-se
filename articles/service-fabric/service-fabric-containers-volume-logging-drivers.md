---
title: Azure Files volym driv rutin för Service Fabric
description: Service Fabric stöder användning av Azure Files för att säkerhetskopiera volymer från din behållare.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247701"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Azure Files volym driv rutin för Service Fabric

Azure Files volym driv rutinen är ett [Docker-volym-plugin-program](https://docs.docker.com/engine/extend/plugins_volume/) som tillhandahåller [Azure Files](../storage/files/storage-files-introduction.md) -baserade volymer för Docker-behållare. Det paketeras som ett Service Fabric-program som kan distribueras till ett Service Fabric-kluster för att tillhandahålla volymer för andra Service Fabric behållar program i klustret.

> [!NOTE]
> Version 6.5.661.9590 av Azure Files volym-plugin-programmet har släppts för allmän tillgänglighet.
>

## <a name="prerequisites"></a>Förutsättningar
* Windows-versionen av Azure Files volym-plugin-programmet fungerar på [Windows Server version 1709](/windows-server/get-started/whats-new-in-windows-server-1709), [windows 10 version 1709](/windows/whats-new/whats-new-windows-10-version-1709) eller senare operativ system.

* Linux-versionen av Azure Files volym-plugin-programmet fungerar på alla operativ system versioner som stöds av Service Fabric.

* Plugin-programmet Azure Files volym fungerar bara på Service Fabric version 6,2 och senare.

* Följ anvisningarna i Azure Files- [dokumentationen](../storage/files/storage-how-to-create-file-share.md) för att skapa en fil resurs för Service Fabric behållar programmet som ska användas som volym.

* Du behöver [PowerShell med Service Fabric-modulen](./service-fabric-get-started.md) eller [SFCTL](./service-fabric-cli.md) installerat.

* Om du använder Hyper-V-behållare måste följande kodfragment läggas till i avsnittet ClusterManifest (lokalt kluster) eller fabricSettings i din Azure Resource Manager-mall (Azure-kluster) eller ClusterConfig.jspå (fristående kluster).

I ClusterManifest måste följande läggas till i avsnittet värd. I det här exemplet är volym namnet **sfazurefile** och porten som den lyssnar på klustret är **19100**. Ersätt dem med rätt värden för klustret.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

I avsnittet fabricSettings i din Azure Resource Manager-mall (för Azure-distributioner) eller ClusterConfig.jspå (för fristående distributioner) måste följande kodfragment läggas till. Ersätt sedan volym namn och port värden med dina egna.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Distribuera ett exempel program med hjälp av Service Fabric Azure Files volym driv rutin

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Använda Azure Resource Manager via det angivna PowerShell-skriptet (rekommenderas)

Om klustret är baserat i Azure rekommenderar vi att du distribuerar program till den med hjälp av Azure Resource Manager program resurs modell för enkel användning och hjälper dig att flytta till modellen för att underhålla infrastrukturen som kod. Den här metoden eliminerar behovet av att hålla koll på program versionen för Azure Files volym driv rutinen. Du kan också underhålla separata Azure Resource Manager mallar för varje operativ system som stöds. Skriptet förutsätter att du distribuerar den senaste versionen av Azure Files-programmet och tar parametrar för OS-typ, kluster prenumerations-ID och resurs grupp. Du kan ladda ned skriptet från [Service Fabric hämtnings plats](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Observera att detta automatiskt ställer in List List rutan, som är den port där Azure Files volym-plugin-programmet lyssnar efter förfrågningar från Docker daemon till 19100. Du kan ändra den genom att lägga till parametern "List namn". Se till att porten inte står i konflikt med andra portar som klustret eller programmen använder.
 

Azure Resource Manager distributions kommando för Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager distributions kommando för Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

När du har kört skriptet kan du gå vidare till [avsnittet Konfigurera ditt program.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manuell distribution för fristående kluster

Service Fabric programmet som tillhandahåller volymerna för dina behållare kan laddas ned från [hämtnings platsen för Service Fabric](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip). Programmet kan distribueras till klustret via [PowerShell](./service-fabric-deploy-remove-applications.md)-, [CLI](./service-fabric-application-lifecycle-sfctl.md) -eller FabricClient- [API: er](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Använd kommando raden för att ändra katalogen till rot katalogen för det hämtade programpaketet.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Kopiera sedan programpaketet till avbildnings arkivet med lämpliga värden för [ApplicationPackagePath] och [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registrera program typen

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Skapa programmet och betala nära åtgärd svärdet för **list** rutan. Det här värdet är den port där Azure Files volym-plugin-programmet lyssnar efter begär Anden från Docker daemon. Kontrol lera att den port som har angetts för programmet matchar VolumePluginPorts i ClusterManifest och inte står i konflikt med andra portar som klustret eller programmen använder.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Data Center stöder inte mappning av SMB-monteringar till behållare ([som endast stöds i Windows Server version 1709](/virtualization/windowscontainers/manage-containers/container-storage)). Den här begränsningen förhindrar nätverks volym mappning och Azure Files volym driv rutiner på versioner som är äldre än 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Distribuera programmet i ett lokalt utvecklings kluster
Följ steg 1-3 från [ovanstående.](#manual-deployment-for-standalone-clusters)

 Standard antalet tjänst instanser för Azure Files volym-plugin-programmet är-1, vilket innebär att en instans av tjänsten som distribueras till varje nod i klustret. Men när du distribuerar Azure Files volym-plugin-programmet i ett lokalt utvecklings kluster ska antalet tjänst instanser anges som 1. Detta kan göras via program parametern **InstanceCount** . Därför är kommandot för att skapa Azure Files volym-plugin-programmet i ett lokalt utvecklings kluster:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Konfigurera dina program så att de använder volymen
Följande kodfragment visar hur en Azure Files-baserad volym kan anges i program manifest filen för ditt program. Det specifika intresse elementet är **volym** tag gen:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Driv rutins namnet för Azure Files volym-plugin-programmet är **sfazurefile**. Det här värdet anges för attributet **driv rutin** för **volym** tag gen elementet i applikations manifestet.

I **volym** tag gen i kodfragmentet ovan kräver Azure Files volym-plugin-programmet följande attribut:
- **Källa** – det här är namnet på volymen. Användaren kan välja valfritt namn för volymen.
- **Mål** – det här attributet är den plats som volymen är mappad till i den behållare som körs. Därför kan målet inte vara en plats som redan finns i din behållare

Som du ser i **DriverOption** -elementen i kodfragmentet ovan stöder Azure Files volym-plugin-programmet följande driv rutins alternativ:
- **resurs** namn – namnet på den Azure Files fil resurs som tillhandahåller volymen för behållaren.
- **storageAccountName** – namnet på det Azure Storage-konto som innehåller den Azure Files fil resursen.
- **storageAccountKey** – åtkomst nyckel för Azure Storage-kontot som innehåller den Azure Files fil resursen.
- **storageAccountFQDN** – domän namn som är associerat med lagrings kontot. Om storageAccountFQDN inte anges skapas domän namnet med hjälp av standard suffixet (. File. Core. Windows. net) med storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Använda din egen volym eller loggnings driv rutin
Service Fabric också tillåta användning av dina egna anpassade [volymer](https://docs.docker.com/engine/extend/plugins_volume/) eller [loggnings](https://docs.docker.com/engine/admin/logging/overview/) driv rutiner. Om Docker-volym/loggnings driv rutinen inte är installerad på klustret kan du installera den manuellt med hjälp av RDP/SSH-protokollen. Du kan utföra installationen med dessa protokoll via ett start skript eller ett [SetupEntryPoint-skript](./service-fabric-application-model.md)med hjälp av en [skalnings uppsättning för virtuella datorer](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) .

Ett exempel på skriptet för att installera [Docker-volymens driv rutin för Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) är följande:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Om du ska använda den volym eller loggnings driv rutin som du har installerat i dina program måste du ange lämpliga värden i **volym** -och **LogConfig** -elementen under **ContainerHostPolicies** i program manifestet.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

När du anger ett volym-plugin-program skapar Service Fabric volymen automatiskt med hjälp av de angivna parametrarna. **Käll** tag gen för **volym** elementet är namnet på volymen och **driv rutins** tag gen anger plugin-programmet för volym driv rutinen. **Mål** tag gen är den plats som **källan** är mappad till i den behållare som körs. Därför kan målet inte vara en plats som redan finns i din behållare. Alternativ kan anges med **DriverOption** -taggen på följande sätt:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Program parametrar stöds för volymer som visas i föregående manifest kod avsnitt (leta efter `MyStorageVar` ett exempel som använder).

Om en Docker-logg driv rutin anges måste du distribuera agenter (eller behållare) för att hantera loggarna i klustret. Taggen **DriverOption** kan användas för att ange alternativ för logg driv rutinen.

## <a name="next-steps"></a>Nästa steg
* Om du vill se container exempel, inklusive volym driv rutinen, kan du gå till [Service Fabric container-exempel](https://github.com/Azure-Samples/service-fabric-containers)
* Information om hur du distribuerar behållare till ett Service Fabric kluster finns i artikeln [distribuera en behållare på Service Fabric](./service-fabric-get-started-containers.md)
