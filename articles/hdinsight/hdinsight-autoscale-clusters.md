---
title: Skala Azure HDInsight-kluster automatiskt
description: Använd funktionen för automatisk skalning för att automatiskt skala Azure HDInsight-kluster baserat på ett schema eller prestanda mått.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 130a5a58fc7dab6f94c011cf9764743f9114e48a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942642"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster automatiskt

Azure HDInsight: s kostnads fria funktioner för automatisk skalning kan öka eller minska antalet arbetsnoder i klustret baserat på tidigare angivna villkor. Funktionen för automatisk skalning fungerar genom att skala antalet noder inom de förinställda gränserna utifrån antingen prestanda mått eller ett schema för skalnings-och skalnings åtgärder.

## <a name="how-it-works"></a>Så här fungerar det

Funktionen för automatisk skalning använder två typer av villkor för att utlösa skalnings händelser: tröskelvärden för olika kluster prestanda värden (kallas *belastningsutjämnad skalning*) och tidsbaserade utlösare (kallas *schema-baserad skalning*). Vid inläsnings skalning ändras antalet noder i klustret, inom ett intervall som du anger, för att säkerställa optimal CPU-användning och minimera löpande kostnad. Schemabaserade skalningar ändrar antalet noder i klustret baserat på ett schema för skalnings-och skalnings åtgärder.

Följande videoklipp ger en översikt över de utmaningar som autoskalning löser och hur det kan hjälpa dig att kontrol lera kostnaderna med HDInsight.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Välja inload-baserad eller schema-baserad skalning

Tänk på följande faktorer när du väljer en skalnings typ:

* Belastnings avvikelse: följer belastningen på klustret ett konsekvent mönster vid vissa tidpunkter på vissa dagar? Om inte, är belastnings beroende schemaläggning ett bättre alternativ.
* SLA-krav: autoskalning av skalning är aktiv i stället för förutsägelse. Kommer det att finnas en tillräcklig fördröjning mellan när belastningen börjar öka och när klustret måste ha en mål storlek? Om det finns strikta SLA-krav och belastningen är ett fast känt mönster, är schema-based ett bättre alternativ.

### <a name="cluster-metrics"></a>Kluster mått

Autoskalning övervakar kontinuerligt klustret och samlar in följande mått:

|Metric|Beskrivning|
|---|---|
|Total väntande processor|Det totala antalet kärnor som krävs för att starta körningen av alla väntande behållare.|
|Totalt väntande minne|Det totala minne (i MB) som krävs för att starta körningen av alla väntande behållare.|
|Total ledig CPU|Summan av alla oanvända kärnor på aktiva arbetsnoder.|
|Totalt ledigt minne|Summan av oanvänt minne (i MB) på aktiva arbetsnoder.|
|Använt minne per nod|Belastningen på en arbets nod. En arbetsnoden där 10 GB minne används, anses vara under mer belastning än en arbets tagare med 2 GB använt minne.|
|Antal program huvud per nod|Antalet program huvud behållare som körs på en arbetsnod. En arbetsnoden som är värd för två AM-behållare anses vara viktigare än en arbetsnoden som är värd för noll AM-behållare.|

Ovanstående mått kontrol leras var 60: e sekund. Du kan konfigurera skalnings åtgärder för klustret med hjälp av dessa mått.

### <a name="load-based-scale-conditions"></a>Belastnings beroende skalnings villkor

När följande villkor upptäcks kommer autoskalning att utfärda en skalnings förfrågan:

|Skala upp|Skala ned|
|---|---|
|Den totala väntande processorn är större än den totala kostnads fria processorn i mer än 3 minuter.|Den totala väntande processorn är mindre än den totala lediga processorn i mer än 10 minuter.|
|Totalt minne som väntar är större än det totala lediga minnet i mer än 3 minuter.|Totalt minne som väntar är mindre än det totala lediga minnet i mer än 10 minuter.|

För att skala upp får autoskalning en skalnings förfrågan för att lägga till antalet noder som krävs. Skalan baseras på hur många nya arbetsnoder som behövs för att uppfylla de aktuella processor-och minnes kraven.

Vid nedskalning skickar autoskalning en begäran om att ta bort ett visst antal noder. Skalan baseras på antalet AM-behållare per nod. Och aktuella processor-och minnes krav. Tjänsten identifierar också vilka noder som ska tas bort, baserat på den aktuella jobb körningen. Åtgärden för att skala ned inaktiverar först noderna och tar sedan bort dem från klustret.

### <a name="cluster-compatibility"></a>Kluster kompatibilitet

> [!Important]
> Autoskalningsfunktionen i Azure HDInsight släpptes för allmän tillgänglighet den 7 november 2019 för Spark- och Hadoop-kluster och innehöll förbättringar som inte var tillgängliga i förhandsversionen av funktionen. Om du har skapat ett Spark-kluster före den 7 november 2019 och vill använda autoskalningsfunktionen i ditt kluster är den rekommenderade vägen att skapa ett nytt kluster och aktivera autoskalning i det nya klustret.
>
> Autoskalning för interaktiv fråga (LLAP) släpptes för allmän tillgänglighet för HDI 4,0 27 2020. HBase-kluster är fortfarande i för hands version. Autoskalning är bara tillgängligt för Spark-, Hadoop-, Interactive Query- och HBase-kluster.

I följande tabell beskrivs de kluster typer och versioner som är kompatibla med funktionen för autoskalning.

| Version | Spark | Hive | Interaktiv fråga | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4,0 utan ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 3,6 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |
| HDInsight 4,0 med ESP | Ja | Ja | Ja | Ja* | Inga | Inga | Inga |

\* HBase-kluster kan bara konfigureras för schemabaserade skalning, inte för inläsning.

## <a name="get-started"></a>Kom igång

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Skapa ett kluster med load-based autoskalning

Om du vill aktivera funktionen för automatisk skalning med belastningsutjämnad skalning utför du följande steg som en del av processen för att skapa ett vanligt kluster:

1. På fliken **konfiguration och priser** väljer du kryss rutan **Aktivera autoskalning** .
1. Välj **load-based** under **typ av autoskalning**.
1. Ange de avsedda värdena för följande egenskaper:  

    * Initialt **antal noder** för **arbetsnoden**.
    * **Minsta** antal arbetsnoder.
    * **Högsta** antal arbetsnoder.

    ![Aktivera inläsnings-baserad autoskalning för arbetsnoden](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Det första antalet arbetsnoder måste ligga mellan minimum och maximum, inklusive. Det här värdet definierar den ursprungliga storleken på klustret när den skapas. Det minsta antalet arbetsnoder måste anges till tre eller fler. Om du skalar klustret till färre än tre noder kan det bli fastnat i fel säkert läge på grund av otillräcklig filreplikering.  Mer information finns i avsnittet [komma fastna i fel säkert läge](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Skapa ett kluster med hjälp av schemabaserade autoskalning

Om du vill aktivera funktionen för automatisk skalning med schemabaserade skalning utför du följande steg som en del av processen för att skapa ett vanligt kluster:

1. Markera kryss rutan **Aktivera autoskalning** på fliken **konfiguration + prissättning** .
1. Ange **antalet noder** för **arbetsnoden**, som styr gränsen för att skala upp klustret.
1. Välj alternativet **schema – baserat** på **typ av autoskalning**.
1. Välj **Konfigurera** för att öppna fönstret för **Automatisk skalnings konfiguration** .
1. Välj din tidszon och klicka sedan på **+ Lägg till villkor**
1. Välj vecko dagarna som det nya villkoret ska gälla för.
1. Redigera den tid då villkoret ska börja gälla och antalet noder som klustret ska skalas till.
1. Lägg till fler villkor om det behövs.

    ![Aktivera schema skapande för arbetsnoden](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Antalet noder måste vara mellan 3 och det högsta antalet arbetsnoder som du angav innan du lägger till villkor.

### <a name="final-creation-steps"></a>Slutliga skapande steg

Välj VM-typ för arbetsnoder genom att välja en virtuell dator i list rutan under **Node-storlek**. När du har valt VM-typen för varje nodtyp kan du se det beräknade kostnads intervallet för hela klustret. Justera VM-typerna så att de passar din budget.

![Aktivera schemaläggning av noden för den automatiska skalnings storleken på arbetsnoden](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Din prenumeration har en kapacitets kvot för varje region. Det totala antalet kärnor för dina Head-noder och maximalt antal arbetsnoder får inte överstiga kapacitets kvoten. Den här kvoten är dock en mjuk gräns. Du kan alltid skapa ett support ärende så att det blir lättare att öka.

> [!Note]  
> Om du överskrider den totala kvot gränsen visas ett fel meddelande om att den maximala noden har överskridit de tillgängliga kärnorna i den här regionen, Välj en annan region eller kontakta supporten för att öka kvoten.

Mer information om hur du skapar HDInsight-kluster med hjälp av Azure Portal finns i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Skapa ett kluster med en Resource Manager-mall

#### <a name="load-based-autoscaling"></a>Load-based autoskalning

Du kan skapa ett HDInsight-kluster med belastningsutjämnad autoskalning av en Azure Resource Manager mall genom att lägga till en `autoscale` nod i `computeProfile`  >  `workernode` avsnittet med egenskaperna `minInstanceCount` och `maxInstanceCount` som visas i JSON-kodfragmentet nedan. En fullständig Resource Manager-mall finns i [snabb starts mall: Distribuera Spark-kluster med belastningsutjämnad autoskalning aktive rad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Schema-baserad autoskalning

Du kan skapa ett HDInsight-kluster med schemabaserade autoskalning av en Azure Resource Manager mall genom att lägga till en `autoscale` nod i `computeProfile`  >  `workernode` avsnittet. `autoscale`Noden innehåller en `recurrence` som har en `timezone` och `schedule` som beskriver när ändringen ska äga rum. En fullständig Resource Manager-mall finns i [distribuera Spark-kluster med schema-baserad autoskalning aktive rad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Aktivera och inaktivera autoskalning för ett kluster som körs

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill aktivera autoskalning på ett kluster som körs väljer du **kluster storlek** under **Inställningar**. Välj sedan **Aktivera autoskalning**. Välj den typ av autoskalning som du vill använda och ange alternativ för belastnings beroende eller schemabaserade skalningar. Välj slutligen **Spara**.

![Aktivera schemabaserade autoskalning för arbetsnoden och köra kluster](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Använda REST API

Om du vill aktivera eller inaktivera autoskalning på ett kluster som körs med hjälp av REST API gör du en POST-begäran till slut punkten för autoskalning:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Använd lämpliga parametrar i nytto lasten för begäran. JSON-nyttolasten nedan kan användas för att aktivera autoskalning. Använd nytto lasten `{autoscale: null}` för att inaktivera autoskalning.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Se föregående avsnitt om hur du [aktiverar belastningsutjämnad autoskalning](#load-based-autoscaling) för en fullständig beskrivning av alla nytto Last parametrar.

## <a name="monitoring-autoscale-activities"></a>Övervaka automatiska skalnings aktiviteter

### <a name="cluster-status"></a>Klusterstatus

Kluster statusen som visas i Azure Portal kan hjälpa dig att övervaka automatiska skalnings aktiviteter.

![Aktivera belastnings utjämning av arbetsnodens kluster status för automatisk skalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Alla de kluster status meddelanden som du kan se förklaras i listan nedan.

| Klusterstatus | Description |
|---|---|
| Körs | Klustret fungerar normalt. Alla tidigare autoskalning-aktiviteter har slutförts. |
| Uppdatera  | Konfigurationen för automatisk skalning av klustret uppdateras.  |
| HDInsight-konfiguration  | En åtgärd för att skala upp eller skala upp i klustret pågår.  |
| Uppdaterings fel  | HDInsight har uppfyllt problem under uppdateringen av autoskalning av konfigurationen. Kunder kan välja att antingen försöka uppdatera igen eller inaktivera autoskalning.  |
| Fel  | Något är fel med klustret och det kan inte användas. Ta bort det här klustret och skapa ett nytt.  |

Om du vill visa det aktuella antalet noder i klustret går du till diagrammet **kluster storlek** på sidan **Översikt** för klustret. Eller Välj **kluster storlek** under **Inställningar**.

### <a name="operation-history"></a>Åtgärds historik

Du kan visa klustrets skalnings-och skalnings historik som en del av kluster måtten. Du kan också Visa alla skalnings åtgärder under den senaste dagen, veckan eller en annan tids period.

Välj **mått** under **övervakning**. Välj sedan **Lägg till mått** och **Antal aktiva arbetare** från List rutan **mått** . Välj knappen i det övre högra hörnet för att ändra tidsintervallet.

![Aktivera schema baserat på arbetsnodens mått för autoskalning](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Rekommenderade metoder

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Beakta svars tiden för skalning upp-och nedskalning

Det kan ta 10 till 20 minuter för en skalnings åtgärd att slutföras. När du ställer in ett anpassat schema bör du planera för den här fördröjningen. Om du till exempel behöver kluster storleken 20 kl. 9:00 kan du ange schema utlösaren till en tidigare tidpunkt, till exempel 8:30 AM, så att skalnings åtgärden slutförs med 9:00.

### <a name="prepare-for-scaling-down"></a>Förbered för skalning

När processen för kluster skalning är aktive rad inaktiverar autoskalning noderna för att uppfylla mål storleken. Om aktiviteter körs på dessa noder väntar den automatiska skalningen tills aktiviteterna har slutförts för Spark-och Hadoop-kluster. Eftersom varje arbetsnoden även hanterar en roll i HDFS, flyttas temporära data till de återstående noderna. Se till att det finns tillräckligt med utrymme på de återstående noderna för att vara värd för alla temporära data.

De jobb som körs kommer att fortsätta. Väntande jobb väntar på schemaläggning med färre tillgängliga arbetsnoder.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Var medveten om den minsta kluster storleken

Skala inte upp klustret till färre än tre noder. Om du skalar klustret till färre än tre noder kan det bli fastnat i fel säkert läge på grund av otillräcklig filreplikering. Mer information finns i avsnittet [komma fastna i fel säkert läge](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Öka antalet mappningar och Minskare

Autoskalning för Hadoop-kluster övervakar också HDFS-användning. Om HDFS är upptagen antar det att klustret fortfarande behöver de aktuella resurserna. När det finns enorma data som ingår i frågan kan du öka antalet mappningar och reducerare för att öka parallellitet och påskynda HDFS-åtgärderna. På så sätt utlöses en korrekt skalning när det finns extra resurser. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Ange att Hive-konfigurationen ska innehålla maximalt antal samtidiga frågor i scenariot för hög användning

Autoskalning-händelser ändrar inte Hive-konfigurationen *maximalt antal samtidiga frågor* i Ambari. Det innebär att den interaktiva tjänsten för Hive Server 2 bara kan hantera det aktuella antalet samtidiga frågor vid en viss tidpunkt, även om antalet interaktiva frågor i daemon skalas upp och ned baserat på belastning och schema. Den allmänna rekommendationen är att ställa in den här konfigurationen för scenariot med hög användning för att undvika manuella åtgärder.

Det kan dock uppstå ett startfel om att starta om en Hive-Server 2 om det bara finns ett litet antal arbetsnoder och värdet för maximalt antal samtidiga frågor har kon figurer ATS för hög. Minst måste du ha det lägsta antalet arbetsnoder som kan hantera det angivna antalet Tez-AMS (lika med den maximala konfigurationen för samtidiga frågor). 

## <a name="limitations"></a>Begränsningar

### <a name="node-label-file-missing"></a>Node etikett filen saknas

Med den automatiska skalningen i HDInsight används en Node-fil för att avgöra om en nod är redo att köra uppgifter. Filens etikett fil lagras i HDFS med tre repliker. Om kluster storleken dramatiskt skalas ned och det finns stora mängder temporära data, finns det en liten chans att alla tre repliker kan släppas. Om detta inträffar går klustret in i ett fel tillstånd.

### <a name="interactive-query-daemons-count"></a>Antal interaktiva frågor om daemon

I händelse av autoskalning – aktiverade interaktiva Query-kluster skalar en autoskalning upp/ned-händelse också upp/ned antalet interaktiva Frågeredigeraren till antalet aktiva arbetsnoder. Ändringen av antalet daemonar är inte beständig i `num_llap_nodes` konfigurationen i Ambari. Om Hive-tjänster startas om manuellt återställs antalet interaktiva daemonar för frågor enligt konfigurationen i Ambari.

Om den interaktiva Frågeparametern startas om manuellt måste du manuellt ändra `num_llap_node` konfigurationen (antalet noder som krävs för att köra Hive-funktionen för interaktiv fråga i Hive) under *Avancerad Hive-Interactive-miljö* för att matcha den aktuella aktiva arbets noden.

## <a name="next-steps"></a>Nästa steg

Läs mer om rikt linjer för att skala kluster manuellt i [skalnings rikt linjer](hdinsight-scaling-best-practices.md)
