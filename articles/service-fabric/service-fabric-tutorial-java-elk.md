---
title: Övervaka dina appar i Service Fabric med ELK i Azure
description: I den här självstudien lär du dig hur du ställer in ELK och övervakar Service Fabric-tillämpningar.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5faf26230618161a5b908c9a544a43ec5c33b807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91532030"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Självstudie: Övervaka dina Service Fabric-program med ELK

Den här självstudien är del fyra i en serie. Den visar hur du använder ELK (Elasticsearch, Logstash och Kibana) för att övervaka Service Fabric-tillämpningar som körs i Azure.

I del fyra i serien lär du dig att:
> [!div class="checklist"]
> * Ställa in ELK server i Azure
> * Konfigurera Logstash för att ta emot loggar från Event Hubs
> * Visualisera plattforms- och programloggar i Kibana

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Java Service Fabric Reliable Services-program](service-fabric-tutorial-create-java-app.md)
> * [Distribuera och felsöka ditt program på ett lokalt kluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Distribuera programmet till ett Azure-kluster](service-fabric-tutorial-java-deploy-azure.md)
> * Konfigurera övervakning och diagnostik för programmet
> * [Konfigurera CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Konfigurera programmet så att det genererar loggar för den plats som anges i [del två](service-fabric-tutorial-debug-log-local-cluster.md).
* Genomför [del tre](service-fabric-tutorial-java-deploy-azure.md) och se till att du har ett Service Fabric-kluster som är konfigurerat för att skicka loggar till Event Hubs.
* Principen i Event Hubs som har behörigheten 'Lyssna' och den associerade primärnyckeln från serie tre.

## <a name="download-the-voting-sample-application"></a>Ladda ned exempelprogrammet Röstning

Om du inte byggde exempelprogrammet Röstning i [del ett av självstudiekursen](service-fabric-tutorial-create-java-app.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Skapa en ELK-server i Azure

Du kan använda en förkonfigurerad ELK-miljö för den här självstudiekursen, och om du redan har en går du direkt till avsnittet **Ställa in Logstash**. Om du inte har något skapar du ett i Azure med hjälp av följande steg.

1. Skapa en ELK certifierad av [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) i Azure. För den här självstudien finns det inga särskilda specifikationer att följa när du skapar servern.

2. Gå till din resurs på Azure-portalen och öppna fliken **Startdiagnostik** under avsnittet **Stöd + Felsökning**. Klicka sedan på fliken **Serialiseringslogg**.

    ![Startdiagnostik](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Sök på loggarna efter det lösenord som krävs för att komma åt Kibana-instansen. Det liknar följande kodfragment:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Tryck på anslutningsknappen på översiktssidan för servern i Azure-portalen för att hämta inloggningsuppgifterna.

    ![VM-anslutning](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Anslut via SSH till den server där ELK-avbildningen finns med hjälp av följande kommando

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Konfigurera ELK

1. Det första steget är att läsa in ELK-miljön

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Om du använder en befintlig miljö måste du köra följande kommando för att stoppa Logstash-tjänsten

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Kör följande kommando för att installera Logstash plugin-programmet för Event Hubs.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Skapa eller ändra din befintliga Logstash config-fil med följande innehåll: Om du skapar filen måste den skapas på ```/opt/bitnami/logstash/conf/access-log.conf``` om du använder ELK Bitnami-avbildningen i Azure.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Du kan verifiera konfigurationen genom att köra följande kommando:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Starta Logstash-tjänsten

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Kontrollera din Elasticsearch för att se till att du tar emot data

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Öppna Kibana-instrumentpanelen på **http: \/ /Server-IP** och ange användar namn och lösen ord för Kibana. Har du använt ELK-avbildningen i Azure så är standardanvändarnamnet 'user', och lösenordet är det som du hämtade från **Startdiagnostik**.

    ![Skärm bild som visar en Kibana-instrumentpanel för att visualisera plattforms-och program loggar.](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Starta och köra en ELK-server i Azure
> * Konfigurera servern för att ta emot diagnostikinformation från ditt Service Fabric-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Ställa in CI/CD med Jenkins](service-fabric-tutorial-java-jenkins.md)
