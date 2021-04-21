---
title: Använda Azure AD och Kubernetes RBAC för kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure Active Directory gruppmedlemskap för att begränsa åtkomsten till klusterresurser med hjälp av kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769561"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrollera åtkomsten till klusterresurser med hjälp av rollbaserad åtkomstkontroll i Kubernetes och Azure Active Directory identiteter i Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan konfigureras att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen loggar du in på ett AKS-kluster med en Azure AD-autentiseringstoken. Du kan också konfigurera kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) för att begränsa åtkomsten till klusterresurser baserat på en användares identitet eller gruppmedlemskap.

Den här artikeln visar hur du använder Azure AD-gruppmedlemskap för att styra åtkomsten till namnrymder och klusterresurser med Kubernetes RBAC i ett AKS-kluster. Exempelgrupper och användare skapas i Azure AD. Sedan skapas roller och rollbindningar i AKS-klustret för att ge rätt behörigheter för att skapa och visa resurser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster aktiverat med Azure AD-integrering. Om du behöver ett AKS-kluster kan du [gå till Integrera Azure Active Directory med AKS][azure-ad-aks-cli].

Azure CLI version 2.0.61 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Skapa demogrupper i Azure AD

I den här artikeln ska vi skapa två användarroller som kan användas för att visa hur Kubernetes RBAC och Azure AD kontrollerar åtkomsten till klusterresurser. Följande två exempelroller används:

* **Programutvecklare**
    * En användare med *namnet aksdev* som ingår i *appdev-gruppen.*
* **Tekniker för webbplatstillförlitlighet**
    * En användare med *namnet akssre* som är en del av *opssre-gruppen.*

I produktionsmiljöer kan du använda befintliga användare och grupper i en Azure AD-klientorganisation.

Hämta först resurs-ID:t för ditt AKS-kluster med kommandot [az aks show.][az-aks-show] Tilldela resurs-ID:t till en *variabel AKS_ID* så att den kan refereras till i ytterligare kommandon.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Skapa den första exempelgruppen i Azure AD för programutvecklare med kommandot [az ad group create.][az-ad-group-create] I följande exempel skapas en grupp med namnet *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Skapa nu en Azure-rolltilldelning för *appdev-gruppen* med kommandot [az role assignment create.][az-role-assignment-create] Med den här tilldelningen kan alla medlemmar i gruppen interagera med ett AKS-kluster genom att `kubectl` *ge dem Azure Kubernetes Service-klusteranvändarrollen*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Om du får ett felmeddelande som väntar du några sekunder på att objekt-ID:t för Azure AD-gruppen ska spridas genom katalogen och `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` försöker sedan med kommandot `az role assignment create` igen.

Skapa en andra exempelgrupp, den här för SRE:er med *namnet opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Skapa återigen en Azure-rolltilldelning för att bevilja medlemmar i gruppen *Azure Kubernetes Service klusteranvändarrollen*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Skapa demoanvändare i Azure AD

Med två exempelgrupper som skapats i Azure AD för våra programutvecklare och SRE:er kan vi nu skapa två exempelanvändare. Om du vill testa Kubernetes RBAC-integreringen i slutet av artikeln loggar du in på AKS-klustret med dessa konton.

Ange UPN (User Principal Name) och lösenord för programutvecklaren. Följande kommando uppmanar dig att ange UPN och anger det till *AAD_DEV_UPN* för användning i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett BASH-gränssnitt). UPN måste innehålla det verifierade domännamnet för din klientorganisation, till exempel `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Följande kommando uppmanar dig att ange lösenordet och anger det till *AAD_DEV_PW* för användning i ett senare kommando.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Skapa det första användarkontot i Azure AD med kommandot [az ad user create.][az-ad-user-create]

I följande exempel skapas en användare med visningsnamnet *AKS Dev* och UPN och ett säkert lösenord med hjälp av värdena *i AAD_DEV_UPN* *och AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Lägg nu till användaren i *den appdev-grupp* som skapades i föregående avsnitt med kommandot [az ad group member add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Ange UPN och lösenord för SRE:er. Följande kommando uppmanar dig att ange UPN och anger det till *AAD_SRE_UPN* för användning i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett BASH-gränssnitt). UPN måste innehålla det verifierade domännamnet för din klientorganisation, till exempel `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Följande kommando uppmanar dig att ange lösenordet och anger det till *AAD_SRE_PW* för användning i ett senare kommando.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Skapa ett andra användarkonto. I följande exempel skapas en användare med visningsnamnet *AKS SRE* och UPN och ett säkert lösenord med hjälp av värdena *i AAD_SRE_UPN* *och AAD_SRE_PW*:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Skapa AKS-klusterresurser för apputveckling

Nu skapas Azure AD-grupper och -användare. Azure-rolltilldelningar skapades för att gruppmedlemmarna ska kunna ansluta till ett AKS-kluster som en vanlig användare. Nu ska vi konfigurera AKS-klustret så att de olika grupperna får åtkomst till specifika resurser.

Hämta först autentiseringsuppgifterna för klusteradministratören med [kommandot az aks get-credentials.][az-aks-get-credentials] I något av följande avsnitt får du de vanliga autentiseringsuppgifterna för *användarklustret* för att se hur Azure AD-autentiseringsflödet fungerar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa ett namnområde i AKS-klustret med kommandot [kubectl create namespace.][kubectl-create] I följande exempel skapas ett namnområdesnamn *för dev*:

```console
kubectl create namespace dev
```

I Kubernetes *definierar roller* behörigheterna att bevilja och *RoleBindings* tillämpar dem på önskade användare eller grupper. Dessa tilldelningar kan tillämpas på en viss namnrymd eller i hela klustret. Mer information finns i Använda [Kubernetes RBAC-auktorisering.][rbac-authorization]

Skapa först en roll för *namnområdet dev.* Den här rollen ger fullständiga behörigheter till namnområdet. I produktionsmiljöer kan du ange mer detaljerade behörigheter för olika användare eller grupper.

Skapa en fil med namnet `role-dev-namespace.yaml` och klistra in följande YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Skapa rollen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för ditt YAML-manifest:

```console
kubectl apply -f role-dev-namespace.yaml
```

Hämta sedan resurs-ID:t för *appdev-gruppen* med kommandot [az ad group show.][az-ad-group-show] Den här gruppen anges som ämne för en RoleBinding i nästa steg.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Skapa nu en RoleBinding för *appdev-gruppen* för att använda den tidigare skapade rollen för åtkomst till namnområdet. Skapa en fil med namnet `rolebinding-dev-namespace.yaml` och klistra in följande YAML-manifest. På den sista raden ersätter du *groupObjectId med*  gruppobjektets ID-utdata från föregående kommando:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Skapa RoleBinding med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för ditt YAML-manifest:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Skapa AKS-klusterresurser för SRE:er

Upprepa nu föregående steg för att skapa ett namnområde, en roll och en rollbindning för SRE:erna.

Skapa först ett namnområde för *sre med* kommandot [kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Skapa en fil med namnet `role-sre-namespace.yaml` och klistra in följande YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Skapa rollen med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för ditt YAML-manifest:

```console
kubectl apply -f role-sre-namespace.yaml
```

Hämta resurs-ID:t för *opssre-gruppen* med [kommandot az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Skapa en RoleBinding för *opssre-gruppen* för att använda den tidigare skapade rollen för åtkomst till namnområdet. Skapa en fil med namnet `rolebinding-sre-namespace.yaml` och klistra in följande YAML-manifest. På den sista raden ersätter du *groupObjectId med*  gruppobjektets ID-utdata från föregående kommando:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Skapa RoleBinding med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för ditt YAML-manifest:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagera med klusterresurser med hjälp av Azure AD-identiteter

Nu ska vi testa de förväntade behörigheterna när du skapar och hanterar resurser i ett AKS-kluster. I de här exemplen schemalägger och visar du poddar i användarens tilldelade namnområde. Sedan försöker du schemalägga och visa poddar utanför det tilldelade namnområdet.

Återställ först *kubeconfig-kontexten* med kommandot [az aks get-credentials.][az-aks-get-credentials] I ett tidigare avsnitt anger du kontexten med autentiseringsuppgifterna för klusteradministratören. Administratörsanvändaren kringgår uppmaningar om Azure AD-inloggning. Utan `--admin` parametern tillämpas användarkontexten som kräver att alla begäranden autentiseras med hjälp av Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Schemalägg en grundläggande NGINX-podd med [kommandot kubectl run][kubectl-run] i *dev-namnområdet:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Som inloggningsfråga anger du autentiseringsuppgifterna för ditt `appdev@contoso.com` eget konto som skapades i början av artikeln. När du har loggat in cachelagras kontotoken för framtida `kubectl` kommandon. NGINX schemaläggs korrekt, vilket visas i följande exempelutdata:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Använd nu kommandot [kubectl get pods][kubectl-get] för att visa poddar i *dev-namnområdet.*

```console
kubectl get pods --namespace dev
```

Som du ser i följande exempelutdata har NGINX-podden *körts:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Skapa och visa klusterresurser utanför det tilldelade namnområdet

Försök nu att visa poddar utanför *dev-namnområdet.* Använd [kommandot kubectl get pods][kubectl-get] igen, den här gången för att se `--all-namespaces` följande:

```console
kubectl get pods --all-namespaces
```

Användarens gruppmedlemskap har ingen Kubernetes-roll som tillåter den här åtgärden, som du ser i följande exempelutdata:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

På samma sätt kan du försöka schemalägga en podd i ett annat namnområde, till exempel *namnområdet sre.* Användarens gruppmedlemskap överensstämmer inte med en Kubernetes-roll och roleBinding för att bevilja dessa behörigheter, enligt följande exempelutdata:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testa SRE-åtkomsten till AKS-klusterresurserna

För att bekräfta att vårt Azure AD-gruppmedlemskap och Kubernetes RBAC fungerar korrekt mellan olika användare och grupper kan du prova de tidigare kommandona när du är inloggad *som opssre-användare.*

Återställ *kubeconfig-kontexten* med kommandot [az aks get-credentials][az-aks-get-credentials] som rensar den tidigare cachelagrade autentiseringstoken för *aksdev-användaren:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Försök att schemalägga och visa poddar i det tilldelade *sre-namnområdet.* När du uppmanas till det loggar du in med `opssre@contoso.com` dina egna autentiseringsuppgifter som skapades i början av artikeln:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Som du ser i följande exempelutdata kan du skapa och visa poddarna:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Försök nu att visa eller schemalägga poddar utanför det tilldelade SRE-namnområdet:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Dessa `kubectl` kommandon misslyckas, som du ser i följande exempelutdata. Användarens gruppmedlemskap och Kubernetes Role och RoleBindings beviljar inte behörighet att skapa eller manager-resurser i andra namnområden:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har du skapat resurser i AKS-klustret och användare och grupper i Azure AD. Kör följande kommandon för att rensa alla dessa resurser:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar Kubernetes-kluster finns i [Åtkomst- och identitetsalternativ för AKS).][rbac-authorization]

Metodtips för identitets- och resurskontroll finns i [Metodtips för autentisering och auktorisering i AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
