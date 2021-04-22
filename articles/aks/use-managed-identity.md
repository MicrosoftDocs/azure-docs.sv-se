---
title: Använda hanterade identiteter i Azure Kubernetes Service
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 7eb0ab6247e8afc27f938b8b4a25d1fb1ee723f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877002"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes Service

För närvarande kräver ett Azure Kubernetes Service-kluster (AKS) (specifikt Kubernetes-molnleverantören) en identitet för att skapa ytterligare resurser som lastbalanserare och hanterade diskar i Azure. Den här identiteten kan vara antingen *en hanterad identitet* eller ett *huvudnamn för tjänsten.* Om du använder ett [huvudnamn för](kubernetes-service-principal.md)tjänsten måste du antingen ange ett eller så skapar AKS ett åt dig. Om du använder en hanterad identitet skapas den automatiskt åt dig av AKS. Kluster som använder tjänstens huvudnamn når så småningom ett tillstånd där tjänstens huvudnamn måste förnyas för att klustret ska fungera. Hanteringen av tjänstens huvudnamn ökar komplexiteten, vilket är anledningen till att det är enklare att använda hanterade identiteter i stället. Samma behörighetskrav gäller för både tjänstens huvudnamn och hanterade identiteter.

*Hanterade identiteter* är i grunden en omsvept kring tjänstens huvudnamn och gör hanteringen enklare. Rotation av autentiseringsuppgifter för MI sker automatiskt var 46:e dag enligt Azure Active Directory standard. AKS använder både system-tilldelade och användar tilldelade hanterade identitetstyper. Dessa identiteter är för närvarande oföränderliga. Mer information finns i Hanterade [identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.15.1 eller senare

## <a name="limitations"></a>Begränsningar

* Klientorganisationsflyttning/migrering av kluster med hanterad identitet stöds inte.
* Om klustret har `aad-pod-identity` aktiverats ändrar Node-Managed Identity (NMI)-poddar nodernas iptables för att fånga upp anrop till azure-instansens metadataslutpunkt. Den här konfigurationen innebär att alla förfrågningar som görs till metadataslutpunkten fångas upp av NMI även om podden inte använder `aad-pod-identity` . AzurePodIdentityException CRD kan konfigureras för att informera om att alla begäranden till metadataslutpunkten som kommer från en podd som matchar etiketter som definierats i CRD ska proxyas utan någon bearbetning i `aad-pod-identity` NMI. Systempoddar med etiketten `kubernetes.azure.com/managedby: aks` i _kube-system-namnområdet_ bör undantas i genom att `aad-pod-identity` konfigurera CRD för AzurePodIdentityException. Mer information finns i Inaktivera [aad-pod-identity för en specifik podd eller ett visst program.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Om du vill konfigurera ett undantag installerar du [YAML-undantagsfelet mic.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Sammanfattning av hanterade identiteter

AKS använder flera hanterade identiteter för inbyggda tjänster och tillägg.

| Identitet                       | Name    | Användningsfall | Standardbehörigheter | Ta med din egen identitet
|----------------------------|-----------|----------|
| Kontrollplan | visas inte | Används av AKS-kontrollplanskomponenter för att hantera klusterresurser, inklusive ingående lastbalanserare och AKS-hanterade offentliga IP-adresser och autoskalningsåtgärder för kluster | Deltagarroll för Node-resursgrupp | stöds
| Kubelet | AKS-klusternamn-agentpool | Autentisering med Azure Container Registry (ACR) | NA (för kubernetes v1.15+) | Stöds för närvarande inte
| Tillägg | AzureNPM | Ingen identitet krävs | NA | No
| Tillägg | AzureCNI-nätverksövervakning | Ingen identitet krävs | NA | No
| Tillägg | azure-policy (gatekeeper) | Ingen identitet krävs | NA | No
| Tillägg | azure-policy | Ingen identitet krävs | NA | No
| Tillägg | Kalikå | Ingen identitet krävs | NA | No
| Tillägg | Instrumentpanel | Ingen identitet krävs | NA | No
| Tillägg | HTTPApplicationRouting | Hanterar nödvändiga nätverksresurser | Läsarroll för nodresursgrupp, deltagarroll för DNS-zon | No
| Tillägg | Ingress-programgateway | Hanterar nödvändiga nätverksresurser| Deltagarroll för nodresursgrupp | No
| Tillägg | omsagent | Används för att skicka AKS-mått till Azure Monitor | Rollen Övervakningsmåttutgivare | No
| Tillägg | Virtual-Node (ACIConnector) | Hanterar nödvändiga nätverksresurser för Azure Container Instances (ACI) | Deltagarroll för nodresursgrupp | No
| OSS-projekt | aad-pod-identity | Gör att program kan komma åt molnresurser på ett säkert sätt med Azure Active Directory (AAD) | NA | Steg för att bevilja behörighet på https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Du kan nu skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Skapa först en Azure-resursgrupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

När klustret har skapats kan du sedan distribuera dina programarbetsbelastningar till det nya klustret och interagera med det precis som du har gjort med tjänsthuvudnamnsbaserade AKS-kluster.

Hämta slutligen autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Uppdatera ett AKS-kluster till hanterade identiteter (förhandsversion)

Du kan nu uppdatera ett AKS-kluster som för närvarande arbetar med tjänstens huvudnamn för att arbeta med hanterade identiteter med hjälp av följande CLI-kommandon.

Registrera först funktionsflaggan för system tilldelad identitet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Uppdatera den system tilldelade identiteten:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registrera funktionsflagga för användar tilldelad identitet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Uppdatera den användar tilldelade identiteten:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> När de system-tilldelade eller användar tilldelade identiteterna har uppdaterats till en hanterad identitet utför du en på noderna för `az aks nodepool upgrade --node-image-only` att slutföra uppdateringen av den hanterade identiteten.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>Hämta och använda den system tilldelade hanterade identiteten för ditt AKS-kluster

Bekräfta att ditt AKS-kluster använder en hanterad identitet med följande CLI-kommando:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Om klustret använder hanterade identiteter visas värdet `clientId` "msi". Ett kluster som använder tjänstens huvudnamn visar i stället objekt-ID:t. Exempel: 

```output
{
  "clientId": "msi"
}
```

När du har verifierat att klustret använder hanterade identiteter kan du hitta objekt-ID:t för den system tilldelade identiteten för kontrollplanet med följande kommando:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Om du vill skapa och använda ett eget VNet, en statisk IP-adress eller en ansluten Azure-disk där resurserna finns utanför resursgruppen för arbetsnoden använder du PrincipalID för klustrets system tilldelade hanterade identitet för att utföra en rolltilldelning. Mer information om rolltilldelning finns i [Delegera åtkomst till andra Azure-resurser.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Det kan ta upp 60 minuter att fylla i behörighet som beviljas till kluster hanterad identitet som används av Azure Cloud-providern.


## <a name="bring-your-own-control-plane-mi"></a>Ta med ditt eget kontrollplans MI
Med en anpassad kontrollplansidentitet kan åtkomst beviljas till den befintliga identiteten innan klustret skapas. Den här funktionen möjliggör scenarier som att använda ett anpassat VNET eller en utgående typ av UDR med en förskapad hanterad identitet.

Du måste ha Azure CLI version 2.15.1 eller senare installerat.

### <a name="limitations"></a>Begränsningar
* Azure Government stöds inte för närvarande.
* Azure China 21Vianet stöds inte för närvarande.

Om du inte har en hanterad identitet ännu bör du gå vidare och skapa en till exempel med hjälp av [az identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Resultatet bör se ut så här:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Om din hanterade identitet ingår i din prenumeration kan du använda [kommandot az identity CLI för][az-identity-list] att köra frågor mot den.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Nu kan du använda följande kommando för att skapa klustret med din befintliga identitet:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Ett lyckat klusterskapande med dina egna hanterade identiteter innehåller denna userAssignedIdentities-profilinformation:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi-preview"></a>Bring your own kubelet MI (förhandsversion)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

En Kubelet-identitet gör att åtkomst kan beviljas till den befintliga identiteten innan klustret skapas. Den här funktionen möjliggör scenarier som anslutning till ACR med en i förväg skapad hanterad identitet.

### <a name="prerequisites"></a>Förutsättningar

- Du måste ha Azure CLI version 2.21.1 eller senare installerat.
- Du måste ha aks-preview, version 0.5.10 eller senare installerad.

### <a name="limitations"></a>Begränsningar

- Fungerar bara med ett User-Assigned hanterat kluster.
- Azure Government stöds inte för närvarande.
- Azure China 21Vianet stöds inte för närvarande.

Registrera först funktionsflaggan för Kubelet-identitet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n CustomKubeletIdentityPreview
```

Det tar några minuter för statusen att visa *Registrerad*. Du kan kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomKubeletIdentityPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="create-or-obtain-managed-identities"></a>Skapa eller hämta hanterade identiteter

Om du inte har en hanterad identitet för kontrollplanet ännu bör du skapa en. I följande exempel används [kommandot az identity][az-identity-create] create:

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

Resultatet bör se ut så här:

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Om du inte har en kubelet-hanterad identitet ännu bör du skapa en. I följande exempel används [kommandot az identity][az-identity-create] create:

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

Resultatet bör se ut så här:

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Om din befintliga hanterade identitet ingår i din prenumeration kan du köra frågor mot den med kommandot [az identity list:][az-identity-list]

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Skapa ett kluster med kubelet-identitet

Nu kan du använda följande kommando för att skapa klustret med dina befintliga identiteter. Ange id:t för kontrollplanet via `assign-identity` och den kubelet-hanterade identiteten via `assign-kublet-identity` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id> \
```

Ett lyckat klusterskapande med din egen kubelet-hanterade identitet innehåller följande utdata:

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>Nästa steg
* Använd [Azure Resource Manager för att ][aks-arm-template] skapa kluster som är aktiverade för hanterad identitet.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
