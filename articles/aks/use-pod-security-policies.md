---
title: Använda poddsäkerhetsprinciper i Azure Kubernetes Service (AKS)
description: Lär dig hur du styr poddantagningar med hjälp av PodSecurityPolicy i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767429"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Förhandsversion – Skydda klustret med poddsäkerhetsprinciper i Azure Kubernetes Service (AKS)

> [!WARNING]
> **Funktionen som beskrivs i det här dokumentet, [](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) pod security policy (preview), börjar utfasningen med Kubernetes version 1.21, med borttagning i version 1.25.** När Kubernetes Upstream närmar sig milstolpen kommer Kubernetes-communityn att arbeta med att dokumentera genomförbara alternativ. Det tidigare meddelandet om utfasning gjordes då det inte fanns något genomförbart alternativ för kunder. Nu när Kubernetes-communityn arbetar med ett alternativ finns det inte längre ett brådskande behov av att bli inaktuella före Kubernetes. 
>
> När poddsäkerhetsprincipen (förhandsversion) är inaktuell måste du inaktivera funktionen på befintliga kluster med den föråldrade funktionen för att utföra framtida klusteruppgraderingar och hålla dig inom Azure-supporten.

För att förbättra säkerheten för ditt AKS-kluster kan du begränsa vilka poddar som kan schemaläggas. Poddar som begär resurser som du inte tillåter kan inte köras i AKS-klustret. Du definierar den här åtkomsten med hjälp av poddsäkerhetsprinciper. Den här artikeln visar hur du använder poddsäkerhetsprinciper för att begränsa distributionen av poddar i AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

Azure CLI version 2.0.61 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägget aks-preview

Om du vill använda poddsäkerhetsprinciper behöver *du CLI-tillägget aks-preview* version 0.4.1 eller senare. Installera *Azure CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrera poddsäkerhetsprincipfunktionsprovider

**Det här dokumentet och funktionen är inställda för utfasning den 15 oktober 2020.**

Om du vill skapa eller uppdatera ett AKS-kluster för att använda poddsäkerhetsprinciper aktiverar du först en funktionsflagga för din prenumeration. Om du vill *registrera funktionsflaggan PodSecurityPolicyPreview* använder du [kommandot az feature register][az-feature-register] enligt följande exempel:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *Registrerad.* Du kan kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Översikt över poddsäkerhetsprinciper

I ett Kubernetes-kluster används en antagningskontrollant för att fånga upp begäranden till API-servern när en resurs ska skapas. Antagningskontrollanten kan *sedan verifiera* resursbegäran mot en uppsättning regler eller *mutera resursen* för att ändra distributionsparametrar.

*PodSecurityPolicy är* en antagningskontrollant som validerar att en poddspecifikation uppfyller dina definierade krav. Dessa krav kan begränsa användningen av privilegierade containrar, åtkomst till vissa typer av lagring eller den användare eller grupp som containern kan köras som. När du försöker distribuera en resurs där poddspecifikationerna inte uppfyller kraven som beskrivs i poddsäkerhetsprincipen nekas begäran. Den här möjligheten att styra vilka poddar som kan schemaläggas i AKS-klustret förhindrar vissa säkerhetsproblem eller behörighetseskaleringar.

När du aktiverar poddsäkerhetsprincip i ett AKS-kluster tillämpas vissa standardprinciper. Dessa standardprinciper ger en standardupplevelse för att definiera vilka poddar som kan schemaläggas. Klusteranvändare kan dock få problem med att distribuera poddar tills du har definierat dina egna principer. Den rekommenderade metoden är att:

* Skapa ett AKS-kluster
* Definiera egna säkerhetsprinciper för poddar
* Aktivera funktionen poddsäkerhetsprincip

För att visa hur standardprinciperna begränsar podddistributioner aktiverar vi först funktionen poddsäkerhetsprinciper i den här artikeln och skapar sedan en anpassad princip.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Beteendeändringar mellan poddsäkerhetsprincip och Azure Policy

Nedan visas en sammanfattning av beteendeändringar mellan poddsäkerhetsprincipen och Azure Policy.

|Scenario| Poddsäkerhetsprincip | Azure Policy |
|---|---|---|
|Installation|Aktivera funktionen poddsäkerhetsprincip |Aktivera Azure Policy-tillägg
|Distribuera principer| Distribuera resurs för poddsäkerhetsprincip| Tilldela Azure-principer till prenumerations- eller resursgruppsomfånget. Tillägget Azure Policy krävs för Kubernetes-resursprogram.
| Standardprinciper | När poddsäkerhetsprincip är aktiverad i AKS tillämpas privilegierade och obegränsade standardprinciper. | Inga standardprinciper tillämpas genom att aktivera Azure Policy-tillägget. Du måste uttryckligen aktivera principer i Azure Policy.
| Vem kan skapa och tilldela principer | Klusteradministratören skapar en resurs för poddsäkerhetsprincip | Användarna måste minst ha behörigheten "ägare" eller "Resursprincipdeltagare" för AKS-klusterresursgruppen. – Via API kan användare tilldela principer i resursomfånget för AKS-klustret. Användaren måste ha minst behörighet som ägare eller resursprincipdeltagare för AKS-klusterresursen. - I Azure Portal kan principer tilldelas på nivån Hanteringsgrupp/prenumeration/resursgrupp.
| Auktorisera principer| Användare och tjänstkonton kräver explicita behörigheter för att använda poddsäkerhetsprinciper. | Ingen ytterligare tilldelning krävs för att auktorisera principer. När principer har tilldelats i Azure kan alla klusteranvändare använda dessa principer.
| Princip tillämplighet | Administratörsanvändaren kringgår tillämpningen av säkerhetsprinciper för poddar. | Alla användare (& icke-administratör) ser samma principer. Det finns inget särskilt hölje baserat på användare. Principprogram kan undantas på namnområdesnivå.
| Principomfång | Poddsäkerhetsprinciper är inte namnområden | Begränsningsmallar som används av Azure Policy är inte namnrymder.
| Åtgärden Neka/granska/Granska/Granska | Poddsäkerhetsprinciper stöder endast nekandeåtgärder. Du kan göra detta med standardvärden för att skapa begäranden. Verifiering kan göras under uppdateringsbegäranden.| Azure Policy stöder både & neka-åtgärder. – Det finns inte stöd för det än, men planerat.
| Efterlevnad av poddsäkerhetsprincip | Det finns ingen synlighet för efterlevnad för poddar som fanns innan du aktiverar poddsäkerhetsprincip. Icke-kompatibla poddar som skapats efter aktivering av poddsäkerhetsprinciper nekas. | Icke-kompatibla poddar som fanns innan Du tillämpar Azure-principer visas i principöverträdelser. Icke-kompatibla poddar som skapats efter aktivering av Azure-principer nekas om principer anges med en neka-effekt.
| Så här visar du principer i klustret | `kubectl get psp` | `kubectl get constrainttemplate` – Alla principer returneras.
| Standard för poddsäkerhetsprincip – Privilegierad | En privilegierad poddsäkerhetsprincipresurs skapas som standard när du aktiverar funktionen. | Privilegierat läge innebär ingen begränsning, vilket innebär att det motsvarar att inte ha Azure Policy tilldelning.
| [Standard för poddsäkerhetsprincip – baslinje/standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Användaren installerar en baslinjeresurs för poddsäkerhetsprincip. | Azure Policy ett [inbyggt baslinjeinitiativ som](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) mappar till baslinjens poddsäkerhetsprincip.
| [Standard för poddsäkerhetsprincip – begränsad](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Användaren installerar en resurs med begränsad poddsäkerhetsprincip. | Azure Policy ett [inbyggt begränsat initiativ som mappar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) till den begränsade poddsäkerhetsprincipen.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivera poddsäkerhetsprincip i ett AKS-kluster

Du kan aktivera eller inaktivera poddsäkerhetsprincip med [hjälp av kommandot az aks update.][az-aks-update] I följande exempel aktiveras poddsäkerhetsprincip på klusternamnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*.

> [!NOTE]
> Aktivera inte poddsäkerhetsprincipen för verklig användning förrän du har definierat dina egna anpassade principer. I den här artikeln aktiverar du poddsäkerhetsprinciper som första steg för att se hur standardprinciperna begränsar podddistributioner.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>AKS-standardprinciper

När du aktiverar poddsäkerhetsprincip skapar AKS en standardprincip med namnet *privileged*. Redigera eller ta inte bort standardprincipen. Skapa i stället egna principer som definierar de inställningar som du vill kontrollera. Först ska vi titta på vad dessa standardprinciper är för hur de påverkar poddistributioner.

Om du vill visa de principer som är tillgängliga använder [du kommandot kubectl get psp,][kubectl-get] som du ser i följande exempel

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*Säkerhetsprincipen* för privilegierade poddar tillämpas på alla autentiserade användare i AKS-klustret. Den här tilldelningen styrs av ClusterRoles och ClusterRoleBindings. Använd kommandot [kubectl get rolebindings][kubectl-get] och sök efter *default:privileged:* binding i *namnområdet kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Som du ser i följande komprimerade utdata tilldelas *psp:privileged* ClusterRole till alla *system:autentiserade* användare. Den här möjligheten ger en grundläggande behörighetsnivå utan att dina egna principer definieras.

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

Det är viktigt att förstå hur dessa standardprinciper interagerar med användarbegäranden för att schemalägga poddar innan du börjar skapa egna poddsäkerhetsprinciper. I följande avsnitt ska vi schemalägga några poddar för att se hur dessa standardprinciper används.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Skapa en testanvändare i ett AKS-kluster

När du använder kommandot [az aks get-credentials][az-aks-get-credentials] läggs som standard administratörsautentiseringsuppgifterna för AKS-klustret till i din  `kubectl` konfiguration. Administratörsanvändaren kringgår tillämpningen av säkerhetsprinciper för poddar. Om du använder Azure Active Directory-integrering för dina AKS-kluster kan du logga in med autentiseringsuppgifterna för en användare som inte är administratör för att se hur principer används. I den här artikeln ska vi skapa ett testanvändarkonto i AKS-klustret som du kan använda.

Skapa ett exempelnamnområde med *namnet psp-aks* för testresurser med kommandot [kubectl create namespace.][kubectl-create] Skapa sedan ett tjänstkonto med namnet *nonadmin-user med* kommandot [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Skapa sedan en RoleBinding för *nonadmin-user* för att utföra grundläggande åtgärder i namnområdet med kommandot [kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Skapa aliaskommandon för administratörsanvändare och icke-administratörsanvändare

Om du vill markera skillnaden mellan den vanliga administratörsanvändaren när du använder och den icke-administratörsanvändare som skapades i föregående steg skapar du två `kubectl` kommandoradsalias:

* Aliaset **kubectl-admin** är för den vanliga administratörsanvändaren och är begränsad till *namnområdet psp-aks.*
* Aliaset **kubectl-nonadminuser** gäller för *den nonadmin-user* som skapades i föregående steg och är begränsad till *namnområdet psp-aks.*

Skapa dessa två alias enligt följande kommandon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testa skapandet av en privilegierad podd

Först testar vi vad som händer när du schemalägger en podd med säkerhetskontexten `privileged: true` . Den här säkerhetskontexten eskalerar poddens behörigheter. I föregående avsnitt som visade standardsäkerhetsprinciperna för AKS-poddar bör *behörighetsprincipen* neka den här begäran.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande YAML-manifest:

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

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Podden kan inte schemaläggas, som du ser i följande exempelutdata:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Podden når inte schemaläggningsfasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en podd utan privilegier

I föregående exempel begärde poddspecifikationen privilegierad eskalering. Den här begäran nekas av *standardprivilegier* för poddsäkerhetsprincipen, så podden kan inte schemaläggas. Nu ska vi prova att köra samma NGINX-podd utan begäran om behörighetseskalering.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande YAML-manifest:

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

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Podden kan inte schemaläggas, som du ser i följande exempelutdata:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Podden når inte schemaläggningsfasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testa att skapa en podd med en specifik användarkontext

I föregående exempel försökte containeravbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av *standardprivilegier* för poddsäkerhetsprincipen, så podden kan inte starta. Nu ska vi prova att köra samma NGINX-podd med en specifik användarkontext, till exempel `runAsUser: 2000` .

Skapa en fil med namnet `nginx-unprivileged-nonroot.yaml` och klistra in följande YAML-manifest:

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

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Podden kan inte schemaläggas, som du ser i följande exempelutdata:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Podden når inte schemaläggningsfasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="create-a-custom-pod-security-policy"></a>Skapa en anpassad poddsäkerhetsprincip

Nu när du har sett beteendet för standardsäkerhetsprinciperna för poddar kan vi ge *icke-användare* ett sätt att schemalägga poddar.

Nu ska vi skapa en princip för att avvisa poddar som begär privilegierad åtkomst. Andra alternativ, till *exempel runAsUser* eller *tillåtna volymer,* är inte uttryckligen begränsade. Den här typen av princip nekar en begäran om privilegierad åtkomst, men annars kan klustret köra de begärda poddarna.

Skapa en fil med namnet `psp-deny-privileged.yaml` och klistra in följande YAML-manifest:

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

Skapa principen med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Om du vill visa de principer som är tillgängliga använder [du kommandot kubectl get psp,][kubectl-get] som du ser i följande exempel. Jämför *principen psp-deny-privileged med* principen för standardprivilegier som framtvingas i föregående exempel för att skapa en podd.  Endast användningen av *PRIV-eskalering* nekas av din princip. Det finns inga begränsningar för användaren eller gruppen för *principen psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Tillåt att användarkontot använder den anpassade poddsäkerhetsprincipen

I föregående steg skapade du en säkerhetsprincip för poddar för att avvisa poddar som begär privilegierad åtkomst. Om du vill tillåta att principen används skapar du en *roll* eller en *ClusterRole*. Sedan associerar du en av dessa roller med hjälp av *en RoleBinding* *eller ClusterRoleBinding*.

I det här exemplet skapar du en  ClusterRole som gör att du kan använda *principen psp-deny-privileged* som skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrole.yaml` och klistra in följande YAML-manifest:

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

Skapa ClusterRole med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Skapa nu en ClusterRoleBinding för att använda ClusterRole som skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrolebinding.yaml` och klistra in följande YAML-manifest:

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

Skapa en ClusterRoleBinding med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> I det första steget i den här artikeln aktiverades funktionen poddsäkerhetsprincip i AKS-klustret. Den rekommenderade praxis var att endast aktivera säkerhetsprincipfunktionen för poddar när du har definierat dina egna principer. Det här är fasen där du aktiverar funktionen poddsäkerhetsprincip. En eller flera anpassade principer har definierats och användarkonton har associerats med dessa principer. Nu kan du aktivera säkerhetsprincipfunktionen för poddar på ett säkert sätt och minimera problem som orsakas av standardprinciperna.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testa att skapa en podd utan privilegier igen

Nu när din anpassade poddsäkerhetsprincip har tillämpats och en bindning för användarkontot att använda principen ska vi försöka skapa en podd utan privilegier igen. Använd samma `nginx-privileged.yaml` manifest för att skapa podden med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Podden har schemalagts. När du kontrollerar statusen för podden med kommandot [kubectl get pods][kubectl-get] körs *podden*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Det här exemplet visar hur du kan skapa anpassade poddsäkerhetsprinciper för att definiera åtkomst till AKS-klustret för olika användare eller grupper. AKS-standardprinciperna ger nära kontroller för vilka poddar som kan köras, så skapa egna anpassade principer för att sedan korrekt definiera de begränsningar du behöver.

Ta bort NGINX-podden utan privilegier med kommandot [kubectl delete][kubectl-delete] och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera poddsäkerhetsprincipen använder [du kommandot az aks update][az-aks-update] igen. I följande exempel inaktiveras podsäkerhetsprincipen på klusternamnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup:*

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

Ta bort säkerhetsprincipen med [kommandot kubectl delete][kubectl-delete] och ange namnet på ditt YAML-manifest:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Ta slutligen bort *namnområdet psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en poddsäkerhetsprincip för att förhindra användningen av privilegierad åtkomst. Det finns många funktioner som en princip kan tillämpa, till exempel volymtyp eller RunAs-användare. Mer information om tillgängliga alternativ finns i referensdokumenten för [Kubernetes pod security policy.][kubernetes-policy-reference]

Mer information om hur du begränsar poddnätverkstrafik finns [i Skydda trafik mellan poddar med nätverksprinciper i AKS.][network-policies]

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
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
