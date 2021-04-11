---
title: Använda kluster Connect för att ansluta till Azure Arc-aktiverade Kubernetes-kluster
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Använd kluster Anslut för att säkert ansluta till Azure Arc-aktiverade Kubernetes-kluster
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451135"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Använda kluster Connect för att ansluta till Azure Arc-aktiverade Kubernetes-kluster

Med kluster anslutning kan du på ett säkert sätt ansluta till Azure Arc-aktiverade Kubernetes-kluster utan att någon inkommande port måste vara aktive rad i brand väggen. Åtkomst till `apiserver` Kubernetes-klustret med ARC-aktiverad möjliggör följande scenarier:
* Aktivera interaktiv fel sökning och fel sökning.
* Ge kluster åtkomst till Azure-tjänster för [anpassade platser](custom-locations.md) och andra resurser som skapas ovanpå det.

En översikt över den här funktionen finns i artikeln [kluster Connect – Azure Arc Enabled Kubernetes](conceptual-cluster-connect.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Förutsättningar   

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0

- Installera `connectedk8s` Azure CLI-tillägget för version >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Om du redan har installerat `connectedk8s` tillägget uppdaterar du tillägget till den senaste versionen:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Ett befintligt Azure Arc-aktiverat Kubernetes-kopplat kluster.
    - Om du inte har anslutit ett kluster ännu använder du vår [snabb start](quickstart-connect-cluster.md).
    - [Uppgradera dina agenter](agent-upgrade.md#manually-upgrade-agents) till version >= 1.1.0.

- Aktivera klustret Anslut på alla Azure Arc-aktiverade Kubernetes-kluster genom att köra följande kommando på en dator där `kubeconfig` filen pekas i klustret:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Aktivera nedanstående slut punkter för utgående åtkomst utöver de som nämns under [ansluta ett Kubernetes-kluster till Azure-bågen](quickstart-connect-cluster.md#meet-network-requirements):

    | Slutpunkt | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Användning

Två autentiseringsalternativ stöds med funktionen kluster anslutning: 
* Azure Active Directory (Azure AD) 
* Token för tjänst konto

### <a name="option-1-azure-active-directory"></a>Alternativ 1: Azure Active Directory

1. När `kubeconfig` filen pekar på `apiserver` ditt Kubernetes-kluster skapar du en ClusterRoleBinding eller RoleBinding till Azure AD-entiteten (tjänstens huvud namn eller användare) som kräver åtkomst:

    **För användare:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **För Azure AD-program:**

    1. Hämta det `objectId` som är associerat med ditt Azure AD-program:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Skapa en ClusterRoleBinding eller RoleBinding för Azure AD-entiteten (tjänstens huvud namn eller användare) som behöver åtkomst till det här klustret:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. När du har loggat in på Azure CLI med hjälp av Azure AD-entiteten, måste du hämta klustrets anslutning `kubeconfig` som krävs för att kommunicera med klustret från valfri plats (från och med utanför brand väggen runt klustret):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Använd `kubectl` för att skicka begär anden till klustret:

    ```console
    kubectl get pods
    ```
    
    Nu bör du se ett svar från klustret som innehåller listan över alla poddar under `default` namn området.

### <a name="option-2-service-account-bearer-token"></a>Alternativ 2: tjänst kontots Bearer-token

1. När `kubeconfig` filen pekar på `apiserver` ditt Kubernetes-kluster skapar du ett tjänst konto i alla namn områden (följande kommando skapar det i standard namn området):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Skapa ClusterRoleBinding eller RoleBinding för att ge [tjänst kontot rätt behörigheter för klustret](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Hämta tjänst kontots token med följande kommandon

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Hämta kluster anslutningen som `kubeconfig` krävs för att kommunicera med klustret från valfri plats (från och med utanför brand väggen runt klustret):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Använd `kubectl` för att skicka begär anden till klustret:

    ```console
    kubectl get pods
    ```

    Nu bör du se ett svar från klustret som innehåller listan över alla poddar under `default` namn området.

## <a name="known-limitations"></a>Kända begränsningar

När du gör förfrågningar till Kubernetes-klustret, om den Azure AD-entitet som används ingår i fler än 200 grupper, observeras följande fel som är en känd begränsning:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

För att få ett tidigare fel:
1. Skapa ett [huvud namn för tjänsten](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), vilket är mindre troligt att vara medlem i fler än 200 grupper.
1. [Logga in](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) på Azure CLI med tjänstens huvud namn innan du kör `az connectedk8s proxy` kommandot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Konfigurera [Azure AD RBAC](azure-rbac.md) i klustren