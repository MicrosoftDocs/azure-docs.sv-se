---
title: Lär dig Azure Policy för Kubernetes
description: Lär dig hur Azure Policy använder Rego och öppna princip agenten för att hantera kluster som kör Kubernetes i Azure eller lokalt.
ms.date: 03/22/2021
ms.topic: conceptual
ms.openlocfilehash: 60ffcfac688eb40f47efefb74f79d27a2cb82446
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868162"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Förstå Azure Policy för Kubernetes-kluster

Azure Policy utökar [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, en OPA- _webhook_ för [Open policy-agenten](https://www.openpolicyagent.org/) () för att tillämpa i skala och skydda dina kluster på ett centraliserat, konsekvent sätt. Azure Policy gör det möjligt att hantera och rapportera om kompatibilitetstillstånd för dina Kubernetes-kluster från en plats. Tillägget fungerar med följande funktioner:

- Kontrollerar Azure Policy tjänsten för princip tilldelningar i klustret.
- Distribuerar princip definitioner till klustret som [villkors mall](https://github.com/open-policy-agent/gatekeeper#constraint-templates) och anpassade resurser för [begränsning](https://github.com/open-policy-agent/gatekeeper#constraints) .
- Rapporterar gransknings-och kompatibilitetsinformation tillbaka till Azure Policy-tjänsten.

Azure Policy för Kubernetes har stöd för följande kluster miljöer:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc-aktiverade Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS-motor](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Tilläggen för AKS-motorn och Arc-aktiverade Kubernetes är i för **hands version**. Azure Policy för Kubernetes stöder endast Linux-nodkonfigurationer och inbyggda princip definitioner. Inbyggda princip definitioner finns i kategorin **Kubernetes** . De begränsade definitionerna för för hands versions princip med **EnforceOPAConstraint** och **EnforceRegoPolicy** och den relaterade **Kubernetes tjänste** kategorin är _inaktuella_. Använd i stället effekterna _granskning_ och _neka_ med resurs leverantörs läge `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Översikt

Utför följande åtgärder för att aktivera och använda Azure Policy med ditt Kubernetes-kluster:

1. Konfigurera ditt Kubernetes-kluster och installera tillägget:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc-aktiverade Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-motor](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Vanliga problem med installationen finns i [Felsöka-Azure policy-tillägg](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Förstå Azure Policy språk för Kubernetes](#policy-language)

1. [Tilldela en inbyggd definition till ditt Kubernetes-kluster](#assign-a-built-in-policy-definition)

1. [Vänta på verifiering](#policy-evaluation)

## <a name="limitations"></a>Begränsningar

Följande allmänna begränsningar gäller för Azure Policy-tillägget för Kubernetes-kluster:

- Azure Policy-tillägg för Kubernetes stöds på Kubernetes version **1,14** eller senare.
- Azure Policy-tillägg för Kubernetes kan bara distribueras till Linux-nodkonfigurationer
- Endast inbyggda princip definitioner stöds
- Maximalt antal icke-kompatibla poster per princip per kluster: **500**
- Maximalt antal icke-kompatibla poster per prenumeration: **1 000 000**
- Installationer av Gatekeeper utanför Azure Policy-tillägget stöds inte. Avinstallera alla komponenter som installeras av en tidigare Gatekeeper-installation innan du aktiverar Azure Policy-tillägget.
- [Anledningen till att icke-kompatibilitet](../how-to/determine-non-compliance.md#compliance-reasons) inte är tillgängliga för `Microsoft.Kubernetes.Data` 
   [resurs leverantörs läge](./definition-structure.md#resource-provider-modes). Använd [komponent information](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Undantag](./exemption-structure.md) stöds inte för [resurs leverantörs lägen](./definition-structure.md#resource-provider-modes).

Följande begränsningar gäller endast för Azure Policy-tillägget för AKS:

- [AKS Pod-säkerhetsprincipen](../../../aks/use-pod-security-policies.md) och Azure policy-tillägget för AKS kan inte både aktive ras. Mer information finns i [säkerhets begränsning för AKS-Pod](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Namn områden som undantas automatiskt av Azure Policy tillägg för utvärdering: _Kube-system_, _Gatekeeper-system_ och _AKS-Periscope_.

## <a name="recommendations"></a>Rekommendationer

Följande är allmänna rekommendationer för att använda Azure Policy-tillägget:

- Azure Policy-tillägget kräver att tre Gatekeeper-komponenter körs: 1 audit Pod och 2 webhook Pod-repliker. Dessa komponenter förbrukar mer resurser som antalet Kubernetes-resurser och princip tilldelningar ökar i klustret, vilket kräver gransknings-och tvångs åtgärder.

  - För färre än 500-poddar i ett enda kluster med högst 20 begränsningar: 2 virtuella processorer och 350 MB minne per komponent.
  - För över 500 poddar i ett enda kluster med högst 40 begränsningar: 3 virtuella processorer och 600 MB minne per komponent.

- Windows-poddar [stöder inte säkerhets kontexter](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Därför går det inte att eskalera några av de Azure Policy definitionerna, t. ex. att inte tillåta rot privilegier i Windows-poddar och gäller endast för Linux-poddar.

Följande rekommendation gäller endast för AKS och Azure Policy-tillägget:

- Använd systemnode-poolen med en `CriticalAddonsOnly` utsmak för att schemalägga Gatekeeper-poddar. Mer information finns i [använda system Node Pools](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Skydda utgående trafik från AKS-kluster. Mer information finns i [kontroll av utgående trafik för klusternoder](../../../aks/limit-egress-traffic.md).
- Om klustret har `aad-pod-identity` Aktiver ATS ändrar NMI-poddar noderna program varan iptables för att avlyssna anrop till Azure instance metadata-slutpunkten. Den här konfigurationen innebär att alla begär Anden som görs till metadata-slutpunkten fångas upp av NMI även om Pod inte använder `aad-pod-identity` . AzurePodIdentityException CRD kan konfigureras för att informera om `aad-pod-identity` att förfrågningar till slut punkten för metadata från en pod som matchar etiketter som definierats i CRD ska vara proxy utan bearbetning i NMI. Systemets poddar med `kubernetes.azure.com/managedby: aks` etikett i _Kube-systemets_ namnrymd ska undantas i `aad-pod-identity` genom att konfigurera AzurePodIdentityException-CRD. Mer information finns i [inaktivera AAD-Pod-Identity för en specifik POD eller ett program](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Om du vill konfigurera ett undantag installerar du [yaml MIC-Exception](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Installera Azure Policy tillägg för AKS

Innan du installerar Azure Policy tillägg eller aktiverar någon av tjänst funktionerna måste prenumerationen aktivera **Microsoft. PolicyInsights** -resurs leverantörer.

1. Du behöver Azure CLI-versionen 2.12.0 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Registrera resurs leverantörer och för hands versions funktioner.

   - Azure Portal:

     Registrera **Microsoft. PolicyInsights** -resurs leverantörer. Anvisningar finns i [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Om begränsade definitioner för för hands versions principer har installerats tar du bort tillägget med knappen **inaktivera** i AKS-klustret på sidan **principer** .

1. AKS-klustret måste vara version _1,14_ eller högre. Använd följande skript för att verifiera din AKS-kluster version:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installera version _2.12.0_ eller högre av Azure CLI. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

När ovanstående nödvändiga steg har slutförts installerar du Azure Policy-tillägget i det AKS-kluster som du vill hantera.

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att välja **alla tjänster** och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ett av dina AKS-kluster.

  1. Välj **principer** på vänster sida av Kubernetes-tjänst sidan.

  1. På huvud sidan väljer du knappen **aktivera tillägg** .

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Om knappen **Inaktivera tillägg** är aktive rad och ett meddelande om migrerings varning v2 visas, är v1-tillägget installerat och måste tas bort innan du tilldelar v2-princip definitioner. Det _inaktuella_ v1-tillägget ersätts automatiskt med v2-tillägget från 24 augusti
     > 2020. Nya v2-versioner av princip definitionerna måste sedan tilldelas. Följ dessa steg för att uppgradera nu:
     >
     > 1. Verifiera att ditt AKS-kluster har v1-tillägget installerat genom att gå till sidan **principer** i ditt AKS-kluster och har "det aktuella klustret använder Azure policy tillägg v1..." meddelande.
     > 1. [Ta bort tillägget](#remove-the-add-on-from-aks).
     > 1. Välj knappen **aktivera tillägg** för att installera v2-versionen av tillägget.
     > 1. [Tilldela v2-versioner av dina v1-inbyggda princip definitioner](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Kör följande kommando för att verifiera att installationen av tillägget lyckades och att poddar för _Azure-principen_ och _Gatekeeper_ körs:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Till sist kontrollerar du att det senaste tillägget har installerats genom att köra det här Azure CLI-kommandot och Ersätt `<rg>` med namnet på din resurs grupp och `<cluster-name>` namnet på ditt AKS-kluster: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Resultatet bör se ut som i följande utdata-och **config. version** ska vara `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Installera Azure Policy tillägg för Azure Arc-aktiverade Kubernetes (för hands version)

Innan du installerar Azure Policy tillägg eller aktiverar någon av tjänst funktionerna måste prenumerationen aktivera **Microsoft. PolicyInsights** Resource Provider och skapa en roll tilldelning för kluster tjänstens huvud namn.

1. Du behöver Azure CLI-versionen 2.12.0 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Om du vill aktivera resurs leverantören följer du stegen i [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller kör antingen Azure CLI-eller Azure PowerShell-kommandot:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes-klustret måste vara version _1,14_ eller högre.

1. Installera [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Ditt Kubernetes-kluster har Aktiver ATS för Azure-bågen. Mer information finns i [onboarding a Kubernetes Cluster to Azure Arc](../../../azure-arc/kubernetes/connect-cluster.md).

1. Ha det fullständigt kvalificerade Azure-resurs-ID: t för Azure Arc-Kubernetes-klustret.

1. Öppna portar för tillägget. Azure Policy-tillägget använder dessa domäner och portar för att hämta princip definitioner och tilldelningar och rapportera kompatibilitet för klustret tillbaka till Azure Policy.

   |Domain |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Tilldela roll tilldelningen princip insikts data skrivare (förhands granskning) till Azure Arc-Kubernetes klustret. Ersätt `<subscriptionId>` med ditt prenumerations-ID `<rg>` med Azure Arc-aktiverade Kubernetes-klustrets resurs grupp och `<clusterName>` med namnet på det Azure Arc-aktiverade Kubernetes-klustret. Håll koll på de returnerade värdena för _appId_, _Password_ och _Tenant_ för installations stegen.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Exempel på utdata från ovanstående kommandon:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

När ovanstående nödvändiga steg har slutförts installerar du Azure Policy-tillägget i ditt Azure Arc-aktiverade Kubernetes-kluster:

1. Lägg till Azure Policy-lagrings platsen till Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installera Azure Policy-tillägget med Helm-diagram:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Mer information om vilka Helm-diagram som installeras finns i [Azure policy-tillägget Helm Chart Definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) på GitHub.

Kör följande kommando för att verifiera att installationen av tillägget lyckades och att poddar för _Azure-principen_ och _Gatekeeper_ körs:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Installera Azure Policy-tillägget för AKS-motorn (för hands version)

Innan du installerar Azure Policy tillägg eller aktiverar någon av tjänst funktionerna måste prenumerationen aktivera **Microsoft. PolicyInsights** Resource Provider och skapa en roll tilldelning för kluster tjänstens huvud namn.

1. Du behöver Azure CLI-versionen 2.0.62 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Om du vill aktivera resurs leverantören följer du stegen i [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller kör antingen Azure CLI-eller Azure PowerShell-kommandot:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Skapa en roll tilldelning för kluster tjänstens huvud namn.

   - Om du inte känner till app-ID för kluster tjänstens huvud namn, kan du se det med följande kommando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Tilldela roll tilldelningen princip insikts data skrivare (förhands granskning) till appens huvud namn för kluster tjänst (värdet _aadClientID_ från föregående steg) med Azure CLI. Ersätt `<subscriptionId>` med ditt prenumerations-ID och `<aks engine cluster resource group>` med resurs gruppen som KUBERNETES-AKS--klustret är i.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

När ovanstående nödvändiga steg har slutförts, installerar du Azure Policy-tillägget. Installationen kan ske under skapandet eller uppdaterings cykeln för en AKS-motor eller som en oberoende åtgärd i ett befintligt kluster.

- Installera vid skapande eller uppdaterings cykel

  Om du vill aktivera Azure Policy-tillägget under skapandet av ett nytt självhanterat kluster eller som en uppdatering av ett befintligt kluster, ska du ta med egenskaps kluster definitionen [addon](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) för AKS-motorn.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Mer information om finns i kluster definitionen för den externa guiden [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installera i befintligt kluster med Helm-diagram

  Använd följande steg för att förbereda klustret och installera tillägget:

  1. Installera [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Lägg till Azure Policy lagrings platsen till Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Mer information finns i [Helm Chart – snabb starts guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installera tillägget med ett Helm-diagram. Ersätt `<subscriptionId>` med ditt prenumerations-ID och `<aks engine cluster resource group>` med resurs gruppen som KUBERNETES-AKS--klustret är i.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Mer information om vilka Helm-diagram som installeras finns i [Azure policy-tillägget Helm Chart Definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) på GitHub.

     > [!NOTE]
     > På grund av förhållandet mellan Azure Policy-tillägg och resurs grupps-ID: t har Azure Policy endast stöd för ett AKS motor kluster för varje resurs grupp.

Kör följande kommando för att verifiera att installationen av tillägget lyckades och att poddar för _Azure-principen_ och _Gatekeeper_ körs:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Princip språk

Azure Policy språk strukturen för att hantera Kubernetes följer de befintliga princip definitionerna. Med ett [resurs leverantörs läge](./definition-structure.md#resource-provider-modes) av `Microsoft.Kubernetes.Data` används effekterna [granskning](./effects.md#audit) och [neka](./effects.md#deny) för att hantera dina Kubernetes-kluster. _Audit_ och _Deny_ måste tillhandahålla **information** egenskaper som är särskilt för att arbeta med [OPA constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) och Gatekeeper v3.

Som en del av egenskaperna _details. constraintTemplate_ och _information. constraint_ i princip definitionen, Azure policy skickar URI: erna för dessa [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att verifiera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy att du kan återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig rapporterings upplevelse för moln efterlevnad. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Tilldela en inbyggd principdefinition

Om du vill tilldela en princip definition till ditt Kubernetes-kluster måste du ha tilldelats lämplig roll tilldelnings åtgärd för Azure-rollbaserad åtkomst kontroll (Azure RBAC). De inbyggda rollerna **resurs princip deltagare** och **ägare** i Azure är de här åtgärderna. Mer information finns i [Azure RBAC-behörigheter i Azure policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Hitta de inbyggda princip definitionerna för att hantera klustret med hjälp av Azure Portal med följande steg:

1. Starta tjänsten Azure Policy i Azure Portal. Välj **alla tjänster** i den vänstra rutan och Sök sedan efter och välj **princip**.

1. I den vänstra rutan på sidan Azure Policy väljer du **definitioner**.

1. I list rutan Kategori använder du **Markera alla** för att ta bort filtret och väljer sedan **Kubernetes**.

1. Välj princip definitionen och välj sedan knappen **tilldela** .

1. Ange **omfattningen** för hanterings gruppen, prenumerationen eller resurs gruppen för det Kubernetes-kluster där princip tilldelningen ska gälla.

   > [!NOTE]
   > När du tilldelar en Azure Policy för Kubernetes-definitionen måste **omfånget** innehålla kluster resursen. För ett AKS-motor kluster måste **omfattningen** vara resurs gruppen för klustret.

1. Ge princip tilldelningen ett **namn** och en **Beskrivning** som du kan använda för att identifiera det enkelt.

1. Ange att [principen ska tvingas](./assignment-structure.md#enforcement-mode) till något av värdena nedan.

   - **Aktive rad** – tillämpa principen på klustret. Kubernetes-begäranden med överträdelser nekas.

   - **Disabled** – Genomdriv inte principen i klustret. Kubernetes-begäranden med överträdelser nekas inte. Resultat av utvärdering av efterlevnad är fortfarande tillgängliga. När du utvärderar nya princip definitioner för att köra kluster är alternativet _inaktiverat_ användbart för att testa princip definitionen som begär Anden om att överträdelser inte nekas.

1. Välj **Nästa**.

1. Ange **parameter värden**

   - Om du vill undanta Kubernetes-namnrymder från princip utvärdering anger du listan över namn områden i undantag för parameter **namn område**. Vi rekommenderar att du undantar: _Kube-system_, _Gatekeeper-system_ och _Azure-Arc_.

1. Välj **Granska + skapa**.

Du kan också använda snabb start för att [tilldela en princip-Portal](../assign-policy-portal.md) för att hitta och tilldela en Kubernetes-princip. Sök efter en Kubernetes-princip definition i stället för exemplet på granska virtuella datorer.

> [!IMPORTANT]
> Inbyggda princip definitioner är tillgängliga för Kubernetes-kluster i kategorin **Kubernetes**. En lista över inbyggda princip definitioner finns i [Kubernetes-exempel](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Princip utvärdering

Tillägget checkar in med Azure Policy-tjänsten för ändringar i princip tilldelningarna var 15: e minut.
Under den här uppdaterings cykeln söker tillägget efter ändringar. Dessa ändringar genererar, uppdaterar eller tar bort villkoret och begränsningarna.

I ett Kubernetes-kluster, om ett namn område har någon av följande etiketter, nekas inte etablerings begär Anden med överträdelser. Resultat av utvärdering av efterlevnad är fortfarande tillgängliga.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> En kluster administratör kan ha behörighet att skapa och uppdatera constraint-mallar och villkors resurser som installeras av Azure Policy-tillägget, men dessa stöds inte eftersom manuella uppdateringar skrivs över. Gatekeeper fortsätter att utvärdera principer som fanns innan du installerade tillägget och tilldelar Azure Policy princip definitioner.

Var 15: e minut anropar tillägget för en fullständig genomsökning av klustret. Efter att ha samlat in information om den fullständiga genomsökningen och eventuella utvärderingar i real tid i samband med ändringar i klustret, rapporterar tillägget tillbaka resultatet till Azure Policy för att inkludera [information om kompatibilitet](../how-to/get-compliance-data.md) , t. ex. Azure policy tilldelning. Endast resultat för aktiva princip tilldelningar returneras under gransknings cykeln. Gransknings resultat kan också visas som [överträdelser](https://github.com/open-policy-agent/gatekeeper#audit) som anges i fältet status för den misslyckade begränsningen. Mer information om _icke-kompatibla_ resurser finns i [komponent information för resurs leverantörs lägen](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Varje Kompatibilitetsrapport i Azure Policy för dina Kubernetes-kluster inkluderar alla överträdelser inom de senaste 45 minuterna. Tidsstämpeln anger när ett fel inträffade.

Några andra överväganden:

- Om kluster prenumerationen har registrerats med Azure Security Center så används Azure Security Center Kubernetes-principer automatiskt på klustret.

- När en princip för att neka tillämpas på kluster med befintliga Kubernetes-resurser fortsätter alla befintliga resurser som inte är kompatibla med den nya principen att köras. När den icke-kompatibla resursen blir ombokad på en annan nod, blockerar gatekeepern resurs skapandet.

- När ett kluster har en princip för neka som verifierar resurser, ser inte användaren ett avvisnings meddelande när du skapar en distribution. Anta till exempel en Kubernetes-distribution som innehåller replicasets och poddar. När en användare kör `kubectl describe deployment $MY_DEPLOYMENT` , returnerar den inget avvisnings meddelande som en del av händelser. Men `kubectl describe replicasets.apps $MY_DEPLOYMENT` returnerar de händelser som är associerade med avvisande.

## <a name="logging"></a>Loggning

Som Kubernetes-styrenhet/-behållare behåller både _Azure-policyn_ och _Gatekeeper_ -poddar loggar i Kubernetes-klustret. Loggarna kan visas på sidan **Insights** i Kubernetes-klustret. Mer information finns i [övervaka din Kubernetes-kluster prestanda med Azure Monitor för behållare](../../../azure-monitor/containers/container-insights-analyze.md).

Om du vill visa tilläggs loggarna använder du `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Mer information finns i [fel söknings Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) i Gatekeeper-dokumentationen.

## <a name="troubleshooting-the-add-on"></a>Felsöka tillägget

Mer information om hur du felsöker tillägg för Kubernetes finns i [avsnittet Kubernetes](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) i artikeln Azure policy fel sökning.

## <a name="remove-the-add-on"></a>Ta bort tillägget

### <a name="remove-the-add-on-from-aks"></a>Ta bort tillägget från AKS

Om du vill ta bort Azure Policy-tillägget från AKS-klustret använder du antingen Azure Portal eller Azure CLI:

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att välja **alla tjänster** och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ditt AKS-kluster där du vill inaktivera Azure Policy-tillägget.

  1. Välj **principer** på vänster sida av Kubernetes-tjänst sidan.

  1. På huvud sidan väljer du knappen **Inaktivera tillägg** .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Ta bort tillägget från Azure Arc-aktiverade Kubernetes

Om du vill ta bort Azure Policy-tillägget och Gatekeeper från ditt Azure Arc-aktiverade Kubernetes-kluster kör du följande Helm-kommando:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Ta bort tillägget från AKS-motorn

Om du vill ta bort Azure Policy-tillägget och Gatekeeper från ditt AKS-motor kluster använder du metoden som överensstämmer med hur tillägget installerades:

- Om det är installerat genom att ange egenskapen **addons** i kluster definitionen för AKS-motorn:

  Distribuera om kluster definitionen till AKS-motorn när du har ändrat egenskapen **addons** för _Azure-policyn_ till false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Mer information finns i [AKS-motorn – inaktivera Azure policy tillägg](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Om det installeras med Helm-diagram kör du följande Helm-kommando:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostikdata som samlats in av Azure Policy-tillägget

Azure Policy-tillägget för Kubernetes samlar in begränsade kluster diagnostikdata. Dessa diagnostikdata är viktiga tekniska data relaterade till program vara och prestanda. Den används på följande sätt:

- Behåll Azure Policy-tillägget uppdaterat
- Behåll Azure Policy-tillägget säkert, tillförlitligt, presterande
- Förbättra Azure Policy-tillägg genom den sammanställda analysen av användningen av tillägget

Informationen som samlas in av tillägget är inte personlig information. Följande information samlas in för närvarande:

- Azure Policy tilläggs agent version
- Kluster typ
- Kluster region
- Kluster resurs grupp
- Resurs-ID för kluster
- ID för kluster prenumeration
- Kluster-OS (exempel: Linux)
- Kluster ort (exempel: Seattle)
- Kluster status eller provins (exempel: Washington)
- Klustrets land eller region (exempel: USA)
- Undantag/fel som påträffats av Azure Policy tillägg under agent installation vid princip utvärdering
- Antal princip definitioner för gatekeeper som inte har installerats av Azure Policy tillägg

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).