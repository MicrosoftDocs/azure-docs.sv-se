---
title: Övervaka container instances
description: Hur du övervakar förbrukningen av beräknings resurser som CPU och minne av dina behållare i Azure Container Instances.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: ae9725ffe66bebbed26745c311b2ada07d5d2c00
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589294"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Övervaka behållarresurser i Azure Container Instances

[Azure Monitor][azure-monitoring] ger inblick i de beräknings resurser som används av dina behållar instanser. Med den här informationen om resursanvändning kan du fastställa de bästa resurs inställningarna för dina behållar grupper. Azure Monitor tillhandahåller också mått som spårar nätverks aktivitet i dina behållar instanser.

I den här dokument informationen samlas Azure Monitor mått för container instanser med både Azure Portal och Azure CLI.

> [!IMPORTANT]
> Azure Monitor mått i Azure Container Instances finns för närvarande i för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Azure Monitor mått är för närvarande endast tillgängliga för Linux-behållare.

## <a name="available-metrics"></a>Tillgängliga mått

Azure Monitor tillhandahåller följande [mått för Azure Container instances][supported-metrics]. Dessa mått är tillgängliga för en behållar grupp och enskilda behållare. Som standard sammanställs måtten som medelvärden.

- **CPU-användning** mäts i **millicores**. 
  - En Millicore är 1/1000th för en processor kärna, så 500 millicores representerar användningen av 0,5 CPU Core.
- **Minnes användning** i byte
- **Mottagna nätverks byte** per sekund
- **Överförda nätverks byte** per sekund 

## <a name="get-metrics---azure-portal"></a>Hämta mått – Azure Portal

När en containergrupp skapas är Azure Monitor-data tillgängliga i Azure Portal. Om du vill se mått för en behållar grupp går du till **översikts** sidan för behållar gruppen. Här kan du se i förväg skapade diagram för vart och ett av de tillgängliga måtten.

![dubbelt diagram][dual-chart]

Använd en [dimension][monitor-dimension] för att visa mått per behållare i en behållar grupp som innehåller flera behållare. Utför följande steg för att skapa ett diagram med enskilda containrar:

1. På sidan **Översikt** väljer du ett av mått diagram, t. ex. **CPU**. 
1. Välj knappen **Använd delning** och välj **container namn**.

![Skärm dum par visar måtten för en behållar instans med tillämpa delning valt och behållar namn valt.][dimension]

## <a name="get-metrics---azure-cli"></a>Hämta mått – Azure CLI

Mått för container instanser kan också samlas in med hjälp av Azure CLI. Hämta först containergruppens ID med följande kommando. Ersätt `<resource-group>` med resursgruppens namn och `<container-group>` med namnet på containergruppen.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Använd följande kommando för att hämta **CPU**-användningsstatistik.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Ändra värdet för `--metric` parametern i kommandot för att hämta andra mått som [stöds][supported-metrics]. Använd till exempel följande kommando för att hämta **minnes** användnings mått. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

För en grupp med flera behållare `containerName` kan dimensionen läggas till för att returnera mått per behållare.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-övervakning i [översikten över Azure övervakning][azure-monitoring].

Lär dig hur du skapar [mått aviseringar][metric-alert] för att få ett meddelande när ett mått för Azure Container instances överskrider ett tröskelvärde.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
