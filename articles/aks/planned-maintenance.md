---
title: Använd planerat underhåll för ditt Azure Kubernetes service (AKS)-kluster (för hands version)
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder planerat underhåll i Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: deeb8375e2c1d30a71b0791886362bfb045ef6d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727832"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Använda planerat underhåll för att schemalägga underhålls perioder för ditt Azure Kubernetes service (AKS)-kluster (för hands version)

Ditt AKS-kluster har regelbundet underhåll som utförs automatiskt. Som standard kan det här arbetet ske när som helst. Med planerat underhåll kan du schemalägga veckovis underhålls fönster som kommer att uppdatera ditt kontroll plan samt din Kube-poddar på en VMSS-instans och minimera arbets belastnings påverkan. När du har schemalagt det utförs allt underhåll under det valda fönstret. Du kan schemalägga ett eller flera vecko Visa fönster i klustret genom att ange ett dags-eller tidsintervall på en angiven dag. Underhålls fönster konfigureras med hjälp av Azure CLI.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Begränsningar

När planerat underhåll används gäller följande begränsningar:

- AKS förbehåller sig rätten att dela upp dessa fönster efter oplanerade/reaktiva underhålls åtgärder som är brådskande eller kritiska.
- För närvarande anses underhålls åtgärder *endast vara bästa* möjliga och är inte garanterat att ske inom ett angivet fönster.
- Det går inte att blockera uppdateringar i mer än sju dagar.

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Du behöver också *AKS – för hands* version av Azure CLI-tillägget 0.5.4 eller senare. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] . Eller installera eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Tillåt underhåll varje måndag 1:10:00 till 2:10:00

Du kan använda kommandot för att lägga till en underhålls period `az aks maintenanceconfiguration add` .

> [!IMPORTANT]
> Planerat underhålls fönster anges i UTC (Coordinated Universal Time).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Följande exempel på utdata visar underhålls perioden från 1:10:00 till 2:10:00 varje måndag.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Om du vill tillåta underhåll när som helst under en dag utelämnar du parametern *Start timme* . Följande kommando anger till exempel underhålls perioden för hela dagen varje måndag:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Lägg till en underhålls konfiguration med en JSON-fil

Du kan också använda en JSON-fil för att skapa en underhålls period i stället för att använda parametrar. Skapa en `test.json` fil med följande innehåll:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Ovanstående JSON-fil anger underhålls perioder varje tisdag 1:10:00-3:10:00 och varje onsdag vid 1:10:00-2:10:00 och 6:10:00-7:10:00. Det finns också ett undantag från *2021-05-26T03:00:00Z* till *2021-05-30T12:00:00Z* där underhåll inte är tillåtet även om det överlappar en underhålls period. Följande kommando lägger till underhålls Fönstren från `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Uppdatera ett befintligt underhålls fönster

Om du vill uppdatera en befintlig underhålls konfiguration använder du `az aks maintenanceconfiguration update` kommandot.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Lista alla underhålls fönster i ett befintligt kluster

Använd kommandot för att se alla aktuella underhålls konfigurations fönster i AKS-klustret `az aks maintenanceconfiguration list` .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

I utdata nedan kan du se att det finns två underhålls fönster konfigurerade för myAKSCluster. Ett fönster finns på måndagar vid 1:10:00 och ett annat fönster är på fredag 4:10:00.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Visa ett särskilt underhålls konfigurations fönster i ett AKS-kluster

Om du vill se ett särskilt underhålls konfigurations fönster i AKS-klustret använder du `az aks maintenanceconfiguration show` kommandot.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Följande exempel på utdata visar underhålls perioden för *standard*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Ta bort ett visst underhålls konfigurations fönster i ett befintligt AKS-kluster

Om du vill ta bort ett visst underhålls konfigurations fönster i AKS-klustret använder du `az aks maintenanceconfiguration delete` kommandot.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med att uppgradera ditt AKS-kluster finns i [uppgradera ett AKS-kluster][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
