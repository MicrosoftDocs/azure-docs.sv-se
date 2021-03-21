---
title: Integrera Azure Active Directory med Azure Kubernetes service (bakåtkompatibelt)
description: Lär dig hur du använder Azure CLI för att skapa och Azure Active Directory-aktiverade Azure Kubernetes service (AKS)-kluster (bakåtkompatibelt)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 055afe24cb72b331e64e1a2b3d786503ef31a105
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176407"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrera Azure Active Directory med Azure Kubernetes-tjänsten med hjälp av Azure CLI (bakåtkompatibelt)

Azure Kubernetes service (AKS) kan konfigureras att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen kan du logga in på ett AKS-kluster med en Azure AD-autentiseringstoken. Kluster operatörer kan också konfigurera Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC) baserat på användarens identitet eller katalog grupp medlemskap.

Den här artikeln visar hur du skapar nödvändiga Azure AD-komponenter, sedan distribuerar ett Azure AD-aktiverat kluster och skapar en grundläggande Kubernetes-roll i AKS-klustret.

Det fullständiga exempel skriptet som används i den här artikeln finns i [Azure CLI-exempel – AKS-integrering med Azure AD][complete-script].

> [!Important]
> AKS har en ny förbättrad [AKS-hanterad Azure AD][managed-aad] -upplevelse som inte kräver att du hanterar Server-eller klient program. Om du vill migrera följer du anvisningarna [här][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>Följande begränsningar gäller:

- Azure AD kan bara aktive ras på Kubernetes RBAC-aktiverat kluster.
- Azure AD Legacy-integrering kan bara aktive ras när klustret skapas.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Gå till [https://shell.azure.com](https://shell.azure.com) för att öppna Cloud Shell i webbläsaren.

För konsekvens och för att hjälpa till att köra kommandona i den här artikeln skapar du en variabel för det önskade AKS-klustrets namn. I följande exempel används namnet *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Azure AD-autentisering tillhandahålls för AKS-kluster med OpenID Connect. OpenID Connect är ett identitets lager som byggts ovanpå OAuth 2,0-protokollet. Mer information om OpenID Connect finns i [Open ID Connect-dokumentationen][open-id-connect].

Från inifrån Kubernetes-klustret används webhook-token-autentisering för att verifiera autentiseringstoken. Webhook-token-autentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om autentisering med webhook-token finns i [dokumentationen för webhook-autentisering][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Den här åtgärden måste utföras av en Azure-innehavaradministratör.

## <a name="create-azure-ad-server-component"></a>Skapa Azure AD Server-komponent

För att integrera med AKS skapar du och använder ett Azure AD-program som fungerar som en slut punkt för identitets förfrågningarna. Det första Azure AD-program du behöver får du från Azure AD-grupp medlemskap för en användare.

Skapa serverprogram komponenten med kommandot [AZ AD App Create][az-ad-app-create] och uppdatera sedan grupp medlemskaps anspråken med hjälp av kommandot [AZ AD App Update][az-ad-app-update] . I följande exempel används variabeln *aksname* som definierats i avsnittet [innan du börjar](#before-you-begin) och skapar en variabel

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Skapa nu ett tjänst huvud namn för serverprogrammet med hjälp av kommandot [AZ AD SP Create][az-ad-sp-create] . Tjänstens huvud namn används för att autentisera sig själv i Azure-plattformen. Hämta sedan SPN-hemligheten med hjälp av kommandot [AZ AD SP Credential reset][az-ad-sp-credential-reset] och tilldela variabeln namnet *serverApplicationSecret* för användning i något av följande steg:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD-tjänstens huvud namn måste ha behörighet att utföra följande åtgärder:

* Läs katalogdata
* Logga in och läsa användarprofil

Tilldela dessa behörigheter med hjälp av kommandot [AZ AD App permission Add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Bevilja slutligen de behörigheter som tilldelats i föregående steg för Server programmet med hjälp av kommandot [AZ AD App permission Grant][az-ad-app-permission-grant] . Det här steget Miss lyckas om det aktuella kontot inte är en klient organisations administratör. Du måste också lägga till behörigheter för Azure AD-program för att begära information som annars kan kräva administrativt medgivande med hjälp av [AZ AD App permission admin-medgivande][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Skapa Azure AD-klient komponent

Det andra Azure AD-programmet används när en användare loggar in i AKS-klustret med Kubernetes CLI ( `kubectl` ). Det här klient programmet tar emot autentiseringsbegäran från användaren och verifierar sina autentiseringsuppgifter och behörigheter. Skapa Azure AD-appen för klient komponenten med hjälp av kommandot [AZ AD App Create][az-ad-app-create] :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Skapa ett huvud namn för tjänsten för klient programmet med hjälp av kommandot [AZ AD SP Create][az-ad-sp-create] :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Hämta oAuth2-ID: t för Server-appen för att tillåta autentiserings flödet mellan de två app-komponenterna med hjälp av kommandot [AZ AD App show][az-ad-app-show] . Detta oAuth2-ID används i nästa steg.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Lägg till behörigheterna för klient program-och serverprogram komponenter för att använda oAuth2-kommunikations flödet med hjälp av kommandot [AZ AD App permission Add][az-ad-app-permission-add] . Ge sedan behörighet för klient programmet att kommunicera med serverprogrammet med hjälp av kommandot [AZ AD App permission Grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuera klustret

När de två Azure AD-programmen har skapats skapar du nu själva AKS-klustret. Skapa först en resurs grupp med kommandot [AZ Group Create][az-group-create] . I följande exempel skapas resurs gruppen i regionen *östra* :

Skapa en resurs grupp för klustret:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Hämta klient-ID: t för din Azure-prenumeration med kommandot [AZ Account show][az-account-show] . Skapa sedan AKS-klustret med kommandot [AZ AKS Create][az-aks-create] . Kommandot för att skapa AKS-klustret tillhandahåller Server-och klient program-ID: n, Server programmets tjänstens huvud namn och klient-ID: t:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Slutligen hämtar du autentiseringsuppgifterna för kluster administratören med kommandot [AZ AKS get-credentials][az-aks-get-credentials] . I något av följande steg får du de vanliga *användar* kluster autentiseringsuppgifterna för att se hur Azure AD-autentiseringsschemat fungerar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Skapa Kubernetes RBAC-bindning

Innan ett Azure Active Directory konto kan användas med AKS-klustret måste du skapa en roll bindning eller en kluster roll bindning. *Roller* definierar behörigheterna som ska beviljas och *bindningar* tillämpar dem på önskade användare. De här tilldelningarna kan tillämpas på ett angivet namn område eller i hela klustret. Mer information finns i [använda KUBERNETES RBAC-auktorisering][rbac-authorization].

Hämta User Principal Name (UPN) för den användare som för närvarande är inloggad med kommandot [AZ AD Signed-User show][az-ad-signed-in-user-show] . Det här användar kontot är aktiverat för Azure AD-integrering i nästa steg.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Om användaren som du tilldelar Kubernetes RBAC-bindning för finns i samma Azure AD-klient tilldelar du behörigheter baserat på *userPrincipalName*. Om användaren finns i en annan Azure AD-klient frågar du efter och använder egenskapen *ObjectID* i stället.

Skapa ett YAML-manifest med namnet `basic-azure-ad-binding.yaml` och klistra in följande innehåll. På den sista raden ersätter du *userPrincipalName_or_objectId*  med UPN-eller objekt-ID-utdata från föregående kommando:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Skapa ClusterRoleBinding med kommandot [kubectl Apply][kubectl-apply] och ange fil namnet för ditt yaml-manifest:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Åtkomst till kluster med Azure AD

Nu ska vi testa integreringen av Azure AD-autentisering för AKS-klustret. Ange `kubectl` konfigurations kontexten för att använda vanliga användarautentiseringsuppgifter. Den här kontexten skickar alla autentiseringsbegäranden tillbaka till Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Använd nu kommandot [kubectl get poddar][kubectl-get] för att Visa poddar i alla namn områden:

```console
kubectl get pods --all-namespaces
```

Du får en inloggnings fråga för att autentisera med Azure AD-autentiseringsuppgifter med hjälp av en webbläsare. När du har autentiserat `kubectl` visar kommandot poddar i AKS-klustret, vilket visas i följande exempel på utdata:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Autentiseringstoken som togs emot för `kubectl` cachelagras. Du ombeds bara att logga in när token har upphört att gälla eller att konfigurations filen för Kubernetes har återskapats.

Om ett fel meddelande visas när du har loggat in med en webbläsare som i följande exempel på utdata, kontrollerar du följande möjliga problem:

```output
error: You must be logged in to the server (Unauthorized)
```

* Du definierade lämpligt objekt-ID eller UPN, beroende på om användar kontot finns i samma Azure AD-klient eller inte.
* Användaren är inte medlem i fler än 200 grupper.
* Hemligheten som definierats i program registreringen för servern matchar det värde som kon figurer ATS med `--aad-server-app-secret`

## <a name="next-steps"></a>Nästa steg

Det fullständiga skriptet som innehåller de kommandon som visas i den här artikeln finns i [Azure AD integration-skriptet i AKS-exemplen lagrings platsen][complete-script].

Om du vill använda Azure AD-användare och-grupper för att kontrol lera åtkomsten till kluster resurser, se [kontrol lera åtkomst till kluster resurser med hjälp av Kubernetes-rollbaserad åtkomst kontroll och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [åtkomst-och identitets alternativ för AKS)][rbac-authorization].

Metod tips för identitets-och resurs kontroll finns i [metod tips för autentisering och auktorisering i AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
