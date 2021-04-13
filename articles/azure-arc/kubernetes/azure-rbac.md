---
title: Azure RBAC för Azure Arc-aktiverade Kubernetes-kluster
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Använda Azure RBAC för verifierings kontroller i Azure Arc-aktiverade Kubernetes-kluster
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304213"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Integrera Azure Active Directory med Azure Arc-aktiverade Kubernetes-kluster

Kubernetes [ClusterRoleBinding och RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) objekt typer hjälper till att definiera auktorisering i Kubernetes internt. Med den här funktionen kan du använda Azure Active Directory-och roll tilldelningar i Azure för att kontrol lera auktoriserings kontroller i klustret. Detta innebär att du nu kan använda Azure Role-tilldelningar för att styra vem som kan läsa, skriva och ta bort dina Kubernetes-objekt, till exempel distribution, Pod och tjänster

En konceptuell översikt över den här funktionen finns i artikeln [Azure RBAC – Azure Arc Enabled Kubernetes](conceptual-azure-rbac.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Installera eller uppgradera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) till version >= 2.16.0

- Installera `connectedk8s` Azure CLI-tillägget för version >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Om `connectedk8s` tillägget redan har installerats kan du uppdatera det till den senaste versionen med hjälp av följande kommando: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Ett befintligt Azure Arc-aktiverat Kubernetes-kopplat kluster.
    - Om du inte har anslutit ett kluster ännu använder du vår [snabb start](quickstart-connect-cluster.md).
    - [Uppgradera dina agenter](agent-upgrade.md#manually-upgrade-agents) till version >= 1.1.0.

> [!NOTE]
> Den här funktionen kan inte konfigureras för hanterade Kubernetes-erbjudanden av moln leverantörer som elastiska Kubernetes-tjänster eller Google Kubernetes-motor där användaren inte har åtkomst till `apiserver` klustret. För Azure Kubernetes service-kluster (AKS) är den här [funktionen tillgänglig internt](../../aks/manage-azure-rbac.md) och kräver inte att AKS-klustret är anslutet till Azure-bågen.

## <a name="set-up-azure-ad-applications"></a>Konfigurera Azure AD-program

### <a name="create-server-application"></a>Skapa serverprogram

1. Skapa ett nytt Azure AD-program och hämta dess `appId` värde, som används i senare steg som `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Uppdatera anspråken för program grupp medlemskap:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Skapa ett huvud namn för tjänsten och hämta dess `password` fält värde, vilket krävs senare `serverApplicationSecret` när du aktiverar den här funktionen i klustret:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Ge program-API-behörighet:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Det här steget måste utföras av en Azure-innehavaradministratör.
    > * För användning av den här funktionen i produktion rekommenderar vi att du skapar en annan server tillämpning för varje kluster.

### <a name="create-client-application"></a>Skapa klient program

1. Skapa ett nytt Azure AD-program och hämta dess "appId"-värde, som används i senare steg som `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Skapa ett huvud namn för tjänsten för det här klient programmet:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Hämta `oAuthPermissionId` for Server-programmet:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Bevilja de behörigheter som krävs för klient programmet:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Skapa en roll tilldelning för serverprogrammet

Serverprogrammet måste ha `Microsoft.Authorization/*/read` behörighet för att kontrol lera om användaren som gör begäran har behörighet för de Kubernetes-objekt som ingår i begäran.

1. Skapa en fil med namnet accessCheck.jspå med följande innehåll:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Ersätt `<subscription-id>` med det faktiska prenumerations-ID: t.

2. Kör följande kommando för att skapa den nya anpassade rollen:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. Från kommandot utdata från ovan sparar du värdet för `id` fältet, som används i senare steg som `roleId` .

4. Skapa en roll tilldelning på serverprogrammet som en tilldelad roll med den roll som du skapade ovan:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Aktivera Azure RBAC i kluster

1. Aktivera Azure RBAC på ditt Arc-aktiverade Kubernetes-kluster genom att köra följande kommando:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Innan du kör kommandot ovan måste du kontrol lera att `kubeconfig` filen på datorn pekar på det kluster där du vill aktivera Azure RBAC-funktionen.
    > 2. Använd `--skip-azure-rbac-list` med kommandot ovan för en kommaavgränsad lista över användar namn/e-post/OID som genomgår verifierings kontroller med hjälp av inbyggda Kubernetes-ClusterRoleBinding och RoleBinding-objekt i stället för Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>För ett allmänt kluster där ingen avstämdning körs på apiserver-specifikationen:

1. SSH till varje huvud nod i klustret och utför följande steg:

    1. Öppna `apiserver` manifest i redigerings läge:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Lägg till följande specifikation under `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Lägg till följande specifikation under `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Lägg till följande `apiserver` argument:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Om Kubernetes-klustret är av version >= 1.19.0, `apiserver argument` måste följande också anges:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Spara och avsluta redigeraren för att uppdatera `apiserver` pod.


### <a name="for-a-cluster-created-using-cluster-api"></a>För ett kluster som skapats med kluster-API

1. Kopiera skydds hemligheten som innehåller autentiserings-och auktoriserings-webhook-konfigurationsfiler `from the workload cluster` till datorn:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Ändra `namespace` fältet i `azure-arc-guard-manifests.yaml` filen till namn området i hanterings klustret där du tillämpar de anpassade resurserna för att skapa arbets belastnings kluster.

1. Använd det här manifestet:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Redigera `KubeadmControlPlane` objektet genom att köra `kubectl edit kcp <clustername>-control-plane` :
    
    1. Lägg till följande kodfragment under `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Lägg till följande kodfragment under `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Lägg till följande kodfragment under `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Spara och avsluta för att uppdatera `KubeadmControlPlane` objektet. Vänta tills de här ändringarna har realiserats i arbets belastnings klustret.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Skapa roll tilldelningar för användare för åtkomst till klustret

Ägare av Azure Arc-aktiverade Kubernetes-resursen kan antingen använda inbyggda roller eller anpassade roller för att ge andra användare åtkomst till Kubernetes-klustret.

### <a name="built-in-roles"></a>Inbyggda roller

| Roll | Beskrivning |
|---|---|
| [Azure Arc Kubernetes Viewer](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Tillåter skrivskyddad åtkomst för att se de flesta objekt i ett namn område. Med den här rollen kan du inte Visa hemligheter. Detta beror `read` på att behörigheten för hemligheter möjliggör åtkomst till `ServiceAccount` autentiseringsuppgifter i namn området, vilket i sin tur gör att API-åtkomst används i `ServiceAccount` (en form av behörighets eskalering). |
| [Azure Arc Kubernetes-skrivare](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Tillåter Läs-/skriv åtkomst till de flesta objekt i ett namn område. Den här rollen tillåter inte visning eller ändring av roller eller roll bindningar. Den här rollen ger dock åtkomst till hemligheter och kör poddar som alla `ServiceAccount` i namn området, så att den kan användas för att få åtkomst nivåer för API: er i `ServiceAccount` namn området. |
| [Azure Arc Kubernetes-administratör](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Tillåter administratörs åtkomst. Avsedd att beviljas inom ett namn område med hjälp av en RoleBinding. Om det används i en RoleBinding, tillåter Läs-och skriv åtkomst till de flesta resurser i ett namn område, inklusive möjligheten att skapa roller och roll bindningar i namn området. Den här rollen tillåter inte skriv åtkomst till resurs kvot eller själva namn området. |
| [Azure Arc Kubernetes-kluster administratör](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Tillåter åtkomst till superanvändare för att köra alla åtgärder på alla resurser. När det används i en ClusterRoleBinding ger den fullständig kontroll över alla resurser i klustret och i alla namn områden. När det används i en RoleBinding ger den fullständig kontroll över varje resurs i roll bindningens namnrymd, inklusive själva namn området.|

Du kan skapa roll tilldelningar som är begränsade till Arc-aktiverat Kubernetes-kluster på `Access Control (IAM)` bladet i kluster resursen på Azure Portal. Du kan också använda Azure CLI-kommandon, som du ser nedan:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

var `AZURE-AD-ENTITY-ID` kan vara ett användar namn (till exempel testuser@mytenant.onmicrosoft.com ) eller till och med `appId` för ett huvud namn för tjänsten.

Här är ett annat exempel på hur du skapar en roll tilldelning som är begränsad till en angiven namnrymd i klustret –

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Även om roll tilldelningar som är begränsade till klustret kan skapas med hjälp av antingen Azure Portal eller CLI, kan roll tilldelningarna som är begränsade till namn områden bara skapas med CLI.

### <a name="custom-roles"></a>Anpassade roller

Du kan välja att skapa en egen roll definition för användning i roll tilldelningar.

Gå igenom exemplet nedan för en roll definition som gör att en användare bara kan läsa distributioner. Mer information finns i [den fullständiga listan med data åtgärder som du kan använda för att skapa en roll definition](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Kopiera JSON-objektet nedan till en fil med namnet custom-role.jspå. Ersätt `<subscription-id>` plats hållaren med det faktiska prenumerations-ID: t. Den anpassade rollen nedan använder en av data åtgärderna och låter dig Visa alla distributioner i omfånget (kluster/namn område) där roll tilldelningen skapas.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Skapa roll definitionen genom att köra kommandot nedan från mappen där du sparade `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Skapa en roll tilldelning med den här anpassade roll definitionen:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Konfigurera kubectl med användarautentiseringsuppgifter

Det finns två sätt att hämta `kubeconfig` filen som krävs för att komma åt klustret:
1. Använd [kluster Connect](cluster-connect.md) -funktionen ( `az connectedk8s proxy` ) i det aktiverade Kubernetes-klustret i Azure-bågen.
1. Kluster administratören delar `kubeconfig` filen med alla andra användare.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Om du får åtkomst till kluster med funktionen kluster anslutning

Kör följande kommando för att starta Proxy-processen:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

När du har kört proxyservern kan du öppna en annan flik i konsolen för att [börja skicka dina begär anden till klustret](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Om kluster administratören har delat `kubeconfig` filen med dig 

1. Kör följande kommando för att ange autentiseringsuppgifter för användaren:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Öppna `kubeconfig` filen som du skapade tidigare. Under `contexts` kontrollerar du den kontext som är associerad med kluster punkter i de användarautentiseringsuppgifter som skapades i föregående steg.

1. Lägg till **konfigurations läges** inställning under användar konfiguration:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Skickar begär anden till kluster

1. Kör alla `kubectl` kommandon. Exempel:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. När du har tillfrågats om en webbläsarbaserad autentisering kopierar du enhetens inloggnings-URL `https://microsoft.com/devicelogin` och öppnar den i webbläsaren.

1. Ange den kod som skrivs ut i-konsolen, kopiera och klistra in koden i terminalen i Indataporten för enhets autentisering.

1. Ange användar namnet ( testuser@mytenant.onmicrosoft.com ) och det associerade lösen ordet.

1. Om du ser ett fel meddelande som detta innebär det att du inte har behörighet att komma åt den begärda resursen:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    En administratör måste skapa en ny roll tilldelning som auktoriserar den här användaren för att få åtkomst till resursen.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Anslut säkert till klustret med [kluster anslutning](cluster-connect.md)