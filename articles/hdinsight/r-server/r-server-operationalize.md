---
title: Operationalisera ML-tjänster i HDInsight – Azure
description: Lär dig hur du operationalisera din data modell för att göra förutsägelser med ML-tjänster i Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/27/2018
ms.openlocfilehash: 7d597c4dbb81562050e9523c61d47d0020fc9059
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869487"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operationalisera ML Services-kluster på Azure HDInsight

När du har använt ML Services-kluster i HDInsight för att slutföra din data modellering kan du operationalisera modellen för att göra förutsägelser. Den här artikeln innehåller anvisningar om hur du utför den här uppgiften.

## <a name="prerequisites"></a>Förutsättningar

* Ett ML Services-kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj ml- **tjänster** för **kluster typ**.

* En SSH-klient (Secure Shell): En SSH-klient används för att fjärransluta till HDInsight-klustret och köra kommandon direkt på klustret. Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operationalisera ML-kluster med konfiguration med en enda ruta

> [!NOTE]  
> Stegen nedan gäller för R Server 9,0 och ML Server 9,1. För ML Server 9,3, se [använda administrations verktyget för att hantera driftsättning-konfigurationen](/machine-learning-server/operationalize/configure-admin-cli-launch).

1. SSH till kantnoden.

    ```bash
    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

    Instruktioner för hur du använder SSH med Azure HDInsight finns i [använda SSH med HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ändra katalog för den aktuella versionen och sudo punkt net-dll: 

    - För Microsoft ML Server 9,1:

        ```bash
        cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
        sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll
        ```

    - Microsoft R-server 9.0:

        ```bash
        cd /usr/lib64/microsoft-deployr/9.0.1
        sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
        ```

1. Du visas med alternativen för att välja bland. Välj det första alternativet, som du ser i följande skärm bild, för att **konfigurera ml Server för driftsättning**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-1.png" alt-text="Verktyget R Server Administration Välj" border="true":::

1. Du visas nu med alternativet att välja hur du vill operationalisera ML Server. Från de visade alternativen väljer du det första genom att ange **en**.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-2.png" alt-text="R Server administrations verktyg operationalisera" border="true":::

1. När du uppmanas till det anger du och anger lösen ordet för en lokal administratörs användare.

1. Du bör se utdata som föreslår att åtgärden lyckades. Du uppmanas också att välja ett annat alternativ på menyn. Välj E för att gå tillbaka till huvud menyn.

    :::image type="content" source="./media/r-server-operationalize/admin-util-one-box-3.png" alt-text="R Server administrations verktyget lyckades" border="true":::

1. Du kan också utföra diagnostiska kontroller genom att köra ett diagnostiskt test på följande sätt:

    a. Från huvud menyn väljer du **6** för att köra diagnostiska tester.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic1.png" alt-text="Verktyget R Server Administration diagnostik" border="true":::

    b. Välj **en** på menyn diagnostiska test. När du uppmanas till det anger du det lösen ord som du har angett för den lokala administratörs användaren.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic2.png" alt-text="Test av R Server administrations verktyg" border="true":::

    c. Kontrol lera att resultatet visar att den övergripande hälsan är ett pass.

    :::image type="content" source="./media/r-server-operationalize/hdinsight-diagnostic3.png" alt-text="Verktyg för R Server-Administration" border="true":::

    d. Från meny alternativen anger du **E** för att återgå till huvud menyn och anger sedan **8** för att avsluta administrations verktyget.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Långa fördröjningar när webb tjänsten förbrukas på Apache Spark

Om du stöter på långa fördröjningar när du försöker använda en webb tjänst som skapats med mrsdeploy-funktioner i en Apache Spark beräknings kontext kan du behöva lägga till några saknade mappar. Spark-programmet tillhör en användare som kallas '*rserve2*' när den anropas från en webbtjänst med hjälp av mrsdeploy-funktioner. Så här kan du lösa problemet:

```r
# Create these required folders for user 'rserve2' in local and hdfs:

hadoop fs -mkdir /user/RevoShare/rserve2
hadoop fs -chmod 777 /user/RevoShare/rserve2

mkdir /var/RevoShare/rserve2
chmod 777 /var/RevoShare/rserve2


# Next, create a new Spark compute context:

rxSparkConnect(reset = TRUE)
```

I det här skedet är konfigurationen för driftsättning klar. Nu kan du använda `mrsdeploy` paketet på din RClient för att ansluta till driftsättning på Edge-noden och börja använda dess funktioner som [fjärrkörning](/machine-learning-server/r/how-to-execute-code-remotely) och [webb tjänster](/machine-learning-server/operationalize/concept-what-are-web-services). Beroende på om klustret är konfigurerat i ett virtuellt nätverk eller inte kan du behöva konfigurera portvidarebefordran via SSH-inloggning. I följande avsnitt beskrivs hur du konfigurerar den här tunneln.

### <a name="ml-services-cluster-on-virtual-network"></a>ML Services-kluster i virtuellt nätverk

Se till att du tillåter trafik genom port 12800 till kantnoden. På så sätt kan du använda kantnoden för att ansluta till driftsättningsfunktionen.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Om `remoteLogin()` inte kan ansluta till kantnoden, men SSH till kantnod fungerar, kontrollerar du att regeln för att tillåta trafik via port 12800 har ställts in på rätt sätt. Om problemet kvarstår kan du kringgå det genom att ställa in portvidarebefordran via SSH. Anvisningar finns i följande avsnitt:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Kluster för ML-tjänster har inte kon figurer ATS i det virtuella nätverket

Om inget kluster har konfigurerats på vnet, eller om du har problem med anslutningen via vnet, kan du använda SSH-portvidarebefordran:

```bash
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

När SSH-sessionen är aktiv vidarebefordras trafiken från den lokala datorns port 12800 till Edge-nodens port 12800 via SSH-sessionen. Se till att du använder `127.0.0.1:12800` i metoden `remoteLogin()`. Detta loggar in på Edge-nodens driftsättning via vidarebefordran av port.

```r
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skala operationella datornoder på HDInsight Worker-noder

Om du vill skala Compute-noderna måste du först inaktivera arbetsnoderna och sedan konfigurera Compute-noderna på de inaktiverade arbetsnoderna.

### <a name="step-1-decommission-the-worker-nodes"></a>Steg 1: inaktivera arbetsnoderna

ML Services-kluster hanteras inte via [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Om arbetsnoderna inte har inaktiverats fungerar inte-resurs hanteraren som förväntat eftersom den inte är medveten om de resurser som tas upp av servern. För att undvika detta rekommenderar vi att du inaktiverar arbetsnoderna innan du skalar ut beräkningsnoderna.

Följ dessa steg om du vill inaktivera arbetsnoder:

1. Logga in på klustrets Ambari-konsol och klicka på fliken **värdar** .

1. Välj Worker-noder (som ska inaktive RAS).

1. Klicka på **åtgärder**  >  **markerade värdar**  >  **värdar**  >  **Aktivera underhålls läge**. I bilden nedan har vi till exempel valt att inaktivera wn3 och wn4.  

   :::image type="content" source="./media/r-server-operationalize/get-started-operationalization.png" alt-text="Apache Ambari aktivera underhålls läge" border="true":::  

* Välj **åtgärder**  >  **valda värdar**  >  **DataNodes** > Klicka på **inaktivera**.
* Välj **åtgärder**  >  **valda värdar**  >  **) nodemanagers** > Klicka på **inaktivera**.
* Välj **åtgärder**  >  **valda värdar**  >  **DataNodes** > Klicka på **stoppa**.
* Välj **åtgärder**  >  **valda värdar**  >  **) nodemanagers** > Klicka på **stoppa**.
* Välj **åtgärder** som är  >  **markerade värdar**  >  **värdar** > Klicka på **stoppa alla komponenter**.
* Avmarkera arbetsnoderna och markera huvudnoderna.
* Välj **åtgärder**  >  **valda värdar** > "**värdar**  >  **starta om alla komponenter**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Steg 2: Konfigurera Compute-noder på varje inaktive rad arbetsnoder

1. SSH till varje inaktiverad arbetsnod.

1. Kör admin-verktyget med relevant DLL för det ML Services-kluster som du har. Kör följande för ML Server 9,1:

    ```bash
    dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll
    ```

1. Ange **1** om du vill välja alternativ **Konfigurera ml Server för driftsättning**.

1. Välj alternativet **C** för att välja `C. Compute node` . Då konfigureras beräkningsnoden på arbetsnoden.

1. Avsluta admin-verktyget.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Steg 3: Lägg till information om Compute-noder på webbnoden

När alla inaktiverade arbetsnoder har kon figurer ATS för att köra en Compute-nod kommer du tillbaka till noden Edge och lägga till inaktiverade arbetsnoders IP-adresser i ML Server webbnodens konfiguration:

1. SSH till kantnoden.

1. Kör `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Leta efter avsnittet "URI" och Lägg till arbetsnodens IP-och port information.

    ```json
    "Uris": {
        "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
        "Values": [
            "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
        ]
    }
    ```

## <a name="next-steps"></a>Nästa steg

* [Hantera ML-tjänstkluster i HDInsight](r-server-hdinsight-manage.md)
* [Alternativ för beräkningskontexter för ML-tjänstkluster i HDInsight](r-server-compute-contexts.md)
* [Alternativ för Azure Storage för ML-tjänstkluster i HDInsight](r-server-storage.md)