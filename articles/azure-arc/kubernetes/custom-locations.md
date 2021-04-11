---
title: Anpassade platser i Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Använd anpassade platser för att distribuera Azure PaaS-tjänster på Azure Arc-aktiverade Kubernetes-kluster
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451159"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Anpassade platser i Azure Arc-aktiverade Kubernetes

Som ett Azure location-tillägg ger *anpassade platser* ett sätt för klient administratörer att använda sina Azure-Kubernetes-kluster som mål platser för att distribuera Azure Services-instanser. Exempel på Azure-resurser är Azure Arc-aktiverad SQL-hanterad instans och Azure Arc-aktiverat PostgreSQL-skalning.

På samma sätt som för Azure-platser kan slutanvändare inom klienten med åtkomst till anpassade platser distribuera resurser där deras privata data bearbetning används.

En översikt över den här funktionen finns på [anpassade platser – Azure Arc-aktiverad Kubernetes-](conceptual-custom-locations.md) artikel.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0.

- `connectedk8s` (version >= 1.1.0), `k8s-extension` (version >= 0.2.0) och `customlocation` (version >= 0.1.0) Azure CLI-tillägg. Installera dessa Azure CLI-tillägg genom att köra följande kommandon:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Om `connectedk8s` `k8s-extension` `customlocation` tilläggen och redan har installerats kan du uppdatera dem till den senaste versionen med hjälp av följande kommando:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- Leverantörs registreringen är klar för `Microsoft.ExtendedLocation` .
    1. Ange följande kommandon:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Övervaka registrerings processen. Registreringen kan ta upp till 10 minuter.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Regioner som stöds för anpassade platser:**
>* East US
>* Europa, västra

## <a name="enable-custom-locations-on-cluster"></a>Aktivera anpassade platser i kluster

Om du vill aktivera den här funktionen i klustret kör du följande kommando:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. Funktionen för anpassade platser är beroende av kluster Connect-funktionen. Därför måste båda funktionerna vara aktiverade för att anpassade platser ska fungera.
> 2. `az connectedk8s enable-features` måste köras på en dator där `kubeconfig` filen pekar på det kluster där funktionerna ska aktive ras.

## <a name="create-custom-location"></a>Skapa anpassad plats

1. Skapa ett Azure Arc-aktiverat Kubernetes-kluster.
    - Om du inte har anslutit ett kluster ännu använder du vår [snabb start](quickstart-connect-cluster.md).
    - [Uppgradera dina agenter](agent-upgrade.md#manually-upgrade-agents) till version >= 1.1.0.

1. Distribuera kluster tillägget för den Azure-tjänst vars instans du slutligen vill ha på den anpassade platsen:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > Utgående proxy utan autentisering och utgående proxy med grundläggande autentisering stöds av den Arc som är aktive rad Data Services kluster tillägget. Utgående proxy som förväntar sig att betrodda certifikat stöds inte för närvarande.

1. Hämta Azure Resource Manager identifierare för Azure Arc-aktiverade Kubernetes-klustret som refereras till i senare steg som `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Hämta Azure Resource Manager identifieraren för det kluster tillägg som distribueras ovanpå Azure Arc-aktiverade Kubernetes-kluster som refereras till i senare steg som `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Skapa en anpassad plats genom att referera till Azure-bågen aktiverat Kubernetes-kluster och tillägget:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Anslut säkert till klustret med [kluster anslutning](cluster-connect.md)