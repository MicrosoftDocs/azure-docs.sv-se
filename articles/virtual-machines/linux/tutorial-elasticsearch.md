---
title: Installera Elasticsearch på en virtuell utvecklingsdator i Azure
description: Självstudier – Installera Elastic Stack på en virtuell utvecklingsdator med Linux i Azure
services: virtual-machines
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: b3213d7de9aee88486fa1db1388c51948fbde430
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557819"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Installera Elastic Stack på en virtuell dator i Azure VM

Den här artikeln beskriver hur du installerar [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) och [Kibana](https://www.elastic.co/products/kibana) på en virtuell dator med Ubuntu i Azure. Om du vill se hur Elastic Stack fungerar i praktiken kan du ansluta till Kibana och arbeta med en del exempeldata. 

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en virtuell dator med Ubuntu i en Azure-resursgrupp
> * Installera Elasticsearch, Logstash och Kibana på den virtuella datorn
> * Skicka exempeldata till Elasticsearch med Logstash 
> * Öppna portar och arbeta med data i Kibana-konsolen


 Denna installation är lämplig för grundläggande utveckling med Elastic Stack. Mer information om Elastic Stack samt rekommendationer för en produktionsmiljö finns i [Elastic-dokumentationen](https://www.elastic.co/guide/index.html) och [Azure Architecture Center](/azure/architecture/elasticsearch/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.4 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm). 

Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Om du inte redan känner till den offentliga IP-adressen för den virtuella datorn kör du kommandot [AZ Network Public-IP List](/cli/azure/network/public-ip) :

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt med den korrekta offentliga IP-adressen för den virtuella datorn. I det här exemplet är IP-adressen *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Installera Elastic Stack

Importera signeringsnyckeln för Elasticsearch och uppdatera APT-källistan så den innehåller paketdatabasen för Elastic:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Installera Java Virtual Machine på den virtuella datorn och konfigurera variabeln JAVA_HOME – Elastic Stack-komponenterna behöver detta för att kunna köras.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Kör följande kommandon för att uppdatera Ubuntu-paketkällorna och installera Elasticsearch, Kibana och Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Detaljerade installationsanvisningar, inklusive katalogstrukturer och inledande konfiguration finns i [Elastics dokumentation](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Starta Elasticsearch 

Starta Elasticsearch på den virtuella datorn med följande kommando:

```bash
sudo systemctl start elasticsearch.service
```

Detta kommando genererar inga utdata. Du kontrollerar därför att Elasticsearch körs på den virtuella datorn med detta `curl`-kommando:

```bash
sudo curl -XGET 'localhost:9200/'
```

Om Elasticsearch körs ser du utdata som liknar dessa:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Starta Logstash och lägga till data i Elasticsearch

Starta Logstash med följande kommando:

```bash 
sudo systemctl start logstash.service
```

Testa Logstash i interaktivt läge och kontrollera att det fungerar korrekt:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Det här är en grundläggande logstash-[pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) som skickar standardindata till standardutdata. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Konfigurera Logstash så att kernel-meddelanden från den här virtuella dator skickas till Elasticsearch. Skapa en ny fil i en tom katalog med namnet `vm-syslog-logstash.conf` och klistra in följande Logstash-konfiguration:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Testa denna konfiguration och skicka syslog-data till Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Syslog-posterna visas i terminalen allteftersom de skickas till Elasticsearch. Använd `CTRL+C` och lämna Logstash när du skickat lite data.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Starta Kibana och visualisera data i Elasticsearch

Redigera `/etc/kibana/kibana.yml` och ändra IP-adressen som Kibana lyssnar på så att du kommer åt den från webbläsaren.

```bash
server.host: "0.0.0.0"
```

Starta Kibana med följande kommando:

```bash
sudo systemctl start kibana.service
```

Öppna port 5601 från Azure CLI och tillåt fjärråtkomst till Kibana-konsolen:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Öppna Kibana-konsolen och välj **Skapa** för att generera ett standardindex baserat på de syslog-data som skickades till Elasticsearch tidigare. 

![Skärm bild som visar Kibana-konsolen och markerar knappen Skapa.](media/elasticsearch-install/kibana-index.png)

Välj **Utforska** på Kibana-konsolen och sök, bläddra och filtrera syslog-händelserna.

![Bläddra i Syslog-händelser i Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien installerade du Elastic Stack på en virtuell utvecklingsdator i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator med Ubuntu i en Azure-resursgrupp
> * Installera Elasticsearch, Logstash och Kibana på den virtuella datorn
> * Skicka exempeldata till Elasticsearch från Logstash 
> * Öppna portar och arbeta med data i Kibana-konsolen
