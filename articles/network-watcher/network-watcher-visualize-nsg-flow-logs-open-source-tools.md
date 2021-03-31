---
title: Visualisera NSG Flow-loggar – elastisk stack
titleSuffix: Azure Network Watcher
description: Hantera och analysera flödes loggar för nätverks säkerhets grupper i Azure med hjälp av Network Watcher och elastisk stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026479"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualisera NSG-flödesloggar från Azure Network Watcher med hjälp av verktyg med öppen källkod

Flödes loggar för nätverks säkerhets grupper innehåller information som kan användas för att förstå inkommande och utgående IP-trafik för nätverks säkerhets grupper. I dessa flödes loggar visas utgående och inkommande flöden per regel, vilket nätverkskort flödet gäller för, 5 tuple-information om flödet (källa/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

Dessa flödes loggar kan vara svåra att manuellt parsa och få insikter från. Det finns dock flera verktyg för öppen källkod som kan användas för att visualisera dessa data. I den här artikeln får du en lösning för att visualisera dessa loggar med hjälp av den elastiska stacken som gör att du snabbt kan indexera och visualisera flödes loggar på en Kibana-instrumentpanel.

## <a name="scenario"></a>Scenario

I den här artikeln skapar vi en lösning som gör att du kan visualisera flödes loggar för nätverks säkerhets grupper med hjälp av den elastiska stacken.  Ett plugin-program för Logstash hämtar flödes loggarna direkt från den lagrings-blob som har kon figurer ATS för flödes loggar. Sedan kommer flödes loggarna att indexeras och användas för att skapa en Kibana-instrumentpanel för att visualisera informationen med hjälp av den elastiska stacken.

![Diagrammet visar ett scenario som gör att du kan visualisera flödes loggar för nätverks säkerhets grupper med hjälp av den elastiska stacken.][scenario]

## <a name="steps"></a>Steg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera flödes loggning för nätverks säkerhets grupp
I det här scenariot måste du ha nätverks säkerhets gruppens flödes loggning aktiverat på minst en nätverks säkerhets grupp i ditt konto. Anvisningar om hur du aktiverar nätverks säkerhets flödes loggar finns i följande artikel [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Konfigurera den elastiska stacken
Genom att ansluta NSG Flow-loggar med den elastiska stacken kan vi skapa en Kibana-instrumentpanel som gör det möjligt för oss att söka i, rita, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera ElasticSearch

1. Den elastiska stacken från version 5,0 och senare kräver Java 8. Kör kommandot `java -version` för att kontrol lera din version. Om du inte har installerat Java kan du läsa mer i dokumentationen om [Azure-suppored JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support).
2. Hämta rätt binärt paket för systemet:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Du hittar andra installations metoder i [ElasticSearch-installationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Kontrol lera att ElasticSearch körs med kommandot:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Du bör se ett svar som liknar detta:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Mer information om hur du installerar elastisk sökning finns i [installations anvisningarna](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installera Logstash

1. Kör följande kommandon för att installera Logstash:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Härnäst behöver vi konfigurera Logstash för att komma åt och parsa flödes loggarna. Skapa en logstash. conf-fil med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Mer information om hur du installerar Logstash finns i den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera plugin-programmet Logstash indata för Azure Blob Storage

Med det här Logstash-plugin-programmet kan du direkt komma åt flödes loggarna från det angivna lagrings kontot. För att installera det här plugin-programmet, från standard installations katalogen för Logstash (i det här fallet/usr/share/logstash/bin), kör du kommandot:

```bash
logstash-plugin install logstash-input-azureblob
```

Starta Logstash genom att köra kommandot:

```bash
sudo /etc/init.d/logstash start
```

Mer information om det här plugin-programmet finns i [dokumentationen](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Använd kommandona för att köra Kibana:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Om du vill visa Kibana-webbgränssnittet navigerar du till `http://localhost:5601`
4. I det här scenariot är index mönstret som används för flödes loggarna "NSG-Flow-logs". Du kan ändra index mönstret i avsnittet "utdata" i filen logstash. conf.
5. Om du vill visa Kibana-instrumentpanelen på distans skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana-instrumentpanel

Ett exempel på en instrument panel för att visa trender och information i dina aviseringar visas på följande bild:

![bild 1][1]

Hämta [instrument panels filen](https://aka.ms/networkwatchernsgflowlogdashboard), [visualiserings filen](https://aka.ms/networkwatchernsgflowlogvisualizations)och den [sparade Sök filen](https://aka.ms/networkwatchernsgflowlogsearch).

Under fliken **hantering** i Kibana navigerar du till **sparade objekt** och importerar alla tre filerna. Sedan kan du öppna och läsa in exempel instrument panelen från fliken **instrument panel** .

Du kan också skapa egna visualiseringar och instrument paneler som skräddarsys mot mått av ditt eget intresse. Läs mer om att skapa Kibana-visualiseringar från Kibanas [officiella dokumentation](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm).

### <a name="visualize-nsg-flow-logs"></a>Visualisera NSG flödes loggar

Instrument panelen exempel innehåller flera visualiseringar av flödes loggarna:

1. Flöden efter beslut/riktning över tid – serie diagram som visar antalet flöden under tids perioden. Du kan redigera tidsenhet och omfång för båda dessa visualiseringar. Flöden efter beslut visar en andel tillåtna eller nekade beslut, medan flöden efter riktning visar förhållandet mellan inkommande och utgående trafik. Med dessa visuella objekt kan du undersöka trafik trender över tid och leta efter eventuella toppar eller ovanliga mönster.

   ![Skärm bild som visar ett exempel på en instrument panel med flöden efter beslut och riktning över tid.][2]

2. Flöden efter mål-/käll port – cirkel diagram som visar nedbrytningen av flöden till respektive port. I den här vyn kan du se dina vanligaste portar. Om du klickar på en enskild port i cirkel diagrammet filtreras resten av instrument panelen ned till flöden av den porten.

   ![Skärm bild som visar ett exempel på en instrument panel med flöden efter mål-och käll port.][3]

3. Antal flöden och tidigaste logg tid – mått som visar dig antalet registrerade flöden och datum för den tidigaste loggen.

   ![Skärm bild som visar ett exempel på en instrument panel med antalet flöden och den tidigaste logg tiden.][4]

4. Flöden efter NSG och regel – ett stapeldiagram som visar distributionen av flöden inom varje NSG, samt distribution av regler inom varje NSG. Härifrån kan du se vilka NSG och regler som genererade den mest trafik.

   ![Skärm bild som visar ett exempel på en instrument panel med flöden efter N S G och regel.][5]

5. De 10 främsta käll-och mål-IP-stapeldiagram som visar de 10 främsta käll-och mål-IP-adresserna. Du kan justera dessa diagram om du vill visa fler eller färre översta IP-adresser. Härifrån kan du se de vanligaste IP-adresserna samt det trafikbeslut (Tillåt eller neka) som görs för varje IP-adress.

   ![Skärm bild som visar ett exempel på en instrument panel med flöden per topp 10 källa och mål I P-adresser.][6]

6. Flow-tupler – i den här tabellen visas den information som finns i varje flödes tupel, samt dess motsvarande redigera och regel.

   ![Skärm bild som visar Flow-tupler i en tabell.][7]

Med hjälp av frågefönstret överst i instrument panelen kan du filtrera instrument panelen baserat på vilken parameter som helst i flödena, till exempel prenumerations-ID, resurs grupper, regel eller någon annan variabel av intresse. Mer information om Kibana-frågor och filter finns i den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Slutsats

Genom att kombinera flödes loggar för nätverks säkerhets grupper med den elastiska stacken har vi kommit igång med kraftfulla och anpassningsbara sätt att visualisera vår nätverks trafik. Med dessa instrument paneler kan du snabbt få och dela insikter om din nätverks trafik, samt filtrera och undersöka eventuella avvikelser. Med Kibana kan du skräddarsy dessa instrument paneler och skapa olika visualiseringar för att uppfylla alla krav på säkerhet, granskning och efterlevnad.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar dina NSG Flow-loggar med Power BI genom att besöka [visualisera NSG flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png