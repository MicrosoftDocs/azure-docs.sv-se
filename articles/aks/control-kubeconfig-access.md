---
title: Begränsa åtkomsten till kubeconfig i Azure Kubernetes service (AKS)
description: Lär dig hur du styr åtkomsten till konfigurations filen för Kubernetes (kubeconfig) för kluster administratörer och kluster användare
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693986"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Använd rollbaserad åtkomst kontroll i Azure för att definiera åtkomst till konfigurations filen Kubernetes i Azure Kubernetes service (AKS)

Du kan interagera med Kubernetes-kluster med `kubectl` verktyget. Azure CLI är ett enkelt sätt att få åtkomst till autentiseringsuppgifter och konfigurations information för att ansluta till dina AKS-kluster med hjälp av `kubectl` . Om du vill begränsa vem som kan hämta den Kubernetes konfigurations informationen (*kubeconfig*) och begränsa behörigheterna som de har, kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC).

Den här artikeln visar hur du tilldelar Azure-roller som begränsar vem som kan hämta konfigurations information för ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Den här artikeln kräver också att du kör Azure CLI-version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Tillgängliga kluster roll behörigheter

När du interagerar med ett AKS-kluster med `kubectl` verktyget används en konfigurations fil som definierar kluster anslutnings information. Den här konfigurations filen lagras vanligt vis i *~/.Kube/config*. Flera kluster kan definieras i den här *kubeconfig* -filen. Du växlar mellan kluster med kommandot [kubectl config use-context][kubectl-config-use-context] .

Med kommandot [AZ AKS get-credentials][az-aks-get-credentials] kan du hämta autentiseringsuppgifter för ett AKS-kluster och slå samman dem till *kubeconfig* -filen. Du kan använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till dessa autentiseringsuppgifter. Med de här Azure-rollerna kan du definiera vem som kan hämta *kubeconfig* -filen och vilka behörigheter de har i klustret.

De två inbyggda rollerna är:

* **Administratörs roll för Azure Kubernetes service Cluster**  
  * Tillåter åtkomst till API-anrop för *Microsoft. container service/managedClusters/listClusterAdminCredential/Action* . Detta API-anrop [visar autentiseringsuppgifter för kluster administratören][api-cluster-admin].
  * Hämtar *kubeconfig* för *clusterAdmin* -rollen.
* **Användar roll för Azure Kubernetes service-kluster**
  * Tillåter åtkomst till API-anrop för *Microsoft. container service/managedClusters/listClusterUserCredential/Action* . Det här API-anropet [visar autentiseringsuppgifter för klustrets användare][api-cluster-user].
  * Hämtar *kubeconfig* för *clusterUser* -rollen.

Dessa Azure-roller kan tillämpas på en Azure Active Directory (AD) användare eller grupp.

> [!NOTE]
> I kluster som använder Azure AD har användare med rollen *clusterUser* en tom *kubeconfig* -fil som efterfrågar en inloggning. När användaren har loggat in har användarna åtkomst baserat på deras inställningar för Azure AD-användare eller grupper. Användare med rollen *clusterAdmin* har administratörs åtkomst.
>
> Kluster som inte använder Azure AD använder bara *clusterAdmin* -rollen.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Tilldela roll behörigheter till en användare eller grupp

Om du vill tilldela en av de tillgängliga rollerna måste du hämta resurs-ID för AKS-klustret och ID: t för Azure AD-användarkontot eller-gruppen. Följande exempel kommandon:

* Hämta kluster resurs-ID: t med kommandot [AZ AKS show][az-aks-show] för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* . Ange ditt eget kluster och resurs grupp namn efter behov.
* Använd [AZ-kontot show][az-account-show] och [AZ AD User show][az-ad-user-show] commands för att hämta ditt användar-ID.
* Ange slutligen en roll med hjälp av kommandot [AZ roll tilldelning skapa][az-role-assignment-create] .

I följande exempel tilldelas *rollen Azure Kubernetes service Cluster admin* till ett enskilt användar konto:

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
> I vissa fall skiljer sig *User.name* i kontot från *userPrincipalName*, till exempel med användare av Azure AD-gäst:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> I det här fallet ställer du in värdet för *ACCOUNT_UPN* till *USERPRINCIPALNAME* från Azure AD-användaren. Till exempel om ditt konto *User.name* är *User \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Om du vill tilldela behörigheter till en Azure AD-grupp uppdaterar du den `--assignee` parameter som visas i föregående exempel med objekt-ID för *gruppen* i stället för en *användare*. Om du vill hämta objekt-ID för en grupp använder du kommandot [AZ AD Group show][az-ad-group-show] . I följande exempel hämtas objekt-ID: t för Azure AD-gruppen med namnet *AppDev*: `az ad group show --group appdev --query objectId -o tsv`

Du kan ändra den tidigare tilldelningen till *kluster användar rollen* vid behov.

Följande exempel på utdata visar att roll tilldelningen har skapats:

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

## <a name="get-and-verify-the-configuration-information"></a>Hämta och verifiera konfigurations informationen

Med tilldelade Azure-roller använder du kommandot [AZ AKS get-credentials][az-aks-get-credentials] för att hämta *kubeconfig* -definitionen för ditt AKS-kluster. I följande exempel hämtas autentiseringsuppgifter för *-admin* , som fungerar korrekt om användaren har beviljats rollen som *kluster administratör*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Du kan sedan använda kommandot [kubectl config View][kubectl-config-view] för att kontrol lera att *kontexten* för klustret visar att administratörs konfigurations informationen har tillämpats:

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

## <a name="remove-role-permissions"></a>Ta bort roll behörigheter

Om du vill ta bort roll tilldelningar använder du kommandot [AZ roll tilldelning ta bort][az-role-assignment-delete] . Ange konto-ID och kluster resurs-ID som hämtades i föregående kommandon. Om du har tilldelat rollen till en grupp i stället för en användare, anger du lämpligt grupp objekt-ID i stället för kontots objekt-ID för `--assignee` parametern:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Nästa steg

För ökad säkerhet vid åtkomst till AKS-kluster, [integrera Azure Active Directory autentisering][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
