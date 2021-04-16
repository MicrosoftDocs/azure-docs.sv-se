---
title: 'Snabbstart: Skapa en Java-app på Azure Service Fabric'
description: I den här snabbstarten skapar du ett Java-program för Azure med ett Service Fabric-exempelprogram (tillförlitliga tjänster).
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 199fe459ec8fd58d05b4946dda4291c46a4881c3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530902"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Snabbstart: Distribuera en Java-app till Azure Service Fabric på Linux

I den här snabbstarten distribuerar du ett Java-program till Azure Service Fabric med Eclipse IDE på en Linux-utvecklardator. När du är klar har du ett röstningsprogram med en Java-webbklient som sparar röstningsresultat i en tillståndskänslig backend-tjänst i klustret.

Azure Service Fabric är en plattform för distribuerade system för distribution och hantering av mikrotjänster och containrar.

## <a name="prerequisites"></a>Förutsättningar

- [Java-miljö](./service-fabric-get-started-linux.md#set-up-java-development) och [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+ och](https://www.eclipse.org/downloads/packages/) [Eclipse-plugin-programmet för Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK och kommandoradsgränssnitt (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Ladda ned exemplet

Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Köra appen lokalt

1. Starta ditt lokala kluster genom att köra följande kommando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Det kan ta lite tid att starta det lokala klustret. För att bekräfta att klustret är helt aktiverat öppnar du Service Fabric Explorer på `http://localhost:19080`. När du ser fem felfria noder vet du att det lokala klustret är igång och redo.

    ![Azure Service Fabric Explorer visar felfria noder](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Öppna Eclipse.
3. Välj **File**  >  **Import**  >  **Gradle** Existing  >  Gradle Project (Filimport av befintligt **Gradle-projekt)** och följ guiden.
4. Välj **Katalog** och välj katalogen **Voting (Röstning)** i mappen **service-fabric-java-quickstart** som du klonade från GitHub. Välj **Slutför**.

    ![Importera Gradle-projekt till Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Nu finns projektet `Voting` i Package Explorer (Paketutforskaren) för Eclipse.
6. Högerklicka på projektet och välj **Publicera** program under Service Fabric **listrutan.** Välj **PublishProfiles/Local.jspå** som Målprofil och välj **Publicera.**

    ![Azure Service Fabric publicera lokal JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Öppna valfri webbläsare och öppna programmet genom att gå till `http://localhost:8080`.

    ![Azure Service Fabric lokal värd](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Du kan nu lägga till en uppsättning röstningsalternativ och börja ta emot röster. Programmet körs och lagrar alla data i Service Fabric-klustret, utan att en separat databas krävs.

![Exempel Service Fabric Azure-röstning](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Skala program och tjänster i ett kluster

Tjänsterna kan enkelt skalas över ett kluster när belastningen på tjänsterna ändras. Du kan skala en tjänst genom att ändra antalet instanser som körs i klustret. Det finns många sätt att skala dina tjänster. Du kan till exempel använda skript eller kommandon från Service Fabric CLI ( `sfctl` ). I följande steg använder du Service Fabric Explorer.

Service Fabric Explorer körs i Service Fabric kluster och kan nås från en webbläsare genom att bläddra till klustrets HTTP-hanteringsport (19080). Till exempel `http://localhost:19080`.

Så här skalar du webbklienttjänsten:

1. Öppna Service Fabric Explorer i klustret. Till exempel `https://localhost:19080`.
2. Välj ellipsen (**...**) bredvid noden **fabric:/Voting/VotingWeb** i trädvyn och välj **Scale Service (Skala tjänst).**

    ![Skala en tjänst i Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Du kan nu välja att skala antalet instanser av frontwebbtjänsten.

3. Ändra antalet till **2 och** välj **Scale Service (Skala tjänst).**
4. Välj **noden fabric:/Voting/VotingWeb** i trädvyn och expandera partitionsnoden (som representeras av en GUID).

    ![Skalad tjänst i Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Du kan nu se att tjänsten har två instanser och i trädvyn du se vilka noder instanserna körs på.

Med den här enkla hanteringsåtgärden har du dubblerat tillgängliga resurser för bearbetning av användarbelastningen i frontwebbtjänsten. Det är viktigt att förstå att du inte behöver flera instanser av en tjänst för att den ska kunna köras på ett tillförlitligt sätt. Om ett fel uppstår för en tjänst ser Service Fabric till att en ny tjänstinstans körs i klustret.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att:

* Använda Eclipse som ett verktyg för Service Fabric Java-program
* Distribuera Java-program till ditt lokala kluster
* Skala ut programmet över flera noder

I självstudien för Java-appar finns mer information om hur du arbetar med Java-appar i Service Fabric.

> [!div class="nextstepaction"]
> [Distribuera ett Java-program](./service-fabric-tutorial-create-java-app.md)
