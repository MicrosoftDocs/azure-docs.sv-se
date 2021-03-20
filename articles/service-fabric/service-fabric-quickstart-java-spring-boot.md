---
title: 'Snabb start: skapa en våren Boot-app på Azure Service Fabric'
description: I den här snabbstarten distribuerar du ett Spring Boot-program för Azure Service Fabric med ett Spring Boot-exempelprogram.
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 84ce5920af95113801f468e3149421f3b9bd8901
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530008"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Snabb start: Distribuera en Java våren Boot-app på Azure Service Fabric

I den här snabb starten distribuerar du ett Java våren Boot-program till Azure Service Fabric med hjälp av välbekanta kommando rads verktyg på Linux eller MacOS. Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av mikrotjänster och containrar. 

## <a name="prerequisites"></a>Förutsättningar

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java-miljö](./service-fabric-get-started-linux.md#set-up-java-development) och [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK & Service Fabric kommando rads gränssnitt (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java-miljö och Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK & Service Fabric kommando rads gränssnitt (CLI)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Ladda ned exemplet

Kör följande kommando i ett terminalfönster för att klona våren Boot [komma igång](https://github.com/spring-guides/gs-spring-boot) Sample-appen till din lokala dator.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Skapa Spring Boot-programmet 
I katalogen *GS-våren-Boot/Complete* kör du kommandot nedan för att skapa programmet 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Paketera Spring Boot-programmet 
1. I katalogen *GS-våren-Boot* i din kloning kör du `yo azuresfguest` kommandot. 

1. Ange följande information för varje fråga.

    ![Yeoman-poster för våren Boot](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. I mappen *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/Code* skapar du en fil med namnet *entryPoint.sh*. Lägg till följande kod i *entryPoint.sh* -filen. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. Lägg till resursen **slut punkter** i filen *GS-våren-Boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    *ServiceManifest.xml* ser nu ut så här: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

I det här skedet har du skapat ett Service Fabric-program för exemplet Spring Boot Getting Started som du kan distribuera till Service Fabric.

## <a name="run-the-application-locally"></a>Köra appen lokalt

1. Starta ditt lokala kluster på Ubuntu-datorer genom att köra följande kommando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Om du använder en Mac startar du det lokala klustret från Docker-avbildningen (detta antar att du har följt [förutsättningarna](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) för att konfigurera det lokala klustret för Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Det kan ta lite tid att starta det lokala klustret. För att bekräfta att klustret är helt aktiverat öppnar du Service Fabric Explorer på `http://localhost:19080`. När du ser fem felfria noder vet du att det lokala klustret är igång och redo. 
    
    ![Service Fabric Explorer visar felfria noder](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Öppna mappen *GS-våren-Boot/SpringServiceFabric* .
1. Kör följande kommando för att ansluta till det lokala klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Kör *install.sh* -skriptet.

    ```bash
    ./install.sh
    ```

1. Öppna valfri webbläsare och öppna programmet genom att gå till `http://localhost:8080`.

    ![Exempel på vår Start Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Nu kan du komma åt Spring Boot-programmet som har distribuerats till ett Service Fabric-kluster.

Mer information finns i exemplet på vår Start [komma igång](https://spring.io/guides/gs/spring-boot/) på vår webbplats.

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster

Tjänsterna kan enkelt skalas över ett kluster om belastningen på tjänsterna ändras. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret. Det går att skala tjänsterna på flera sätt, till exempel med skript eller kommandon från Service Fabric CLI (sfctl). I följande steg använder du Service Fabric Explorer.

Service Fabric Explorer körs i alla Service Fabric-kluster och kan nås från en webbläsare genom att du bläddrar till klustrets HTTP-hanteringsport (19080), till exempel `http://localhost:19080`.

Så här skalar du webbklienttjänsten:

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
1. Välj ellipsen (**...**) bredvid noden **Fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och välj **Scale service (skala tjänst**).

    ![Exempel på Service Fabric Explorer Scale service](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Du kan nu välja att skala antalet instanser av tjänsten.

1. Ändra talet till **3** och välj **Scale service (skala tjänst)**.

    Ett annat sätt att skala tjänsten med hjälp av kommandoraden är som följer.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Välj noden **Fabric:/SpringServiceFabric/SpringGettingStarted** i trädvyn och expandera noden partition (representeras av ett GUID).

    ![Tjänsten för Service Fabric Explorer skalning har slutförts](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Tjänsten har tre instanser, och trädvyn visar vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om ett fel uppstår för en tjänst ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="fail-over-services-in-a-cluster"></a>Redundanstjänster i ett kluster

För att demonstrera tjänstredundans simuleras en nodomstart med Service Fabric Explorer. Se till att endast en instans av din tjänst körs.

1. Öppna Service Fabric Explorer i ditt kluster, till exempel `http://localhost:19080`.
1. Välj ellipsen (**...**) bredvid noden som kör instansen av tjänsten och starta om noden.

    ![Service Fabric Explorer starta om nod](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Instansen av tjänsten flyttas till en annan nod och ditt program får inget avbrott.

    ![Service Fabric Explorer restart-noden lyckades](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Distribuera ett Spring Boot-program till Service Fabric
* Distribuera programmet till ditt lokala kluster
* Skala ut programmet över flera noder
* Utför redundans av tjänsten utan att påverka tillgängligheten

I självstudien för Java-appar finns mer information om hur du arbetar med Java-appar i Service Fabric.

> [!div class="nextstepaction"]
> [Distribuera ett Java-program](./service-fabric-tutorial-create-java-app.md)
