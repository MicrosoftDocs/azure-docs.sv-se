---
title: Skydda poddar med Azure Policy i Azure Kubernetes service (AKS)
description: Lär dig hur du skyddar poddar med Azure Policy på Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/22/2020
ms.openlocfilehash: 34f2bfe346d7163a254e2ccecd1d7ef63ddb4194
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092626"
---
# <a name="secure-pods-with-azure-policy"></a>Skydda poddar med Azure Policy

För att förbättra säkerheten för ditt AKS-kluster kan du kontrol lera vilka funktioner poddar beviljas och om allt körs mot företagets policy. Den här åtkomsten definieras via inbyggda principer som tillhandahålls av [Azure policy-tillägget för AKS][kubernetes-policy-reference]. Genom att tillhandahålla ytterligare kontroll över säkerhets aspekterna av din POD-specifikation, som rot privilegier, möjliggör striktare säkerhets kontroll och insyn i vad som distribueras i klustret. Om en POD inte uppfyller de villkor som anges i principen kan Azure Policy förhindra att Pod startar eller flaggar en överträdelse. Den här artikeln visar hur du använder Azure Policy för att begränsa distributionen av poddar i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Installera Azure Policy-tillägget för AKS

För att skydda AKS-poddar via Azure Policy måste du installera Azure Policy-tillägget för AKS i ett AKS-kluster. Följ de här [stegen för att installera Azure policy-tillägget](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

Det här dokumentet förutsätter att du har följande, som distribueras i den genom gång som är länkad ovan.

* Registrera `Microsoft.ContainerService` och- `Microsoft.PolicyInsights` resurs leverantörer med `az provider register`
* Azure CLI 2,12 eller senare
* Ett AKS-kluster i en version av 1,15 eller senare installerat med Azure Policy-tillägget

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Översikt över att skydda poddar med Azure Policy för AKS

>[!NOTE]
> Det här dokumentet innehåller information om hur du använder Azure Policy för att skydda poddar, vilket är efterföljaren till [funktionen Kubernetes Pod säkerhets princip i för hands versionen](use-pod-security-policies.md).
> **Både Pod säkerhets princip (för hands version) och Azure Policy-tillägget för AKS kan inte aktive ras samtidigt.**
> 
> Om du installerar Azure Policy-tillägget i ett kluster med säkerhets principen Pod aktive rad, [följer du dessa steg för att inaktivera Pod säkerhets princip](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

I ett AKS-kluster används en åtkomst kontroll för att avlyssna begär anden till API-servern när en resurs ska skapas och uppdateras. Kontrollanten kan sedan *Verifiera* resurs förfrågan mot en uppsättning regler på om den ska skapas eller inte.

Tidigare har funktionen [Pod säkerhets princip (för hands version)](use-pod-security-policies.md) Aktiver ATS via Kubernetes-projektet för att begränsa vilka poddar som kan distribueras.

Genom att använda Azure Policy-tillägget kan ett AKS-kluster använda inbyggda Azure-principer, vilket skyddar poddar och andra Kubernetes-resurser som liknar Pod-säkerhetsprincipen tidigare. Azure Policy-tillägget för AKS installerar en hanterad instans av [Gatekeeper](https://github.com/open-policy-agent/gatekeeper), en verifierar en styrenhet för åtkomst kontroll. Azure Policy för Kubernetes bygger på öppna policy-agenten med öppen källkod, som förlitar sig på [Rego policy-språket](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Det här dokumentet innehåller information om hur du använder Azure Policy för att skydda poddar i ett AKS-kluster och hur du kan migrera från Pod säkerhets principer (för hands version).

## <a name="limitations"></a>Begränsningar

Följande allmänna begränsningar gäller för Azure Policy-tillägget för Kubernetes-kluster:

- Azure Policy-tillägg för Kubernetes stöds på Kubernetes version **1,14** eller senare.
- Azure Policy-tillägg för Kubernetes kan bara distribueras till Linux-nodkonfigurationer
- Endast inbyggda princip definitioner stöds
- Maximalt antal icke-kompatibla poster per princip per kluster: **500**
- Maximalt antal icke-kompatibla poster per prenumeration: **1 000 000**
- Installationer av Gatekeeper utanför Azure Policy-tillägget stöds inte. Avinstallera alla komponenter som installeras av en tidigare Gatekeeper-installation innan du aktiverar Azure Policy-tillägget.
- [Orsaker till att inkompatibilitet](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) inte är tillgängligt för detta [resurs leverantörs läge](../governance/policy/concepts/definition-structure.md#resource-provider-modes)

Följande begränsningar gäller endast för Azure Policy-tillägget för AKS:

- [AKS Pod-säkerhetsprincipen (för hands version)](use-pod-security-policies.md) och Azure policy-tillägget för AKS kan inte både aktive ras. 
- Namn områden som undantas automatiskt av Azure Policy tillägg för utvärdering: _Kube-system_, _Gatekeeper-system_ och _AKS-Periscope_. Om du använder Calico nätverks princip med Kubernetes version 1,20 och senare utesluts 2 fler namn områden automatiskt, som är _Calico-system_ och _Tiger-operator_.

### <a name="recommendations"></a>Rekommendationer

Följande är allmänna rekommendationer för att använda Azure Policy-tillägget:

- Azure Policy tillägget kräver 3 Gatekeeper-komponenter för att köra: 1 audit Pod och 2 webhook Pod-repliker. Dessa komponenter förbrukar mer resurser som antalet Kubernetes-resurser och princip tilldelningar ökar i klustret som kräver gransknings-och tvångs åtgärder.

  - För mindre än 500 poddar i ett enda kluster med högst 20 begränsningar: 2 virtuella processorer och 350 MB minne per komponent.
  - För över 500 poddar i ett enda kluster med högst 40 begränsningar: 3 virtuella processorer och 600 MB minne per komponent.

Följande rekommendation gäller endast för AKS och Azure Policy-tillägget:

- Använd systemnode-poolen med en `CriticalAddonsOnly` utsmak för att schemalägga Gatekeeper-poddar. Mer information finns i [använda system Node Pools](use-system-pools.md#system-and-user-node-pools).
- Skydda utgående trafik från AKS-kluster. Mer information finns i [kontroll av utgående trafik för klusternoder](limit-egress-traffic.md).
- Om klustret har `aad-pod-identity` Aktiver ATS ändrar NMI-poddar noderna program varan iptables för att avlyssna anrop till Azure instance metadata-slutpunkten. Den här konfigurationen innebär att alla begär Anden som görs till metadata-slutpunkten fångas upp av NMI även om Pod inte använder `aad-pod-identity` . AzurePodIdentityException CRD kan konfigureras för att informera om `aad-pod-identity` att förfrågningar till slut punkten för metadata från en pod som matchar etiketter som definierats i CRD ska vara proxy utan bearbetning i NMI. Systemets poddar med `kubernetes.azure.com/managedby: aks` etikett i _Kube-systemets_ namnrymd ska undantas i `aad-pod-identity` genom att konfigurera AzurePodIdentityException-CRD. Mer information finns i [inaktivera AAD-Pod-Identity för en specifik POD eller ett program](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Om du vill konfigurera ett undantag installerar du [yaml MIC-Exception](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

Azure Policy-tillägget kräver att processor-och minnes resurser körs. Dessa krav ökar när storleken på ett kluster ökar. Se [Azure Policy rekommendationer][policy-recommendations] för att få allmän vägledning om hur du använder Azure policy-tillägget.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Azure-principer för att skydda Kubernetes-poddar

När du har installerat Azure Policy-tillägget tillämpas inga principer som standard.

Det finns 11 inbyggda enskilda Azure-principer och två inbyggda initiativ som särskilt skyddar poddar i ett AKS-kluster.
Varje princip kan anpassas med en funktion. En fullständig lista över [AKS-principer och deras effekter som stöds finns här][policy-samples]. Läs mer om [Azure policy effekter](../governance/policy/concepts/effects.md).

Azure-principer kan tillämpas på hanterings grupp, prenumeration eller resurs grupps nivå. När du tilldelar en princip på resurs grupps nivå ser du till att mål AKS klustrets resurs grupp har valts inom omfånget för principen. Varje kluster i det tilldelade omfånget med Azure Policy tillägget installerat är inom omfånget för principen.

Om du använder [Pod säkerhets princip (förhands granskning) ](use-pod-security-policies.md)lär du dig hur du [migrerar till Azure policy och om andra beteende skillnader](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Inbyggda princip initiativ

Ett initiativ i Azure Policy är en samling princip definitioner som är skräddarsydda för att uppnå ett singular överåtgående mål. Användningen av initiativ kan förenkla hanteringen och tilldelningen av principer i AKS-kluster. Ett initiativ finns som ett enda objekt, Läs mer om [Azure policy initiativ](../governance/policy/overview.md#initiative-definition).

Azure Policy för Kubernetes erbjuder två inbyggda initiativ som skyddar poddar, [bas linje](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) och [begränsad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Båda de inbyggda initiativen skapas från definitioner som används i [Pod säkerhets policy från Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Pod säkerhets princip kontroll](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy definitions länk| [Bas linje initiativ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Begränsat initiativ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Tillåt inte körning av privilegierade behållare|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Ja | Ja
|Tillåt inte delad användning av värd namn områden|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Ja | Ja
|Begränsa all användning av värd nätverk och portar|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Ja | Ja
|Begränsa användningen av värd fil systemet|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Ja | Ja
|Begränsa Linux-funktioner till [standard uppsättningen](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Ja | Ja
|Begränsa användningen av definierade volym typer|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Ja – tillåtna volym typer är `configMap` ,,, `emptyDir` `projected` `downwardAPI` , `persistentVolumeClaim`|
|Eskalering av privilegier till rot|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|Begränsa behållarens användar-och grupp-ID|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|Begränsa tilldelningen av en FSGroup som äger Pod-volymer|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Ja – tillåtna regler är `runAsUser: mustRunAsNonRoot` , `supplementalGroup: mustRunAs 1:65536` , `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Kräver seccomp-profil|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Ja, allowedProfiles är * `docker/default` eller `runtime/default` |

\* Docker/standard är föråldrad i Kubernetes sedan v 1.11

### <a name="additional-optional-policies"></a>Ytterligare valfria principer

Det finns ytterligare Azure-principer som kan tillämpas på ett och samma plats utanför ett initiativ. Överväg att lägga till dessa principer förutom initiativ om dina krav inte uppfylls av de inbyggda initiativen.

|[Pod säkerhets princip kontroll](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy definitions länk| Använd utöver bas linje initiativ | Tillämpa utöver begränsat initiativ |
|---|---|---|---|
|Definiera AppArmor-profilen som används av behållare|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Valfritt | Valfritt |
|Tillåt monteringar som inte är skrivskyddade|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Valfritt | Valfritt |
|Begränsa till vissa FlexVolume-drivrutiner|[Offentligt moln](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Valfritt – Använd om du bara vill begränsa FlexVolume-drivrutiner, men inte andra som anges av "begränsa användningen av definierade volym typer" | Ej tillämpligt – det begränsade initiativet inkluderar "begränsa användningen av definierade volym typer", vilket inte tillåter alla FlexVolume-drivrutiner |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Inbyggda principer för hanterade AKS-kluster stöds inte

> [!NOTE]
> Följande tre principer **stöds inte i AKS** på grund av anpassnings aspekter som hanteras och skyddas av AKS som en hanterad tjänst. Dessa principer är särskilt utformade för Azure Arc-anslutna kluster med ohanterade kontroll plan.

|[Pod säkerhets princip kontroll](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Definiera en behållares anpassade SELinux-kontext|
|Begränsa sysctl-profilen som används av behållare|
|Standard proc Mount-typer definieras för att minska attack ytan|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Undantag för namn område

> [!WARNING]
> Poddar i admin-namnrymder som Kube-system måste köras för att ett kluster ska förbli felfritt. om du tar bort ett obligatoriskt namn område från listan över undantagna fördefinierade namn områden kan det uppstå princip överträdelser på grund av en nödvändig system pod.

AKS kräver att system poddar körs på ett kluster för att tillhandahålla kritiska tjänster, till exempel DNS-matchning. Principer som begränsar Pod-funktioner kan påverka stabiliteten i systemet pod. Därför undantas följande namn rymder **från princip utvärderingen vid begäran om att skapa, uppdatera och granska principer**. Detta innebär att nya distributioner till dessa namn områden exkluderas från Azure-principer.

1. kube-system
1. Gatekeeper-system
1. azure-arc
1. AKS – Periscope

Ytterligare anpassade namn områden kan undantas från utvärdering vid skapande, uppdatering och granskning. Dessa undantag ska användas om du har specialiserade poddar som körs i ett sanktionerat namn område och vill undvika att utlösa gransknings överträdelser.

## <a name="apply-the-baseline-initiative"></a>Använd bas linje initiativ

> [!TIP]
> Alla principer används som standard för en gransknings funktion. Effekter kan uppdateras till neka när som helst genom att Azure Policy.

För att tillämpa bas linje initiativet kan vi tilldela genom Azure Portal.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Följ [den här länken till Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) för att granska Pod säkerhets bas linje initiativ
1. Ange **omfånget** till prenumerations nivån eller endast resurs gruppen som innehåller AKS-kluster med Azure policy tillägget aktiverat
1. Välj sidan **parametrar** och uppdatera **resultatet** från `audit` till `deny` för att blockera nya distributioner som bryter mot bas linje initiativ
1. Lägg till ytterligare namn rymder som ska undantas från utvärdering under skapande, uppdatering och granskning, så [tvingas vissa namn områden att undantas från princip utvärderingen.](#namespace-exclusion) 
 ![ uppdaterings funktion](media/use-pod-security-on-azure-policy/update-effect.png)
1. Välj **Granska + skapa** för att skicka principerna

Bekräfta att principer tillämpas på klustret genom att köra `kubectl get constrainttemplates` .

> [!NOTE]
> Det kan ta [upp till 20 minuter för principer att synkronisera](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) i varje kluster.

Utdata bör likna följande:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Verifiera avvisande av en privilegie rad Pod

Vi börjar med att testa vad som händer när du schemalägger en POD med säkerhets kontexten för `privileged: true` . Den här säkerhets kontexten eskalerar Pod privilegier. Bas linje initiativet tillåter inte privilegie rad poddar, så begäran kommer att nekas, vilket leder till att distributionen avvisas.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f nginx-privileged.yaml
```

Som förväntat kan Pod inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en ej privilegie rad Pod

I föregående exempel försökte behållar avbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av initiativet för bas linje policyn, så Pod kan inte starta. Nu ska vi prova att köra samma NGINX-Pod utan privilegie rad åtkomst.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande yaml-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod har schemalagts. När du kontrollerar status för Pod med kommandot [kubectl get poddar][kubectl-get] *körs* pod:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

I det här exemplet visas ett bas linje initiativ som endast påverkar distributioner som bryter mot principer i samlingen. Tillåtna distributioner fortsätter att fungera.

Ta bort NGINX Pod med kommandot [kubectl Delete][kubectl-delete] och ange namnet på din yaml-manifest:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Inaktivera principer och Azure Policy-tillägget

Så här tar du bort bas linje initiativ:

1. Navigera till princip fönstret på Azure Portal
1. Välj **tilldelningar** i det vänstra fönstret
1. Klicka på "..." bredvid bas linje profilen
1. Välj Ta bort tilldelning

![Ta bort tilldelning](media/use-pod-security-on-azure-policy/delete-assignment.png)

Om du vill inaktivera Azure Policy-tillägget använder du kommandot [AZ AKS Disable-addons][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Lär dig hur du tar bort [Azure policy-tillägget från Azure Portal](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrera från Kubernetes Pod säkerhets princip till Azure Policy

För att migrera från Pod säkerhets princip måste du vidta följande åtgärder i ett kluster.

1. [Inaktivera Pod säkerhets princip](use-pod-security-policies.md#clean-up-resources) på klustret
1. Aktivera [Azure policy-tillägget][kubernetes-policy-reference]
1. Aktivera önskade Azure-principer från [Tillgängliga inbyggda principer][policy-samples]

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

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du använder en Azure-princip som begränsar privilegie rad poddar från att distribueras för att förhindra användning av privilegie rad åtkomst. Det finns många principer som kan tillämpas, till exempel principer som begränsar användningen av volymer. Mer information om tillgängliga alternativ finns i [referens dokumenten Azure policy for Kubernetes][kubernetes-policy-reference].

Mer information om hur du begränsar Pod nätverks trafik finns i [skydda trafik mellan poddar med hjälp av nätverks principer i AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://azure.github.io/aad-pod-identity/docs/configure/application_exception

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
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
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete