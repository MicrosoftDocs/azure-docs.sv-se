---
title: Använda Azure AD och Kubernetes RBAC för kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Azure Active Directory grupp medlemskap för att begränsa åtkomsten till kluster resurser med hjälp av rollbaserad åtkomst kontroll i Kubernetes (Kubernetes RBAC) i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: aa9a3340d6ff0a9818ba92b94b345bd86a78d426
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106028"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrol lera åtkomsten till kluster resurser med Kubernetes-rollbaserad åtkomst kontroll och Azure Active Directory identiteter i Azure Kubernetes-tjänsten

Azure Kubernetes service (AKS) kan konfigureras att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen loggar du in på ett AKS-kluster med en Azure AD-autentiseringstoken. Du kan också konfigurera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) om du vill begränsa åtkomsten till kluster resurser baserat på användarens identitet eller grupp medlemskap.

Den här artikeln visar hur du använder medlemskap i Azure AD-grupp för att kontrol lera åtkomst till namn områden och kluster resurser med Kubernetes RBAC i ett AKS-kluster. Exempel grupper och användare skapas i Azure AD, och sedan skapas roller och RoleBindings i AKS-klustret för att ge rätt behörigheter för att skapa och visa resurser.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster som är aktiverat med Azure AD-integrering. Om du behöver ett AKS-kluster kan du läsa [integrera Azure Active Directory med AKS][azure-ad-aks-cli].

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Skapa demonstrations grupper i Azure AD

I den här artikeln ska vi skapa två användar roller som kan användas för att visa hur Kubernetes RBAC och Azure AD styr åtkomst till kluster resurser. Följande två exempel roller används:

* **Programutvecklare**
    * En användare med namnet *aksdev* som är en del av *AppDev* -gruppen.
* **Platsens Tillförlitlighets tekniker**
    * En användare med namnet *akssre* som är en del av *opssre* -gruppen.

I produktions miljöer kan du använda befintliga användare och grupper i en Azure AD-klient.

Börja med att hämta resurs-ID för ditt AKS-kluster med kommandot [AZ AKS show][az-aks-show] . Tilldela resurs-ID till en variabel med namnet *AKS_ID* så att det kan refereras till i ytterligare kommandon.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Skapa den första exempel gruppen i Azure AD för program utvecklare med hjälp av kommandot [AZ AD Group Create][az-ad-group-create] . I följande exempel skapas en grupp med namnet *AppDev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Nu ska du skapa en Azure Role-tilldelning för gruppen *AppDev* med kommandot [AZ roll tilldelning skapa][az-role-assignment-create] . Den här tilldelningen gör att alla medlemmar i gruppen kan `kubectl` interagera med ett AKS-kluster genom att ge dem *användar rollen Azure Kubernetes service Cluster*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Om du får ett fel meddelande, till exempel `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , väntar du några sekunder på att Azure AD-gruppobjekt-ID: t sprids genom katalogen och kör sedan `az role assignment create` kommandot igen.

Skapa en andra exempel grupp, en för SREs med namnet *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Skapa en Azure-roll-tilldelning igen för att bevilja medlemmar i gruppen *Azure Kubernetes service-kluster användar rollen*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Skapa demo användare i Azure AD

Med två exempel grupper som skapats i Azure AD för våra programutvecklare och SREs, kan du nu skapa två exempel användare. Om du vill testa Kubernetes RBAC-integrering i slutet av artikeln loggar du in på AKS-klustret med dessa konton.

Ange User Principal Name (UPN) och lösen ord för programutvecklarna. Följande kommando efterfrågar ditt UPN och anger det för att *AAD_DEV_UPN* ska användas i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett bash-gränssnitt). UPN måste innehålla det verifierade domän namnet för din klient, till exempel `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Följande kommando efterfrågar lösen ordet och anger det för att *AAD_DEV_PW* ska användas i ett senare kommando.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Skapa det första användar kontot i Azure AD med hjälp av kommandot [AZ AD User Create][az-ad-user-create] .

I följande exempel skapas en användare med visnings namnet *AKS dev* och UPN och säkra lösen ord med hjälp av värdena i *AAD_DEV_UPN* och *AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Lägg nu till användaren till *AppDev* -gruppen som skapades i föregående avsnitt med hjälp av kommandot [AZ AD-grupp medlem Add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Ange UPN och lösen ord för SREs. Följande kommando efterfrågar ditt UPN och anger det för att *AAD_SRE_UPN* ska användas i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett bash-gränssnitt). UPN måste innehålla det verifierade domän namnet för din klient, till exempel `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Följande kommando efterfrågar lösen ordet och anger det för att *AAD_SRE_PW* ska användas i ett senare kommando.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Skapa ett andra användar konto. I följande exempel skapas en användare med visnings namnet *AKS SRE* och UPN och säkert lösen ord med hjälp av värdena i *AAD_SRE_UPN* och *AAD_SRE_PW*:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Skapa AKS-klusterresurser för app-utvecklarna

Azure AD-grupperna och-användarna skapas nu. Azure Role-tilldelningar skapades för att grupp medlemmarna ska kunna ansluta till ett AKS-kluster som en vanlig användare. Nu ska vi konfigurera AKS-klustret så att dessa olika grupper får åtkomst till vissa resurser.

Börja med att hämta autentiseringsuppgifter för kluster administratören med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . I något av följande avsnitt får du de vanliga *användar* kluster autentiseringsuppgifterna för att se hur Azure AD-autentiseringsschemat fungerar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Skapa ett namn område i AKS-klustret med kommandot [kubectl Create namespace][kubectl-create] . I följande exempel skapas en *dev*-namn rymd:

```console
kubectl create namespace dev
```

I Kubernetes definierar *roller* de behörigheter som ska beviljas och *RoleBindings* tillämpar dem på önskade användare eller grupper. De här tilldelningarna kan tillämpas på ett angivet namn område eller i hela klustret. Mer information finns i [använda KUBERNETES RBAC-auktorisering][rbac-authorization].

Börja med att skapa en roll för *dev* -namnområdet. Den här rollen ger fullständig behörighet till namn området. I produktions miljöer kan du ange mer detaljerade behörigheter för olika användare eller grupper.

Skapa en fil med namnet `role-dev-namespace.yaml` och klistra in följande yaml-manifest:

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

Skapa rollen med kommandot [kubectl Apply][kubectl-apply] och ange fil namnet för ditt yaml-manifest:

```console
kubectl apply -f role-dev-namespace.yaml
```

Sedan hämtar du resurs-ID för *AppDev* -gruppen med hjälp av kommandot [AZ AD Group show][az-ad-group-show] . Den här gruppen anges som ämne för en RoleBinding i nästa steg.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Nu ska du skapa en RoleBinding för *AppDev* -gruppen för att använda den tidigare skapade rollen för namn områdes åtkomst. Skapa en fil med namnet `rolebinding-dev-namespace.yaml` och klistra in följande yaml-manifest. På den sista raden ersätter du *groupObjectId*  med gruppobjekt-ID: t från föregående kommando:

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

Skapa RoleBinding med kommandot [kubectl Apply][kubectl-apply] och ange fil namnet för ditt yaml-manifest:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Skapa AKS kluster resurser för SREs

Upprepa nu de föregående stegen för att skapa ett namn område, en roll och en RoleBinding för SREs.

Börja med att skapa ett namn område för *SRE* med hjälp av kommandot [kubectl Create namespace][kubectl-create] :

```console
kubectl create namespace sre
```

Skapa en fil med namnet `role-sre-namespace.yaml` och klistra in följande yaml-manifest:

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

Skapa rollen med kommandot [kubectl Apply][kubectl-apply] och ange fil namnet för ditt yaml-manifest:

```console
kubectl apply -f role-sre-namespace.yaml
```

Hämta resurs-ID för *opssre* -gruppen med hjälp av kommandot [AZ AD Group show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Skapa en RoleBinding för *opssre* -gruppen för att använda den tidigare skapade rollen för namn områdes åtkomst. Skapa en fil med namnet `rolebinding-sre-namespace.yaml` och klistra in följande yaml-manifest. På den sista raden ersätter du *groupObjectId*  med gruppobjekt-ID: t från föregående kommando:

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

Skapa RoleBinding med kommandot [kubectl Apply][kubectl-apply] och ange fil namnet för ditt yaml-manifest:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagera med kluster resurser med hjälp av Azure AD-identiteter

Nu ska vi testa de förväntade behörigheterna som fungerar när du skapar och hanterar resurser i ett AKS-kluster. I de här exemplen kan du schemalägga och Visa poddar i användarens tilldelade namn område. Sedan försöker du schemalägga och Visa poddar utanför det tilldelade namn området.

Börja med att återställa *kubeconfig* -kontexten med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . I ett tidigare avsnitt ställer du in kontexten med hjälp av autentiseringsuppgifter för kluster administratören. Administratörs användaren kringgår inloggnings meddelanden i Azure AD. Utan `--admin` parametern tillämpas användar kontexten som kräver att alla begär Anden autentiseras med hjälp av Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Schemalägg en grundläggande NGINX-Pod med kommandot [kubectl Run][kubectl-run] i namn området för *utveckling* :

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Som inloggnings fråga anger du autentiseringsuppgifterna för ditt eget konto som `appdev@contoso.com` du skapade i början av artikeln. När du har loggat in cachelagras kontots token för framtida `kubectl` kommandon. NGINX har schemalagts, som visas i följande exempel på utdata:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Använd nu kommandot [kubectl get poddar][kubectl-get] för att Visa poddar i namn området för *utveckling* .

```console
kubectl get pods --namespace dev
```

Som du ser i följande exempel resultat *körs* nginx-pod:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Skapa och Visa kluster resurser utanför det tilldelade namn området

Försök nu att Visa poddar utanför *dev* -namnområdet. Använd kommandot [kubectl get poddar][kubectl-get] igen, den här gången för att se följande `--all-namespaces` :

```console
kubectl get pods --all-namespaces
```

Användarens grupp medlemskap har ingen Kubernetes-roll som tillåter den här åtgärden, som visas i följande exempel på utdata:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

På samma sätt försöker du schemalägga en POD i olika namn områden, till exempel namn området *SRE* . Användarens grupp medlemskap överensstämmer inte med en Kubernetes-roll och RoleBinding för att bevilja dessa behörigheter, som visas i följande exempel på utdata:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testa SRE-åtkomsten till AKS-kluster resurserna

För att bekräfta att Azure AD-gruppmedlemskapet och Kubernetes RBAC fungerar korrekt mellan olika användare och grupper, kan du prova med föregående kommandon när du är inloggad som *opssre* -användare.

Återställa *kubeconfig* -kontexten med kommandot [AZ AKS get-credentials][az-aks-get-credentials] som rensar den tidigare cachelagrade autentiseringstoken för *aksdev* -användaren:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Försök att schemalägga och Visa poddar i det tilldelade *SRE* -namnområdet. När du uppmanas till det loggar du in med dina egna `opssre@contoso.com` autentiseringsuppgifter som skapats i början av artikeln:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Som du ser i följande exempel resultat kan du skapa och Visa poddar:

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

`kubectl`Kommandona fungerar inte, vilket visas i följande exempel på utdata. Användarens grupp medlemskap och Kubernetes-rollen och RoleBindings beviljar inte behörigheter att skapa eller hantera resurser i andra namn områden:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade du resurser i AKS-klustret och användare och grupper i Azure AD. Om du vill rensa alla dessa resurser kör du följande kommandon:

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

Mer information om hur du skyddar Kubernetes-kluster finns i [åtkomst-och identitets alternativ för AKS)][rbac-authorization].

Metod tips för identitets-och resurs kontroll finns i [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
