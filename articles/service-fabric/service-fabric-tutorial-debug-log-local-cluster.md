---
title: Felsöka en Java-app på ett lokalt Service Fabric-kluster
description: I den här självstudiekursen lär du dig att felsöka och hämta loggar från en Service Fabric Java-tillämpning som körs på ett lokalt kluster.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 801702d43bae6d925fa4f7fbc37cf44bf585fa6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531998"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Självstudier: Felsöka en Java-tillämpning som distribuerats i ett lokalt Service Fabric-kluster

Den här självstudien är del två i en serie. Du får lära dig att ansluta en fjärrfelsökare till Service Fabric-programmet med Eclipse. Dessutom får du lära dig hur du dirigerar om loggar från program som körs till en plats som passar utvecklaren.

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Java Service Fabric Reliable Services-program](service-fabric-tutorial-create-java-app.md)
> * Distribuera och felsöka ditt program på ett lokalt kluster
> * [Distribuera programmet till ett Azure-kluster](service-fabric-tutorial-java-deploy-azure.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-java-elk.md)
> * [Konfigurera CI/CD](service-fabric-tutorial-java-jenkins.md)


I del två i serien lär du dig hur du:
> [!div class="checklist"]
> * Felsöka Java-tillämpningar med hjälp av Eclipse
> * Omdirigera loggar till en plats som kan konfigureras


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Konfigurera din utvecklingsmiljö för [Mac](service-fabric-get-started-mac.md) eller [Linux](service-fabric-get-started-linux.md). Följ anvisningarna för att installera plugin-programmet Eclipse, Gradle, Service Fabric-SDK och Service Fabric CLI (sfctl).

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet Röstning

Om du inte byggde exempelprogrammet Röstning i [del ett av självstudiekursen](service-fabric-tutorial-create-java-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Bygg och distribuera](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) programmet till det lokala utvecklings klustret.

## <a name="debug-java-application-using-eclipse"></a>Felsöka Java-tillämpningar med hjälp av Eclipse

1. Öppna Eclipse IDE på din dator och klicka på **File -> Import...** (Arkiv -> Importera...).

2. I popup-fönstret väljer du alternativet **General -> Existing Projects into Workspace** (Allmänt -> Befintliga projekt till arbetsytan) och trycker på Nästa.

3. I fönstret Import Projects (Importera projekt) väljer du alternativet **Select root directory** (Välj rotkatalogen) och katalogen **Voting** (Röstning). Om du har genomgått den första delen av självstudiekursen borde katalogen **Voting** (Röstning) redan finnas i katalogen **Eclipse-workspace** (Eclipse-arbetsyta).

4. Uppdatera entryPoint.sh för tjänsten som du vill felsöka så att den startar Java-processen med fjärrstyrda felsökningsparametrar. I den här självstudien används den tillstånds lösa klient delen: *röstning/VotingApplication/VotingWebPkg/Code/entryPoint. sh*. Port 8001 har angetts för fel sökning i det här exemplet.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Uppdatera applikationsmanifestet genom att ange instansantalet eller replikantalet för tjänsten som felsöks till ett. Den här inställningen förhindrar konflikter kring den port som används för felsökning. För tillståndslösa tjänster kan du t.ex. ange ``InstanceCount="1"`` och för tillståndskänsliga tjänster kan du ange målet och minsta replikuppsättningsstorlek till 1 enligt följande: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. Gå till Eclipse IDE och välj **Run -> Debug Configurations -> Remote Java Application** (Kör -> Felsöka konfigurationer -> Fjärranslutna Java-program), klicka på knappen **New** (Ny), ange egenskaper enligt nedan och klicka på **Apply** (Tillämpa).

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Lägg till en brytpunkt på rad 109 i filen *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*.

8. Högerklicka på projektet **Voting** i Package Explorer och klicka på **Service Fabric -> Publish Application...** (Service Fabric -> Publicera program...).

9. I fönstret **Publish Application** (Publicera program) väljer du **Local.json** i listrutan och klickar på **Publish** (Publicera).

10. I Eclipse IDE väljer du **Run -> Debug Configurations -> Remote Java Application** (Kör -> Felsöka konfigurationer -> Fjärranslutna Java-program), klickar på konfigurationen **Voting** (Röstning) och klickar på **Debug** (Felsök).

11. Gå till webbläsaren och få åtkomst till **localhost: 8080**. Detta kommer automatiskt att nå Bryt punkten och Sol förmörkelse anger **fel söknings perspektivet**.

Nu kan du använda samma steg för att felsöka alla Service Fabric program i Sol förmörkelse.

## <a name="redirect-application-logs-to-custom-location"></a>Omdirigera programloggar till en anpassad plats

Följande steg guidar dig igenom dirigering av programloggar från standardplatsen */var/log/syslog* plats till en anpassad plats.

1. För närvarande stöder program som körs i Service Fabric Linux-kluster bara plockning av en enda loggfil. Om du vill konfigurera ett program så att loggarna alltid går till */tmp/mysfapp0.0.log* skapar du en fil med namnet Logging. properties i följande plats *röstning/VotingApplication/VotingWebPkg/Code/Logging. Properties* och lägger till följande innehåll.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Lägg till följande parameter i *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* för Java-körningskommandot:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    I följande exempel visas ett exempel på körning med fel söknings programmet, på samma sätt som för körningen i föregående avsnitt.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

Nu har du lärt dig hur du felsöker och komma åt dina programloggar när du utvecklar Service Fabric Java-tillämpningar.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Felsöka Java-tillämpningar med hjälp av Eclipse
> * Omdirigera loggar till en plats som kan konfigureras

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Distribuera programmet i Azure](service-fabric-tutorial-java-deploy-azure.md)