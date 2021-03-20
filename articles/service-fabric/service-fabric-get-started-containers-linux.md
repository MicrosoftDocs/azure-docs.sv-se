---
title: Skapa ett program för Azure Service Fabric container i Linux
description: Skapa din första Linux-containerapp på Azure Service Fabric. Skapa en Docker-avbildning med din app, överför avbildningen till ett containerregister och skapa och distribuera en Service Fabric-containerapp.
ms.topic: conceptual
ms.date: 1/4/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0481cc2d36f7882bbd8eea9b984c3dc388de5dee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96534088"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Skapa din första Service Fabric-containerapp i Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Linux-container i ett Service Fabric-kluster. Den här artikeln vägleder dig genom att skapa en Docker-avbildning som innehåller ett Python [Flask](http://flask.pocoo.org/)-program och distribuera den till ett Service Fabric-kluster. Du kan också dela programmet via [Azure Container-registret](../container-registry/index.yml). Den här artikeln förutsätter att du har grundläggande kunskaper om Docker. Mer information om Docker finns i [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Översikt över Docker).

> [!NOTE]
> Den här artikeln gäller en Linux-utvecklings miljö.  Service Fabric kluster körning och Docker-körningsmiljön måste köras på samma OS.  Det går inte att köra Linux-behållare i ett Windows-kluster.

## <a name="prerequisites"></a>Förutsättningar
* En utvecklingsdator som kör:
  * [Service Fabric SDK och verktyg](service-fabric-get-started-linux.md).
  * [Docker CE för Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Ett Linux-kluster med tre eller fler noder.

* Ett register i Azure Container Registry – [Skapa ett behållarregister](../container-registry/container-registry-get-started-portal.md) i din Azure-prenumeration. 

## <a name="define-the-docker-container"></a>Definiera Dockercontainer
Skapa en avbildning baserat på [Python-avbildningen](https://hub.docker.com/_/python/) på Docker Hub. 

Ange Docker-containern i en Dockerfile. Dockerfile innehåller instruktioner för att konfigurera miljön i din container, läsa in programmet du vill köra och mappa portar. Dockerfile är indata för kommandot `docker build` som skapar avbildningen. 

Skapa en tom katalog och skapa filen *Dockerfile* (utan filtillägget). Lägg till följande i *Dockerfile* och spara dina ändringar:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Läs [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) för mer information.

## <a name="create-a-basic-web-application"></a>Skapa en grundläggande webbapp
Skapa en flask-webbapplikation som lyssnar på port 80 och returnerar ”Hello World”!. Skapa filen *requirements.txt* i samma katalog. Lägg till följande och spara dina ändringar:
```
Flask
```

Skapa även filen *app.py* och lägg till följande kodavsnitt:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="login-to-docker-and-build-the-image"></a>Logga in på Docker och bygg avbildningen

Nu ska vi skapa den avbildning som kör ditt webb program. När du hämtar offentliga bilder från Docker (t. ex. `python:2.7-slim` i vårt Dockerfile), är det bäst att autentisera med ditt Docker Hub-konto i stället för att göra en anonym pull-begäran.

> [!NOTE]
> När du gör vanliga anonyma pull-begäranden kan du se Docker-fel som liknar `ERROR: toomanyrequests: Too Many Requests.` eller `You have reached your pull rate limit.` autentiseras för Docker Hub för att förhindra dessa fel. Mer information finns i [Hantera offentligt innehåll med Azure Container Registry](../container-registry/buffer-gate-public-content.md) .

Öppna ett PowerShell-fönster och navigera till den katalog som innehåller din Dockerfile. Kör sedan följande kommandon:

```
docker login
docker build -t helloworldapp .
```

Med det här kommandot skapas den nya avbildningen med hjälp av instruktionerna i din Dockerfile och avbildningen får namnet `helloworldapp`. Om du vill skapa en containeravbildning börjar du med att ladda ned basavbildningen från den Docker-hubb som programmet ska läggas till i. 

När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Köra appen lokalt
Kontrollera att av programmet körs lokalt innan du skickar det till containerregistret. 

Kör programmet, vilket mappar port 4000 på datorn till containerns exponerade port 80:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* namnger den container som körs (i stället för container-ID:t).

Anslut till den container som körs. Öppna en webbläsare som pekar på IP-adressen som returnerades på port 4000, till exempel "http: \/ /localhost: 4000". Nu visas normalt rubriken "Hello World!" i webbläsaren.

![Hello World!][hello-world]

Om du vill stoppa containern kör du:

```bash
docker stop my-web-site
```

Ta bort containern från utvecklingsdatorn:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Överför avbildningen till containerregistret
När du har kontrollerat att behållaren körs på Docker överför du avbildningen till registret i Azure Container Registry.

Kör `docker login` för att logga in i behållar registret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Eller så kan du logga in med ditt användar namn och lösen ord för registret.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i `samples`-namnområdet för att undvika oreda i registrets rot.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Överför avbildningen till containerregistret:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Paketetera Docker-avbildningen med Yeoman
I Service Fabric SDK för Linux finns en [Yeoman](https://yeoman.io/)-generator som gör det enkelt att skapa ditt program och lägga till en containeravbildning. Nu ska vi använda Yeoman för att skapa ett program med en enda dockerbehållare som kallas *SimpleContainerApp*.

Om du vill skapa ett program för Service Fabric-container kan du öppna ett terminalfönster och köra `yo azuresfcontainer`. 

Namnge programmet (t.ex. `mycontainer`) och namnge tillämpningstjänsten (t.ex. `myservice`).

Som avbildningsnamn anger du en URL för containeravbildningen i ett containerregister (till exempel ”myregistry.azurecr.io/samples/helloworldapp”). 

Eftersom den här avbildningen har en definierad startpunkt arbetsbelastningen måste du inte uttryckligen ange inkommande kommandon (kommandon körs i den container som kommer att hålla den container som körs efter start). 

Ange ett instansantal på ”1”.

Ange port mappning i lämpligt format. I den här artikeln måste du ange ```80:4000``` som port mappning. Genom att göra detta har du konfigurerat att inkommande begär Anden som kommer till port 4000 på värddatorn omdirigeras till port 80 på behållaren.

![Service Fabric Yeoman-generator för containrar][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Konfigurera autentisering av container-lagringsplats

Mer information om hur du konfigurerar olika typer av autentisering för hämtning av behållare avbildning finns i [autentisering av container-lagringsplatsen](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Konfigurera isoleringsläge
Med 6,3 runtime-versionen stöds VM-isolering för Linux-behållare, vilket ger stöd för två isolerings lägen för behållare: process och Hyper-V. I isolerings läget för Hyper-V isoleras kernelerna mellan varje behållare och behållar värden. Hyper-V-isolering implementeras med hjälp av [Rensa behållare](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker). Isolerings läget anges för Linux-kluster i `ServicePackageContainerPolicy` elementet i applikations manifest filen. Isoleringslägena som kan anges är `process`, `hyperv` och `default`. Standardvärdet är process isolerings läge. Följande kodfragment visar hur isoleringsläget har angetts i applikationsmanifestfilen.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Konfigurera resursstyrning
Med [resursstyrning](service-fabric-resource-governance.md) begränsas resurserna som containern kan använda på värddatorn. `ResourceGovernancePolicy`-elementet som anges i applikationsmanifestet, används för att deklarera resursgränser för ett tjänstkodpaket. Resursgränser kan anges för följande resurser: Memory, MemorySwap, CpuShares (relativ processorvikt), MemoryReservationInMB, BlkioWeight (relativ BlockIO-vikt). I det här exemplet hämtar tjänstpaketet Guest1Pkg en kärna på klusternoderna där det är placerat. Minnesgränser är absoluta, så kodpaketet är begränsat till 1024 MB minne (med samma reservation). Kodpaket (containrar eller processer) kan inte tilldela mer minne än den här gränsen, och försök att göra detta leder till undantag utanför minnet. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Konfigurera Docker HEALTHCHECK 

Från och med v6.1 integrerar Service Fabric händelser för [Docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) automatiskt i systemets hälsorapport. Det innebär att om containern har **HEALTHCHECK** aktiverad kommer Service Fabric att rapportera hälsa varje gång containerns hälsostatus förändras enligt rapporten från Docker. En hälsorapport som är **OK** visas i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) när *health_status* är *healthy* och **WARNING** visas när *health_status* är *unhealthy*. 

Från och med den senaste uppdaterings versionen av v 6.4 har du möjlighet att ange att HEALTHCHECK-utvärderingar i Docker ska rapporteras som ett fel. Om det här alternativet är aktiverat visas en hälso rapport för **OK** när *health_status* är *felfri* och **fel** visas när *health_status* *inte är felfritt.*

**HEALTHCHECK** -instruktionen som pekar på den faktiska kontrollen som utförs för övervakning av container hälsa måste finnas i den Dockerfile som används när behållar avbildningen genereras.

![Skärm bild som visar information om det distribuerade tjänst paketet NodeServicePackage.][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

Du kan konfigurera **HEALTHCHECK**-beteendet för varje behållare genom att ange alternativen för **HealthConfig** som en del av **ContainerHostPolicies** i ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Som standard är *IncludeDockerHealthStatusInSystemHealthReport* inställt på **True**, *RestartContainerOnUnhealthyDockerHealthStatus* är inställt på **false** och *TreatContainerUnhealthyStatusAsError* har angetts till **false**. 

Om *RestartContainerOnUnhealthyDockerHealthStatus* är inställt på **true** kommer en behållare som upprepade gånger rapporteras som ej felfri att startas om (eventuellt på andra noder).

Om *TreatContainerUnhealthyStatusAsError* är inställt på **Sant** visas **fel** hälso rapporter när behållarens *health_status* inte är *felfri*.

Om du vill inaktivera integrering av **HEALTHCHECK** för hela Service Fabric-klustret måste du ställa in [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) på **false**.

## <a name="deploy-the-application"></a>Distribuera programmet
När du har skapat programmet kan du distribuera det till det lokala klustret med Service Fabric CLI.

Anslut till det lokala Service Fabric-klustret.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Använd installations skriptet som finns i mallarna för https://github.com/Azure-Samples/service-fabric-containers/ att kopiera programpaketet till klustrets avbildnings Arkiv, registrera program typen och skapa en instans av programmet.


```bash
./install.sh
```

Öppna en webbläsare och gå till Service Fabric Explorer på http: \/ /localhost: 19080/Explorer (Ersätt localhost med den virtuella datorns privata IP om du använder Vagrant på Mac OS X). Expandera programnoden och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

Anslut till den container som körs. Öppna en webbläsare som pekar på IP-adressen som returnerades på port 4000, till exempel "http: \/ /localhost: 4000". Nu visas normalt rubriken "Hello World!" i webbläsaren.

![Hello World!][hello-world]


## <a name="clean-up"></a>Rensa
Använd installationsskriptet som medföljer mallen för att ta bort programinstansen från det lokala utvecklingsklustret och avregistrera programtypen.

```bash
./uninstall.sh
```

När du har överfört avbildningen till containerregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och appmanifesten som används i den här artikeln.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Lägga till fler tjänster till ett befintligt program

Om du vill lägga till en till containertjänst till ett program som redan har skapats med hjälp av yeoman utför du följande steg:

1. Ändra katalogen till roten för det befintliga programmet. Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Ställ in tidsintervall innan containern tvångsavslutas

Du kan ställa in ett tidsintervall för hur lång exekveringstid som ska gå innan containern tas bort när borttagning av tjänsten (eller flytt till en annan nod) har påbörjats. När du ställer in ett tidsintervall skickas kommandot `docker stop <time in seconds>` till containern.  Mer information finns i [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Tidsintervallet anges i avsnittet `Hosting`. I följande klustermanifestutdrag visas hur du ställer in väntetidsintervallet:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

Standardtidsintervallet är inställt på 10 sekunder. Eftersom inställningen är dynamisk uppdateras tidsgränsen med en konfigurationsuppdatering på klustret. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Ställ in exekveringstid för att ta bort containeravbildningar som inte används

Du kan ställa in Service Fabric-klustret på att ta bort oanvända containeravbildningar från noden. Med den här inställningen kan du få tillbaka diskutrymme om det finns för många containeravbildningar på noden. Aktivera funktionen genom att uppdatera avsnittet `Hosting` i klustermanifestet enligt följande utdrag: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
} 
```

Avbildningar som inte ska raderas kan du ange under parametern `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Konfigurera nedladdningstid för containeravbildning

Service Fabric-körningen tilldelar 20 minuter för att ladda ned och extrahera containeravbildningar, vilket fungerar för de flesta containeravbildningar. För stora avbildningar, eller om nätverksanslutningen är långsam, kan det vara nödvändigt att öka den tid körningen väntar innan nedladdning och extrahering av avbildningen avbryts. Den här timeouten anges med attributet **ContainerImageDownloadTimeout** i avsnittet **Hosting** i klustermanifestet, på det sätt som visas i följande kodavsnitt:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Ange bevarandeprincip för container

Service Fabric (version 6.1 eller senare) har stöd för bevarande av containrar som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla containrar med fel att bevaras. Om attributet **ContainersRetentionCount** inte anges kommer inga behållare att bevaras. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in containertjänsten på en viss nod när den här funktionen används för att förhindra att containertjänsten flyttas till andra noder. Alla containrar som bevaras med den här funktionen måste tas bort manuellt.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Starta Docker-daemon med anpassade argument

Du kan starta Docker-daemon med anpassade argument med version 6.2 eller högre av Service Fabric runtime. Om du inte anger anpassade argument, skickar inte Service Fabric några andra argument till docker-motorn, förutom argumentet `--pidfile`. Därför får inte `--pidfile` skickas som ett argument. Dessutom ska argumentet fortsätta att få docker-daemonen att lyssna på pipen med standardnamnet på Windows (eller unix-domänsocket på Linux) för att Service Fabric ska kunna kommunicera med daemon. De anpassade argumenten har angetts i klustermanifestet under **värdavsnittet** i **ContainerServiceArguments**. Ett exempel visas i följande kodavsnitt: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-containers-overview.md).
* Läs kursen [Distribuera ett .NET-program i en container](service-fabric-host-app-in-a-container.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-container](https://github.com/Azure-Samples/service-fabric-containers) på GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
