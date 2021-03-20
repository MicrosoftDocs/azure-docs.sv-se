---
title: Paketera och distribuera behållare
description: I den här självstudiekursen lär du dig skapa en Azure Service Fabric-programdefinition med Yeoman och att paketera programmet.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 995291a783d14a6d2db8ed8319c720f55c009d91
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92738859"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Självstudie: Paketera och distribuera containers som en Service Fabric-app med Yeoman

Den här självstudien är del två i en serie. I den här självstudien används ett verktyg för mallgenerering (Yeoman) för att skapa en Service Fabric-programdefinition. Programmet kan sedan användas för att distribuera containrar till Service Fabric. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installera Yeoman
> * Skapa ett programpaket med Yeoman
> * Konfigurera inställningar i programpaketet för användning med containrar
> * Skapa programmet
> * Distribuera och köra programmet
> * Rensa programmet

## <a name="prerequisites"></a>Förutsättningar

* De behållaravbildningar som överfördes till det Azure Container Registry som skapades i [del 1](service-fabric-tutorial-create-container-images.md) av denna självstudie används.
* [Konfigurerad](service-fabric-tutorial-create-container-images.md) Linux-utvecklingsmiljö.

## <a name="install-yeoman"></a>Installera Yeoman

Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa program från terminalen med en Yeoman-mallgenerator. Följ stegen nedan för att se till att du har Yeoman-mallgeneratorn.

1. Installera NodeJS och NPM på datorn. Mac OSX-användare måste använda pakethanteraren Homebrew

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. Installera Yeoman mallgenerator på maskinen från NPM

    ```bash
    npm install -g yo
    ```
3. Installera Service Fabric Yeoman-containergenerator

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Paketera en Docker-avbildningscontainer med Yeoman

1. Skapa ett Service Fabric-behållarprogram i mappen ”container-tutorial” i den klonade lagringsplatsen genom att köra följande kommando.

    ```bash
    yo azuresfcontainer
    ```
2. Ange ”TestContainer” som namn på programmet
3. Ange ”azurevotefront” som namn på tillämpningstjänsten.
4. Ange sökvägen till behållar avbildningen i ACR för klient delens lagrings platsen, till exempel " \<acrName> . azurecr.io/Azure-Vote-front:v1". \<acrName>Fältet måste vara detsamma som det värde som användes i föregående självstudie.
5. Tryck på Retur och lämna avsnittet Kommandon tomt.
6. Ange ett instansantal på 1.

Följande visar indata och utdata vid körning av yo-kommandot:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Om du vill lägga till en till containertjänst till ett program som redan har skapats med hjälp av Yeoman utför du följande steg:

1. Byt katalog en nivå till **TestContainer**-katalogen, till exempel *./TestContainer*
2. Kör `yo azuresfcontainer:AddService`
3. Kalla tjänsten ”azurevoteback”
4. Ange sökvägen för containeravbildningen – ”alpine:redis”
5. Tryck på Retur och lämna avsnittet Kommandon tomt
6. Ange ett instansantal på ”1”.

De poster som används för att lägga till tjänsten visas:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Under resten av den här självstudien kommer vi att arbeta i katalogen **TestContainer**. Till exempel *./TestContainer/TestContainer*. Katalogen ska innehålla följande.

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Konfigurera applikationsmanifestet med autentiseringsuppgifter för Azure Container Registry

Service Fabric behöver autentiseringsuppgifter för att hämta behållaravbildningar från Azure Container Registry. Dessa anges i **ApplicationManifest.xml**.

Logga in på din ACR-instans. Använd kommandot **az acr login** till att slutföra åtgärden. Ange det unika namn du angav för containerregistret när det skapades.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar ett meddelande om att **inloggningen har** slutförts.

Kör sedan följande kommando för att hämta lösenordet för containerregistret. Lösenordet används av Service Fabric för att autentisera med ACR och hämta containeravbildningarna.

```azurecli
az acr credential show -n <acrName> --query passwords[0].value
```

I **ApplicationManifest.xml** lägger du till kodfragmentet under elementet **ServiceManifestImport** för klientdelstjänsten. Ange **acrName** i fältet **Kontonamn** och lösenordet från föregående kommando i fältet **Lösenord**. En fullständig **ApplicationManifest.xml** finns i slutet av det här dokumentet.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurera kommunikation och portmappning mellan container och värd

### <a name="configure-communication-port"></a>Konfigurera kommunikationsporten

Konfigurera en HTTP-slutpunkt så att klienter kan kommunicera med din tjänst. Öppna filen *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* och deklarera en slutpunktsresurs i elementet **ServiceManifest**.  Lägg till protokollet, porten och namnet. I den här självstudien lyssnar tjänsten på port 80. Följande kodfragment placeras under taggen *ServiceManifest* i resursen.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Modifiera på tjänstemanifestet på samma sätt för serverdeltjänsten. Öppna *./TestContainer/azurevotebackPkg/ServiceManifest.xml* och deklarera en slutpunktsresurs i elementet **ServiceManifest**. För denna självstudiekurs behålls redis-standardvärdet på 6379. Följande kodfragment placeras under taggen *ServiceManifest* i resursen.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Genom att tillhandahålla **UriScheme** registreras automatiskt containerslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil för serverdeltjänsten finns i slutet av den här artikeln.

### <a name="map-container-ports-to-a-service"></a>Mappa containerportar till en tjänst

För att göra containrarna tillgängliga i klustret måste vi även skapa en portbindning i ”ApplicationManifest.xml”. Principen **PortBinding** använder de **slutpunkter** vi definierade i **ServiceManifest.xml**-filerna som referens. Inkommande begäranden till dessa slutpunkter mappas till de containerportar som öppnas och binds här. I **ApplicationManifest.xml**-filen lägger du till följande kod för att binda port 80 och 6379 till slutpunkterna. En fullständig **ApplicationManifest.xml** finns i slutet av det här dokumentet.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Lägg till ett DNS-namn till serverdelstjänsten

Service Fabric kan inte tilldela DNS-namnet till serverdelstjänsten om namnet inte anges i **ApplicationManifest.xml**. Lägg till attributet **ServiceDnsName** till elementet **Service** som visat:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Klientdelstjänsten läser en miljövariabel för att ta reda på Redis-instansens DNS-namn. Miljövariabeln är redan definierad i den Dockerfile som användes för att skapa Docker-avbildningen. Inga åtgärder krävs.

```dockerfile
ENV REDIS redisbackend.testapp
```

Följande kodfragment visar hur Python-koden i klientdelen läser in miljövariabeln som beskrivs i Dockerfile. Inga åtgärder krävs.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Mallen för ett tjänstepaketprogram är nu tillgänglig för distribuering till ett kluster. I följande självstudiekurs distribueras programmet och körs i ett Service Fabric-kluster.

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster

Om du vill distribuera programmet till Azure behöver du ett Service Fabric-kluster som kör programmet. Följande kommandon skapar ett kluster med fem noder i Azure.  Kommandona skapar även ett självsignerat certifikat, lägger till det i ett nyckelvalv och laddar ned certifikatet lokalt som en PEM-fil. Det nya certifikatet används för att skydda klustret när distribueras och används för att autentisera klienter.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  
# The certificate is downloaded locally as a PEM file.
az sf cluster create --resource-group $ResourceGroupName --location $Location \ 
--certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \ 
--cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName \ 
--vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Frontwebbtjänsten är konfigurerad för att lyssna efter inkommande trafik på port 80. Som standard är port 80 öppen på dina virtuella klusterdatorer och Azure-lastbalanseraren.
>

Mer information om hur du skapar ett eget kluster finns i [Skapa ett Service Fabric-kluster i Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Bygg och distribuera programmet till klustret

Du kan distribuera programmet till Azure-klustret med Service Fabric CLI. Om Service Fabric CLI inte är installerat på datorn följer du [dessa](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) anvisningar och installerar det.

Anslut till Service Fabric-klustret i Azure. Byt exempelslutpunkten mot din egen. Slutpunkten måste vara en fullständig webbadress som liknar den som visas nedan.  PEM-filen är det självsignerade certifikat som skapades tidigare.

```bash
sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
```

Använd installationsskriptet som medföljer i katalogen **TestContainer** för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

```bash
./install.sh
```

Öppna en webbläsare och gå till Service Fabric Explorer vid http: \/ /containertestcluster.eastus.cloudapp.Azure.com:19080/Explorer. Expandera programnoden och observera att det finns en post för din programtyp och en för instansen.

![Service Fabric Explorer][sfx]

Öppna en webbläsare och gå till klustrets URL – till exempel http:/containertestcluster.eastus.cloudapp.azure.com:80 för att ansluta till det program som körs \/ . Röstningsprogrammet ska visas i webbläsaren.

![Skärm bild som visar Azure röstnings app med knappar för katter, hundar och återställning och total summor.][votingapp]

## <a name="clean-up"></a>Rensa

Använd avinstallationsskriptet som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen. Det tar lite tid att rensa instansen med det här kommandot. Kommandot ”install.sh” kan inte köras omedelbart efter det här skriptet.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Exempel på manifest

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Front-end ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen packades flera containrar in i ett Service Fabric-program med Yeoman. Programmet distribuerades sedan och kördes på ett Service Fabric-kluster. Följande steg har slutförts:

> [!div class="checklist"]
> * Installera Yeoman
> * Skapa ett programpaket med Yeoman
> * Konfigurera inställningar i programpaketet för användning med containrar
> * Skapa programmet
> * Distribuera och köra programmet
> * Rensa programmet

Gå vidare till nästa självstudie om du vill veta mer om redundansväxling och skalning av programmet i Service Fabric.

> [!div class="nextstepaction"]
> [Läs om redundansväxling och skalning av program](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png
