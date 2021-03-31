---
title: Uppgradera ingress-styrenheten med Helm
description: Den här artikeln innehåller information om hur du uppgraderar ett Application Gateway ingress med Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84806782"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Så här uppgraderar du en inkommande Application Gateway-styrenhet med hjälp av Helm 

Azure Application Gateway ingångs kontroll för Kubernetes (AGIC) kan uppgraderas med hjälp av en Helm-lagringsplats som finns på Azure Storage.

Innan du påbörjar uppgraderings proceduren måste du kontrol lera att du har lagt till den nödvändiga lagrings platsen:

- Visa dina nyligen tillagda Helm-databaser med:

    ```bash
    helm repo list
    ```

- Lägg till AGIC-lagrings platsen med:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Uppgradera

1. Uppdatera AGIC Helm-lagringsplatsen för att få den senaste versionen:

    ```bash
    helm repo update
    ```

1. Visa tillgängliga versioner av `application-gateway-kubernetes-ingress` diagrammet:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Exempel svar:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Den senaste tillgängliga versionen från listan ovan är: `0.7.0-rc1`

1. Visa de Helm-diagram som för närvarande är installerade:

    ```bash
    helm list
    ```

    Exempel svar:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Helm-diagrammets installation från exempel svaret ovan heter `odd-billygoat` . Vi kommer att använda det här namnet för resten av kommandona. Det faktiska distributions namnet är förmodligen annorlunda.

1. Uppgradera Helm-distributionen till en ny version:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Återställning

Om distributionen av Helm skulle fungera kan du återställa till en tidigare version.

1. Hämta det senaste kända felfria versions numret:

    ```bash
    helm history odd-billygoat
    ```

    Exempel på utdata:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Från exempel resultatet av `helm history` kommandot ser det ut som den senaste lyckade distributionen av vår `odd-billygoat` revision `1`

1. Återställ till den senaste lyckade ändringen:

    ```bash
    helm rollback odd-billygoat 1
    ```
