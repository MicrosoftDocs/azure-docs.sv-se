---
title: Samla & analysera resursloggar
description: Lär dig hur du skickar resursloggar och händelsedata från containergrupper i Azure Container Instances till Azure Monitor loggar
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: e46a1df65a4cfe5d10a58704aff485aa2834b55f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763927"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Loggning av containergrupp och instans med Azure Monitor loggar

Log Analytics-arbetsytor är en central plats där du kan lagra och köra frågor mot loggdata, inte bara från Azure-resurser, utan även lokala resurser och resurser i andra moln. Azure Container Instances har inbyggt stöd för att skicka loggar och händelsedata till Azure Monitor loggar.

Om du vill skicka loggen för containergruppen och händelsedata Azure Monitor loggar anger du ett befintligt ID och en arbetsytenyckel för Log Analytics-arbetsytan när du konfigurerar en containergrupp. 

I följande avsnitt beskrivs hur du skapar en loggningsaktiverad containergrupp och hur du frågar loggar. Du kan också uppdatera [en containergrupp med ett](container-instances-update.md) arbetsyte-ID och en arbetsytenyckel för att aktivera loggning.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> För närvarande kan du bara skicka händelsedata från Linux-containerinstanser till Log Analytics.

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera loggning i dina containerinstanser, behöver du följande:

* [Log Analytics-arbetsyta](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (eller [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Hämta Log Analytics-autentiseringsuppgifter

Azure Container Instances behöver behörighet för att skicka data till din Log Analytics-arbetsyta. Om du vill ge behörighet och aktivera loggning, måste du ange ID för Log Analytics-arbetsytan och en av dess nycklar (primär eller sekundär) när du skapar containergrupp.

Gör följande för att hämta ID och den primära nyckeln för Log Analytics-arbetsytan:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Inställningar** väljer du **Agenthantering**
1. Anteckna:
   * **Arbetsyte-ID**
   * **Primärnyckel**

## <a name="create-container-group"></a>Skapa containergrupp

Nu när du har Log Analytics-arbetsytans ID och primärnyckel är du redo att skapa en grupp för loggningsaktiverad containergrupp.

I följande exempel visas två sätt att skapa en containergrupp som består av en enda [fluentd-container:][fluentd] Azure CLI och Azure CLI med en YAML-mall. Fluentd-containern skapar flera rader med utdata i standardkonfigurationen. Eftersom dessa utdata skickas till din Log Analytics-arbetsyta fungerar det bra för att demonstrera visning och frågning av loggar.

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

Kör sedan följande kommando för att distribuera containergruppen. Ersätt `myResourceGroup` med en resursgrupp i din prenumeration (eller skapa först en resursgrupp med namnet "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Du bör få ett svar från Azure som innehåller distributionsinformation strax efter kommandot utfärdats.

## <a name="view-logs"></a>Visa loggar

När du har distribuerat containergruppen, kan det ta flera minuter (upp till 10) för de första loggposterna att visas i Azure-portalen. 

Så här visar du containergruppens loggar i `ContainerInstanceLog_CL` tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt** väljer du **Loggar**  
1. Skriv följande fråga: `ContainerInstanceLog_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du inte ser några resultat först väntar du några minuter och väljer sedan knappen **Kör** för att köra frågan igen. Som standard visas loggposter i **tabellformat.** Du kan därefter expandera en rad för att visa innehållet i en enskild loggpost.

![Logga sökresultat i Azure-portalen][log-search-01]

## <a name="view-events"></a>Visa händelser

Du kan också visa händelser för containerinstanser i Azure Portal. Händelser omfattar den tid då instansen skapas och när den startas. Så här visar du händelsedata i `ContainerEvent_CL` tabellen:

1. Navigera till Log Analytics-arbetsytan i Azure-portalen
1. Under **Allmänt** väljer du **Loggar**  
1. Skriv följande fråga: `ContainerEvent_CL | limit 50`
1. Välj **Kör**

Du bör se flera resultat som visas av frågan. Om du inte ser några resultat först väntar du några minuter och väljer sedan knappen **Kör** för att köra frågan igen. Som standard visas poster i **tabellformat.** Du kan sedan expandera en rad för att se innehållet i en enskild post.

![Händelsesökningsresultat i Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Fråga containerloggar

Azure Monitor-loggar innehåller ett omfattande [frågespråk][query_lang] för att hämta information från potentiellt tusentals rader med loggutdata.

Den grundläggande strukturen för en fråga är källtabellen (i den här artikeln eller ) följt av en serie operatorer avgränsade `ContainerInstanceLog_CL` `ContainerEvent_CL` med pipe-tecknet ( `|` ). Du kan länka flera operatorer för att förfina resultatet och utför avancerade funktioner.

Om du vill se exempel på frågeresultat klistrar du in följande fråga i frågetextrutan och väljer **knappen** Kör för att köra frågan. Den här frågan visar alla loggposter vars Meddelande-fält innehåller ordet varning:

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

* [Förstå loggsökningar i Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md)
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
[az-container-create]: /cli/azure/container#az_container_create