---
title: Distribuera Azure IoT Edge-arbetsbelastningar (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Distribuera Azure IoT Edge-arbetsbelastningar
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091655"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Distribuera Azure IoT Edge-arbetsbelastningar (för hands version)

## <a name="overview"></a>Översikt

Azure-bågen och Azure IoT Edge kompletterar var and s funktioner på ett bra sätt. Azure Arc tillhandahåller mekanismer för kluster operatörer till att konfigurera de grundläggande komponenterna i ett kluster samt tillämpa och tillämpa kluster principer. Och IoT Edge gör det möjligt för program operatörer att fjärrdistribuera och hantera arbets belastningar i skala med bekväm moln inmatning och dubbelriktade kommunikations primitiver. Diagrammet nedan illustrerar detta:

![Konfiguration av IoT-båge](./media/edge-arc.png)

## <a name="pre-requisites"></a>Förutsättningar

* [Registrera en IoT Edge-enhet](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) och [distribuera modulen simulerad temperatur sensor](../../iot-edge/quickstart-linux.md#deploy-a-module). Glöm inte att notera enhetens anslutnings sträng.

* Använd [IoT Edge support för Kubernetes](https://aka.ms/edgek8sdoc) för att distribuera det via Azure Arcs flödes operator.

* Hämta filen [**Values. yaml**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) för IoT Edge Helm-diagrammet och Ersätt **deviceConnectionString** placeholder i slutet av filen med det som anges i steg 1. Du kan ange andra alternativ för diagram installationen som stöds efter behov. Skapa ett namn område för IoT Edge arbets belastningen och skapa en hemlighet i det:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Du kan också ställa in detta på distans med hjälp av [kluster konfigurations exemplet](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Anslut ett kluster

Använd `az` CLI- `connectedk8s` tillägget för att ansluta ett Kubernetes-kluster till Azure-bågen:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Skapa en konfiguration för IoT Edge

Exempel på lagrings platsen: https://github.com/veyalla/edgearc

Den här lagrings platsen pekar på IoT Edge Helm-diagrammet och refererar till hemligheten som skapats i avsnittet krav.

1. Använd `az` CLI- `k8sconfiguration` tillägget för att skapa en konfiguration för att länka det anslutna klustret till git-lagrings platsen:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    I en minut eller två bör du se de IoT Edge arbets belastnings moduler som distribuerats till `iotedge` namn området i klustret. Du kan visa loggarna för `SimulatedTemperatureSensor` Pod i det namn området för att se de exempel värden som genereras. Du kan också se att meddelandena kommer till din IoT Hub med hjälp av [tillägget Azure IoT Hub Toolkit för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Rensa

Du kan ta bort konfigurationen med:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Nästa steg

[Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
