---
title: Konfigurera Grafana för att visualisera mått som släpps från Azure Managed instance för Apache Cassandra
description: Lär dig hur du installerar och konfigurerar Grafana i en virtuell dator för att visualisera mått som genereras från en Azure-hanterad instans för Apache Cassandra-kluster.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746585"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Konfigurera Grafana för att visualisera mått som har spridits från det hanterade instans klustret

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du distribuerar en Azure-hanterad instans för Apache Cassandra-kluster etablerar tjänsten en server som är värd för [Prometheus](https://prometheus.io/) som kan användas av olika klient verktyg. Prometheus är en övervaknings lösning med öppen källkod. Den hanterade instansen genererar mått och behåller 10 minuter eller 10 GB data (beroende på vilket tröskel som uppnås först). I den här artikeln beskrivs hur du konfigurerar Grafana för att visualisera mått som genereras från det hanterade instans klustret. Följande uppgifter krävs för att visualisera mått:

* Distribuera en virtuell Ubuntu-dator i Azure-Virtual Network där den hanterade instansen finns.
* Installera [Grafana-verktyget](https://grafana.com/grafana/) med öppen källkod för att skapa instrument paneler och visualisera mått som släpps från Prometheus.

## <a name="deploy-a-ubuntu-server"></a>Distribuera en Ubuntu-Server

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Navigera till resurs gruppen där ditt hanterade instans kluster finns. Välj **Lägg till** och Sök efter **Ubuntu Server 18,04 LTS** -avbildning:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Hitta Ubuntu Server-avbildningen från Azure Portal." border="true":::

1. Välj avbildningen och välj **skapa**.

1. På bladet **skapa en virtuell dator** anger du värden för följande fält, du kan lämna standardvärden för andra fält:

   * **Namn på virtuell dator** – ange ett namn för den virtuella datorn.
   * **Region** – Välj samma Region där Virtual Network har distribuerats.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Fyll i formuläret för att skapa en virtuell dator med Ubuntu Server-avbildning." border="true":::

1. På fliken **nätverk** väljer du Virtual Network som den hanterade instansen distribueras till:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Konfigurera nätverks inställningarna för Ubuntu-servern." border="true":::

1. Klicka slutligen på **Granska + skapa** för att skapa din Grafana-Server.

## <a name="install-grafana"></a>Installera Grafana

1. Från Azure Portal öppnar du Virtual Network där du har distribuerat den hanterade instansen och Grafana-servern. Du bör se en instans med en skalnings uppsättning för virtuella datorer med namnet **Cassandra-hopp (instans 0)**. Den här Prometheus-statistik finns i den här skalnings uppsättningen för den virtuella datorn. Anteckna IP-adressen för den här instansen:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Hämta Prometheus-instansens IP-adress." border="true":::

1. Anslut till din nyligen skapade Ubuntu-Server med hjälp av [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) eller det önskade klient verktyget för att ansluta via SSH.

1. När du har anslutit till den virtuella datorn måste du installera och konfigurera Grafana för att ansluta till den skalnings uppsättning för virtuella datorer där Mät värdena finns. Öppna en kommando tolk och ange `nano` kommandot för att öppna en nano text redigerare. Klistra in följande skript i text redigeraren, se till att ersätta `<prometheus IP address>` med IP-adressen som du registrerade i föregående steg:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Skriv `ctrl + X` för att spara filen. Du kan ge filen ett namn `grafana.sh` .

1. Ange `./grafana.sh` kommandot i kommando tolken för att installera Grafana.

1. När installationen är klar kommer Grafana att vara tillgänglig på **port 3000** i SERVERNS IP-adress, som visas på följande skärm bild:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Kör Grafana på port 3000." border="true":::

1. Du kan välja mellan instrument paneler med öppen källkod som skapats för Apache Cassandra i Grafana, till exempel JSON [-filen Cluster-Overview](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Hämta och importera instrument panelens JSON-definition till Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importera Grafana JSON-definition." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Ladda upp Grafana JSON-definition." border="true":::

1. Du kan sedan övervaka ditt Cassandra-hanterade instans kluster med den valda instrument panelen:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Visa mått för Cassandra-hanterade instanser på instrument panelen." border="true":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar instrument paneler för att visualisera mått i Prometheus med Grafana. Lär dig mer om Azure Managed instance för Apache Cassandra med följande artiklar:

* [Översikt över Azure Managed instance för Apache Cassandra](introduction.md)
* [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks (förhands granskning)](deploy-cluster-databricks.md)
