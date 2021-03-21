---
title: Samla in & analysera resurs loggar
description: Lär dig hur du skickar resurs loggar och händelse data från behållar grupper i Azure Container Instances till Azure Monitor loggar
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: cfdcd1cc8e36a118c4e3c4435eaa002e4d3b1b93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579333"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Behållar grupp och instans loggning med Azure Monitor loggar

Log Analytics-arbetsytor är en central plats för att lagra och fråga loggdata från Azure-resurser, utan även lokala resurser och resurser i andra moln. Azure Container Instances innehåller inbyggt stöd för att skicka loggar och händelse data till Azure Monitor loggar.

Om du vill skicka logg-och händelse data för container gruppen till Azure Monitor loggar anger du ett befintligt Log Analytics-arbetsyte-ID och en arbets yta nyckel när du konfigurerar en behållar 

I följande avsnitt beskrivs hur du skapar en loggnings aktive rad behållar grupp och hur du frågar loggar. Du kan också [Uppdatera en behållar grupp](container-instances-update.md) med ett arbetsyte-ID och en arbetsyte nyckel för att aktivera loggning.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> För närvarande kan du bara skicka händelse data från Linux container instances till Log Analytics.

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera loggning i dina containerinstanser, behöver du följande:

* [Log Analytics arbets yta](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (eller [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Hämta Log Analytics-autentiseringsuppgifter

Azure Container Instances behöver behörighet för att skicka data till din Log Analytics-arbetsyta. Om du vill ge behörighet och aktivera loggning, måste du ange ID för Log Analytics-arbetsytan och en av dess nycklar (primär eller sekundär) när du skapar containergrupp.

Gör följande för att hämta ID och den primära nyckeln för Log Analytics-arbetsytan:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Inställningar** väljer du **agent hantering**
1. Anteckna:
   * **Arbetsyte-ID**
   * **Primär nyckel**

## <a name="create-container-group"></a>Skapa containergrupp

Nu när du har Log Analytics-arbetsytans ID och primärnyckel är du redo att skapa en grupp för loggningsaktiverad containergrupp.

Följande exempel visar två sätt att skapa en behållar grupp som består av en enda [Fluent][fluentd] -behållare: Azure CLI och Azure CLI med en yaml-mall. Den här Fluent-behållaren genererar flera rader utdata i standard konfigurationen. Eftersom dessa utdata skickas till din Log Analytics-arbetsyta fungerar det bra för att demonstrera visning och frågning av loggar.

### <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Om du vill distribuera med Azure CLI anger du värden för parametrarna `--log-analytics-workspace` och `--log-analytics-workspace-key` i kommandot [az container create][az-container-create]. Ersätt de två arbetsytevärdena med de värden du hämtade i föregående steg (och uppdatera resursgruppens namn) innan du kör följande kommando.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Distribuera med YAML

Använd den här metoden om du föredrar att distribuera containergrupper med YAML. Följande YAML definierar en containergrupp med en enda container. Kopiera YAML-koden till en ny fil och ersätt `LOG_ANALYTICS_WORKSPACE_ID` och `LOG_ANALYTICS_WORKSPACE_KEY` med värdena du hämtade i föregående steg. Spara filen som **deploy-aci.yaml**.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör sedan följande kommando för att distribuera behållar gruppen. Ersätt `myResourceGroup` med en resurs grupp i din prenumeration (eller skapa först en resurs grupp med namnet "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Du bör få ett svar från Azure som innehåller distributionsinformation strax efter kommandot utfärdats.

## <a name="view-logs"></a>Visa loggar

När du har distribuerat containergruppen, kan det ta flera minuter (upp till 10) för de första loggposterna att visas i Azure-portalen. 

Så här visar du behållar gruppens loggar i `ContainerInstanceLog_CL` tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt** väljer du **loggar**  
1. Skriv följande fråga: `ContainerInstanceLog_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du inte ser några resultat i första hand väntar du några minuter och väljer sedan **Kör** -knappen för att köra frågan igen. Som standard visas logg poster i **tabell** format. Du kan därefter expandera en rad för att visa innehållet i en enskild loggpost.

![Logga sökresultat i Azure-portalen][log-search-01]

## <a name="view-events"></a>Visa händelser

Du kan också visa händelser för behållar instanser i Azure Portal. Händelser inkluderar tiden då instansen skapas och när den startas. Så här visar du händelse data i `ContainerEvent_CL` tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt** väljer du **loggar**  
1. Skriv följande fråga: `ContainerEvent_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du inte ser några resultat i första hand väntar du några minuter och väljer sedan **Kör** -knappen för att köra frågan igen. Poster visas som standard i **tabell** format. Sedan kan du expandera en rad för att se innehållet i en enskild post.

![Händelse Sök resultat i Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Fråga containerloggar

Azure Monitor-loggar innehåller ett omfattande [frågespråk][query_lang] för att hämta information från potentiellt tusentals rader med loggutdata.

Den grundläggande strukturen i en fråga är käll tabellen (i den här artikeln, `ContainerInstanceLog_CL` eller `ContainerEvent_CL` ) följt av en serie operatorer avgränsade med pipe-tecknet ( `|` ). Du kan länka flera operatorer för att förfina resultatet och utför avancerade funktioner.

Om du vill se resultatet av frågan klistrar du in följande fråga i text rutan fråga och väljer **Kör** -knappen för att köra frågan. Den här frågan visar alla loggposter vars Meddelande-fält innehåller ordet varning:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Mer komplexa frågor stöds också. Den här frågan visar till exempel bara de loggposter för behållargruppen mycontainergroup001 som skapats den senaste timmen:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Nästa steg

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Mer information om att köra frågor mot loggar och konfigurera aviseringar i Azure Monitor-loggar finns i:

* [Förstå loggs ökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md)
* [Enhetliga aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Övervaka containerns CPU och minne

Information om övervakning av containerinstansens CPU- och minnesresurser finns i:

* [Övervaka behållarens resurser i Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create