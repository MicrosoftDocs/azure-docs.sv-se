---
title: Begränsa åtkomsten till kubeconfig i Azure Kubernetes Service (AKS)
description: Lär dig hur du styr åtkomsten till Kubernetes-konfigurationsfilen (kubeconfig) för klusteradministratörer och klusteranvändare
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 279ca9800d7d721cc2e77d269cb577d8bd166d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765565"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Använd rollbaserad åtkomstkontroll i Azure för att definiera åtkomst till Kubernetes-konfigurationsfilen i Azure Kubernetes Service (AKS)

Du kan interagera med Kubernetes-kluster med hjälp av `kubectl` verktyget . Azure CLI tillhandahåller ett enkelt sätt att hämta autentiseringsuppgifter och konfigurationsinformation för åtkomst för att ansluta till dina AKS-kluster med hjälp av `kubectl` . Om du vill begränsa vem som kan hämta informationen om Kubernetes-konfiguration *(kubeconfig)* och begränsa de behörigheter som de sedan har kan du använda rollbaserad åtkomstkontroll i Azure (Azure RBAC).

Den här artikeln visar hur du tilldelar Azure-roller som begränsar vem som kan hämta konfigurationsinformation för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Den här artikeln kräver också att du kör Azure CLI version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Tillgängliga behörigheter för klusterroller

När du interagerar med ett AKS-kluster `kubectl` med verktyget används en konfigurationsfil som definierar klusteranslutningsinformation. Den här konfigurationsfilen lagras vanligtvis *i ~/.kube/config*. Flera kluster kan definieras i den här *kubeconfig-filen.* Du växlar mellan kluster med hjälp av [kommandot kubectl config use-context.][kubectl-config-use-context]

Med [kommandot az aks get-credentials][az-aks-get-credentials] kan du hämta autentiseringsuppgifterna för ett AKS-kluster och slå samman dem i *kubeconfig-filen.* Du kan använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att styra åtkomsten till dessa autentiseringsuppgifter. Med dessa Azure-roller kan du definiera vem som kan hämta *kubeconfig-filen* och vilka behörigheter de sedan har i klustret.

De två inbyggda rollerna är:

* **Azure Kubernetes Service administratörsroll för kluster**  
  * Tillåter åtkomst till *API-anropet Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* Det här [API-anropet visar en lista över autentiseringsuppgifterna för klusteradministratören.][api-cluster-admin]
  * Hämtar *kubeconfig för* *clusterAdmin-rollen.*
* **Azure Kubernetes Service klusteranvändarroll**
  * Tillåter åtkomst till *API-anropet Microsoft.ContainerService/managedClusters/listClusterUserCredential/action.* Det här [API-anropet visar en lista över autentiseringsuppgifterna för klusteranvändaren.][api-cluster-user]
  * Hämtar *kubeconfig för* *clusterUser-rollen.*

Dessa Azure-roller kan tillämpas på en Azure Active Directory (AD)-användare eller grupp.

> [!NOTE]
> I kluster som använder Azure AD har användare med *rollen clusterUser* en tom *kubeconfig-fil* som uppmanar till en inloggning. När de har loggat in har användarna åtkomst baserat på deras Azure AD-användar- eller gruppinställningar. Användare med *rollen clusterAdmin* har administratörsåtkomst.
>
> Kluster som inte använder Azure AD använder bara *rollen clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Tilldela rollbehörigheter till en användare eller grupp

Om du vill tilldela en av de tillgängliga rollerna måste du hämta resurs-ID:t för AKS-klustret och ID:t för Azure AD-användarkontot eller Azure AD-gruppen. Följande exempelkommandon:

* Hämta klusterresurs-ID:t med kommandot [az aks show][az-aks-show] för klustret med namnet *myAKSCluster* i *resursgruppen myResourceGroup.* Ange ditt eget kluster- och resursgruppsnamn efter behov.
* Använd [kommandona az account show][az-account-show] och [az ad user show][az-ad-user-show] för att hämta ditt användar-ID.
* Slutligen tilldelar du en roll med [kommandot az role assignment][az-role-assignment-create] create.

I följande exempel tilldelas *Azure Kubernetes Service klusteradministratörsrollen* till ett enskilt användarkonto:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> I vissa fall är *user.name* i kontot annorlunda än *userPrincipalName,* till exempel med Azure AD-gästanvändare:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> I det här fallet anger du värdet *ACCOUNT_UPN* till *userPrincipalName* från Azure AD-användaren. Om ditt konto till exempel *user.name* *är användaren \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Om du vill tilldela behörigheter till en Azure AD-grupp uppdaterar du parametern som visas i föregående exempel med objekt-ID för gruppen `--assignee` i stället för en *användare*.  Om du vill hämta objekt-ID:t för en grupp använder [du kommandot az ad group show.][az-ad-group-show] I följande exempel hämtar objekt-ID:t för Azure AD-gruppen med *namnet appdev*: `az ad group show --group appdev --query objectId -o tsv`

Du kan ändra den tidigare tilldelningen till *klusteranvändarrollen* efter behov.

Följande exempelutdata visar att rolltilldelningen har skapats:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Hämta och verifiera konfigurationsinformationen

När Azure-roller har tilldelats använder du [kommandot az aks get-credentials][az-aks-get-credentials] för att hämta *kubeconfig-definitionen* för ditt AKS-kluster. I följande exempel hämtar *autentiseringsuppgifterna för --admin,* vilket fungerar korrekt om användaren har beviljats *klusteradministratörsrollen*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Du kan sedan använda [kommandot kubectl config view][kubectl-config-view] för att verifiera att kontexten för klustret visar att informationen om administratörskonfigurationen har tillämpats: 

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Ta bort rollbehörigheter

Om du vill ta bort rolltilldelningar använder du [kommandot az role assignment delete.][az-role-assignment-delete] Ange konto-ID:t och klusterresurs-ID:t, som du fick i föregående kommandon. Om du har tilldelat rollen till en grupp i stället för en användare anger du lämpligt gruppobjekt-ID i stället för kontoobjekt-ID för `--assignee` parametern:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Nästa steg

För förbättrad säkerhet vid åtkomst till AKS-kluster, integrera [Azure Active Directory autentisering][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-role-assignment-delete]: /cli/azure/role/assignment#az_role_assignment_delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
