---
title: Använda hanterade identiteter i Azure Kubernetes-tjänsten
description: Lär dig hur du använder hanterade identiteter i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 3ace7f1c93ab3918f460d245a863db43d98f1db5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176101"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Använda hanterade identiteter i Azure Kubernetes-tjänsten

För närvarande kräver ett Azure Kubernetes service (AKS)-kluster (specifikt Kubernetes Cloud Provider) en identitet för att skapa ytterligare resurser som belastningsutjämnare och hanterade diskar i Azure. Den här identiteten kan vara antingen en *hanterad identitet* eller ett *huvud namn för tjänsten*. Om du använder ett [huvud namn för tjänsten](kubernetes-service-principal.md)måste du antingen ange ett eller AKS skapar ett för din räkning. Om du använder hanterad identitet skapas detta automatiskt åt dig av AKS. Kluster som använder tjänstens huvud namn når till sist ett tillstånd där tjänstens huvud namn måste förnyas för att hålla klustret igång. Hantering av tjänstens huvud namn ökar komplexiteten, vilket innebär att det är lättare att använda hanterade identiteter i stället. Samma behörighets krav gäller för både tjänstens huvud namn och hanterade identiteter.

*Hanterade identiteter* är i grunden en omslutning av tjänstens huvud namn och gör hanteringen enklare. Rotation av autentiseringsuppgifter för MI sker automatiskt var 46: e dag enligt Azure Active Directory standard. AKS använder både systemtilldelade och användarspecifika hanterade identitets typer. Dessa identiteter är för närvarande oföränderliga. Läs mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha följande resurs installerad:

- Azure CLI, version 2.15.1 eller senare

## <a name="limitations"></a>Begränsningar

* Klienterna flyttar/migrerar för hanterade identitets aktiverade kluster stöds inte.
* Om klustret har `aad-pod-identity` Aktiver ATS ändrar Node-Managed identitet (NMI) poddar program varan iptables för att avlyssna anrop till Azure instance metadata-slutpunkten. Den här konfigurationen innebär att alla begär Anden som görs till metadata-slutpunkten fångas upp av NMI även om Pod inte använder `aad-pod-identity` . AzurePodIdentityException CRD kan konfigureras för att informera om `aad-pod-identity` att förfrågningar till slut punkten för metadata från en pod som matchar etiketter som definierats i CRD ska vara proxy utan bearbetning i NMI. Systemets poddar med `kubernetes.azure.com/managedby: aks` etikett i _Kube-systemets_ namnrymd ska undantas i `aad-pod-identity` genom att konfigurera AzurePodIdentityException-CRD. Mer information finns i [inaktivera AAD-Pod-Identity för en specifik POD eller ett program](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Om du vill konfigurera ett undantag installerar du [yaml MIC-Exception](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Sammanfattning av hanterade identiteter

AKS använder flera hanterade identiteter för inbyggda tjänster och tillägg.

| Identitet                       | Name    | Användningsfall | Standard behörigheter | Ta med din egen identitet
|----------------------------|-----------|----------|
| Kontrollplan | inte synlig | Används av AKS Control plan-komponenter för att hantera kluster resurser, inklusive belastnings utjämning och AKS-hanterade offentliga IP-adresser och åtgärder för automatisk skalnings åtgärd i klustret | Deltagar roll för nod resurs grupp | stöds
| Kubelet | AKS-kluster namn – agentpoolegenskap | Autentisering med Azure Container Registry (ACR) | NA (för Kubernetes v 1.15 +) | Stöds för närvarande inte
| Tillägg | AzureNPM | Ingen identitet krävs | NA | Inga
| Tillägg | AzureCNI nätverks övervakning | Ingen identitet krävs | NA | Inga
| Tillägg | Azure-policy (Gatekeeper) | Ingen identitet krävs | NA | Inga
| Tillägg | Azure-princip | Ingen identitet krävs | NA | Inga
| Tillägg | Calico | Ingen identitet krävs | NA | Inga
| Tillägg | Instrumentpanel | Ingen identitet krävs | NA | Inga
| Tillägg | HTTPApplicationRouting | Hanterar nödvändiga nätverks resurser | Läsar roll för nod resurs grupp, deltagar roll för DNS-zon | Inga
| Tillägg | Ingress Application Gateway | Hanterar nödvändiga nätverks resurser| Deltagar roll för nod resurs grupp | Inga
| Tillägg | omsagent | Används för att skicka AKS-mått till Azure Monitor | Övervaknings mått utgivar rollen | Inga
| Tillägg | Virtual-Node (ACIConnector) | Hanterar nödvändiga nätverks resurser för Azure Container Instances (ACI) | Deltagar roll för nod resurs grupp | Inga
| OSS-projekt | AAD-Pod – identitet | Gör det möjligt för program att komma åt moln resurser på ett säkert sätt med Azure Active Directory (AAD) | NA | Steg för att bevilja behörighet på https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Skapa ett AKS-kluster med hanterade identiteter

Nu kan du skapa ett AKS-kluster med hanterade identiteter med hjälp av följande CLI-kommandon.

Börja med att skapa en Azure-resurs grupp:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Skapa sedan ett AKS-kluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Ett lyckat kluster skapas med hanterade identiteter som innehåller den här tjänstens huvud princips profil information:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Använd följande kommando för att fråga ObjectID för din hanterade identitet för kontroll planet:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Resultatet bör se ut så här:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

När klustret har skapats kan du distribuera dina program arbets belastningar till det nya klustret och interagera med det precis som du har gjort med service-huvudbaserade AKS-kluster.

> [!NOTE]
> För att skapa och använda ditt eget VNet, en statisk IP-adress eller en ansluten Azure-disk där resurserna ligger utanför resurs gruppen för arbetsnoder, använder du PrincipalID för den tilldelade hanterade identiteten i kluster systemet för att utföra en roll tilldelning. Mer information om roll tilldelning finns i [Delegera åtkomst till andra Azure-resurser](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Behörighets bidrag till kluster hanterad identitet som används av Azure Cloud Provider kan ta upp till 60 minuter att fylla i.

Slutligen kan du hämta autentiseringsuppgifter för att få åtkomst till klustret:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Uppdatera ett AKS-kluster till hanterade identiteter (för hands version)

Nu kan du uppdatera ett AKS-kluster som för närvarande arbetar med tjänstens huvud namn för att arbeta med hanterade identiteter med hjälp av följande CLI-kommandon.

Registrera först funktions flaggan för systemtilldelad identitet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Uppdatera den systemtilldelade identiteten:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registrera funktions flaggan för användarens tilldelade identitet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Uppdatera den användare som tilldelats identiteten:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> När systemtilldelade eller användarspecifika identiteter har uppdaterats till hanterad identitet, utför du en `az aks nodepool upgrade --node-image-only` på noderna för att slutföra uppdateringen av den hanterade identiteten.

## <a name="bring-your-own-control-plane-mi"></a>Ta med ditt eget kontroll plan MI
En anpassad kontroll plan identitet ger åtkomst till den befintliga identiteten innan klustret skapas. Den här funktionen möjliggör scenarier som att använda en anpassad VNET eller outboundType av UDR med en förgenererad hanterad identitet.

Du måste ha Azure CLI, version 2.15.1 eller senare installerat.

### <a name="limitations"></a>Begränsningar
* Azure Government stöds inte för närvarande.
* Azure Kina 21Vianet stöds inte för närvarande.

Om du inte har en hanterad identitet ännu bör du gå vidare och skapa en till exempel genom att använda [AZ Identity CLI][az-identity-create].

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

Om din hanterade identitet är en del av din prenumeration kan du använda [AZ Identity CLI-kommandot][az-identity-list] för att fråga den.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Nu kan du använda följande kommando för att skapa ditt kluster med din befintliga identitet:

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

Ett lyckat kluster skapas med dina egna hanterade identiteter som innehåller den här userAssignedIdentities profil informationen:

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

## <a name="next-steps"></a>Nästa steg
* Använd [Azure Resource Manager arm-mallar ][aks-arm-template] för att skapa hanterade identitets aktiverade kluster.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
