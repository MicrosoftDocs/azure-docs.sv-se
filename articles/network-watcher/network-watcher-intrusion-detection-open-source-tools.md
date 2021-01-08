---
title: Utför nätverks intrångs identifiering med verktyg med öppen källkod
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du använder Azure Network Watcher och verktyg med öppen källkod för att utföra identifiering av nätverks intrång
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014560"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Utför identifiering av nätverks intrång med Network Watcher och verktyg för öppen källkod

Paket insamlingar är en viktig komponent för att implementera system för identifiering av nätverks intrång (ID) och utföra övervakning av nätverks säkerhet (NSM). Det finns flera ID-verktyg för öppen källkod som bearbetar paket insamlingar och letar efter signaturer för möjliga nätverks intrång och skadlig aktivitet. Genom att använda paket avbildningar som tillhandahålls av Network Watcher kan du analysera nätverket för skadliga intrång eller sårbarheter.

Ett sådant verktyg för öppen källkod är suricata, en ID-motor som använder rulesets för att övervaka nätverks trafik och utlöser aviseringar när misstänkta händelser inträffar. Suricata erbjuder en multi-threaded-motor, vilket innebär att den kan utföra analys av nätverks trafik med ökad hastighet och effektivitet. Mer information om suricata och dess funktioner finns på webbplatsen på https://suricata-ids.org/ .

## <a name="scenario"></a>Scenario

Den här artikeln förklarar hur du konfigurerar din miljö för att utföra identifiering av nätverks intrång med hjälp av Network Watcher, suricata och den elastiska stacken. Network Watcher tillhandahåller paket avbildningar som används för att utföra identifiering av nätverks intrång. Suricata bearbetar paket fångsterna och utlöser aviseringar baserat på paket som matchar de tilldelade ruleset för hot. De här aviseringarna lagras i en loggfil på den lokala datorn. Med hjälp av den elastiska stacken kan loggarna som genereras av suricata indexeras och användas för att skapa en Kibana-instrumentpanel, vilket ger dig en visuell representation av loggarna och ett sätt att snabbt få insikter om potentiella nätverks sårbarheter.  

![enkelt webb program scenario][1]

Du kan konfigurera båda verktygen för öppen källkod på en virtuell Azure-dator, så att du kan utföra den här analysen i din egen Azure-nätverks miljö.

## <a name="steps"></a>Steg

### <a name="install-suricata"></a>Installera suricata

För alla andra installations metoder går du till https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. Kör följande kommandon i kommando rads terminalen för den virtuella datorn:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Verifiera installationen genom att köra kommandot `suricata -h` för att se en fullständig lista över kommandon.

### <a name="download-the-emerging-threats-ruleset"></a>Ladda ned de nya hoten ruleset

I det här skedet har vi inga regler för att suricata ska kunna köras. Du kan skapa egna regler om det finns särskilda hot för nätverket som du vill identifiera, eller också kan du använda utvecklade regel uppsättningar från ett antal leverantörer, till exempel nya hot eller VRT-regler från snort. Vi använder de lättillgängliga nya hot-ruleset här:

Hämta regel uppsättningen och kopiera dem till katalogen:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Bearbeta paket insamlingar med suricata

Kör följande kommando för att bearbeta paket insamlingar med suricata:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Om du vill kontrol lera de resulterande aviseringarna läser du filen snabb. log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurera den elastiska stacken

Även om de loggar som suricata producerar innehåller värdefull information om vad som händer i vårt nätverk är dessa loggfiler inte lätta att läsa och förstå. Genom att ansluta suricata med den elastiska stacken kan vi skapa en Kibana-instrumentpanel som gör det möjligt för oss att söka i, rita, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera ElasticSearch

1. Den elastiska stacken från version 5,0 och senare kräver Java 8. Kör kommandot `java -version` för att kontrol lera din version. Om du inte har installerat Java kan du läsa mer i dokumentationen om [Azure-suppored JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support).

1. Hämta rätt binärt paket för systemet:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Du hittar andra installations metoder i [ElasticSearch-installationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Kontrol lera att ElasticSearch körs med kommandot:

    ```
    curl http://127.0.0.1:9200
    ```

    Du bör se ett svar som liknar detta:

    ```
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

Mer information om hur du installerar elastisk sökning finns i sidan [installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Installera Logstash

1. Kör följande kommandon för att installera Logstash:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Nu måste vi konfigurera Logstash för att läsa från utdata från eve.jsi filen. Skapa en logstash. conf-fil med:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Lägg till följande innehåll i filen (kontrol lera att sökvägen till filen eve.jsär korrekt):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Se till att ge rätt behörigheter till eve.jspå filen så att Logstash kan mata in filen.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Starta Logstash genom att köra kommandot:

    ```
    sudo /etc/init.d/logstash start
    ```

Mer information om hur du installerar Logstash finns i den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Använd kommandona för att köra Kibana:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Om du vill visa Kibana-webbgränssnittet navigerar du till `http://localhost:5601`
1. I det här scenariot är index mönstret som används för suricata-loggarna "logstash-*"

1. Om du vill visa Kibana-instrumentpanelen på distans skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana-instrumentpanel

I den här artikeln har vi tillhandahållit ett exempel på en instrument panel där du kan visa trender och information i dina aviseringar.

1. Hämta instrument panels filen [här](https://aka.ms/networkwatchersuricatadashboard), visualiserings filen [här](https://aka.ms/networkwatchersuricatavisualization)och den sparade Sök filen [här](https://aka.ms/networkwatchersuricatasavedsearch).

1. Under fliken **hantering** i Kibana navigerar du till **sparade objekt** och importerar alla tre filerna. Sedan kan du öppna och läsa in exempel instrument panelen från fliken **instrument panel** .

Du kan också skapa egna visualiseringar och instrument paneler som skräddarsys mot mått av ditt eget intresse. Läs mer om att skapa Kibana-visualiseringar från Kibanas [officiella dokumentation](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm).

![Kibana-instrumentpanel][2]

### <a name="visualize-ids-alert-logs"></a>Visualisera ID aviserings loggar

Instrument panelen exempel innehåller flera visualiseringar av suricata-aviserings loggar:

1. Aviseringar från GeoIP – en karta som visar distributionen av aviseringar efter deras land/region baserat på geografisk plats (bestäms av IP)

    ![Geo-IP][3]

1. De 10 vanligaste aviseringarna – en sammanfattning av de 10 vanligaste utlösta aviseringarna och deras beskrivning. Om du klickar på en enskild avisering filtreras instrument panelen till den information som hör till den specifika aviseringen.

    ![bild 4][4]

1. Antal aviseringar – totalt antal aviseringar som utlösts av ruleset

    ![bild 5][5]

1. De 20 främsta käll-och mål-IP-adresserna – cirkel diagram som visar de 20 främsta IP-adresser och portar som aviseringar utlöstes på. Du kan filtrera ned vissa IP-adresser för att se hur många och vilka typer av aviseringar som utlöses.

    ![bild 6][6]

1. Aviserings Sammanfattning – en tabell som sammanfattar detaljerad information om varje enskild avisering. Du kan anpassa den här tabellen om du vill visa andra intressanta parametrar för varje avisering.

    ![bild 7][7]

Mer dokumentation om hur du skapar anpassade visualiseringar och instrument paneler finns i [Kibanas officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Slutsats

Genom att kombinera paket avbildningar som tillhandahålls av Network Watcher och ID-verktyg med öppen källkod, till exempel suricata, kan du utföra identifiering av nätverks intrång för en mängd olika hot. Med dessa instrument paneler kan du snabbt upptäcka trender och avvikelser i nätverket, och du kan också lära dig att identifiera Rotors Aker till aviseringar som skadliga användar agenter eller sårbara portar. Med den här extraherade informationen kan du fatta välgrundade beslut om hur du ska reagera på och skydda nätverket från eventuella skadliga intrångs försök och skapa regler för att förhindra framtida intrång i nätverket.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du aktiverar paket fångster baserat på aviseringar genom [att gå till Använd paket fångst för att utföra proaktiv nätverks övervakning med Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Lär dig hur du visualiserar dina NSG Flow-loggar med Power BI genom att besöka [visualisera NSG flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png