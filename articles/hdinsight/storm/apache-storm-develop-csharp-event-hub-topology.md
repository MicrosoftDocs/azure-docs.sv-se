---
title: Bearbeta händelser från Event Hubs med storm – Azure HDInsight
description: Lär dig hur du bearbetar data från Azure Event Hubs med en C# Storm-topologi som skapats i Visual Studio med hjälp av HDInsight-verktygen för Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4393c6797f5a164a063b55f8994d7d37d278f3c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929192"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)

Lär dig hur du arbetar med Azure Event Hubs från [Apache Storm](https://storm.apache.org/) i HDInsight. I det här dokumentet används en C# Storm-topologi för att läsa och skriva data från Event Hubs

> [!NOTE]  
> En Java-version av det här projektet finns i [process händelser från Azure Event Hubs med Apache storm på HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

Stegen i det här dokumentet använder SCP.NET, ett NuGet-paket som gör det enkelt att skapa C#-topologier och komponenter som kan användas med storm på HDInsight.

HDInsight 3,4 och större använder mono för att köra C#-topologier. Exemplet som används i det här dokumentet fungerar med HDInsight 3,6. Om du planerar att skapa egna .NET-lösningar för HDInsight kan du kontrol lera det [svartvita kompatibilitetsproblemet](https://www.mono-project.com/docs/about-mono/compatibility/) .

### <a name="cluster-versioning"></a>Kluster version

Microsoft. SCP. net. SDK NuGet-paketet som du använder för ditt projekt måste matcha den huvud version av storm som är installerad på HDInsight. HDInsight-versionerna 3,5 och 3,6 använder Storm 1. x, så du måste använda SCP.NET version 1.0. x med dessa kluster.

C#-topologier måste också vara riktade till .NET 4,5.

## <a name="how-to-work-with-event-hubs"></a>Så här arbetar du med Event Hubs

Microsoft tillhandahåller en uppsättning Java-komponenter som kan användas för att kommunicera med Event Hubs från en Storm-topologi. Du kan hitta filen Java Archive (JAR) som innehåller en version av HDInsight 3,6 som är kompatibel med den här typen av komponenter [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> När komponenterna är skrivna i Java kan du enkelt använda dem från en C#-topologi.

Följande komponenter används i det här exemplet:

* __EventHubSpout__: läser data från Event Hubs.
* __EventHubBolt__: skriver data till Event Hubs.
* __EventHubSpoutConfig__: används för att konfigurera EventHubSpout.
* __EventHubBoltConfig__: används för att konfigurera EventHubBolt.

### <a name="example-spout-usage"></a>Exempel på kanalen-användning

SCP.NET tillhandahåller metoder för att lägga till en EventHubSpout i topologin. Dessa metoder gör det enklare att lägga till en kanalen än att använda allmänna metoder för att lägga till en Java-komponent. Följande exempel visar hur du skapar en kanalen med hjälp av metoderna __SetEventHubSpout__ och **EventHubSpoutConfig** som tillhandahålls av SCP.net:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

I föregående exempel skapas en ny kanalen-komponent som heter __EventHubSpout__ och konfigurerar den att kommunicera med en händelsehubben. Parallel-tipset för komponenten anges till antalet partitioner i händelsehubben. Med den här inställningen kan Storm skapa en instans av komponenten för varje partition.

### <a name="example-bolt-usage"></a>Exempel på blixt användning

Använd **JavaComponmentConstructor** -metoden för att skapa en förekomst av bulten. Följande exempel visar hur du skapar och konfigurerar en ny instans av **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> I det här exemplet används ett clojure-uttryck som skickas som en sträng, i stället för att använda **JavaComponentConstructor** för att skapa en **EventHubBoltConfig**, som kanalen-exemplet gjorde. Någon av metoderna fungerar. Använd den metod som är bäst för dig.

## <a name="download-the-completed-project"></a>Ladda ned det slutförda projektet

Du kan ladda ned en fullständig version av projektet som skapats i den här artikeln från [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Du måste dock fortfarande ange konfigurations inställningar genom att följa stegen i den här artikeln.

### <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.

* En [Azure Event Hub](../../event-hubs/event-hubs-create.md).

* [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* [HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 eller senare i utvecklings miljön. JDK-nedladdningar är tillgängliga från [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).

  * **JAVA_HOME** miljö variabeln måste peka på den katalog som innehåller Java.
  * Katalogen **% JAVA_HOME%/bin** måste finnas i sökvägen.

## <a name="download-the-event-hubs-components"></a>Ladda ned Event Hubs-komponenterna

Hämta Event Hubs kanalen och bult-komponenten från [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Skapa en katalog med namnet `eventhubspout` och spara filen i katalogen.

## <a name="configure-event-hubs"></a>Konfigurera Event Hubs

Event Hubs är data källan för det här exemplet. Använd informationen i avsnittet "skapa en Event Hub" i [komma igång med Event Hubs](../../event-hubs/event-hubs-create.md).

1. När händelsehubben har skapats visar du inställningarna för **EventHub** i Azure Portal och väljer **principer för delad åtkomst**. Välj **+ Lägg** till för att skapa följande principer:

   | Name | Behörigheter |
   | --- | --- |
   | skrivare |Skicka |
   | Acrobat |Lyssna |

    ![Skärm bild av fönstret dela åtkomst principer](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Välj principer för **läsare** och **skrivare** . Kopiera och spara värdet för primär nyckel för båda principerna, eftersom dessa värden används senare.

## <a name="configure-the-eventhubwriter"></a>Konfigurera EventHubWriter

1. Om du inte redan har installerat den senaste versionen av HDInsight Tools för Visual Studio kan du läsa [Kom igång med HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Hämta lösningen från [eventhub-Storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Öppna **EventHubExample. SLN**. I **EventHubWriter** -projektet öppnar du **App.config** -filen. Använd informationen från händelsehubben som du konfigurerade tidigare för att fylla i värdet för följande nycklar:

   | Tangent | Värde |
   | --- | --- |
   | EventHubPolicyName |skrivare (om du har använt ett annat namn för principen med *send* -behörighet använder du den i stället.) |
   | EventHubPolicyKey |Nyckeln för skrivar principen. |
   | EventHubNamespace |Det namn område som innehåller händelsehubben. |
   | EventHubName |Ditt Event Hub-namn. |
   | EventHubPartitionCount |Antalet partitioner i händelsehubben. |

4. Spara och Stäng **App.configs** filen.

## <a name="configure-the-eventhubreader"></a>Konfigurera EventHubReader

1. Öppna **EventHubReader** -projektet.

2. Öppna **App.config** -filen för **EventHubReader**. Använd informationen från händelsehubben som du konfigurerade tidigare för att fylla i värdet för följande nycklar:

   | Tangent | Värde |
   | --- | --- |
   | EventHubPolicyName |läsare (om du har använt ett annat namn på principen med behörigheten *Lyssna* använder du den i stället.) |
   | EventHubPolicyKey |Nyckeln för läsar principen. |
   | EventHubNamespace |Det namn område som innehåller händelsehubben. |
   | EventHubName |Ditt Event Hub-namn. |
   | EventHubPartitionCount |Antalet partitioner i händelsehubben. |

3. Spara och Stäng **App.configs** filen.

## <a name="deploy-the-topologies"></a>Distribuera topologierna

1. Från **Solution Explorer** högerklickar du på projektet **EventHubReader** och väljer **Skicka till storm på HDInsight**.

    ![Skärm bild av Solution Explorer, med skicka till storm på HDInsight markerat](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. I dialog rutan **sändning Topology** väljer du ditt **Storm-kluster**. Expandera **ytterligare konfigurationer**, Välj **sökvägar för Java-fil**, Välj **...** och välj den katalog som innehåller jar-filen som du laddade ned tidigare. Klicka slutligen på **Skicka**.

    ![Skärm bild av dialog rutan Skicka topologi](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. När topologin har skickats visas **visnings programmet Storm-topologier** . Om du vill visa information om topologin väljer du **EventHubReader** -topologin i det vänstra fönstret.

    ![Skärm bild av Storm-topologi Viewer](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Från **Solution Explorer** högerklickar du på projektet **EventHubWriter** och väljer **Skicka till storm på HDInsight**.

5. I dialog rutan **sändning Topology** väljer du ditt **Storm-kluster**. Expandera **ytterligare konfigurationer**, Välj **sökvägar för Java-fil**, Välj **...** och välj den katalog som innehåller den jar-fil som du laddade ned tidigare. Klicka slutligen på **Skicka**.

6. När topologin har skickats uppdaterar du listan topologi i storm topologys **Viewer** för att kontrol lera att båda topologierna körs i klustret.

7. I **Storm** topologys Viewer väljer du **EventHubReader** -topologin.

8. Öppna komponent sammanfattningen för bult genom att dubbelklicka på **LogBolt** -komponenten i diagrammet.

9. I avsnittet **körningar** väljer du en av länkarna i kolumnen **port** . Detta visar information som loggats av komponenten. Den loggade informationen liknar följande text:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Stoppa topologierna

Om du vill stoppa topologierna väljer du varje topologi i **Storm Topology Viewer** och klickar sedan på **Avsluta**.

![Skärm bild av storm Topology Viewer med knappen Stopp markerad](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Ta bort klustret

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Java-Event Hubs kanalen och bult från en C#-topologi för att arbeta med data i Azure Event Hubs. Mer information om hur du skapar C#-topologier finns i följande avsnitt:

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guide för SCP-programmering](apache-storm-scp-programming-guide.md)
* [Exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md)