---
title: Hantera Azure RBAC i Kubernetes från Azure
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure RBAC för Kubernetes-auktorisering med Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783007"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Använda Azure RBAC för Kubernetes-auktorisering (förhandsversion)

I dag kan du redan [använda integrerad autentisering mellan Azure Active Directory (Azure AD) och AKS](managed-aad.md). När den här integreringen är aktiverad kan kunder använda Azure AD-användare, grupper eller tjänsthuvudnamn som ämnen i Kubernetes RBAC. Mer information [finns här.](azure-ad-rbac.md)
Med den här funktionen behöver du inte hantera användaridentiteter och autentiseringsuppgifter separat för Kubernetes. Du måste dock fortfarande konfigurera och hantera Azure RBAC och Kubernetes RBAC separat. Mer information om autentisering och auktorisering med RBAC på AKS finns [här.](concepts-identity.md)

Det här dokumentet beskriver en ny metod som möjliggör enhetlig hantering och åtkomstkontroll över Azure-resurser, AKS- och Kubernetes-resurser.

## <a name="before-you-begin"></a>Innan du börjar

Möjligheten att hantera RBAC för Kubernetes-resurser från Azure ger dig möjlighet att hantera RBAC för klusterresurserna med antingen Azure eller interna Kubernetes-mekanismer. När det här alternativet är aktiverat verifieras Azure AD-huvudkonton exklusivt av Azure RBAC medan vanliga Kubernetes-användare och tjänstkonton exklusivt verifieras av Kubernetes RBAC. Mer information om autentisering och auktorisering med RBAC på AKS finns [här.](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Förutsättningar 
- Kontrollera att du har Azure CLI version 2.9.0 eller senare
- Kontrollera att `EnableAzureRBACPreview` funktionsflaggan är aktiverad.
- Kontrollera att `aks-preview` [CLI-tillägget][az-extension-add] v0.4.55 eller senare är installerat
- Kontrollera att du har [installerat kubectl v1.18.3+][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registrera `EnableAzureRBACPreview` förhandsgranskningsfunktionen

Om du vill skapa ett AKS-kluster som använder Azure RBAC för Kubernetes-auktorisering måste du aktivera `EnableAzureRBACPreview` funktionsflaggan för din prenumeration.

Registrera `EnableAzureRBACPreview` funktionsflaggan med [kommandot az feature register][az-feature-register] enligt följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Du kan kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill skapa ett AKS-kluster som använder Azure RBAC behöver du *CLI-tillägget aks-preview* version 0.4.55 eller senare. Installera *Azure CLI-tillägget aks-preview* med [kommandot az extension add][az-extension-add] eller installera eventuella tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

- Kräver [hanterad Azure AD-integrering](managed-aad.md).
- Du kan inte integrera Azure RBAC för Kubernetes-auktorisering i befintliga kluster under förhandsversionen, men du kommer att kunna med allmän tillgänglighet (GA).
- Använd [kubectl v1.18.3+][az-aks-install-cli].
- Om du har CRD:er och skapar anpassade rolldefinitioner är det enda sättet att täcka CRD:er i dag att tillhandahålla `Microsoft.ContainerService/managedClusters/*/read` . AKS arbetar med att tillhandahålla mer detaljerade behörigheter för CRD:er. För återstående objekt kan du använda de specifika API-grupperna, till exempel: `Microsoft.ContainerService/apps/deployments/read` .
- Det kan ta upp till 5 minuter innan nya rolltilldelningar sprids och uppdateras av auktoriseringsservern.
- Kräver att Azure AD-klienten som har konfigurerats för autentisering är samma som klientorganisationen för den prenumeration som innehåller AKS-klustret. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Skapa ett nytt kluster med Azure RBAC och hanterad Azure AD-integrering

Skapa ett AKS-kluster med hjälp av följande CLI-kommandon.

Skapa en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa AKS-klustret med hanterad Azure AD-integrering och Azure RBAC för Kubernetes-auktorisering.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Ett lyckat skapande av ett kluster med Azure AD-integrering och Azure RBAC för Kubernetes-auktorisering innehåller följande avsnitt i svarstexten:

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

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Skapa rolltilldelningar för användare att komma åt kluster

AKS har följande fyra inbyggda roller:


| Roll                                | Beskrivning  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-läsare  | Tillåter skrivskyddade åtkomst för att se de flesta objekt i ett namnområde. Det tillåter inte visning av roller eller rollbindningar. Den här rollen tillåter inte visning eftersom läsning av innehållet i hemligheter ger åtkomst till ServiceAccount-autentiseringsuppgifter i namnområdet, vilket skulle tillåta API-åtkomst som alla ServiceAccount i namnområdet (en form av `Secrets` behörighetseskalering)  |
| Azure Kubernetes Service RBAC Writer | Tillåter läs-/skrivåtkomst till de flesta objekt i ett namnområde. Den här rollen tillåter inte visning eller ändring av roller eller rollbindningar. Den här rollen tillåter dock åtkomst till och kör poddar som alla ServiceAccount i namnområdet, så den kan användas för att få API-åtkomstnivåer för alla `Secrets` ServiceAccount i namnområdet. |
| Azure Kubernetes Service RBAC-administratör  | Tillåter administratörsåtkomst som är avsedd att beviljas inom ett namnområde. Tillåter läs-/skrivåtkomst till de flesta resurser i ett namnområde (eller klusteromfång), inklusive möjligheten att skapa roller och rollbindningar i namnområdet. Den här rollen tillåter inte skrivåtkomst till resurskvoten eller till själva namnområdet. |
| Azure Kubernetes Service RBAC-klusteradministratör  | Tillåter superanvändaråtkomst att utföra åtgärder på alla resurser. Den ger fullständig kontroll över alla resurser i klustret och i alla namnområden. |


Rolltilldelningar som är begränsade till hela **AKS-klustret** kan göras antingen på bladet Access Control (IAM) för klusterresursen på Azure Portal eller med hjälp av Azure CLI-kommandon enligt nedan:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

där `<AAD-ENTITY-ID>` kan vara ett användarnamn (till exempel ) eller till och med user@contoso.com ClientID för tjänstens huvudnamn.

Du kan också skapa rolltilldelningar som är begränsade till ett specifikt **namnområde** i klustret:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Idag måste rolltilldelningar som är begränsade till namnrymder konfigureras via Azure CLI.


### <a name="create-custom-roles-definitions"></a>Skapa anpassade rolldefinitioner

Om du vill kan du välja att skapa en egen rolldefinition och sedan tilldela enligt ovan.

Nedan visas ett exempel på en rolldefinition som gör att en användare endast kan läsa distributioner och inget annat. Du kan kontrollera den fullständiga listan över möjliga åtgärder [här.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


Kopiera nedanstående json till en fil med namnet `deploy-view.json` .

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

Ersätt `<YOUR SUBSCRIPTION ID>` med ID:t från din prenumeration, som du kan få genom att köra:

```azurecli-interactive
az account show --query id -o tsv
```


Nu kan vi skapa rolldefinitionen genom att köra kommandot nedan från mappen där du sparade `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Nu när du har din rolldefinition kan du tilldela den till en användare eller annan identitet genom att köra:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Använda Azure RBAC för Kubernetes-auktorisering med `kubectl`

> [!NOTE]
> Kontrollera att du har den senaste kubectl-versionen genom att köra kommandot nedan:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Du kan behöva köra den `sudo` med behörighet. 

Nu när du har tilldelat önskad roll och behörighet. Du kan börja anropa Kubernetes-API:et, till exempel från `kubectl` .

För det här ändamålet hämtar vi först klustrets kubeconfig med hjälp av kommandot nedan:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Du behöver den [inbyggda Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) klusteranvändaren för att utföra steget ovan.

Nu kan du använda kubectl för att till exempel visa en lista över noderna i klustret. Första gången du kör den måste du logga in, och efterföljande kommandon använder respektive åtkomsttoken.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Använda Azure RBAC för Kubernetes-auktorisering med `kubelogin`

För att avblockera ytterligare scenarier som icke-interaktiva inloggningar, äldre versioner eller användning av enkel inloggning över flera kluster utan att behöva logga in på ett nytt kluster, och beviljat att din token fortfarande är giltig, skapade AKS ett `kubectl` exec-plugin-program med namnet [`kubelogin`](https://github.com/Azure/kubelogin) .

Du kan använda den genom att köra:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Första gången måste du logga in interaktivt som med vanlig kubectl, men efteråt behöver du inte längre göra det, inte ens för nya Azure AD-kluster (så länge din token fortfarande är giltig).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Rensa

### <a name="clean-role-assignment"></a>Tilldelning av ren roll

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Kopiera ID:t eller ID:t från alla tilldelningar som du har gjort och sedan.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Rensa rolldefinition

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Ta bort kluster och resursgrupp

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om AKS-autentisering, auktorisering, Kubernetes RBAC och Azure RBAC [här.](concepts-identity.md)
- Läs mer om Azure RBAC [här.](../role-based-access-control/overview.md)
- Läs mer om alla åtgärder som du kan använda för att detaljerad definiera anpassade Azure-roller för Kubernetes-auktorisering [här.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
