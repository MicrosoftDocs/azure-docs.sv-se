---
title: Hantera NSG Flow-loggar med Grafana
titleSuffix: Azure Network Watcher
description: Hantera och analysera flödes loggar för nätverks säkerhets grupper i Azure med hjälp av Network Watcher och Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656316"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Hantera och analysera flödes loggar för nätverks säkerhets grupper med hjälp av Network Watcher och Grafana

[Flödes loggar för nätverks säkerhets grupper (NSG)](network-watcher-nsg-flow-logging-overview.md) innehåller information som kan användas för att förstå inkommande och utgående IP-trafik på nätverks gränssnitt. I dessa flödes loggar visas utgående och inkommande flöden för en regel per NSG, vilket nätverkskort flödet avser, 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port, protokoll) och om trafiken tillåts eller nekas.

Du kan ha många NSG: er i nätverket med flödes loggning aktive rad. Den här mängden loggnings data gör det besvärligt att analysera och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa NSG flödes loggar med Grafana, ett diagram verktyg med öppen källkod, ElasticSearch, en distribuerad sökning och analys motor och Logstash, som är en data behandlings pipeline med öppen källkod på Server sidan.  

## <a name="scenario"></a>Scenario

NSG flödes loggar aktive ras med Network Watcher och lagras i Azure Blob Storage. Ett Logstash-plugin-program används för att ansluta och bearbeta flödes loggar från Blob Storage och skicka dem till ElasticSearch.  När flödes loggarna lagras i ElasticSearch kan de analyseras och visualiseras i anpassade instrument paneler i Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera flödes loggning för nätverks säkerhets grupp

I det här scenariot måste du ha nätverks säkerhets gruppens flödes loggning aktiverat på minst en nätverks säkerhets grupp i ditt konto. Anvisningar om hur du aktiverar nätverks säkerhets flödes loggar finns i följande artikel [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Att tänka på vid konfiguration

I det här exemplet är Grafana, ElasticSearch och Logstash konfigurerade på en Ubuntu 16,04 LTS-server som distribueras i Azure. Den här minimala installationen används för att köra alla tre komponenter – de körs på samma virtuella dator. Den här installationen bör endast användas för testning och icke-kritiska arbets belastningar. Logstash, ElasticSearch och Grafana kan alla skapas för att skalas oberoende över flera instanser. Mer information finns i dokumentationen för var och en av dessa komponenter.

### <a name="install-logstash"></a>Installera Logstash

Du kan använda Logstash för att förenkla JSON-formaterade flödes loggar till en flödes tupel nivå.

1. Kör följande kommandon för att installera Logstash:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurera Logstash för att parsa flödes loggarna och skicka dem till ElasticSearch. Skapa en Logstash. conf-fil med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen. Ändra lagrings kontots namn och åtkomst nyckel för att återspegla dina lagrings konto uppgifter:

   ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

Den angivna konfigurations filen för Logstash består av tre delar: indata, filter och utdata.
I indata-avsnittet anges indata källor för de loggar som Logstash ska bearbeta – i det här fallet ska vi använda ett plugin-program för "azureblob" (installeras i nästa steg) som ger oss åtkomst till NSG Flow log JSON-filer som lagras i Blob Storage. 

Filter avsnittet fören klar varje flödes logg fil så att varje enskild flödes tupel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar avsnittet utdata varje Logstash-händelse till ElasticSearch-servern. Du kan ändra Logstash-konfigurations filen så att den passar just dina behov.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera plugin-programmet Logstash indata för Azure Blob Storage

Med det här Logstash-plugin-programmet kan du direkt komma åt flödes loggarna från deras angivna Blob Storage-konto. Om du vill installera plugin-programmet från standard installations katalogen för Logstash (i det här fallet/usr/share/logstash/bin) kör du kommandot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om den här plugin-modulen finns i [Logstash in plugin-programmet för Azure Storage blobbar](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Installera ElasticSearch

Du kan använda följande skript för att installera ElasticSearch. Information om hur du installerar ElasticSearch finns i [elastisk stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Installera Grafana

Kör följande kommandon för att installera och köra Grafana:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Mer installations information finns i [installera på Debian/Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Lägg till ElasticSearch-servern som en data Källa

Därefter måste du lägga till det ElasticSearch-index som innehåller flödes loggar som en data källa. Du kan lägga till en data källa genom att välja **Lägg till data källa** och fylla i formuläret med relevant information. Du hittar ett exempel på den här konfigurationen på följande skärm bild:

![Lägg till datakälla](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Nu när du har konfigurerat Grafana för att läsa från ElasticSearch-indexet som innehåller NSG flödes loggar, kan du skapa och anpassa instrument paneler. Om du vill skapa en ny instrument panel väljer du **skapa din första instrument panel**. I följande exempel diagram konfiguration visas flöden segmenterade av NSG-regeln:

![Instrument panels diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Följande skärm bild visar ett diagram och diagram som visar de vanligaste flödena och deras frekvens. Flöden visas också av NSG-regeln och flöden efter beslut. Grafana är mycket anpassningsbart, så det är tillrådligt att skapa instrument paneler som passar dina speciella övervaknings behov. I följande exempel visas en typisk instrument panel:

![Skärm bild som visar exempel diagram konfigurationen med flöden segmenterade av NSG-regeln.](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Slutsats

Genom att integrera Network Watcher med ElasticSearch och Grafana har du nu ett bekvämt och centraliserat sätt att hantera och visualisera NSG flödes loggar samt andra data. Grafana har ett antal andra kraftfulla diagram funktioner som också kan användas för att hantera flödes loggar ytterligare och bättre förstå nätverks trafiken. Nu när du har skapat en Grafana-instans och anslutit till Azure kan du fortsätta att utforska de andra funktionerna som den erbjuder.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du använder [Network Watcher](network-watcher-monitoring-overview.md).

