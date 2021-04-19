---
title: Krav | Direkt anslutningsläge
description: Krav för att distribuera datastyrenheten i direkt anslutningsläge.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716320"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Distribuera datakontrollant – direkt anslutningsläge (krav)

Den här artikeln beskriver hur du förbereder distributionen av en datakontrollant för Azure Arc-aktiverade datatjänster i direkt anslutningsläge.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

I en sammanfattning på hög nivå omfattar förutsättningarna:

1. Installera verktygen
1. Lägga till tillägg
1. Skapa tjänstens huvudnamn och konfigurera roller för mått
1. Ansluta Kubernetes-kluster till Azure Azure Arc Kubernetes

När du har slutfört dessa krav kan du distribuera [Azure Arc datakontrollant | Direkt connect-läge](deploy-data-controller-direct-mode.md).

I de återstående avsnitten i den här artikeln beskrivs förutsättningarna.

## <a name="install-tools"></a>Installera verktygen

- Helm version 3.3+[(installera](https://helm.sh/docs/intro/install/))
- Azure CLI ([installera](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Lägga till tillägg för Azure CLI

Dessutom krävs följande az-tillägg:
- Azure `k8s-extension` CLI-tillägg (0.2.0)
- Azure CLI `customlocation` (0.1.0)

Exemplet `az` och dess CLI-tillägg skulle vara:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Skapa tjänstens huvudnamn och konfigurera roller för mått

Följ stegen som beskrivs i artikeln [Ladda upp mått](upload-metrics-and-logs-to-azure-monitor.md) och skapa ett huvudnamn för tjänsten och bevilja rollerna enligt beskrivningen i artikeln. 

SPN ClientID, TenantID och Klienthemlighet krävs när du distribuerar Azure Arc [datakontrollant](deploy-data-controller-direct-mode.md). 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Ansluta Kubernetes-kluster till Azure Azure Arc Kubernetes

Slutför den här uppgiften genom att följa stegen i [Ansluta ett befintligt Kubernetes-kluster till Azure Arc.](../kubernetes/quickstart-connect-cluster.md)

## <a name="next-steps"></a>Nästa steg

När du har slutfört dessa krav kan du distribuera [Azure Arc datakontrollant | Direkt anslutningsläge](deploy-data-controller-direct-mode.md).
