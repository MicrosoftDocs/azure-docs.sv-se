---
title: Distribuera och hantera Apache Storm topologier på Azure HDInsight
description: Lär dig hur du distribuerar, övervakar och hanterar Apache Storm-topologier med storm-instrumentpanelen på Linux-baserade HDInsight. Använd Hadoop-verktyg för Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: fb57992c8e26560061faf68443c4993801f9713d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871579"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuera och hantera Apache Storm topologier på Azure HDInsight

I det här dokumentet lär du dig grunderna för att hantera och övervaka [Apache Storm](https://storm.apache.org/) topologier som körs på storm i HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.

* Valfritt Bekant med Secure Shell (SSH) och Secure Copy (SCP). Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Valfritt Visual Studio, Azure SDK 2.5.1 eller senare och Data Lake verktyg för Visual Studio. Mer information finns i [Apache Hadoop & Visual Studio data Lake-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Skicka en topologi med Visual Studio

Du kan använda Data Lake verktyg för Visual Studio för att skicka C#-eller hybrid topologier till Storm-klustret. I följande steg används ett exempel program. Information om hur du skapar topologi med hjälp av Data Lake-verktygen finns i [Apache Storm topologier med Visual Studio och C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Om du inte redan har installerat den senaste versionen av Data Lake Tools för Visual Studio kan du läsa [använd data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Azure Data Lake-och Stream Analyticss verktygen kallades tidigare HDInsight-verktyg för Visual Studio.
    >
    > Azure Data Lake och Stream Analytics verktyg för Visual Studio ingår i **Azure Development** -arbetsbelastningen för visual Studio 2019.

1. Starta Visual Studio.

1. I fönstret **Starta** väljer du **skapa ett nytt projekt**.

1. I fönstret **skapa ett nytt projekt** markerar du rutan Sök och anger `Storm` . Välj sedan **Storm-exempel** från resultat listan och välj **Nästa**.

1. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn** och går till eller skapar en **plats där** du vill spara det nya projektet i. Välj sedan **Skapa**.

    :::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png" alt-text="Konfigurera det nya projekt fönstret, Visual Studio" border="true":::

1. Från **Server Explorer** högerklickar du på **Azure** och väljer **Anslut till Microsoft Azure prenumeration...** och slutför inloggnings processen.

1. Från **Solution Explorer** högerklickar du på projektet och väljer **Skicka till storm på HDInsight**.

    > [!NOTE]  
    > Ange inloggnings uppgifter för din Azure-prenumeration om du uppmanas att göra det. Om du har mer än en prenumeration loggar du in på den som innehåller din storm i HDInsight-klustret.

1. I dialog rutan **sändning Topology** , under List rutan **Storm-kluster** väljer du storm på HDInsight-kluster och väljer sedan **Skicka**. Du kan övervaka om överföringen lyckas genom att visa fönstret **utdata** .

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Skicka en topologi med SSH och Storm-kommandot

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen använder du följande kommando för att starta **WORDCOUNT** -exempel sto pol Ogin:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Det här kommandot startar exempeltopologin för WordCount (ordräkning) på klustret. Den här topologin skapar slumpmässigt meningar och räknar antalet förekomster av varje ord i meningarna.

    > [!NOTE]  
    > När du skickar topologi till klustret måste du först kopiera. jar-filen som innehåller klustret innan du använder `storm` kommandot. Om du vill kopiera filen till klustret kan du använda `scp` kommandot. Ange till exempel `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > *WORDCOUNT* -exemplet och andra Storm starter-exempel ingår redan i klustret på `/usr/hdp/current/storm-client/contrib/storm-starter/` .

## <a name="submit-a-topology-programmatically"></a>Skicka en topologi program mässigt

Du kan distribuera en topologi program mässigt med Nimbus-tjänsten. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) innehåller ett exempel på ett Java-program som visar hur du distribuerar och startar en topologi via Nimbus-tjänsten.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Övervaka och hantera en topologi i Visual Studio

När du skickar en topologi med Visual Studio visas **visnings fönstret Storm-topologier** . Välj topologin i listan om du vill visa information om topologi som körs.

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png" alt-text="Övervaka topologi, vyer för Storm-topologier, Visual Studio" border="true":::

> [!NOTE]  
> Du kan också visa **Storm-topologier** från **Server Explorer**. Expandera **Azure**  >  **HDInsight**, högerklicka på en storm i HDInsight-kluster och välj sedan **Visa Storm-topologier**.

Välj formen för kanaler eller bultarna om du vill visa information om dessa komponenter. En knapp beskrivning med komponent information visas för det valda objektet.

### <a name="deactivate-and-reactivate-a-topology"></a>Inaktivera och återaktivera en topologi

Om du inaktiverar en topologi pausas den tills topologin stoppas eller återaktiveras. Om du vill utföra dessa åtgärder använder du knapparna **inaktivera** och **återaktivera** i avsnittet **åtgärder** överst i vyn över **Storm-topologier** .

### <a name="rebalance-a-topology"></a>Balansera om en topologi

Ombalansering av en topologi gör att systemet kan ändra topologins parallellitet. Om du till exempel har ändrat storlek på klustret för att lägga till fler anteckningar, tillåter ombalansering att en topologi visar de nya noderna.

Om du vill balansera om en topologi använder du knappen för **ombalans** i avsnittet **åtgärder** i **vyn Storm-topologier** .

> [!WARNING]  
> Ombalansering av en topologi inaktiverar topologin, distribuerar om arbetare jämnt över klustret och återställer sedan topologin till det tillstånd som den var i innan ombalanseringen gjordes. Om topologin var aktiv aktive ras den igen. Om topologin är inaktive rad förblir den inaktive rad.

### <a name="kill-a-running-topology"></a>Avsluta en topologi som körs

Storm-topologier fortsätter att köras tills de har stoppats eller så tas klustret bort. Om du vill stoppa en topologi använder du knappen **Kill** i avsnittet **åtgärder** .

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Övervaka och hantera en topologi med SSH och Storm-kommandot

Med `storm` verktyget kan du arbeta med topologier som körs från kommando raden. Använd `storm -h` för en fullständig lista över kommandon.

### <a name="list-topologies"></a>Lista topologier

Använd följande kommando för att visa en lista över alla topologier som körs:

```shell
storm list
```

Det här kommandot returnerar information liknande följande text:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Inaktivera och återaktivera en topologi

Om du inaktiverar en topologi pausas den tills topologin stoppas eller återaktiveras. Använd följande kommandon för att inaktivera eller återaktivera:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Avsluta en topologi som körs

Storm-topologier, när de har startats, fortsätter att köras tills den stoppats Om du vill stoppa en topologi använder du följande kommando:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Balansera om en topologi

Ombalansering av en topologi gör att systemet kan ändra topologins parallellitet. Om du till exempel har ändrat storlek på klustret för att lägga till fler anteckningar, tillåter ombalansering att en topologi visar de nya noderna.

> [!WARNING]  
> Ombalansering av en topologi inaktiverar topologin, distribuerar om arbetare jämnt över klustret och återställer sedan topologin till det tillstånd som den var i innan ombalanseringen gjordes. Om topologin var aktiv aktive ras den igen. Om den är inaktive rad förblir den inaktive rad.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Övervaka och hantera en topologi med storm-ANVÄNDARGRÄNSSNITTET

Storm-ANVÄNDARGRÄNSSNITTET tillhandahåller ett webb gränssnitt för att arbeta med topologier som körs, och det ingår i ditt HDInsight-kluster. Om du vill se Storm-ANVÄNDARGRÄNSSNITTET använder du en webbläsare för att öppna `https://CLUSTERNAME.azurehdinsight.net/stormui` , där *kluster* namn är namnet på klustret.

> [!NOTE]  
> Om du uppmanas att ange ett användar namn och lösen ord anger du det användar namn och lösen ord för kluster administratör som du använde när du skapade klustret.

### <a name="storm-ui-main-page"></a>Huvud sida för Storm-gränssnitt

Huvud sidan i storm-ANVÄNDARGRÄNSSNITTET innehåller följande information:

| Avsnitt | Beskrivning |
| --- | --- |
| Klustersammanfattning| Grundläggande information om Storm-klustret. |
| Sammanfattning av Nimbus | En lista med grundläggande Nimbus-information. |
| Översikt över topologi | En lista över topologier som körs. Om du vill visa mer information om en speciell topologi väljer du dess länk i kolumnen **namn** . |
| Sammanfattning av ansvarig | Information om Storm-ansvariga. Om du vill se de arbets resurser som är associerade med en speciell ansvarig väljer du dess länk i kolumnen **värd** eller **ID** . |
| Nimbus-konfiguration | Nimbus-konfiguration för klustret. |

Huvud sidan för Storm-gränssnittet liknar den här webb sidan:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png" alt-text="Huvud sida, Storm-användargränssnitt, Apache Storm topologier, Azure" border="true":::

#### <a name="topology-summary"></a>Översikt över topologi

Om du väljer en länk från avsnittet **topologi Sammanfattning** visas följande information om topologin:

| Avsnitt | Beskrivning |
| --- | --- |
| Översikt över topologi | Grundläggande information om topologin. |
| Åtgärder för topologi| Hanterings åtgärder som du kan utföra för topologin. De tillgängliga åtgärderna beskrivs senare i det här avsnittet. |
| Topology-statistik | Statistik om topologin. Om du vill ange en tidsram för en post i det här avsnittet väljer du dess länk i **fönster** kolumnen. |
| Kanaler *(tidsram)* | Kanaler som används av topologin. Om du vill visa mer information om en speciell kanalen väljer du dess länk i kolumnen **ID** . |
| Bultar *(tidsram)* | Bultarna som används av topologin. Om du vill visa mer information om en speciell bult väljer du dess länk i kolumnen **ID** . |
| Arbets resurser | En lista över arbets resurser. Om du vill visa mer information om en speciell arbets resurs väljer du dess länk i kolumnen **värd** . |
| Visualisering av topologi | Knappen **Visa visualisering** som visar en visualisering av topologin. |
| Konfiguration av topologi | Konfigurationen för den valda topologin. |

Sammanfattnings sidan för Storm-topologin liknar den här webb sidan:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png" alt-text="Sidan Topology Summary, Storm UI, Apache Storm, Azure" border="true":::

I avsnittet **topologi åtgärder** kan du välja följande knappar för att utföra en åtgärd:

| Button (Knapp) | Beskrivning |
| --- | --- |
| Aktivera | Återupptar bearbetning av en inaktive rad topologi. |
| Inaktivera | Pausar en topologi som körs. |
| Balansera om | Justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Med den här åtgärden kan topologin justera parallellt för att kompensera för det ytterligare eller minskade antalet noder i klustret.<br/><br/>Mer information finns i <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">förstå parallellitet för en Apache Storm topologi</a>.
| Döda | Avslutar en Storm-topologi efter den angivna tids gränsen. |
| Felsöka | Startar en felsökningssession för den topologi som körs. |
| Stoppa fel sökning | Avslutar felsökningssessionen för den topologi som körs. |
| Ändra loggnings nivå | Ändrar fel söknings logg nivån. |

##### <a name="spout-and-bolt-summary"></a>Sammanfattning av kanalen och bult

Om du väljer en kanalen i avsnittet **kanaler** eller **bultar** visas följande information om det valda objektet:

| Avsnitt | Beskrivning |
| --- | --- |
| Sammanfattning av komponent | Grundläggande information om kanalen eller bult. |
| Komponent åtgärder | **Felsök** och **stoppa fel söknings** knapparna. |
| Kanalen stats statistik eller bult | Statistik om kanalen eller bult. Om du vill ange en tidsram för en post i det här avsnittet väljer du dess länk i **fönster** kolumnen. |
| (Endast bult)<br/>Ingångs statistik *(tidsram)* | Information om de indata strömmar som förbrukas av bulten. |
| Utmatnings statistik *(tidsram)* | Information om strömmar som genereras av kanalen eller bult. |
| Profilering och fel sökning | Kontroller för profilering och fel sökning av komponenterna på den här sidan. Du kan ange värdet för **status/timeout (minuter)** och du kan välja knappar för **JStack**, **starta om Worker** och **heap**. |
| Körningar *(tidsram)* | Information om instanser av kanalen eller bult. Om du vill visa en logg med diagnostisk information som skapats för den här instansen väljer du **port** posten för en speciell utförar. Du kan också se de arbets resurser som är associerade med en speciell utförar genom att välja dess länk i kolumnen **värd** . |
| Fel | Eventuell fel information för kanalen eller bult. |

Sammanfattnings sidan för Storm-bult liknar den här webb sidan:

:::image type="content" source="./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png" alt-text="Sammanfattnings sida för bult, Storm UI, Apache Storm, Azure" border="true":::

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Övervaka och hantera topologin med hjälp av REST API

Storm-ANVÄNDARGRÄNSSNITTET skapas ovanpå REST API, så du kan utföra liknande hanterings-och övervaknings uppgifter med hjälp av REST API. Du kan använda REST API för att skapa anpassade verktyg för att hantera och övervaka Storm-topologier.

Mer information finns i [Apache Storm UI REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Följande information är speciell för att använda REST API med Apache Storm i HDInsight.

> [!IMPORTANT]  
> Storm-REST API är inte offentligt tillgänglig via Internet. Den måste kunna nås via en SSH-tunnel till HDInsight-klustrets huvud nod. Information om hur du skapar och använder en SSH-tunnel finns i [använda SSH-tunnlar för att få åtkomst till Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Bas-URI

Bas-URI: n för REST API på Linux-baserade HDInsight-kluster finns i URL-adress `https://HEADNODEFQDN:8744/api/v1/` , där du ersätter *HEADNODEFQDN* med Head-noden. Huvud nodens domän namn genereras när klustret skapas och är inte statiskt.

Du kan hitta det fullständigt kvalificerade domän namnet (FQDN) för noden kluster huvud på flera sätt:

| Identifierings metod för fullständigt domän namn | Beskrivning |
| --- | --- |
| SSH-session | Använd kommandot `headnode -f` från en SSH-session till klustret. |
| Ambari-webb | På Ambari-klustrets webb sida ( `https://CLUSTERNAME.azurehdinsight.net` ) väljer du **tjänster** överst på sidan och väljer sedan **Storm**. Välj **Storm UI Server** på fliken **Sammanfattning** . FQDN för den nod som är värd för Storm-ANVÄNDARGRÄNSSNITTET och REST API visas längst upp på sidan. |
| Ambari REST API | Använd kommandot `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` för att hämta information om noden som Storm-användargränssnittet och REST API körs på. Ersätt de två instanserna av *kluster* namn med kluster namnet. När du uppmanas till det anger du lösen ordet för användar kontot (admin). I svaret innehåller posten "host_name" för JSON-utdata FQDN för noden. |

### <a name="authentication"></a>Autentisering

Begär anden till REST API måste använda *grundläggande autentisering*, så du måste använda administratörens namn och lösen ord för HDInsight-klustret.

> [!NOTE]  
> Eftersom grundläggande autentisering skickas med klartext bör du *alltid* använda HTTPS för att skydda kommunikationen med klustret.

### <a name="return-values"></a>Returvärden

Information som returneras från REST API kan bara användas i klustret. Till exempel är det fullständigt kvalificerade domän namnet (FQDN) som returneras för [Apache ZooKeeper](https://zookeeper.apache.org/) servrar inte tillgängligt från Internet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [utvecklar Java-baserade topologier med Apache maven](apache-storm-develop-java-topology.md).

En lista över fler exempel topologier finns i [exempel Apache Storm topologier i Azure HDInsight](apache-storm-example-topology.md).
