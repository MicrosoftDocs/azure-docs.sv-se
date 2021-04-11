---
title: Azure Arc-aktiverade Kubernetes kluster tillägg
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Distribuera och hantera livs cykel för tillägg på Azure Arc-aktiverade Kubernetes
ms.openlocfilehash: 63fb14818d148dcc579300fdb4c89d636b47fd05
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451152"
---
# <a name="kubernetes-cluster-extensions"></a>Kubernetes kluster tillägg

Funktionen Kubernetes Extensions aktiverar följande på Azure Arc-aktiverade Kubernetes-kluster:

* Azure Resource Manager-baserad distribution av kluster tillägg.
* Livs cykel hantering av tillägg Helm-diagram.

En översikt över den här funktionen finns i avsnittet [kluster tillägg – Azure Arc-aktiverade Kubernetes](conceptual-extensions.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0.
- `connectedk8s` (version >= 1.1.0) och `k8s-extension` (version >= 0.2.0) Azure CLI-tillägg. Installera dessa Azure CLI-tillägg genom att köra följande kommandon:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Om `connectedk8s` -och `k8s-extension` -tillägget redan har installerats kan du uppdatera dem till den senaste versionen med hjälp av följande kommando:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Ett befintligt Azure Arc-aktiverat Kubernetes-kopplat kluster.
    - Om du inte har anslutit ett kluster ännu använder du vår [snabb start](quickstart-connect-cluster.md).
    - [Uppgradera dina agenter](agent-upgrade.md#manually-upgrade-agents) till version >= 1.1.0.

## <a name="currently-available-extensions"></a>För närvarande tillgängliga tillägg

| Anknytning | Beskrivning |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Ger insyn i prestandan för arbets belastningar som har distribuerats i Kubernetes-klustret. Samlar in minnes-och processor användnings mått från styrenheter, noder och behållare. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Samlar in Gransknings logg data från Control plan-noder i Kubernetes-klustret. Innehåller rekommendationer och hot aviseringar baserade på insamlade data. |

## <a name="usage-of-cluster-extensions"></a>Användning av kluster tillägg

### <a name="create-extensions-instance"></a>Skapa tilläggs instans

Skapa en ny tilläggs instans med `k8s-extension create` och skicka in värden för de obligatoriska parametrarna. Kommandot nedan skapar en Azure Monitor för container Extension-instansen på ditt Azure Arc-aktiverade Kubernetes-kluster:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Utdataparametrar**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Tjänsten kan inte spara känslig information i mer än 48 timmar. Om Azure Arc-aktiverade Kubernetes-agenter inte har nätverks anslutning i mer än 48 timmar och inte kan avgöra om du ska skapa ett tillägg i klustret, så övergår tillägget till `Failed` State. När `Failed` du är i läget måste du köra `k8s-extension create` igen för att skapa ett nytt tillägg Azure-resurs.
> * * Azure Monitor för behållare är ett singleton-tillägg (endast ett obligatoriskt per kluster). Du måste rensa alla tidigare Helm-diagram installationer av Azure Monitor för behållare (utan tillägg) innan du installerar samma via-tillägg. Följ anvisningarna för [att ta bort Helm-diagrammet innan `az k8s-extension create` du kör det ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Obligatoriska parametrar**

| Parameternamn | Beskrivning |
|----------------|------------|
| `--name` | Namn på tilläggs instansen |
| `--extension-type` | Den typ av tillägg som du vill installera i klustret. Till exempel: Microsoft. AzureMonitor. Containers, Microsoft. azuredefender. Kubernetes | 
| `--scope` | Installations omfång för tillägget- `cluster` eller `namespace` |
| `--cluster-name` | Namnet på den Azure Arc-aktiverad Kubernetes-resurs som tilläggs instansen måste skapas på |
| `--resource-group` | Resurs gruppen som innehåller Azure Arc-aktiverade Kubernetes-resursen |
| `--cluster-type` | Den kluster typ som tilläggs instansen måste skapas på. Endast aktuell `connectedClusters` , som motsvarar Azure Arc-aktiverade Kubernetes, är ett godkänt värde |

**Valfria parametrar**

| Parameternamn | Beskrivning |
|--------------|------------|
| `--auto-upgrade-minor-version` | Boolesk egenskap som anger om tilläggets lägre version ska uppgraderas automatiskt eller inte. Standard: `true`.  Om den här parametern har angetts till true kan du inte ange `version` parameter eftersom versionen uppdateras dynamiskt. Om det är inställt på `false` , uppgraderas inte tillägget automatiskt även för korrigerings versioner. |
| `--version` | Den version av tillägget som ska installeras (en version som ska användas för att fästa tilläggs instansen till). Får inte anges om Auto-Upgrade-del-version är inställt på `true` . |
| `--configuration-settings` | Inställningar som kan överföras till tillägget för att styra dess funktioner. De skickas in som blank steg som avgränsade `key=value` par efter parameter namnet. Om den här parametern används i kommandot kan du `--configuration-settings-file` inte använda den i samma kommando. |
| `--configuration-settings-file` | Sökväg till JSON-filen med nyckel värdes par som ska användas för att överföra konfigurations inställningar till tillägget. Om den här parametern används i kommandot kan du `--configuration-settings` inte använda den i samma kommando. |
| `--configuration-protected-settings` | De här inställningarna kan inte hämtas med `GET` API-anrop eller `az k8s-extension show` kommandon och används därför för att överföra känsliga inställningar. De skickas in som blank steg som avgränsade `key=value` par efter parameter namnet. Om den här parametern används i kommandot kan du `--configuration-protected-settings-file` inte använda den i samma kommando. |
| `--configuration-protected-settings-file` | Sökväg till JSON-filen med nyckel värdes par som ska användas för att överföra känsliga inställningar till tillägget. Om den här parametern används i kommandot kan du `--configuration-protected-settings` inte använda den i samma kommando. |
| `--release-namespace` | Den här parametern anger det namn område inom vilket versionen ska skapas. Den här parametern är bara relevant om `scope` parametern har angetts till `cluster` . |
| `--release-train` |  Tilläggs författare kan publicera versioner i olika versions tåg som `Stable` , `Preview` osv. Om den här parametern inte anges uttryckligen `Stable` används som standard. Det går inte att använda den här parametern när `autoUpgradeMinorVersion` parametern har angetts till `false` . |
| `--target-namespace` | Den här parametern anger det namn område inom vilket versionen kommer att skapas. Behörighet för det system konto som har skapats för den här tilläggs instansen kommer att begränsas till det här namn området. Den här parametern är bara relevant om `scope` parametern är inställd på `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Visa information om en tilläggs instans

Visa information om en nu installerad tilläggs instans med `k8s-extension show` , och skicka värden för de obligatoriska parametrarna:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Utdataparametrar**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Visa en lista över alla tillägg som är installerade i klustret

Visa en lista med alla tillägg som är installerade i ett kluster med `k8s-extension list` och som skickar in värden för de obligatoriska parametrarna.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Utdataparametrar**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="update-an-existing-extension-instance"></a>Uppdatera en befintlig tilläggs instans

Uppdatera en tilläggs instans i ett kluster med `k8s-extension update` och överför värdena för att uppdatera.  Det här kommandot uppdaterar endast `auto-upgrade-minor-version` `release-train` egenskaperna,, och `version` . Exempel:

- **Uppdatera lanserings tåg:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --release-train Preview
    ```

- **Inaktivera instansen för automatisk uppgradering och fäst tillägg till en angiven version:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version false --version 2.2.2
    ```

- **Aktivera automatisk uppgradering för tilläggs instansen:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version true
    ```

> [!NOTE]
> `version`Parametern kan endast anges när `--auto-upgrade-minor-version` har angetts till `false` .

### <a name="delete-extension-instance"></a>Ta bort tilläggs instans

Ta bort en tilläggs instans i ett kluster med `k8s-extension delete` och skicka värden för de obligatoriska parametrarna.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Den Azure-resurs som representerar det här tillägget tas bort omedelbart. Helm-versionen på klustret som är associerad med det här tillägget tas bara bort när agenterna som körs på Kubernetes-klustret har nätverks anslutning och kan kontaktas för Azure-tjänster igen för att hämta det önskade läget.


## <a name="next-steps"></a>Nästa steg

Läs mer om de kluster tillägg som för närvarande är tillgängliga för Azure Arc-aktiverade Kubernetes:
> [!div class="nextstepaction"]
> [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)