---
title: 'API: er för Azure Service Fabric Java-klient'
description: 'Skapa och använda Service Fabric Java-klient-API: er med hjälp av Service Fabric klient REST API specifikation'
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324632"
---
# <a name="azure-service-fabric-java-client-apis"></a>API: er för Azure Service Fabric Java-klient

Service Fabric klient-API: er gör det möjligt att distribuera och hantera mikrotjänster baserade på program och behållare i ett Service Fabric kluster på Azure, lokalt, på den lokala utvecklings datorn eller i andra moln. Den här artikeln beskriver hur du genererar och använder Service Fabric Java-klient-API: er ovanpå Service Fabric klient REST API: er

## <a name="generate-the-client-code-using-autorest"></a>Generera klient koden med hjälp av AutoRest

[AutoRest](https://github.com/Azure/autorest) är ett verktyg som genererar klient bibliotek för åtkomst till RESTful-webbtjänster. Inmatad till AutoRest är en specifikation som beskriver REST API med hjälp av OpenAPI Specification-formatet. [Service Fabric klient REST-API: er](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) följer den här specifikationen.

Följ stegen nedan för att generera Service Fabric Java-klient kod med hjälp av verktyget AutoRest.

1. Installera nodejs och NPM på datorn

    Om du använder Linux:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Om du använder Mac OS X:
    ```bash
    brew install node
    ```

2. Installera AutoRest med NPM.
    ```bash
    npm install -g autorest
    ```

3. Förgrena och klona [Azure-REST-API-specs-](https://github.com/Azure/azure-rest-api-specs)  lagringsplatsen på din lokala dator och gå till den klonade platsen från terminalen på datorn.


4. Gå till den plats som anges nedan i din klonade lagrings platsen.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Om klustrets version inte är 6,0. * går du till lämplig katalog i mappen stabilt.
    >   

5. Kör följande AutoRest-kommando för att generera Java-klient koden.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Nedan visas ett exempel som demonstrerar användningen av AutoRest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Följande kommando använder ``servicefabric.json`` filen som indata och genererar Java-klientens kod i ``java-rest-api-     code`` mappen och omsluter koden i  ``servicefabricrest`` namn området. Efter det här steget hittar du två mappar ``models`` ``implementation`` och två filer ``ServiceFabricClientAPIs.java`` och ``package-info.java`` genererade i ``java-rest-api-code`` mappen.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Ta med och Använd den genererade klienten i projektet

1. Lägg till den genererade koden korrekt i projektet. Vi rekommenderar att du skapar ett bibliotek med den genererade koden och inkluderar det här biblioteket i projektet.
2. Om du skapar ett bibliotek inkluderar du följande beroende i bibliotekets projekt. Om du följer en annan metod är det lämpligt att ta med beroendet.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Om du till exempel använder maven build-systemet inkluderar du följande i ``pom.xml`` filen:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Skapa en RestClient med hjälp av följande kod:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Använd klient objekt och gör lämpliga anrop vid behov. Här följer några exempel som demonstrerar användningen av klient objekt. Vi förutsätter att programpaketet har skapats och laddats upp i avbildnings arkivet innan du använder API: erna nedan.
    * Etablera ett program
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Skapa ett program

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Förstå den genererade koden
För varje API kommer du att se fyra överlagringar av implementeringen. Om det finns valfria parametrar kan du se fyra fler variationer, inklusive de valfria parametrarna. Överväg till exempel API: et ``removeReplica`` .
 1. **offentlig void-removeReplica (String nodnamn, UUID partitionId, String replicaId, boolesk forceRemove, lång tids gräns)**
    * Detta är den synkrona varianten av API-anropet removeReplica
 2. **offentlig ServiceFuture \<Void> -removeReplicaAsync (String nodnamn, UUID partitionId, String replicaId, boolesk forceRemove, lång tids gräns, slutlig ServiceCallback \<Void> ServiceCallback)**
    * Den här varianten av API-anrop kan användas om du vill använda framtida baserad asynkron programmering och använda återanrop
 3. **offentlig \<Void> removeReplicaAsync (String nodnamn, UUID partitionId, String replicaId)**
    * Den här varianten av API-anrop kan användas om du vill använda reaktiv asynkron programmering
 4. **offentligt observerbar \<ServiceResponse\<Void>>-removeReplicaWithServiceResponseAsync (String nodnamn, UUID partitionId, String replicaId)**
    * Den här varianten av API-anrop kan användas om du vill använda reaktiv asynkron programmering och hantera RAW rest-svar

## <a name="next-steps"></a>Nästa steg
* Läs mer om [REST-API: er för Service Fabric](/rest/api/servicefabric/)
