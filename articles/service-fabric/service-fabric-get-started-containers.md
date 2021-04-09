---
title: Skapa ett program för Azure Service Fabric container
description: Skapa din första Windows-containerapp på Azure Service Fabric. Bygg en Docker-avbildning med ett python-program, skicka avbildningen till ett behållar register och sedan bygga och distribuera behållaren till Azure Service Fabric.
ms.topic: conceptual
ms.date: 01/25/2019
ms.custom: devx-track-python
ms.openlocfilehash: 197423670ffe05f15fdc5bfd351efdfba33b53cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96533782"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Skapa din första Service Fabric-containerapp i Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-container i ett Service Fabric-kluster. Den här artikeln beskriver hur du skapar en Docker-avbildning som innehåller ett webb program för python- [flaska](http://flask.pocoo.org/) och distribuerar det till ett Azure Service Fabric-kluster. Du kan också dela programmet via [Azure Container-registret](../container-registry/index.yml). Den här artikeln förutsätter att du har grundläggande kunskaper om Docker. Mer information om Docker finns i [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Översikt över Docker).

> [!NOTE]
> Den här artikeln gäller för en Windows-utvecklings miljö.  Service Fabric kluster körning och Docker-körningsmiljön måste köras på samma OS.  Det går inte att köra Windows-behållare i ett Linux-kluster.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

* En utvecklingsdator som kör:
  * Visual Studio 2015 eller Visual Studio 2019.
  * [Service Fabric SDK och verktyg](service-fabric-get-started.md).
  *  Docker för Windows. [Hämta Docker CE för Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Efter installationen startar du Docker, högerklickar på ikonen för fack och väljer **Switch to Windows containers** (Växla till Windows-behållare). Detta steg krävs för att köra Docker-avbildningar baserade på Windows.

* Ett Windows-kluster med tre eller fler noder som körs på Windows Server med behållare. 

  I den här artikeln måste versionen (build) av Windows Server med behållare som körs på klusternoderna matcha den på din utvecklings dator. Detta beror på att du skapar Docker-avbildningen på utvecklings datorn och att det finns kompatibilitetsproblem mellan versioner av behållar-OS och det värd-OS som den har distribuerats på. Mer information finns i [Windows Server container OS och Host OS-kompatibilitet](#windows-server-container-os-and-host-os-compatibility). 
  
    Du kan ta reda på vilken version av Windows Server med behållare du behöver för klustret genom att köra `ver` kommandot från en Windows-kommandotolk på utvecklings datorn. Se [Windows Server container OS och Host OS-kompatibilitet](#windows-server-container-os-and-host-os-compatibility) innan du [skapar ett kluster](service-fabric-cluster-creation-via-portal.md).

* Ett register i Azure Container Registry – [Skapa ett behållarregister](../container-registry/container-registry-get-started-portal.md) i din Azure-prenumeration.

> [!NOTE]
> Distribution av behållare till ett Service Fabric kluster som körs på Windows 10 stöds.  I [den här artikeln](service-fabric-how-to-debug-windows-containers.md) finns information om hur du konfigurerar Windows 10 för att köra Windows-behållare.

> [!NOTE]
> Service Fabric versioner 6,2 och senare har stöd för distribution av behållare till kluster som körs på Windows Server version 1709.

## <a name="define-the-docker-container"></a>Definiera Dockercontainer

Skapa en avbildning baserat på [Python-avbildningen](https://hub.docker.com/_/python/) på Docker Hub.

Ange Docker-containern i en Dockerfile. Dockerfile innehåller instruktioner för att konfigurera miljön i din container, läsa in programmet du vill köra och mappa portar. Dockerfile är indata för kommandot `docker build` som skapar avbildningen.

Skapa en tom katalog och skapa filen *Dockerfile* (utan filtillägget). Lägg till följande i *Dockerfile* och spara dina ändringar:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Läs [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) för mer information.

## <a name="create-a-basic-web-application"></a>Skapa en grundläggande webbapp
Skapa en Flask-webbapplikation som lyssnar på port 80 och returnerar `Hello World!`. Skapa filen *requirements.txt* i samma katalog. Lägg till följande och spara dina ändringar:
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

<a id="Build-Containers"></a>

## <a name="login-to-docker-and-build-the-image"></a>Logga in på Docker och bygg avbildningen

Nu ska vi skapa den avbildning som kör ditt webb program. När du hämtar offentliga bilder från Docker (t. ex. `python:2.7-windowsservercore` i vårt Dockerfile), är det bäst att autentisera med ditt Docker Hub-konto i stället för att göra en anonym pull-begäran.

> [!NOTE]
> När du gör vanliga anonyma pull-begäranden kan du se Docker-fel som liknar `ERROR: toomanyrequests: Too Many Requests.` eller `You have reached your pull rate limit.` autentiseras för Docker Hub för att förhindra dessa fel. Mer information finns i [Hantera offentligt innehåll med Azure Container Registry](../container-registry/buffer-gate-public-content.md) .

Öppna ett PowerShell-fönster och navigera till den katalog som innehåller din Dockerfile. Kör sedan följande kommandon:

```
docker login
docker build -t helloworldapp .
```

Med det här kommandot skapas den nya avbildningen med hjälp av instruktionerna i din Dockerfile och avbildningen får namnet `helloworldapp`. Om du vill skapa en containeravbildning börjar du med att ladda ned basavbildningen från den Docker-hubb som programmet ska läggas till i. 

När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Köra appen lokalt
Kontrollera avbildningen lokalt innan du överför den till containerregistret. 

Kör programmet:

```
docker run -d --name my-web-site helloworldapp
```

*name* namnger den container som körs (i stället för container-ID:t).

När containern har startat letar du reda på dess IP-adress så att du kan ansluta till den container som körs via en webbläsare:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Om kommandot inte returnerar något kör du följande kommando och kontrollerar **NetworkSettings** -> **Networks** -elementet för IP-adressen:
```
docker inspect my-web-site
```

Anslut till den container som körs. Öppna en webbläsare som pekar på den returnerade IP-adressen, till exempel "http: \/ /172.31.194.61". Nu visas normalt rubriken "Hello World!" i webbläsaren.

Om du vill stoppa containern kör du:

```
docker stop my-web-site
```

Ta bort containern från utvecklingsdatorn:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Överför avbildningen till containerregistret

När du har kontrollerat att behållaren körs på utvecklingsdatorn överför du avbildningen till registret i Azure Container Registry.

Kör ``docker login`` för att logga in i behållar registret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Eller så kan du logga in med ditt användar namn och lösen ord för registret.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i ```samples```-namnområdet för att undvika oreda i registrets rot.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Överför avbildningen till containerregistret:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Skapa containertjänsten i Visual Studio
SDK:en och verktygen för Service Fabric innehåller en tjänstmall som hjälper dig att skapa ett containerprogram.

1. Starta Visual Studio. Välj **Arkiv**  >  **nytt**  >  **projekt**.
2. Välj **Service Fabric-programmet**, ge det namnet "MyFirstContainer" och klicka på **OK**.
3. Välj **Container** i listan med **tjänstmallar**.
4. I **Avbildningsnamn** skriver du "myregistry.azurecr.io/samples/helloworldapp" (den avbildning som du skickade till lagringsplatsen för containern).
5. Namnge tjänsten och klicka på **OK**.

## <a name="configure-communication"></a>Konfigurera kommunikation
Containertjänsten behöver en slutpunkt för kommunikation. Lägg till ett `Endpoint`-element med protokollet, porten och typen i filen ServiceManifest.xml. I det här exemplet används en fast port, 8081. Om ingen port har angetts väljs en port slumpmässigt från portintervallet för programmet. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Du kan lägga till ytterligare slut punkter för en tjänst genom att deklarera ytterligare slut punkts element med tillämpliga egenskaps värden. Varje port kan bara deklarera ett protokoll värde.

Genom att definiera en slutpunkt publicerar Service Fabric slutpunkten i namngivningstjänsten. Andra tjänster som körs i klustret kan lösa den här containern. Du kan också utföra kommunikation mellan containrar med hjälp av den [omvända proxyn](service-fabric-reverseproxy.md). Du utför kommunikation genom att tillhandahålla HTTP-lyssningsporten för den omvända proxyn och namnet på de tjänster som du vill kommunicera med som miljövariabler.

Tjänsten lyssnar på en specifik port (8081 i det här exemplet). När programmet distribueras till ett kluster i Azure, körs både klustret och programmet bakom en Azure-lastbalanserare. Programporten måste vara öppen i Azure-lastbalanseraren så att inkommande trafik kan nå tjänsten.  Du kan öppna den här porten i Azures lastbalanserare med hjälp av ett [PowerShell-skript](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) eller på [Azure Portal](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Konfigurera och ange miljövariabler
Miljövariabler kan anges för varje kodpaketet i tjänstmanifestet. Den här funktionen är tillgänglig för alla tjänster oavsett om de har distribueras som containrar eller processer, eller körbara gäster. Du kan åsidosätta värden för miljövariabler i applikationsmanifestet eller ange dem under distributionen som programparametrar.

Följande XML-kodfragment i tjänstmanifestet visar ett exempel på hur du anger miljövariabler för ett kodpaket:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Dessa miljövariabler kan åsidosättas i applikationsmanifestet:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Konfigurera mappning mellan containerport och värdport och identifiering mellan containrar
Konfigurera en värdport som används för att kommunicera med containern. Portbindningen mappar porten där tjänsten lyssnar inuti containern till en port på värden. Lägg till ett `PortBinding`-element i `ContainerHostPolicies`-elementet i filen ApplicationManifest.xml. I den här artikeln är `ContainerPort` 80 (containern exponerar port 80, som anges i Dockerfile) och `EndpointRef` är ”Guest1TypeEndpoint” (slutpunkten som tidigare definierats i tjänstmanifestet). Inkommande begäranden till tjänsten på port 8081 mappas till port 80 på containern.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> Du kan lägga till ytterligare PortBindings för en tjänst genom att deklarera ytterligare PortBinding-element med tillämpliga egenskaps värden.

## <a name="configure-container-repository-authentication"></a>Konfigurera autentisering av container-lagringsplats

Mer information om hur du konfigurerar olika typer av autentisering för hämtning av behållare avbildning finns i [autentisering av container-lagringsplatsen](configure-container-repository-credentials.md).

## <a name="configure-isolation-mode"></a>Konfigurera isoleringsläge
Windows stöder två isoleringslägen för containrar: process och Hyper-V. Om processisoleringsläget används delar alla containrar som körs på samma värddator kärna med värden. Om Hyper-V-isoleringsläget används isoleras kärnorna mellan varje Hyper-V-container och containervärden. Isoleringsläget anges i `ContainerHostPolicies`-elementet i applikationsmanifestfilen. Isoleringslägena som kan anges är `process`, `hyperv` och `default`. Standardvärdet är process isolerings läge på Windows Server-värdar. På Windows 10-värdar stöds endast isolerings läget för Hyper-V, så behållaren körs i läget för Hyper-V-isolering oavsett inställningen isolerings läge. Följande kodfragment visar hur isoleringsläget har angetts i applikationsmanifestfilen.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > Hyper-V-isoleringsläget är tillgängligt på Ev3 och Dv3 Azure SKU:er som har stöd för kapslad virtualisering. 
   >
   >

## <a name="configure-resource-governance"></a>Konfigurera resursstyrning
Med [resursstyrning](service-fabric-resource-governance.md) begränsas resurserna som containern kan använda på värddatorn. `ResourceGovernancePolicy`-elementet som anges i applikationsmanifestet, används för att deklarera resursgränser för ett tjänstkodpaket. Resursgränser kan anges för följande resurser: Memory, MemorySwap, CpuShares (relativ processorvikt), MemoryReservationInMB, BlkioWeight (relativ BlockIO-vikt). I det här exemplet hämtar tjänstpaketet Guest1Pkg en kärna på klusternoderna där det är placerat. Minnesgränser är absoluta, så kodpaketet är begränsat till 1024 MB minne (med samma reservation). Kodpaket (containrar eller processer) kan inte tilldela mer minne än den här gränsen, och försök att göra detta leder till undantag utanför minnet. För att tvingande resursbegränsning ska fungera bör minnesbegränsningar ha angetts för alla kodpaket inom ett tjänstpaket.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
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

![Skärm bild som visar information om det distribuerade tjänst paketet NodeServicePackage.][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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

## <a name="deploy-the-container-application"></a>Distribuera containerappen
Spara alla dina ändringar och skapa programmet. Om du vill publicera appen högerklickar du på **MyFirstContainer** i Solution Explorer och väljer **Publish** (Publicera).

I **anslutningsslutpunkten** anger du hanteringsslutpunkten för klustret. Till exempel `containercluster.westus2.cloudapp.azure.com:19000`. Slutpunkten för klientanslutningen finns på översiktsfliken för ditt kluster i [Azure Portal](https://portal.azure.com).

Klicka på **Publicera**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett webbaserat verktyg för att granska och hantera appar och noder i ett Service Fabric-kluster. Öppna en webbläsare och gå till `http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/` och följ appdistributionen. Appen distribueras men är i ett feltillstånd tills avbildningen har laddats ned på klusternoderna (vilket kan ta en stund beroende på avbildningens storlek): ![Fel][1]

Appen är klar när den har ```Ready```status: ![Ready][2] (Klar)

Öppna en webbläsare och navigera till `http://containercluster.westus2.cloudapp.azure.com:8081`. Nu visas normalt rubriken "Hello World!" i webbläsaren.

## <a name="clean-up"></a>Rensa

Det kostar pengar så länge klustret körs. Fundera på om du vill [ta bort klustret](./service-fabric-tutorial-delete-cluster.md).

När du har överfört avbildningen till containerregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Windows Server container OS-och värd-OS-kompatibilitet

Windows Server-behållare är inte kompatibla i alla versioner av ett värd operativ system. Exempel:
 
- Windows Server-behållare som skapats med hjälp av Windows Server version 1709 fungerar inte på en värd som kör Windows Server version 2016. 
- Windows Server-behållare som skapats med Windows Server 2016 fungerar bara i ett isolerings läge för Hyper-V på en värd som kör Windows Server version 1709. 
- Med Windows Server-behållare som skapats med Windows Server 2016 kan det vara nödvändigt att se till att revisionen av behållar-OS och värd operativ systemet är samma när du kör i process isolerings läge på en värd som kör Windows Server 2016.
 
Mer information finns i [kompatibilitet med Windows container version](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Överväg kompatibiliteten för värd operativ systemet och behållar operativ systemet när du skapar och distribuerar behållare till ditt Service Fabric-kluster. Exempel:

- Se till att distribuera behållare med ett operativ system som är kompatibelt med operativ systemet på klusternoderna.
- Kontrol lera att isolerings läget som anges för container-appen är konsekvent med stöd för behållar-OS på den nod där det distribueras.
- Överväg hur operativ system uppgraderingar till klusternoder eller behållare kan påverka deras kompatibilitet. 

Vi rekommenderar följande metoder för att se till att behållare distribueras korrekt på ditt Service Fabric-kluster:

- Använd explicit Bildtaggning med Docker-avbildningarna för att ange den version av Windows Server-operativsystem som en behållare har skapats från. 
- Använd [operativ system taggning](#specify-os-build-specific-container-images) i program manifest filen för att kontrol lera att programmet är kompatibelt över olika versioner och uppgraderingar av Windows Server.

> [!NOTE]
> Med Service Fabric version 6,2 och senare kan du distribuera behållare baserade på Windows Server 2016 lokalt på en Windows 10-värd. På Windows 10 körs behållare i isolerings läge för Hyper-V, oberoende av isolerings läget som anges i applikations manifestet. Mer information finns i [Konfigurera isolerings läge](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Ange specifika containeravbildningar för operativsystemet 

Windows Server-behållare kanske inte är kompatibla i olika versioner av operativ systemet. Till exempel fungerar inte Windows Server-behållare som skapats med Windows Server 2016 i Windows Server version 1709 i process isolerings läge. Om klusternoderna uppdateras till den senaste versionen kan det hända att behållar tjänster som skapats med tidigare versioner av operativ systemet Miss lyckas. För att kringgå detta med version 6,1 av körningen och senare, har Service Fabric stöd för att ange flera OS-avbildningar per behållare och tagga dem med build-versioner av operativ systemet i applikations manifestet. Du kan hämta build-versionen av operativ systemet genom att köra `winver` i kommando tolken i Windows. Uppdatera applikationsmanifesten och ange åsidosättningar av avbildning per operativsystemsversion innan du uppdaterar operativsystemet på noderna. Följande kodavsnitt visar hur du kan ange flera containeravbildningar i applikationsmanifestet, **ApplicationManifest.xml**:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
Versionen för Windows Server 2016 är 14393, och versionen för Windows Server version 1709 är 16299. Tjänstmanifestet fortsätter att ange endast en avbildning per containertjänst, vilket följande visar:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > Taggningsfunktioner för operativsystemsversionen är endast tillgängliga för Service Fabric på Windows
   >

Om det underliggande operativsystemet på den virtuella datorn är version 16299 (version 1709) väljer Service Fabric den containeravbildning som motsvarar den Windows Server-versionen. Om en ej taggad containeravbildning också anges tillsammans med taggade containeravbildningar i applikationsmanifestet kommer Service Fabric att hantera den ej taggade avbildningen som en avbildning som fungerar för olika versioner. Tagga containeravbildningen explicit för att undvika problem under uppgraderingar.

Containeravbildningen utan taggar kommer att fungera som en åsidosättning av den som anges i ServiceManifest. Det innebär att avbildningen ”myregistry.azurecr.io/samples/helloworldappDefault” åsidosätter avbildningen ”myregistry.azurecr.io/samples/helloworldapp” i ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och appmanifesten som används i den här artikeln.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Ställ in tidsintervall innan containern tvångsavslutas

Du kan ställa in ett tidsintervall för hur lång exekveringstid som ska gå innan containern tas bort när borttagning av tjänsten (eller flytt till en annan nod) har påbörjats. När du ställer in ett tidsintervall skickas kommandot `docker stop <time in seconds>` till containern.  Mer information finns i [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). Tidsintervallet anges i avsnittet `Hosting`. `Hosting`Avsnittet kan läggas till när klustret skapas eller senare i en konfigurations uppgradering. I följande klustermanifestutdrag visas hur du ställer in väntetidsintervallet:

```json
"fabricSettings": [
    ...,
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
]
```
Standardtidsintervallet är inställt på 10 sekunder. Eftersom inställningen är dynamisk uppdateras tidsgränsen med en konfigurationsuppdatering på klustret. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Ställ in exekveringstid för att ta bort containeravbildningar som inte används

Du kan ställa in Service Fabric-klustret på att ta bort oanvända containeravbildningar från noden. Med den här inställningen kan du få tillbaka diskutrymme om det finns för många containeravbildningar på noden. Om du vill aktivera den här funktionen uppdaterar du avsnittet [hosting](service-fabric-cluster-fabric-settings.md#hosting) i kluster manifestet som visas i följande kodfragment: 


```json
"fabricSettings": [
    ...,
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
]
```

Avbildningar som inte ska raderas kan du ange under parametern `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Konfigurera nedladdningstid för containeravbildning

Service Fabric-körningen tilldelar 20 minuter för att ladda ned och extrahera containeravbildningar, vilket fungerar för de flesta containeravbildningar. För stora avbildningar, eller om nätverksanslutningen är långsam, kan det vara nödvändigt att öka den tid körningen väntar innan nedladdning och extrahering av avbildningen avbryts. Den här timeouten anges med attributet **ContainerImageDownloadTimeout** i avsnittet **Hosting** i klustermanifestet, på det sätt som visas i följande kodavsnitt:

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>Ange bevarandeprincip för container

Service Fabric (version 6.1 eller senare) har stöd för bevarande av containrar som har avslutats eller inte kunde starta, vilket underlättar diagnostisering av startfel. Den här principen kan anges i filen **ApplicationManifest.xml**, vilket visas i följande kodavsnitt:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

Inställningen **ContainersRetentionCount** anger antalet behållare som ska bevaras när de får fel. Om ett negativt värde anges kommer alla containrar med fel att bevaras. Om attributet **ContainersRetentionCount** inte anges kommer inga behållare att bevaras. Attributet **ContainersRetentionCount** har även stöd för programparametrar, så att användarna kan ange olika värden för test- och produktionskluster. Använd placeringsbegränsningar för att rikta in containertjänsten på en viss nod när den här funktionen används för att förhindra att containertjänsten flyttas till andra noder. Alla containrar som bevaras med den här funktionen måste tas bort manuellt.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Starta Docker-daemon med anpassade argument

Du kan starta Docker-daemon med anpassade argument med version 6.2 eller högre av Service Fabric runtime. Om du inte anger anpassade argument, skickar inte Service Fabric några andra argument till docker-motorn, förutom argumentet `--pidfile`. Därför får inte `--pidfile` skickas som ett argument. Dessutom ska argumentet fortsätta att få docker-daemonen att lyssna på pipen med standardnamnet på Windows (eller unix-domänsocket på Linux) för att Service Fabric ska kunna kommunicera med daemon. De anpassade argumenten skickas i klustermanifestet under **värdavsnittet** i **ContainerServiceArguments** enligt följande kodavsnitt: 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-containers-overview.md).
* Läs kursen [Distribuera ett .NET-program i en container](service-fabric-host-app-in-a-container.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-container](https://github.com/Azure-Samples/service-fabric-containers) på GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
