---
title: Analysera flödes loggar för Azure nätverks säkerhets grupper – Graylog | Microsoft Docs
description: Lär dig hur du hanterar och analyserar flödes loggar för nätverks säkerhets grupper i Azure med hjälp av Network Watcher och Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965552"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hantera och analysera flödes loggar för nätverks säkerhets grupper i Azure med hjälp av Network Watcher och Graylog

[Flödes loggar för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md) innehåller information som du kan använda för att förstå inkommande och utgående IP-trafik för Azures nätverks gränssnitt. Flödes loggar visar utgående och inkommande flöden per nätverks säkerhets grupp regel, det nätverks gränssnitt som flödet gäller för, 5-tuple-information (käll-/mål-IP, käll-och mål Port, protokoll) om flödet och om trafiken tilläts eller nekades.

Du kan ha många nätverks säkerhets grupper i nätverket med flödes loggning aktive rad. Flera nätverks säkerhets grupper med aktive rad flödes loggning kan göra det besvärligt att parsa och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa flödes loggar för nätverks säkerhets grupper med hjälp av Graylog, en logg hanterings-och analys verktyg med öppen källkod och Logstash, en data behandlings pipeline med öppen källkod.

> [!Warning]
> Följande steg fungerar med Flow-loggar version 1. Mer information finns i [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna utan ändringar.

## <a name="scenario"></a>Scenario

Flödes loggar för nätverks säkerhets grupper aktive ras med hjälp av Network Watcher. Flödes loggar flödar in i Azure Blob Storage. Ett Logstash-plugin-program används för att ansluta och bearbeta flödes loggar från Blob Storage och skicka dem till Graylog. När flödes loggarna lagras i Graylog kan de analyseras och visualiseras i anpassade instrument paneler.

![Graylog-arbetsflöde](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installations steg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera flödes loggning för nätverks säkerhets grupp

I det här scenariot måste du ha nätverks säkerhets gruppens flödes loggning aktiverat på minst en nätverks säkerhets grupp i ditt konto. Anvisningar om hur du aktiverar flödes loggar för nätverks säkerhets grupper finns i följande artikel [Introduktion till flödes loggning för nätverks säkerhets grupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurera Graylog

I det här exemplet konfigureras både Graylog och Logstash på en Ubuntu 14,04-server som distribueras i Azure.

- I [dokumentationen](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) från Graylog finns stegvisa anvisningar om hur du installerar på Ubuntu.
- Se till att även konfigurera Graylog-webbgränssnittet genom att följa [dokumentationen](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

I det här exemplet används den minsta Graylog-installationen (dvs. en enda instans av en Graylog), men Graylog kan konstrueras för att skala över resurser beroende på system-och produktions behov. Mer information om arkitektoniska överväganden eller en djup arkitektur guide finns i Graylog- [dokumentation](https://docs.graylog.org/en/2.2/pages/architecture.html) och [arkitektur guide](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog kan installeras på många sätt, beroende på din plattform och dina preferenser. En fullständig lista över möjliga installations metoder finns i Graylog officiella [dokumentation](https://docs.graylog.org/en/2.2/pages/installation.html). Graylog-serverprogrammet körs på Linux-distributioner och uppfyller följande krav:

-  Java-SE 8 eller senare – [Azul Azure JDK-dokumentation](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Elastisk sökning 2. x (2.1.0 eller senare)- [installations dokumentation för ElasticSearch](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 eller senare – [MongoDB installations dokumentation](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installera Logstash

Logstash används för att förenkla JSON-formaterade flödes loggar till en flödes tupel nivå. Genom att förenkla flödes loggarna blir det enklare att organisera och söka i Graylog.

1. Kör följande kommandon för att installera Logstash:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurera Logstash för att parsa flödes loggarna och skicka dem till Graylog. Skapa en Logstash. conf-fil:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Lägg till följande innehåll i filen. Ändra de markerade värdena så att de motsvarar dina lagrings konto uppgifter:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Den angivna konfigurations filen för Logstash består av tre delar: indata, filter och utdata. I indata-avsnittet anges indata källor för de loggar som Logstash kommer att bearbeta – i det här fallet ska du använda ett plugin-program för Azure blogg indata (installeras i nästa steg) som gör att vi kan komma åt nätverks säkerhets gruppens flödes logg JSON-filer som lagras i Blob Storage.

Filter avsnittet fören klar varje flödes logg fil så att varje enskild flödes tupel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar avsnittet utdata varje Logstash-händelse till Graylog-servern. Ändra konfigurations filen för Logstash efter behov för att passa dina behov.

   > [!NOTE]
   > Den tidigare konfigurations filen förutsätter att Graylog-servern har kon figurer ATS på den lokala värdens loopback IP-adress 127.0.0.1. Om inte, se till att ändra värd parametern i avsnittet utdata till rätt IP-adress.

Mer information om hur du installerar Logstash finns i Logstash- [dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installera plugin-programmet för Logstash-indata för Azure Blob Storage

Med Logstash-plugin-programmet kan du direkt komma åt flödes loggarna från deras angivna Blob Storage-konto. Om du vill installera plugin-programmet, från standard installations katalogen för Logstash (i det här fallet/usr/share/logstash/bin), kör du följande kommando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om det här plugin-programmet finns i [dokumentationen](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Konfigurera anslutning från Logstash till Graylog

Nu när du har upprättat en anslutning till flödes loggarna med Logstash och konfigurerat Graylog-servern måste du konfigurera Graylog för att godkänna inkommande loggfiler.

1. Navigera till webb gränssnittet för Graylog-servern med hjälp av den URL som du har konfigurerat för det. Du kan komma åt gränssnittet genom att dirigera webbläsaren till `http://<graylog-server-ip>:9000/`

2. Du navigerar till sidan konfiguration genom att välja List rutan **system** i det övre navigerings fältet till höger och sedan klicka på **indata**.
   Du kan också navigera till `http://<graylog-server-ip>:9000/system/inputs`

   ![Komma igång](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. För att starta den nya indatamängden väljer du *GELF UDP* i list rutan **Välj indatamängd** och fyller i formuläret. GELF står för Graylog utökade logg format. GELF-formatet utvecklas av Graylog. Läs mer om fördelarna i Graylog- [dokumentationen](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Se till att binda indatamängden till IP-adressen som du konfigurerade Graylog-servern på. IP-adressen ska matcha **värd** fältet för UDP-utdata från konfigurations filen för Logstash. Standard porten bör vara *12201*. Se till att porten matchar **port** fältet i UDP-utdata som anges i Logstash-konfigurations filen.

   ![Skärm bild som visar Graylog-indata, med alternativ för att starta och hitta indata.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   När du startar indata bör du se att den visas under avsnittet **lokala indata** , som du ser i följande bild:

   ![Skärm bild som visar avsnittet lokala indata som innehåller indata som du har startat.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Läs mer om Graylog-indata i [dokumentationen](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. När dessa konfigurationer har gjorts kan du starta Logstash för att börja läsa i Flow-loggar med följande kommando: `sudo systemctl start logstash.service` .

### <a name="search-through-graylog-messages"></a>Sök igenom Graylog-meddelanden

Efter att Graylog-servern har kunnat samla in meddelanden kan du söka igenom meddelandena. Om du vill kontrol lera meddelanden som skickas till din Graylog-Server klickar du på knappen "**Visa mottagna meddelanden**" i den GELF UDP-indata som du skapade på sidan **indata** -konfiguration. Du dirigeras till en skärm som liknar följande bild: 

![Skärm bild som visar Graylog-servern som visar Sök resultat, histogram och meddelanden.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Om du klickar på den blå länken% {Message} expanderas varje meddelande för att visa parametrarna för varje flödes tupel, som du ser i följande bild:

![Skärm bild som visar meddelande information från Graylog-servern.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Som standard ingår alla meddelande fält i sökningen om du inte väljer ett särskilt meddelande fält att söka efter. Om du vill söka efter vissa meddelanden (dvs. – Flow-tupler från en specifik käll-IP) du kan använda Graylog search Query-språket som [dokumenterat](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analysera flödes loggar för nätverks säkerhets grupper med hjälp av Graylog

Nu när Graylog har kon figurer ATS igång kan du använda några av funktionerna för att bättre förstå dina flödes logg data. Ett sådant sätt är att använda instrument paneler för att skapa olika vyer av dina data.

### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

1. I det övre navigerings fältet väljer du **instrument paneler** eller navigerar till `http://<graylog-server-ip>:9000/dashboards/`

2. Därifrån klickar du på knappen grön **skapa instrument panel** och fyller i det korta formuläret med rubriken och beskrivningen för instrument panelen. Tryck på knappen **Spara** för att skapa den nya instrument panelen. En instrument panel ser ut ungefär som på följande bild:

    ![Skärm bild som visar Graylog Server-instrumentpaneler med alternativen för att skapa och redigera instrument paneler.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Lägg till widgetar

Du kan klicka på rubriken för instrument panelen för att se den, men nu är den tom eftersom vi inte har lagt till några widgetar. En enkel och användbar typ av widget som ska läggas till i instrument panelen är diagram med **snabb värden** , som visar en lista med värden för det valda fältet och deras distribution.

1. Gå tillbaka till Sök resultatet av UDP-indata som tar emot flödes loggar genom att välja **Sök** i det övre navigerings fältet.

2. Under panelen **Sök Resultat** till vänster på skärmen letar du upp fliken **fält** där de olika fälten i varje inkommande flödes meddelande visas.

3. Välj en önskad parameter som ska visualiseras (i det här exemplet är IP-källan markerad). Om du vill visa en lista över möjliga widgetar klickar du på den blå nedrullningsbara pilen till vänster om fältet och väljer sedan **snabb värden** för att generera widgeten. Du bör se något som liknar följande bild:

   ![Käll-IP-adress](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Därifrån kan du välja knappen **Lägg till i instrument panelen** i det övre högra hörnet i widgeten och välja motsvarande instrument panel att lägga till.

5. Gå tillbaka till instrument panelen för att se widgeten som du nyss lade till.

   Du kan lägga till flera andra widgetar, till exempel histogram och antal, till din instrument panel för att hålla koll på viktiga mått, till exempel instrument panelen som visas i följande bild:

   ![Flowlogs-instrumentpanel](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    För ytterligare förklaringar om instrument paneler och andra typer av widgetar, se Graylog- [dokumentationen](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Genom att integrera Network Watcher med Graylog har du nu ett bekvämt och centraliserat sätt att hantera och visualisera flödes loggar för nätverks säkerhets grupper. Graylog har ett antal andra kraftfulla funktioner som strömmar och aviseringar som också kan användas för att hantera flödes loggar ytterligare och bättre förstå nätverks trafiken. Nu när du har Graylog konfigurerat och anslutit till Azure kan du fortsätta att utforska de andra funktionerna som erbjuds.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar flödes loggar för nätverks säkerhets grupper med Power BI genom att besöka [visualisera nätverks säkerhets grupp flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).