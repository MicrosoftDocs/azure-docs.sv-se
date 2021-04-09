---
title: Skapa en behållare för Apache Tomcat på Linux
description: Skapa en Linux-behållare för att exponera ett program som körs på Apache Tomcat-server på Azure Service Fabric. Bygg en Docker-avbildning med ditt program och Apache Tomcat-servern, push-överför avbildningen till ett behållar register, bygg och distribuera ett program för Service Fabric behållare.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 3de97bc277195dff2daf5868c0eb9aec5d6e27c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96534037"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Skapa Service Fabric behållare som kör Apache Tomcat server på Linux
Apache Tomcat är en populär implementering av Java-servlet och Java-serverns teknik med öppen källkod. Den här artikeln visar hur du skapar en behållare med Apache Tomcat och ett enkelt webb program, distribuerar behållaren till ett Service Fabric kluster som kör Linux och ansluter till webb programmet.  

Mer information om Apache Tomcat finns på [Start sidan för Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Förutsättningar
* En utvecklingsdator som kör:
  * [Service Fabric SDK och verktyg](service-fabric-get-started-linux.md).
  * [Docker CE för Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Ett behållar register i Azure Container Registry. Du kan skapa ett behållar register i din Azure-prenumeration med hjälp [av Azure Portal](../container-registry/container-registry-get-started-portal.md) eller [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Skapa en Tomcat-avbildning och kör den lokalt
Följ stegen i det här avsnittet för att skapa en Docker-avbildning baserad på en Apache Tomcat-avbildning och en enkel webbapp och kör den sedan i en behållare i det lokala systemet. 
 
1. Klona [Service Fabric komma igång med Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) samples-lagringsplatsen på din utvecklings dator.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Ändra kataloger till Server exempel katalogen för Apache Tomcat (*Service-Fabric-Java-kom-start/container-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Skapa en Docker-fil baserat på den officiella [Tomcat-avbildningen](https://hub.docker.com/_/tomcat/) som finns på Docker Hub och Tomcat Server-exemplet. Skapa en fil med namnet *Dockerfile* (utan fil namns tillägg) i katalogen *Service-Fabric-Java-kom-start/container-Apache-Tomcat-Web-Server-Sample* . Lägg till följande i *Dockerfile* och spara dina ändringar:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Mer information finns i [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) .


4. Logga in på Docker och kör `docker build` kommandot för att skapa avbildningen som kör ditt webb program:

   ```bash
   docker login
   docker build . -t tomcattest
   ```

   Det här kommandot skapar den nya avbildningen med hjälp av anvisningarna i Dockerfile och namnger (-t taggning) bilden `tomcattest` . För att bygga en behållar avbildning hämtas bas avbildningen först ned från Docker-hubben och programmet läggs till i den. 

   När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Kontrol lera att ditt program i behållare körs lokalt innan du skickar det till behållar registret:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` namnger behållaren, så att du kan referera till den med ett eget namn i stället för dess ID.
   * `-p` anger port mappningen mellan behållaren och värd operativ systemet. 

   > [!Note]
   > Porten som du öppnar med `-p` parametern ska vara den port som Tomcat-programmet lyssnar på. I det aktuella exemplet finns en anslutning som kon figurer ATS i filen *ApacheTomcat/conf/server.xml* för att lyssna på Port 8080 för HTTP-begäranden. Den här porten är mappad till Port 8080 på värden. 

   Läs mer om andra parametrar i [Docker-körnings dokumentationen](https://docs.docker.com/engine/reference/commandline/run/).

1. Testa din behållare genom att öppna en webbläsare och ange en av följande URL: er. En variant av "Hello World!" visas. Välkomst skärmen för varje URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello World-/SayHi](./media/service-fabric-get-started-tomcat/hello.png)

2. Stoppa behållaren och ta bort den från utvecklings datorn:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Push-överför Tomcat-avbildningen till behållar registret
Nu när du har kontrollerat att Tomcat-avbildningen körs i en behållare på din utvecklings dator, push-överför den till en lagrings plats i ett behållar register för att [minska risken](../container-registry/buffer-gate-public-content.md) för bild utveckling och distributions arbets flöden. Den här artikeln använder Azure Container Registry för att lagra avbildningen, men med vissa ändringar av stegen kan du använda valfritt behållar register som du väljer. I den här artikeln antas register namnet vara *registret* och det fullständiga register namnet är myregistry.azurecr.io. Ändra dessa på lämpligt sätt för ditt scenario. 

1. Kör `docker login` för att logga in i behållar registret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

   I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Eller så kan du logga in med ditt användar namn och lösen ord för registret.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i `samples`-namnområdet för att undvika oreda i registrets rot.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Överför avbildningen till containerregistret:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Bygg och distribuera Service Fabric container program
Nu när du har skickat Tomcat-avbildningen till ett behållar register kan du bygga och distribuera ett Service Fabric behållar program som hämtar Tomcat-avbildningen från registret och kör den som en behållar tjänst i klustret. 

1. Skapa en ny katalog utanför din lokala klon (utanför *Service-Fabric-Java-kom igång-* katalog trädet). Växla till den och Använd Yeoman för att skapa en Autogenerera för ett behållar program: 

   ```bash
   yo azuresfcontainer 
   ```
   Ange följande värden när du uppmanas till det:

   * Namnge ditt program: ServiceFabricTomcat
   * Namnet på program tjänsten: TomcatService
   * Ange avbildningens namn: Ange URL: en för behållar avbildningen i behållar registret. till exempel myregistry.azurecr.io/samples/tomcattest.
   * Kommandon: lämna tomt. Eftersom den här avbildningen har en definierad startpunkt arbetsbelastningen måste du inte uttryckligen ange inkommande kommandon (kommandon körs i den container som kommer att hålla den container som körs efter start).
   * Antal instanser av gäst container program: 1

   ![Service Fabric Yeoman-generator för containrar](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. I tjänst manifestet (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) lägger du till följande XML under roten **ServiceManfest** -taggen för att öppna porten som programmet lyssnar på begär Anden. **Slut punkts** tag gen deklarerar protokollet och porten för slut punkten. I den här artikeln lyssnar tjänsten behållar tjänsten på Port 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. I program manifestet (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*), under taggen **service manifest import** , lägger du till följande XML. Ersätt **AccountName** och **lösen ordet** i **RepositoryCredentials** -taggen med namnet på behållar registret och det lösen ord som krävs för att logga in på det.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Taggen **ContainerHostPolicies** anger principer för att aktivera behållar värdar.
    
   * **PortBinding** -taggen konfigurerar port mappnings principen för container port-till-värd. Attributet **ContainerPort** är inställt på 8080 eftersom behållaren exponerar Port 8080, enligt vad som anges i Dockerfile. Attributet **EndpointRef** är inställt på "endpointTest", den slut punkt som definierats i tjänst manifestet i föregående steg. Därför mappas inkommande begär anden till tjänsten på Port 8080 till Port 8080 på behållaren. 
   * Taggen **RepositoryCredentials** anger de autentiseringsuppgifter som containern behöver för att autentisera med det (privata) lagrings plats där den hämtar avbildningen från. Du behöver inte den här principen om avbildningen kommer att hämtas från ett offentligt lager.
    

12. I mappen *ServiceFabricTomcat* ansluter du till Service Fabric-klustret. 

   * Kör följande för att ansluta till det lokala Service Fabrics klustret:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * För att ansluta till ett säkert Azure-kluster kontrollerar du att klient certifikatet finns som en. PEM-fil i katalogen *ServiceFabricTomcat* och kör: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     I föregående kommando ersätter du `your-certificate.pem` med namnet på klient certifikat filen. I utvecklings-och test miljöer används ofta kluster certifikatet som klient certifikat. Utelämna parametern om ditt certifikat inte är självsignerat `-no-verify` . 
       
     Kluster certifikat laddas vanligt vis ned lokalt som. PFX-filer. Om du inte redan har ditt certifikat i PEM-format kan du köra följande kommando för att skapa en. PEM-fil från en. pfx-fil:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Om din PFX-fil inte är lösenordsskyddad använder `-passin pass:` du den sista parametern.


13. Kör installations skriptet som finns i mallen för att distribuera programmet till klustret. Skriptet kopierar programpaketet till klustrets avbildnings Arkiv, registrerar program typen och skapar en instans av programmet.

     ```bash
     ./install.sh
     ```

   När du har kört installations skriptet öppnar du en webbläsare och går till Service Fabric Explorer:
    
   * I ett lokalt kluster använder `http://localhost:19080/Explorer` du (Ersätt *localhost* med den virtuella datorns privata IP om du använder Vagrant på Mac OS X).
   * Använd i ett säkert Azure-kluster `https://PublicIPorFQDN:19080/Explorer` . 
    
   Expandera noden **program** och Observera att det nu finns en post för din program typ, **ServiceFabricTomcatType** och en annan för den första instansen av den typen. Det kan ta några minuter för programmet att distribueras fullständigt, så du måste ha tålamod.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Öppna ett webbläsarfönster och ange någon av följande URL: er för att komma åt programmet på Tomcat-servern. Om du har distribuerat till det lokala klustret använder du *localhost* för *PublicIPorFQDN*. En variant av "Hello World!" visas. Välkomst skärmen för varje URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Rensa
Använd avinstallations skriptet som medföljer mallen för att ta bort program instansen från klustret och avregistrera program typen.

```bash
./uninstall.sh
```

När du har överfört avbildningen till containerregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Nästa steg
* För snabba steg i fler funktioner för Linux-behållare, Läs [skapa ditt första Service Fabric containerprogram i Linux](service-fabric-get-started-containers-linux.md).
* Mer detaljerade anvisningar om Linux-behållare finns i själv studie kursen [skapa en Linux-behållares vägledning](service-fabric-tutorial-create-container-images.md) .
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-containers-overview.md).


