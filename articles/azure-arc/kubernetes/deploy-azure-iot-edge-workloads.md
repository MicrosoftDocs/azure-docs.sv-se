---
title: Distribuera Azure IoT Edge-arbetsbelastningar
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Distribuera Azure IoT Edge-arbetsbelastningar
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121736"
---
# <a name="deploy-azure-iot-edge-workloads"></a>Distribuera Azure IoT Edge-arbetsbelastningar

## <a name="overview"></a>Översikt

Azure-bågen och Azure IoT Edge kan enkelt komplettera var and s funktioner. 

Azure Arc tillhandahåller mekanismer för kluster operatörer för att konfigurera de grundläggande komponenterna i ett kluster och tillämpa och tillämpa kluster principer. 

Azure IoT Edge gör det möjligt för program operatörer att fjärrdistribuera och hantera arbets belastningar i skala med bekväm moln inhämtning och dubbelriktade kommunikations primitiver. 

Diagrammet nedan illustrerar Azure Arc och Azure IoT Edge relationen:

![Konfiguration av IoT-båge](./media/edge-arc.png)

## <a name="pre-requisites"></a>Förutsättningar

* [Registrera en IoT Edge-enhet](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) och [distribuera modulen simulerad temperatur sensor](../../iot-edge/quickstart-linux.md#deploy-a-module). Notera enhetens anslutnings sträng för *värdena. yaml* som nämns nedan.

* Använd [IoT Edge support för Kubernetes](https://aka.ms/edgek8sdoc) för att distribuera det via Azure Arcs flödes operator.

* Hämta filen [*Values. yaml*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) för IoT Edge Helm-diagrammet och Ersätt `deviceConnectionString` plats hållaren i slutet av filen med den anslutnings sträng som du noterade tidigare. Ange andra alternativ för diagram installationen som stöds efter behov. Skapa ett namn område för IoT Edge arbets belastningen och generera en hemlighet i det:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Du kan också konfigurera fjärran sluten med hjälp av [kluster konfigurations exemplet](./tutorial-use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Anslut ett kluster

Använd `az` Azure CLI- `connectedk8s` tillägget för att ansluta ett Kubernetes-kluster till Azure-bågen:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Skapa en konfiguration för IoT Edge

[Git-lagrings platsen](https://github.com/veyalla/edgearc) pekar på IoT Edge Helm-diagrammet och refererar till hemligheten som skapats i avsnittet krav.

Använd `az` Azure CLI- `k8s-configuration` tillägget för att skapa en konfiguration som länkar det anslutna klustret till git-lagrings platsen:

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Om några minuter bör du se de IoT Edge arbets belastnings moduler som distribuerats till klustrets `iotedge` namnrymd. 

Visa `SimulatedTemperatureSensor` Pod-loggarna i det namn området för att se de exempel värden som genereras. Du kan också se att meddelandena kommer till din IoT Hub med hjälp av [tillägget Azure IoT Hub Toolkit för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="cleanup"></a>Rensa

Ta bort konfigurationen med:

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [använder Azure policy för att styra kluster konfigurationen](./use-azure-policy.md).
