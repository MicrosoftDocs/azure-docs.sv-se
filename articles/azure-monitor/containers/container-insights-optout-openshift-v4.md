---
title: Så här slutar du övervaka ditt Azure-och Red Hat OpenShift v4-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan stoppa övervakningen av ditt Azure Red Hat OpenShift och Red Hat OpenShift version 4-kluster med behållar insikter.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731807"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Så här stoppar du övervakning av v4-kluster för Azure och Red Hat OpenShift

När du har aktiverat övervakning av ditt Azure Red Hat OpenShift och Red Hat OpenShift version 4. x-kluster kan du sluta övervaka klustret med behållar insikter om du inte längre vill övervaka det. Den här artikeln visar hur du kan göra detta.  

## <a name="how-to-stop-monitoring-using-helm"></a>Stoppa övervakning med Helm

1. För att först identifiera Helm-diagrammets version av container Insights som är installerad på klustret, kör du följande Helm-kommando.

    ```
    helm list
    ```

    Utdata ser ut ungefär så här:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-Release-1* representerar Helm-diagrammets version för behållar insikter.

2. Om du vill ta bort diagram versionen kör du följande Helm-kommando.

    `helm delete <releaseName>`

    Exempel:

    `helm delete azmon-containers-release-1`

    Detta tar bort versionen från klustret. Du kan kontrol lera genom att köra `helm list` kommandot:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Konfigurations ändringen kan ta några minuter att slutföra. Eftersom Helm spårar dina versioner även efter att du har tagit bort dem, kan du granska ett klusters historik och till och med ta bort en version med `helm rollback` .

## <a name="next-steps"></a>Nästa steg

Om Log Analytics arbets ytan bara har skapats för att stödja övervakning av klustret och den inte längre behövs, måste du ta bort den manuellt. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../logs/delete-workspace.md).