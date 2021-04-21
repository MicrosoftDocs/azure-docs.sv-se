---
title: Använda planerat underhåll för ditt Azure Kubernetes Service (AKS)-kluster (förhandsversion)
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder planerat underhåll i Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782917"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Använd Planerat underhåll för att schemalägga underhållsfönstret för ditt AKS Azure Kubernetes Service kluster (förhandsversion)

Ditt AKS-kluster har regelbundet underhåll som utförs på det automatiskt. Som standard kan det här arbetet ske när som helst. Med planerat underhåll kan du schemalägga veckovisa underhållsfönster som uppdaterar kontrollplanet och kube-systempoddar på en VMSS-instans och minimera påverkan på arbetsbelastningen. När det har schemalagts sker allt underhåll under den valda perioden. Du kan schemalägga ett eller flera veckovisa fönster i klustret genom att ange en dag eller ett tidsperiod för en viss dag. Underhållsfönster konfigureras med hjälp av Azure CLI.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Begränsningar

När du använder planerat underhåll gäller följande begränsningar:

- AKS förbehåller sig rätten att bryta dessa fönster för oplanerade/reaktiva underhållsåtgärder som är brådskande eller kritiska.
- För närvarande betraktas endast underhållsåtgärder *som bästa prestanda* och garanteras inte att de utförs inom en angiven tidsperiod.
- Uppdateringar kan inte blockeras i mer än sju dagar.

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Du behöver även *Azure CLI-tillägget aks-preview* version 0.5.4 eller senare. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add.][az-extension-add] Eller installera eventuella tillgängliga uppdateringar med hjälp av [kommandot az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Tillåt underhåll varje måndag kl. 01:00 till 02:00

Om du vill lägga till en underhållsfönstret kan du använda `az aks maintenanceconfiguration add` kommandot .

> [!IMPORTANT]
> Fönster för planerat underhåll anges i Coordinated Universal Time (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Följande exempelutdata visar underhållsfönstret från 1:00 till 02:00 varje måndag.

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

Om du vill tillåta underhåll när som helst under en dag utelämnar du *parametern starttimme.* Följande kommando anger till exempel underhållsfönstret för hela dagen varje måndag:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Lägga till en underhållskonfiguration med en JSON-fil

Du kan också använda en JSON-fil för att skapa en underhållsfönstret i stället för att använda parametrar. Skapa `test.json` en fil med följande innehåll:

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

Ovanstående JSON-fil anger underhållsfönster varje tisdag 01.00–03.00 och varje onsdag 13.00–02.00 och 06.00–07.00. Det finns också ett undantag från *2021-05-26T03:00:00Z* till *2021-05-30T12:00:00Z* där underhåll inte tillåts även om det överlappar med en underhållsfönstret. Följande kommando lägger till underhållsfönster från `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Uppdatera en befintlig underhållsfönstret

Om du vill uppdatera en befintlig underhållskonfiguration använder du `az aks maintenanceconfiguration update` kommandot .

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Lista alla underhållsfönster i ett befintligt kluster

Om du vill se alla aktuella underhållskonfigurationsfönster i ditt AKS-kluster använder du `az aks maintenanceconfiguration list` kommandot .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

I utdata nedan ser du att det finns två underhållsfönster konfigurerade för myAKSCluster. Ett fönster är på måndagar kl. 01:00 och ett annat fönster är på fredag kl. 04:00.

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

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Visa ett specifikt underhållskonfigurationsfönster i ett AKS-kluster

Om du vill se ett specifikt underhållskonfigurationsfönster i ditt AKS-kluster använder du `az aks maintenanceconfiguration show` kommandot .

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Följande exempelutdata visar underhållsfönstret för *standard :*

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

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Ta bort ett visst underhållskonfigurationsfönster i ett befintligt AKS-kluster

Om du vill ta bort ett visst underhållskonfigurationsfönster i ditt AKS-kluster använder du `az aks maintenanceconfiguration delete` kommandot .

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med att uppgradera ditt AKS-kluster finns [i Uppgradera ett AKS-kluster][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
