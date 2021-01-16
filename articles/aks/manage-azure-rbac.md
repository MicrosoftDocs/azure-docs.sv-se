---
title: Hantera Azure RBAC i Kubernetes från Azure
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure RBAC för Kubernetes-auktorisering med Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: af8d0300b533d9f25cddf225f4ffbe78ca6bf2cb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249643"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Använda Azure RBAC för Kubernetes-auktorisering (förhandsversion)

Idag kan du redan utnyttja [integrerad autentisering mellan Azure Active Directory (Azure AD) och AKS](managed-aad.md). När den här integrationen är aktive rad kan kunder använda Azure AD-användare,-grupper eller tjänstens huvud namn som ämnen i Kubernetes RBAC, se mer [här](azure-ad-rbac.md).
Den här funktionen gör att du kan hantera användar identiteter och autentiseringsuppgifter separat för Kubernetes. Du måste dock fortfarande konfigurera och hantera Azure RBAC och Kubernetes RBAC separat. Mer information om autentisering och auktorisering med RBAC på AKS finns [här](concepts-identity.md).

Det här dokumentet beskriver en ny metod som möjliggör en enhetlig hanterings-och åtkomst kontroll över Azure-resurser, AKS och Kubernetes-resurser.

## <a name="before-you-begin"></a>Innan du börjar

Möjligheten att hantera RBAC för Kubernetes-resurser från Azure ger dig möjlighet att hantera RBAC för kluster resurserna, antingen med hjälp av Azure eller interna Kubernetes-mekanismer. När det är aktiverat verifieras Azure AD-huvudobjekten exklusivt av Azure RBAC medan vanliga Kubernetes-användare och tjänst konton är exklusivt verifierade av Kubernetes RBAC. Mer information om autentisering och auktorisering med RBAC på AKS finns [här](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Förutsättningar 
- Se till att du har Azure CLI-version 2.9.0 eller senare
- Se till att `EnableAzureRBACPreview` funktions flaggan är aktive rad.
- Se till att du har `aks-preview` [CLI-tillägget][az-extension-add] v 0.4.55 eller senare installerat
- Se till att du har installerat [kubectl v-1.18.3 +][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registrera `EnableAzureRBACPreview` förhands gransknings funktion

Om du vill skapa ett AKS-kluster som använder Azure RBAC för Kubernetes-auktorisering måste du aktivera `EnableAzureRBACPreview` funktions flaggan i din prenumeration.

Registrera `EnableAzureRBACPreview` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören för *Microsoft. container service* med hjälp av [AZ Provider register] [AZ-Provider-registrera] kommandot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster som använder Azure RBAC behöver du *AKS-Preview CLI-* tillägget version 0.4.55 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] eller installera alla tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

- Kräver [hanterad Azure AD-integrering](managed-aad.md).
- Du kan inte integrera Azure RBAC för Kubernetes-auktorisering i befintliga kluster under för hands versionen, men du kommer att kunna allmän tillgänglighet (GA).
- Använd [kubectl v 1.18.3 +][az-aks-install-cli].
- Om du har CRDs och gör anpassade roll definitioner, är det enda sättet att se CRDs idag att tillhandahålla `Microsoft.ContainerService/managedClusters/*/read` . AKS arbetar med att tillhandahålla mer detaljerade behörigheter för CRDs. För återstående objekt kan du använda de olika API-grupperna, till exempel: `Microsoft.ContainerService/apps/deployments/read` .
- Nya roll tilldelningar kan ta upp till 5min att spridas och uppdateras av auktoriseringsservern.
- Kräver att Azure AD-klienten som kon figurer ATS för autentisering är samma som klienten för den prenumeration som innehåller AKS-klustret. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Skapa ett nytt kluster med Azure RBAC och hanterad Azure AD-integrering

Skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa AKS-klustret med hanterad Azure AD-integrering och Azure RBAC för Kubernetes-auktorisering.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

En lyckad skapande av ett kluster med Azure AD-integrering och Azure RBAC för Kubernetes-auktorisering har följande avsnitt i svars texten:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Skapa roll tilldelningar för användare till gång till kluster

AKS tillhandahåller följande fyra inbyggda roller:


| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| RBAC-läsare för Azure Kubernetes service  | Tillåter skrivskyddad åtkomst för att se de flesta objekt i ett namn område. Den tillåter inte visning av roller eller roll bindningar. Den här rollen tillåter inte visning `Secrets` , eftersom läsning av innehållet i hemligheter ger åtkomst till ServiceAccount-autentiseringsuppgifter i namn området, vilket skulle tillåta API-åtkomst som valfri ServiceAccount i namn området (en form av behörighets eskalering)  |
| RBAC-skrivare för Azure Kubernetes service | Tillåter Läs-/skriv åtkomst till de flesta objekt i ett namn område. Den här rollen tillåter inte visning eller ändring av roller eller roll bindningar. Den här rollen tillåter dock åtkomst `Secrets` och körning av poddar som alla ServiceAccount i namn området, så att den kan användas för att få åtkomst nivåer för API: er för alla ServiceAccount i namn området. |
| RBAC-administratör för Azure Kubernetes-tjänsten  | Tillåter administratörs åtkomst, som är avsedd att beviljas inom ett namn område. Tillåter Läs-/skriv åtkomst till de flesta resurser i ett namn område (eller ett kluster omfång), inklusive möjligheten att skapa roller och roll bindningar i namn området. Den här rollen tillåter inte skriv åtkomst till resurs kvot eller själva namn området. |
| Azure Kubernetes service RBAC-kluster administratör  | Ger åtkomst till superanvändare för att utföra alla åtgärder på en resurs. Den ger fullständig kontroll över alla resurser i klustret och i alla namn områden. |


Roll tilldelningar som är begränsade till **hela AKS-klustret** kan göras antingen på Access Control (IAM)-bladet i kluster resursen på Azure Portal eller med hjälp av Azure CLI-kommandon som visas nedan:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

var `<AAD-ENTITY-ID>` kan vara ett användar namn (till exempel user@contoso.com ) eller till och med ClientID för ett huvud namn för tjänsten.

Du kan också skapa roll tilldelningar som är begränsade till en angiven **namnrymd** i klustret:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

I dag måste roll tilldelningar som är begränsade till namn områden konfigureras via Azure CLI.


### <a name="create-custom-roles-definitions"></a>Skapa anpassade roll definitioner

Du kan också välja att skapa en egen roll definition och sedan tilldela som ovan.

Nedan visas ett exempel på en roll definition som gör att en användare bara kan läsa distributioner och inget annat. Du kan kontrol lera den fullständiga listan över möjliga åtgärder [här](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


Kopiera nedanstående JSON-fil till en fil med namnet `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

Ersätt `<YOUR SUBSCRIPTION ID>` med ID från din prenumeration, som du kan hämta genom att köra:

```azurecli-interactive
az account show --query id -o tsv
```


Nu kan vi skapa roll definitionen genom att köra kommandot nedan från mappen där du sparade `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Nu när du har roll definitionen kan du tilldela den till en användare eller annan identitet genom att köra:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Använd Azure RBAC för Kubernetes-auktorisering med `kubectl`

> [!NOTE]
> Se till att du har de senaste kubectl genom att köra kommandot nedan:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Du kan behöva köra den med `sudo` privilegier. 

Nu när du har tilldelat önskad roll och behörigheter. Du kan börja anropa Kubernetes-API: t, till exempel från `kubectl` .

För det här ändamålet ska vi först hämta klustrets kubeconfig med kommandot nedan:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Du behöver den inbyggda rollen [Azure Kubernetes service Cluster-användare](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) för att kunna utföra steget ovan.

Nu kan du använda kubectl till, till exempel en lista över noderna i klustret. Första gången du kör det måste du logga in och efterföljande kommandon använder respektive åtkomsttoken.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Använd Azure RBAC för Kubernetes-auktorisering med `kubelogin`

Om du vill häva blockeringen av ytterligare scenarier som icke-interaktiva inloggningar, äldre `kubectl` versioner eller använda SSO i flera kluster utan att behöva logga in på ett nytt kluster, beviljas att din token fortfarande är giltig, AKS skapade ett exec-plugin-program som heter [`kubelogin`](https://github.com/Azure/kubelogin) .

Du kan använda den genom att köra:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Första gången måste du logga in interaktivt som med vanlig kubectl, men därefter kommer du inte längre att behöva, även för nya Azure AD-kluster (så länge din token fortfarande är giltig).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Rensa

### <a name="clean-role-assignment"></a>Rensa roll tilldelning

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Kopiera ID: t eller ID: na från alla tilldelningar du gjorde och sedan.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Rensa roll definition

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Ta bort kluster och resurs grupp

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om AKS-autentisering, auktorisering, Kubernetes RBAC och Azure RBAC [här](concepts-identity.md).
- Läs mer om Azure RBAC [här](../role-based-access-control/overview.md).
- Läs mer om alla åtgärder som du kan använda för att definiera anpassade Azure-roller för Kubernetes [-auktorisering i](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)detalj.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
