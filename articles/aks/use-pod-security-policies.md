---
title: Använda Pod säkerhets principer i Azure Kubernetes service (AKS)
description: Lär dig hur du styr Pod-inåtkomster med PodSecurityPolicy i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: cb317e5e0d1f558121e675f569bad37811768ca6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180317"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>För hands version – skydda klustret med Pod säkerhets principer i Azure Kubernetes service (AKS)

> [!WARNING]
> **Funktionen som beskrivs i det här dokumentet, Pod säkerhets princip (förhands granskning) har angetts för utfasning och kommer inte längre att vara tillgänglig efter den 30 juni 2021** i [Azure policy för AKS](use-azure-policy.md). Utfasnings datumet har förlängts från föregående datum den 15 oktober 2020.
>
> När Pod säkerhets princip (förhands granskning) är föråldrad måste du inaktivera funktionen på alla befintliga kluster med hjälp av den föråldrade funktionen för att utföra framtida kluster uppgraderingar och hålla koll på Azure-supporten.
>
> Vi rekommenderar starkt att du börjar testa scenarier med Azure Policy för AKS, som erbjuder inbyggda principer för att skydda poddar och inbyggda initiativ som mappar till Pod säkerhets principer. För att migrera från Pod säkerhets princip måste du vidta följande åtgärder i ett kluster.
> 
> 1. [Inaktivera Pod säkerhets princip](#clean-up-resources) på klustret
> 1. Aktivera [Azure policy-tillägget][kubernetes-policy-reference]
> 1. Aktivera önskade Azure-principer från [Tillgängliga inbyggda principer][policy-samples]
> 1. Granska [beteende ändringar mellan Pod säkerhets princip och Azure policy](#behavior-changes-between-pod-security-policy-and-azure-policy)

För att förbättra säkerheten för ditt AKS-kluster kan du begränsa vilka poddar som kan schemaläggas. Poddar som begär resurser som du inte tillåter kan inte köras i AKS-klustret. Du definierar den här åtkomsten med Pod säkerhets principer. Den här artikeln visar hur du använder Pod säkerhets principer för att begränsa distributionen av poddar i AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill använda Pod säkerhets principer behöver du *AKS-Preview CLI-* tillägget version 0.4.1 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrera Pod för säkerhets princip

**Det här dokumentet och funktionen är inställda för utfasning den 15 oktober 2020.**

Om du vill skapa eller uppdatera ett AKS-kluster för att använda Pod-säkerhetsprinciper måste du först aktivera en funktions flagga i din prenumeration. Registrera funktions flaggan *PodSecurityPolicyPreview* genom att använda kommandot [AZ Feature register][az-feature-register] , som visas i följande exempel:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Översikt över Pod säkerhets principer

I ett Kubernetes-kluster används en åtkomst kontroll för att avlyssna förfrågningar till API-servern när en resurs ska skapas. -Kontroll enheten kan sedan *Verifiera* resurs förfrågan mot en uppsättning regler, eller genom att *i motsvarande* resurs ändra distributions parametrar.

*PodSecurityPolicy* är en kontroll enhet som validerar en POD-specifikation som uppfyller dina definierade krav. Dessa krav kan begränsa användningen av privilegierade behållare, åtkomst till vissa typer av lagring eller den användare eller grupp som behållaren kan köras som. När du försöker distribuera en resurs där Pod-specifikationerna inte uppfyller kraven som beskrivs i säkerhets principen för Pod nekas begäran. Den här möjligheten att kontrol lera vilka poddar som kan schemaläggas i AKS-klustret förhindrar säkerhets sårbarheter eller behörighets eskalering.

När du aktiverar Pod säkerhets princip i ett AKS-kluster tillämpas vissa standard principer. Dessa standard principer ger en välkomst upplevelse för att definiera vilka poddar som kan schemaläggas. Kluster användare kan dock stöta på problem med att distribuera poddar tills du definierar dina egna principer. Den rekommenderade metoden är att:

* Skapa ett AKS-kluster
* Definiera egna Pod säkerhets principer
* Aktivera säkerhets princip funktionen Pod

För att visa hur standard principerna begränsar Pod-distributioner, i den här artikeln, aktiverar vi först funktionen Pod Security Policies och skapar sedan en anpassad princip.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Beteende ändringar mellan säkerhets principer för Pod och Azure Policy

Nedan visas en sammanfattning av funktions förändringar mellan Pod säkerhets princip och Azure Policy.

|Scenario| Pod säkerhets princip | Azure Policy |
|---|---|---|
|Installation|Aktivera funktionen för säkerhets princip för Pod |Aktivera Azure Policy-tillägg
|Distribuera principer| Distribuera Pod säkerhets princip resurs| Tilldela Azure-principer till prenumerations-eller resurs grupps omfånget. Azure Policy-tillägget krävs för resurs program för Kubernetes.
| Standard principer | När Pod säkerhets princip är aktive rad i AKS tillämpas standard privilegierade och obegränsade principer. | Inga standard principer tillämpas genom att aktivera Azure Policy-tillägget. Du måste uttryckligen aktivera principer i Azure Policy.
| Vem kan skapa och tilldela principer | Kluster administratören skapar en POD säkerhets princip resurs | Användarna måste ha en lägsta roll för behörigheten ägare eller resurs princip deltagare i AKS-klustrets resurs grupp. – Via API kan användare tilldela principer i AKS-kluster resurs omfånget. Användaren måste ha minst behörigheten "ägare" eller "resurs princip deltagare" på AKS-kluster resursen. – I Azure Portal kan principer tilldelas på nivån hanterings grupp/prenumeration/resurs grupp.
| Auktorisering av principer| Användare och tjänst konton kräver explicit behörighet att använda Pod säkerhets principer. | Ingen ytterligare tilldelning krävs för att godkänna principer. När principer har tilldelats i Azure kan alla kluster användare använda dessa principer.
| Princip tillämpbarhet | Administratörs användaren kringgår verk ställandet av Pod säkerhets principer. | Alla användare (admin & icke-administratör) ser samma principer. Det finns inget särskilt hölje baserat på användare. Princip programmet kan undantas på namn områdes nivå.
| Princip omfattning | Pod säkerhets principer är inte namnrymds | Constraint-mallar som används av Azure Policy är inte namnrymder.
| Neka/granska/Mutations åtgärd | Pod säkerhets principer stöder endast neka-åtgärder. Mutation kan göras med standardvärden för Create-begäranden. Verifiering kan utföras under uppdaterings begär Anden.| Azure Policy stöder både gransknings & neka åtgärder. Mutation stöds inte ännu, men planerat.
| Pod för säkerhets principer | Det finns ingen synlighet för kompatibilitet för poddar som fanns innan du aktiverade Pod säkerhets policy. Icke-kompatibla poddar som skapats efter aktivering av Pod-säkerhetsprinciper nekas. | Icke-kompatibla poddar som fanns innan Azure-principer tillämpades skulle visas i princip överträdelser. Icke-kompatibla poddar som skapats efter aktivering av Azure-principer nekas om principer anges med en neka-inverkan.
| Så här visar du principer för klustret | `kubectl get psp` | `kubectl get constrainttemplate` -Alla principer returneras.
| Pod säkerhets princip standard-privilegie rad | En privilegie rad Pod säkerhets princip resurs skapas som standard när funktionen aktive ras. | Privilegierat läge innebär ingen begränsning, vilket innebär att det motsvarar att inte ha någon Azure Policy tilldelning.
| [Pod säkerhets princip standard-bas linje/standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Användaren installerar en POD-princip bas linje resurs. | Azure Policy tillhandahåller ett [inbyggt bas linje initiativ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) som mappar till den grundläggande säkerhets principen för pod.
| [Pod säkerhets princip standard-begränsad](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Användaren installerar en säkerhets princip begränsad resurs för pod. | Azure Policy tillhandahåller ett [inbyggt begränsat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) skydd som mappar till den begränsade säkerhets principen för pod.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivera Pod säkerhets princip i ett AKS-kluster

Du kan aktivera eller inaktivera Pod säkerhets princip med kommandot [AZ AKS Update][az-aks-update] . I följande exempel aktive ras säkerhets principer för Pod på kluster namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*.

> [!NOTE]
> För verklig användning aktiverar du inte säkerhets principen Pod förrän du har definierat dina egna anpassade principer. I den här artikeln aktiverar du Pod säkerhets princip som det första steget för att se hur standard principerna begränsar Pod-distributioner.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standard principer för AKS

När du aktiverar Pod säkerhets policy skapar AKS en standard princip med namnet *Privileged*. Redigera eller ta inte bort standard principen. Skapa i stället egna principer som definierar de inställningar som du vill kontrol lera. Vi ska börja med att titta på vad dessa standard principer är för att påverka Pod-distributioner.

Om du vill visa tillgängliga principer använder du kommandot [kubectl get PSP][kubectl-get] , som du ser i följande exempel

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Säkerhets principen för *privilegie rad* Pod tillämpas på alla autentiserade användare i AKS-klustret. Den här tilldelningen styrs av ClusterRoles och ClusterRoleBindings. Använd kommandot [kubectl get rolebindings][kubectl-get] och Sök efter *standardvärdet: Privileged:* binding i *Kube-systemets* namnrymd:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Som det visas i följande komprimerade utdata tilldelas *PSP: Privileged* ClusterRole till alla *system: autentiserade* användare. Den här möjligheten ger en grundläggande behörighets nivå utan att dina egna principer definieras.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Det är viktigt att förstå hur dessa standard principer interagerar med användar förfrågningar för att schemalägga poddar innan du börjar skapa egna Pod säkerhets principer. I följande avsnitt kan vi schemalägga vissa poddar för att se de här standard principerna i praktiken.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Skapa en test användare i ett AKS-kluster

Som standard när du använder kommandot [AZ AKS get-credentials][az-aks-get-credentials] , *läggs administratörsautentiseringsuppgifter för* AKS-klustret till i `kubectl` konfigurationen. Administratörs användaren kringgår verk ställandet av Pod säkerhets principer. Om du använder Azure Active Directory-integrering för dina AKS-kluster kan du logga in med autentiseringsuppgifterna för en användare som inte är administratör för att se verk ställandet av principer i praktiken. I den här artikeln ska vi skapa ett test användar konto i AKS-klustret som du kan använda.

Skapa ett exempel namn område med namnet *PSP-AKS* för test resurser med hjälp av kommandot [kubectl Create namespace][kubectl-create] . Skapa sedan ett tjänst konto med namnet *ej administratör – användare* med kommandot [kubectl Create ServiceAccount][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Skapa sedan en RoleBinding för den användare som inte är *administratör* för att utföra grundläggande åtgärder i namn området med kommandot [kubectl Create RoleBinding][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Skapa alias kommandon för administratörer och användare som inte är administratörer

Om du vill markera skillnaden mellan den vanliga administratörs användaren när du använder `kubectl` och den icke-administratör som skapades i föregående steg, skapar du två kommando rads Ali Aset:

* **Kubectl-admin-** aliaset är för den vanliga administratörs användaren och är begränsad till *PSP-AKS-* namnområdet.
* **Kubectl-nonadminuser** alias är för den *ej administratörer-användare* som skapades i föregående steg och som är begränsad till namn området *PSP-AKS* .

Skapa dessa två alias som du ser i följande kommandon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testa skapandet av en privilegie rad Pod

Vi börjar med att testa vad som händer när du schemalägger en POD med säkerhets kontexten för `privileged: true` . Den här säkerhets kontexten eskalerar Pod privilegier. I det föregående avsnittet som visade standard säkerhets principerna för AKS-Pod bör *behörighets* principen neka denna begäran.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod kan inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en ej privilegie rad Pod

I det föregående exemplet begärde Pod-specifikationen privilegie rad eskalering. Den här begäran nekas av säkerhets principen standard *Privilege* pod, så Pod kan inte schemaläggas. Nu ska vi prova att köra samma NGINX-Pod utan begäran om behörighets eskalering.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod kan inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testa att skapa en POD med en speciell användar kontext

I föregående exempel försökte behållar avbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av standard säkerhets principen för *privilegie* pod, så Pod kan inte starta. Nu ska vi prova att köra samma NGINX-Pod med en speciell användar kontext, till exempel `runAsUser: 2000` .

Skapa en fil med namnet `nginx-unprivileged-nonroot.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Pod kan inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="create-a-custom-pod-security-policy"></a>Skapa en anpassad säkerhets princip för Pod

Nu när du har sett hur du kan använda standard säkerhets principerna för Pod kan du ge den andra *användaren* möjlighet att schemalägga poddar.

Nu ska vi skapa en princip för att avvisa poddar som begär privilegie rad åtkomst. Andra alternativ, till exempel *runAsUser* eller tillåtna *volymer*, är inte uttryckligen begränsade. Den här typen av princip nekar en begäran om privilegie rad åtkomst, men tillåter annars att klustret kör den begärda poddar.

Skapa en fil med namnet `psp-deny-privileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Skapa principen med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Om du vill visa tillgängliga principer använder du kommandot [kubectl get PSP][kubectl-get] , som du ser i följande exempel. Jämför principen *PSP-Deny-Privileged* med den standard *behörighets* princip som har tillämpats i föregående exempel för att skapa en pod. Endast användningen *av den tidigare eskaleringen* nekas av principen. Det finns inga begränsningar för användaren eller gruppen för principen *PSP-Deny-Privileged* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Tillåt att användar kontot använder den anpassade säkerhets principen för Pod

I föregående steg skapade du en POD säkerhets princip för att avvisa poddar som begär privilegie rad åtkomst. Om du vill tillåta att principen används skapar du en *roll* eller en *ClusterRole*. Sedan associerar du en av dessa roller med hjälp av en *RoleBinding* eller *ClusterRoleBinding*.

I det här exemplet skapar du en ClusterRole som gör att du kan *använda* principen *PSP-Deny-Privileged* som skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrole.yaml` och klistra in följande yaml-manifest:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Skapa ClusterRole med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Skapa nu en ClusterRoleBinding för att använda ClusterRole som skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrolebinding.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Skapa en ClusterRoleBinding med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> I det första steget i den här artikeln har funktionen säkerhets princip för Pod Aktiver ATS i AKS-klustret. Den rekommenderade metoden var att endast aktivera funktionen Pod säkerhets princip när du har definierat dina egna principer. Det här är steget där du aktiverar funktionen Pod Security Policy. En eller flera anpassade principer har definierats och användar konton har associerats med dessa principer. Nu kan du aktivera funktionen Pod säkerhets princip och minimera problemen som orsakas av standard principerna.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testa skapandet av en pod som inte har privilegierat igen

Med din anpassade Pod-säkerhetsprincip tillämpad och en bindning för användar kontot för att använda principen, försöker vi att skapa en pod som inte har privilegierat igen. Använd samma `nginx-privileged.yaml` manifest för att skapa Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod har schemalagts. När du kontrollerar status för Pod med kommandot [kubectl get poddar][kubectl-get] *körs* pod:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Det här exemplet visar hur du kan skapa anpassade Pod säkerhets principer för att definiera åtkomst till AKS-klustret för olika användare eller grupper. Standard principerna för AKS ger tätt kontroll över vad poddar kan köra, så skapa dina egna anpassade principer för att sedan definiera de begränsningar du behöver.

Ta bort NGINX Pod med kommandot [kubectl Delete][kubectl-delete] och ange namnet på din yaml-manifest:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera Pod säkerhets princip använder du kommandot [AZ AKS Update][az-aks-update] igen. I följande exempel inaktive ras Pod säkerhets princip på kluster namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ta sedan bort ClusterRole och ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Ta bort säkerhets principen med kommandot [kubectl Delete][kubectl-delete] och ange namnet på ditt yaml-manifest:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Ta slutligen bort *PSP-AKS-* namnområdet:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar dig hur du skapar en POD säkerhets princip för att förhindra användning av privilegie rad åtkomst. Det finns många funktioner som en princip kan genomdriva, t. ex. typ av volym eller RunAs-användare. Mer information om tillgängliga alternativ finns i [referens dokument för säkerhets princip för Kubernetes Pod][kubernetes-policy-reference].

Mer information om hur du begränsar Pod nätverks trafik finns i [skydda trafik mellan poddar med hjälp av nätverks principer i AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
