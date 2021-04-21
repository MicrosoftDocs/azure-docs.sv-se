---
title: Integrera Azure Active Directory med Azure Kubernetes Service (äldre)
description: Lär dig hur du använder Azure CLI för att Azure Active Directory ett Azure Kubernetes Service (äldre) Azure Kubernetes Service (äldre)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: cb92f84560a88d406f0d519459c27b5d916ec5ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769579"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrera Azure Active Directory med Azure Kubernetes Service med Hjälp av Azure CLI (äldre)

Azure Kubernetes Service (AKS) kan konfigureras att använda Azure Active Directory (AD) för användarautentisering. I den här konfigurationen kan du logga in i ett AKS-kluster med hjälp av en Azure AD-autentiseringstoken. Klusteroperatörer kan också konfigurera Kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC) baserat på en användares identitet eller kataloggruppsmedlemskap.

Den här artikeln visar hur du skapar nödvändiga Azure AD-komponenter och sedan distribuerar ett Azure AD-aktiverat kluster och skapar en grundläggande Kubernetes-roll i AKS-klustret.

Det fullständiga exempelskriptet som används i den här artikeln finns i [Azure CLI-exempel – AKS-integrering med Azure AD.][complete-script]

> [!Important]
> AKS har en ny förbättrad [AKS-hanterad Azure AD-upplevelse][managed-aad] som inte kräver att du hanterar server- eller klientprogram. Om du vill migrera följer du anvisningarna [här.][managed-aad-migrate]

## <a name="the-following-limitations-apply"></a>Följande begränsningar gäller:

- Azure AD kan bara aktiveras på Kubernetes RBAC-aktiverade kluster.
- Äldre Azure AD-integrering kan bara aktiveras när klustret skapas.

## <a name="before-you-begin"></a>Innan du börjar

Azure CLI version 2.0.61 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

Gå till [https://shell.azure.com](https://shell.azure.com) för att Cloud Shell i webbläsaren.

För konsekvens och för att köra kommandona i den här artikeln skapar du en variabel för önskat AKS-klusternamn. I följande exempel används namnet *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Översikt över Azure AD-autentisering

Azure AD-autentisering tillhandahålls till AKS-kluster med OpenID Connect. OpenID Connect är ett identitetslager som bygger på OAuth 2.0-protokollet. Mer information om hur OpenID Connect finns i [dokumentationen för Open ID Connect.][open-id-connect]

Inifrån Kubernetes-klustret används Webhook-tokenautentisering för att verifiera autentiseringstoken. Webhook-tokenautentisering konfigureras och hanteras som en del av AKS-klustret. Mer information om webhook-tokenautentisering finns i [webhook-autentiseringsdokumentationen][kubernetes-webhook].

> [!NOTE]
> När du konfigurerar Azure AD för AKS-autentisering konfigureras två Azure AD-program. Den här åtgärden måste utföras av en Azure-klientadministratör.

## <a name="create-azure-ad-server-component"></a>Skapa En Azure AD-serverkomponent

Om du vill integrera med AKS skapar och använder du ett Azure AD-program som fungerar som slutpunkt för identitetsbegäranden. Det första Azure AD-program som du behöver får Azure AD-gruppmedlemskap för en användare.

Skapa serverprogramkomponenten med kommandot [az ad app create][az-ad-app-create] och uppdatera sedan anspråken för gruppmedlemskap med kommandot az ad [app][az-ad-app-update] update. I följande exempel används *variabeln aksname* som definierats [i avsnittet Innan du](#before-you-begin) börjar och skapar en variabel

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Skapa nu ett huvudnamn för tjänsten för serverappen med [kommandot az ad sp][az-ad-sp-create] create. Tjänstens huvudnamn används för att autentisera sig själv på Azure-plattformen. Hämta sedan hemligheten för tjänstens huvudnamn med kommandot [az ad sp credential reset][az-ad-sp-credential-reset] och tilldela variabeln *serverApplicationSecret* för användning i något av följande steg:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD-tjänstens huvudnamn behöver behörigheter för att utföra följande åtgärder:

* Läs katalogdata
* Logga in och läsa användarprofil

Tilldela dessa behörigheter med kommandot [az ad app permission add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Slutligen beviljar du de behörigheter som tilldelades i föregående steg för serverprogrammet med kommandot [az ad app permission grant.][az-ad-app-permission-grant] Det här steget misslyckas om det aktuella kontot inte är en innehavaradministratör. Du måste också lägga till behörigheter för Azure AD-programmet för att begära information som annars kan kräva administrativt medgivande med hjälp av [az ad app permission admin-consent:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Skapa en Azure AD-klientkomponent

Det andra Azure AD-programmet används när en användare loggar till AKS-klustret med Kubernetes CLI ( `kubectl` ). Det här klientprogrammet tar autentiseringsbegäran från användaren och verifierar sina autentiseringsuppgifter och behörigheter. Skapa Azure AD-appen för klientkomponenten med kommandot [az ad app][az-ad-app-create] create:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Skapa ett huvudnamn för tjänsten för klientprogrammet med kommandot [az ad sp][az-ad-sp-create] create:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Hämta oAuth2-ID:t för serverappen för att tillåta autentiseringsflödet mellan de två appkomponenterna med kommandot [az ad app show.][az-ad-app-show] Detta oAuth2-ID används i nästa steg.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Lägg till behörigheterna för klientprogrammet och serverprogramkomponenterna för att använda oAuth2-kommunikationsflödet med [kommandot az ad app permission add.][az-ad-app-permission-add] Bevilja sedan behörigheter för klientprogrammet för kommunikation med serverprogrammet med kommandot [az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuera klustret

Nu när de två Azure AD-programmen har skapats skapar du själva AKS-klustret. Skapa först en resursgrupp med kommandot [az group][az-group-create] create. I följande exempel skapas resursgruppen i *regionen EastUS:*

Skapa en resursgrupp för klustret:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Hämta klientorganisations-ID för din Azure-prenumeration med [hjälp av kommandot az account show.][az-account-show] Skapa sedan AKS-klustret med kommandot [az aks create.][az-aks-create] Kommandot för att skapa AKS-klustret tillhandahåller server- och klientprogram-ID:n, hemligheten för tjänstens huvudnamn för serverprogrammet och ditt klientorganisations-ID:

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

Hämta slutligen autentiseringsuppgifterna för klusteradministratören med [hjälp av kommandot az aks get-credentials.][az-aks-get-credentials] I något av följande steg får du de vanliga autentiseringsuppgifterna för *användarklustret* för att se hur Azure AD-autentiseringsflödet fungerar.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Skapa Kubernetes RBAC-bindning

Innan ett Azure Active Directory-konto kan användas med AKS-klustret måste du skapa en rollbindning eller klusterrollbindning. *Roller* definierar behörigheterna att bevilja och *bindningar* tillämpar dem på önskade användare. Dessa tilldelningar kan tillämpas på en viss namnrymd eller i hela klustret. Mer information finns i Använda [Kubernetes RBAC-auktorisering.][rbac-authorization]

Hämta användarens huvudnamn (UPN) för den användare som för närvarande är inloggad med kommandot [az ad signed-in-user show.][az-ad-signed-in-user-show] Det här användarkontot är aktiverat för Azure AD-integrering i nästa steg.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Om användaren du beviljar Kubernetes RBAC-bindningen för finns i samma Azure AD-klientorganisation tilldelar du behörigheter baserat på *userPrincipalName*. Om användaren finns i en annan Azure AD-klientorganisation frågar du efter och använder *egenskapen objectId* i stället.

Skapa ett YAML-manifest med `basic-azure-ad-binding.yaml` namnet och klistra in följande innehåll. På den sista raden ersätter *du userPrincipalName_or_objectId*  med UPN- eller objekt-ID-utdata från föregående kommando:

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

Skapa ClusterRoleBinding med kommandot [kubectl apply][kubectl-apply] och ange filnamnet för ditt YAML-manifest:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Komma åt kluster med Azure AD

Nu ska vi testa integreringen av Azure AD-autentisering för AKS-klustret. Ange `kubectl` konfigurationskontexten till att använda vanliga användarautentiseringsuppgifter. Den här kontexten skickar alla autentiseringsbegäranden tillbaka via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Använd nu kommandot [kubectl get pods][kubectl-get] för att visa poddar i alla namnområden:

```console
kubectl get pods --all-namespaces
```

Du får en inloggningsfråga om att autentisera med hjälp av Azure AD-autentiseringsuppgifter via en webbläsare. När du har autentiserats visar kommandot poddarna i `kubectl` AKS-klustret, som du ser i följande exempelutdata:

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

Autentiseringstoken som tas `kubectl` emot för cachelagras. Du får bara logga in om token har upphört att gälla eller kubernetes-konfigurationsfilen skapas på ny plats.

Om du ser ett auktoriseringsfelmeddelande när du har loggat in med en webbläsare som i följande exempelutdata kontrollerar du följande möjliga problem:

```output
error: You must be logged in to the server (Unauthorized)
```

* Du har definierat lämpligt objekt-ID eller UPN, beroende på om användarkontot finns i samma Azure AD-klientorganisation eller inte.
* Användaren är inte medlem i fler än 200 grupper.
* Hemligheten som definierats i programregistreringen för servern matchar värdet som konfigurerats med `--aad-server-app-secret`

## <a name="next-steps"></a>Nästa steg

Det fullständiga skriptet som innehåller kommandona som visas i den här artikeln finns i [Azure AD-integreringsskriptet på lagringsplatsen för AKS-exempel.][complete-script]

Information om hur du använder Azure AD-användare och -grupper för att styra åtkomsten till klusterresurser finns i Kontrollera åtkomsten till klusterresurser med hjälp av [rollbaserad åtkomstkontroll i Kubernetes och Azure AD-identiteter i AKS][azure-ad-rbac].

Mer information om hur du skyddar Kubernetes-kluster finns i [Åtkomst- och identitetsalternativ för AKS).][rbac-authorization]

Metodtips för identitets- och resurskontroll finns i [Metodtips för autentisering och auktorisering i AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
