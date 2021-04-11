---
title: Visa loggar och mått med hjälp av Kibana och Grafana
description: Visa loggar och mått med hjälp av Kibana och Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670009"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Visa loggar och mått med hjälp av Kibana och Grafana

Kibana- och Grafana-webbinstrumentpaneler tillhandahålls för att få insyn och klarhet i de Kubernetes-namnrymder som används av Azure Arc-aktiverade datatjänster.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Övervaka Azure SQL Managed instances på Azure-bågen

Kör följande CLI-kommando för att komma åt de loggar och övervaknings instrument paneler för Arc-aktiverad SQL-hanterad instans `azdata`

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
Relevanta Grafana-instrumentpaneler är:

* "Mått för Azure SQL-hanterad instans"
* "Mått för värd nod"
* "Värden för poddar-mått"


> [!NOTE]
>  När du uppmanas att ange ett användar namn och lösen ord anger du det användar namn och lösen ord som du angav vid den tidpunkt då du skapade data styrenheten för Azure-bågen.

> [!NOTE]
>  Du uppmanas att ange en certifikat varning eftersom de certifikat som används i för hands versionen är självsignerade certifikat.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Övervaka Azure Database for PostgreSQL storskalig på Azure-bågen

Om du vill komma åt de loggar och övervaknings instrument paneler för PostgreSQL-storskalig kör du följande `azdata` CLI-kommando

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

Relevanta postgreSQL-instrumentpaneler är:

* "Postgres-mått"
* "Postgres tabell mått"
* "Mått för värd nod"
* "Värden för poddar-mått"


## <a name="additional-firewall-configuration"></a>Ytterligare brand Väggs konfiguration

Beroende på var data styrenheten har distribuerats kan du se att du behöver öppna portarna i brand väggen för att få åtkomst till Kibana-och Grafana-slutpunkterna.

Nedan visas ett exempel på hur du gör detta för en virtuell Azure-dator. Du måste göra detta om du har distribuerat Kubernetes med hjälp av skriptet.

Stegen nedan visar hur du skapar en NSG-regel för Kibana-och Grafana-slutpunkterna:

### <a name="find-the-name-of-the-nsg"></a>Hitta NSG-namnet

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>Lägg till NSG-regeln

När du har namnet på NSG kan du lägga till en regel med hjälp av följande kommando:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Nästa steg
- Försök [att ladda upp mått och loggar till Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Läs om Grafana:
   - [Komma igång](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana fundament ALS](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana-självstudier](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Läs om Kibana
   - [Introduktion](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana-guide](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Introduktion till instrument panels detalj nivå med data visualiseringar i Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Så här skapar du Kibana-instrumentpaneler](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

