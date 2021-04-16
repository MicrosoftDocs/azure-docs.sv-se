---
title: Lär Azure Policy för Kubernetes
description: Lär dig Azure Policy använder Rego och Open Policy Agent för att hantera kluster som kör Kubernetes i Azure eller lokalt.
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ca33c3a937b0a155928f20469830388a95a08e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506031"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Förstå Azure Policy för Kubernetes-kluster

Azure Policy [utökar Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, en _webhook_ för antagningskontrollant för [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), för att tillämpa framtvinganden och skydd i stor skala på dina kluster på ett centraliserat och konsekvent sätt. Azure Policy gör det möjligt att hantera och rapportera om kompatibilitetstillståndet för dina Kubernetes-kluster från en plats. Tillägget har följande funktioner:

- Kontrollerar med Azure Policy-tjänsten efter principtilldelningar till klustret.
- Distribuerar principdefinitioner till klustret som [en begränsningsmall](https://github.com/open-policy-agent/gatekeeper#constraint-templates) [och begränsar](https://github.com/open-policy-agent/gatekeeper#constraints) anpassade resurser.
- Rapporterar gransknings- och efterlevnadsinformation tillbaka till Azure Policy-tjänsten.

Azure Policy för Kubernetes stöder följande klustermiljöer:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc-aktiverade Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS-motor](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Tilläggen för AKS Engine och Arc-aktiverade Kubernetes finns som **förhandsversion.** Azure Policy kubernetes stöder endast Linux-nodpooler och inbyggda principdefinitioner. Inbyggda principdefinitioner finns i **Kategorin Kubernetes.** De begränsade principdefinitionerna för förhandsversionen med **EnforceOPAConstraint-** och **EnforceRegoPolicy-effekten** och den relaterade **Kubernetes** _Service-kategorin är inaktuella._ Använd i stället effekterna granska _och_ _neka med_ resursproviderläget `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Översikt

Aktivera och använda Azure Policy med kubernetes-klustret genom att utföra följande åtgärder:

1. Konfigurera Kubernetes-klustret och installera tillägget:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc-aktiverade Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS-motor](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Vanliga problem med installationen finns i [Felsöka Azure Policy-tillägget](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Förstå Azure Policy för Kubernetes](#policy-language)

1. [Tilldela en inbyggd definition till ditt Kubernetes-kluster](#assign-a-built-in-policy-definition)

1. [Vänta på validering](#policy-evaluation)

## <a name="limitations"></a>Begränsningar

Följande allmänna begränsningar gäller för Azure Policy för Kubernetes-kluster:

- Azure Policy-tillägg för Kubernetes stöds på Kubernetes version **1.14** eller senare.
- Azure Policy-tillägg för Kubernetes kan bara distribueras till Linux-nodpooler
- Endast inbyggda principdefinitioner stöds
- Maximalt antal icke-kompatibla poster per princip per kluster: **500**
- Maximalt antal icke-kompatibla poster per prenumeration: **1 miljon**
- Installationer av Gatekeeper utanför Azure Policy-tillägget stöds inte. Avinstallera alla komponenter som installerats av en tidigare Gatekeeper-installation innan Azure Policy-tillägget.
- [Orsaker till in kompatibilitet är](../how-to/determine-non-compliance.md#compliance-reasons) inte tillgängliga för `Microsoft.Kubernetes.Data` 
   [resursproviderläget](./definition-structure.md#resource-provider-modes). Använd [komponentinformation](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Undantag stöds](./exemption-structure.md) inte för resursproviderlägen . [](./definition-structure.md#resource-provider-modes)

Följande begränsningar gäller endast för Azure Policy för AKS:

- [AKS Pod-säkerhetsprincipen](../../../aks/use-pod-security-policies.md) och Azure Policy för AKS kan inte båda aktiveras. Mer information finns i [Säkerhetsbegränsningar för AKS-poddar.](../../../aks/use-azure-policy.md)
- Namnrymder utesluts automatiskt av Azure Policy-tillägget för utvärdering: _kube-system,_ _gatekeeper-system_ och _aks-periscope_.

## <a name="recommendations"></a>Rekommendationer

Följande är allmänna rekommendationer för att använda Azure Policy-tillägget:

- Tillägget Azure Policy kräver tre Gatekeeper-komponenter för att köra: 1 granskningspodd och 2 webhook-podrepliker. Dessa komponenter förbrukar fler resurser när antalet Kubernetes-resurser och principtilldelningar ökar i klustret, vilket kräver gransknings- och tvingande åtgärder.

  - För färre än 500 poddar i ett enda kluster med högst 20 begränsningar: 2 virtuella processorer och 350 MB minne per komponent.
  - För fler än 500 poddar i ett enda kluster med högst 40 begränsningar: 3 virtuella processorer och 600 MB minne per komponent.

- Windows-poddar [stöder inte säkerhetskontexterna](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Därför kan vissa av Azure Policy definitionerna, till exempel att inte tillåta rotprivilegier, inte eskaleras i Windows-poddar och gäller endast för Linux-poddar.

Följande rekommendation gäller endast för AKS och Azure Policy-tillägget:

- Använd systemnodpoolen med `CriticalAddonsOnly` taint för att schemalägga Gatekeeper-poddar. Mer information finns i Använda [systemnodpooler.](../../../aks/use-system-pools.md#system-and-user-node-pools)
- Skydda utgående trafik från dina AKS-kluster. Mer information finns i Kontrollera [utgående trafik för klusternoder.](../../../aks/limit-egress-traffic.md)
- Om klustret har `aad-pod-identity` aktiverats ändrar Hanterad identitet för noder (NMI)-poddar nodernas iptables för att fånga upp anrop till Azure Instance Metadata-slutpunkten. Den här konfigurationen innebär att alla förfrågningar som görs till metadataslutpunkten fångas upp av NMI även om podden inte använder `aad-pod-identity` . AzurePodIdentityException CRD kan konfigureras för att informera om att alla begäranden till metadataslutpunkten som kommer från en podd som matchar etiketter som definierats i CRD ska proxyas utan någon bearbetning i `aad-pod-identity` NMI. Systempoddar med etiketten `kubernetes.azure.com/managedby: aks` i _kube-system-namnområdet_ bör undantas i genom att `aad-pod-identity` konfigurera CRD för AzurePodIdentityException. Mer information finns i Inaktivera [aad-pod-identity för en specifik podd eller ett visst program.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Om du vill konfigurera ett undantag installerar du [YAML-undantagsfelet mic.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="install-azure-policy-add-on-for-aks"></a>Installera Azure Policy för AKS

Innan du installerar Azure Policy-tillägget eller aktiverar någon av tjänstfunktionerna måste din prenumeration aktivera **Resursproviders för Microsoft.PolicyInsights.**

1. Azure CLI version 2.12.0 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Registrera resursproviders och förhandsgranskningsfunktioner.

   - Azure Portal:

     Registrera **resursproviders för Microsoft.PolicyInsights.** Anvisningar finns i [Resursproviders och typer.](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Om begränsade principdefinitioner för förhandsversionen har  installerats tar du bort tillägget med knappen Inaktivera i AKS-klustret under **sidan** Principer.

1. AKS-klustret måste vara version _1.14_ eller senare. Använd följande skript för att verifiera din AKS-klusterversion:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installera version _2.12.0_ eller senare av Azure CLI. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

När ovanstående nödvändiga steg har slutförts installerar du Azure Policy i det AKS-kluster som du vill hantera.

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att välja **Alla tjänster** och sedan söka efter och välja **Kubernetes-tjänster.**

  1. Välj ett av dina AKS-kluster.

  1. Välj **Principer** till vänster på Kubernetes-tjänstsidan.

  1. På huvudsidan väljer du **knappen Aktivera** tillägg.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Om knappen Inaktivera tillägg är aktiverad och ett v2-meddelande för migreringsvarning visas installeras **v1-tillägget** och måste tas bort innan du tilldelar v2-principdefinitioner. Det _inaktuella_ v1-tillägget ersätts automatiskt med v2-tillägget från och med 24 augusti.
     > 2020. Nya v2-versioner av principdefinitionerna måste sedan tilldelas. Följ dessa steg om du vill uppgradera nu:
     >
     > 1. Kontrollera att V1-tillägget är installerat i AKS-klustret genom att gå till sidan Principer i AKS-klustret och se "Det aktuella klustret använder Azure Policy add-on v1..."  Meddelande.
     > 1. [Ta bort tillägget](#remove-the-add-on-from-aks).
     > 1. Välj **knappen Aktivera tillägg för** att installera v2-versionen av tillägget.
     > 1. [Tilldela v2-versioner av dina inbyggda principdefinitioner för v1](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Kontrollera att installationen av tillägget har lyckats och att _poddarna azure-policy_ och _gatekeeper_ körs genom att köra följande kommando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Kontrollera slutligen att det senaste tillägget har installerats genom att köra det här Azure CLI-kommandot och ersätta med namnet på din resursgrupp och med namnet på `<rg>` `<cluster-name>` ditt AKS-kluster: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Resultatet bör se ut ungefär som följande utdata och **config.version** bör vara `v2` :

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

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Installera Azure Policy-tillägg för Azure Arc Aktiverat Kubernetes (förhandsversion)

Innan du installerar Azure Policy-tillägget eller aktiverar någon av tjänstfunktionerna måste prenumerationen aktivera resursprovidern **Microsoft.PolicyInsights** och skapa en rolltilldelning för klustrets tjänsthuvudnamn.

1. Azure CLI version 2.12.0 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Om du vill aktivera resursprovidern följer du stegen [i Resursproviders](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) och resurstyper eller kör antingen Azure CLI eller Azure PowerShell kommandot:

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

1. Kubernetes-klustret måste vara version _1.14_ eller senare.

1. Installera [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Kubernetes-klustret har aktiverats för Azure Arc. Mer information finns i Registrera [ett Kubernetes-kluster för att Azure Arc](../../../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Ha det fullständigt kvalificerade Azure-resurs-ID:t för Azure Arc aktiverat Kubernetes-kluster.

1. Öppna portar för tillägget. Tillägget Azure Policy dessa domäner och portar för att hämta principdefinitioner och tilldelningar och rapportera kompatibilitet för klustret tillbaka till Azure Policy.

   |Domain |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Tilldela rolltilldelningen "Policy Insights Data Writer (förhandsversion)" till Azure Arc aktiverat Kubernetes-kluster. Ersätt med ditt prenumerations-ID med Azure Arc-aktiverade Kubernetes-klustrets resursgrupp och med namnet på `<subscriptionId>` Azure Arc aktiverat `<rg>` `<clusterName>` Kubernetes-kluster. Håll reda på de returnerade värdena _för appId,_ _lösenord_ och _klient för_ installationsstegen.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Exempel på utdata från kommandona ovan:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

När ovanstående nödvändiga steg har slutförts installerar du Azure Policy i ditt Azure Arc Kubernetes-kluster:

1. Lägg till Azure Policy-lagringsplatsen för tillägg till Helm:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installera Azure Policy med Helm Chart:

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

   Mer information om vad Helm-diagrammet för tillägget installerar finns i [Azure Policy helm-diagramdefinitionen för tillägg](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) på GitHub.

Kontrollera att installationen av tillägget har lyckats och att _poddarna azure-policy_ och _gatekeeper_ körs genom att köra följande kommando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Installera Azure Policy för AKS-motor (förhandsversion)

Innan du installerar Azure Policy-tillägget eller aktiverar någon av tjänstfunktionerna måste prenumerationen aktivera resursprovidern **Microsoft.PolicyInsights** och skapa en rolltilldelning för klustertjänstens huvudnamn.

1. Azure CLI version 2.0.62 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Om du vill aktivera resursprovidern följer du stegen [i Resursproviders och](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) typer eller kör antingen Kommandot Azure CLI Azure PowerShell azure cli:

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

1. Skapa en rolltilldelning för klustrets tjänsthuvudnamn.

   - Om du inte känner till app-ID:t för tjänstens huvudnamn för kluster letar du upp det med följande kommando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Tilldela rolltilldelningen "Policy Insights Data Writer (förhandsversion)" till app-ID:t för klustertjänstens huvudnamn _(värdet aadClientID_ från föregående steg) med Azure CLI. Ersätt `<subscriptionId>` med ditt prenumerations-ID `<aks engine cluster resource group>` och med resursgruppen finns det själv hanterade Kubernetes-klustret i AKS Engine.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

När ovanstående nödvändiga steg har slutförts installerar du Azure Policy-tillägget. Installationen kan ske under skapande- eller uppdateringscykeln för en AKS-motor eller som en oberoende åtgärd i ett befintligt kluster.

- Installera under skapande eller uppdateringscykel

  Om du vill Azure Policy-tillägget när du skapar ett nytt självstyrt kluster eller som en uppdatering av ett befintligt kluster, inkluderar du definitionen av addons-egenskapsklustret för AKS-motorn. [](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy)

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Mer information om finns i den externa guiden för [AKS Engine-klusterdefinitionen](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installera i befintligt kluster med Helm-diagram

  Använd följande steg för att förbereda klustret och installera tillägget:

  1. Installera [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Lägg till Azure Policy lagringsplatsen i Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Mer information finns i [Helm-diagram – snabbstartsguide.](https://helm.sh/docs/using_helm/#quickstart-guide)

  1. Installera tillägget med ett Helm-diagram. Ersätt `<subscriptionId>` med ditt prenumerations-ID `<aks engine cluster resource group>` och med resursgruppen finns det själv hanterade Kubernetes-klustret i AKS Engine.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Mer information om vad Helm-diagrammet för tillägg installeras finns i [Azure Policy helm-diagramdefinitionen](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) för tillägg på GitHub.

     > [!NOTE]
     > På grund av relationen mellan Azure Policy-tillägget och resursgruppens ID stöder Azure Policy endast ett AKS Engine-kluster för varje resursgrupp.

Kontrollera att installationen av tillägget lyckades och att _poddarna azure-policy_ och _gatekeeper_ körs genom att köra följande kommando:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Principspråk

Den Azure Policy språkstrukturen för att hantera Kubernetes följer de befintliga principdefinitionerna. Med [resursproviderläget](./definition-structure.md#resource-provider-modes) används effekterna granska och neka `Microsoft.Kubernetes.Data` [för](./effects.md#deny) att hantera kubernetes-kluster. [](./effects.md#audit) _Granskning_ och _neka måste ange_ egenskaper som **är** specifika för att arbeta [med OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) gatekeeper v3.

Som en del av egenskaperna _details.constraintTemplate_ och _details.constraint_ i principdefinitionen skickar Azure Policy URI:erna för [dessa CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att verifiera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy det möjligt att återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig rapporteringsupplevelse för molnefterlevnad. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Tilldela en inbyggd principdefinition

Om du vill tilldela en principdefinition till kubernetes-klustret måste du tilldelas lämpliga tilldelningsåtgärder för azure-principbaserad åtkomstkontroll (Azure RBAC). De inbyggda Azure-rollerna **Resursprincipdeltagare** och **Ägare** har dessa åtgärder. Mer information finns i [Azure RBAC-behörigheter i Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

Hitta de inbyggda principdefinitionerna för att hantera klustret med hjälp Azure Portal med följande steg:

1. Starta Azure Policy i Azure Portal. Välj **Alla tjänster** i det vänstra fönstret och sök sedan efter och välj **Princip.**

1. I den vänstra rutan på Azure Policy väljer du **Definitioner**.

1. I listrutan Kategori använder du Markera alla **för** att rensa filtret och väljer sedan **Kubernetes**.

1. Välj principdefinitionen och välj sedan **knappen** Tilldela.

1. Ange **Omfång** till hanteringsgruppen, prenumerationen eller resursgruppen för Kubernetes-klustret där principtilldelningen ska gälla.

   > [!NOTE]
   > När du tilldelar Azure Policy för Kubernetes-definitionen måste **omfånget** innehålla klusterresursen. För ett AKS Engine-kluster **måste omfånget** vara klustrets resursgrupp.

1. Ge principtilldelningen ett **Namn och** en **Beskrivning som** du enkelt kan använda för att identifiera den.

1. Ange [Principtvingande till](./assignment-structure.md#enforcement-mode) något av värdena nedan.

   - **Aktiverad** – Framtvinga principen i klustret. Kubernetes-antagningsbegäranden med överträdelser nekas.

   - **Inaktiverad** – framtvinga inte principen i klustret. Kubernetes-antagningsbegäranden med överträdelser nekas inte. Resultatet av kompatibilitetsutvärderingen är fortfarande tillgängligt. När du distribuerar nya principdefinitioner till kluster som körs _är_ alternativet Inaktiverat användbart för att testa principdefinitionen eftersom antagningsbegäranden med överträdelser inte nekas.

1. Välj **Nästa**.

1. Ange **parametervärden**

   - Om du vill undanta Kubernetes-namnområden från principutvärderingen anger du listan över namnområden i **parametern Namnområdes uteslutningar**. Vi rekommenderar att du undantar: _kube-system,_ _gatekeeper-system_ och _azure-arc_.

1. Välj **Granska + skapa**.

Du kan också använda [snabbstarten Tilldela en princip – portalen](../assign-policy-portal.md) för att hitta och tilldela en Kubernetes-princip. Sök efter en Kubernetes-principdefinition i stället för exemplet "granska virtuella datorer".

> [!IMPORTANT]
> Inbyggda principdefinitioner är tillgängliga för Kubernetes-kluster i kategorin **Kubernetes**. En lista över inbyggda principdefinitioner finns i [Kubernetes-exempel.](../samples/built-in-policies.md#kubernetes)

## <a name="policy-evaluation"></a>Principutvärdering

Tillägget checkar in med Azure Policy tjänst för ändringar i principtilldelningar var 15:e minut.
Under den här uppdateringscykeln söker tillägget efter ändringar. Dessa ändringsutlösare skapar, uppdaterar eller tar bort begränsningsmallar och begränsningar.

Om ett namnområde har någon av följande etiketter i ett Kubernetes-kluster nekas inte antagningsbegäranden med överträdelser. Resultatet av kompatibilitetsutvärderingen är fortfarande tillgängligt.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Även om en klusteradministratör kan ha behörighet att skapa och uppdatera begränsningsmallar och begränsningar som installeras av Azure Policy-tillägget, stöds inte dessa scenarier eftersom manuella uppdateringar skrivs över. Gatekeeper fortsätter att utvärdera principer som fanns innan tillägget installeras och tilldelas Azure Policy principdefinitioner.

Var 15:e minut anropar tillägget en fullständig genomsökning av klustret. Efter att ha samlat [in](../how-to/get-compliance-data.md) information om den fullständiga genomsökningen och eventuella realtidsutvärderingar av Gatekeeper av försök till ändringar i klustret, rapporterar tillägget resultaten tillbaka till Azure Policy för inkludering i efterlevnadsinformation som Azure Policy tilldelning. Endast resultat för aktiva principtilldelningar returneras under granskningscykeln. Granskningsresultat kan också ses som [överträdelser som](https://github.com/open-policy-agent/gatekeeper#audit) anges i statusfältet för den misslyckade begränsningen. Mer information om _icke-kompatibla resurser finns_ i [Komponentinformation för resursproviderlägen.](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)

> [!NOTE]
> Varje efterlevnadsrapport i Azure Policy för kubernetes-kluster innehåller alla överträdelser under de senaste 45 minuterna. Tidsstämpeln anger när en överträdelse inträffade.

Några andra överväganden:

- Om klusterprenumerationen är registrerad Azure Security Center Azure Security Center kubernetes-principer tillämpas på klustret automatiskt.

- När en nekandeprincip tillämpas på kluster med befintliga Kubernetes-resurser fortsätter alla befintliga resurser som inte är kompatibla med den nya principen att köras. När den icke-kompatibla resursen schemalades om på en annan nod blockerar Gatekeeper skapandet av resursen.

- När ett kluster har en princip för att neka som validerar resurser visas inte ett avvisandemeddelande när en distribution skapas. Överväg till exempel en Kubernetes-distribution som innehåller replikuppsättningar och poddar. När en användare kör `kubectl describe deployment $MY_DEPLOYMENT` returnerar den inget avvisandemeddelande som en del av händelser. Returnerar dock `kubectl describe replicasets.apps $MY_DEPLOYMENT` de händelser som är associerade med avvisande.

## <a name="logging"></a>Loggning

Som Kubernetes-kontrollant/container behåller både _azure-policy-_ och _gatekeeper-poddarna_ loggar i Kubernetes-klustret. Loggarna kan visas på sidan **Insikter** i Kubernetes-klustret. Mer information finns i Övervaka [kubernetes-klusterprestanda med hjälp Azure Monitor för containrar.](../../../azure-monitor/containers/container-insights-analyze.md)

Om du vill visa tilläggsloggarna använder du `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Mer information finns i [Felsöka Gatekeeper i](https://github.com/open-policy-agent/gatekeeper#debugging) Gatekeeper-dokumentationen.

## <a name="troubleshooting-the-add-on"></a>Felsöka tillägget

Mer information om hur du felsöker tillägget för Kubernetes finns i [kubernetes-avsnittet](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) i felsökningsartikeln Azure Policy artikeln.

## <a name="remove-the-add-on"></a>Ta bort tillägget

### <a name="remove-the-add-on-from-aks"></a>Ta bort tillägget från AKS

Om du vill Azure Policy från AKS-klustret använder du antingen Azure Portal eller Azure CLI:

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att välja **Alla tjänster** och sedan söka efter och välja **Kubernetes-tjänster.**

  1. Välj det AKS-kluster där du vill inaktivera Azure Policy-tillägget.

  1. Välj **Principer** till vänster på Kubernetes-tjänstsidan.

  1. På huvudsidan väljer du **knappen Inaktivera** tillägg.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Ta bort tillägget från Azure Arc Aktiverat Kubernetes

Om du vill Azure Policy-tillägget och Gatekeeper från ditt kubernetes Azure Arc-aktiverade Kubernetes-kluster kör du följande Helm-kommando:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Ta bort tillägget från AKS-motorn

Om du vill Azure Policy-tillägget och Gatekeeper från AKS-motorklustret använder du metoden som överensstämmer med hur tillägget installerades:

- Om det installeras genom att **ange egenskapen addons** i klusterdefinitionen för AKS-motorn:

  Distribuera om klusterdefinitionen till AKS-motorn när du har ändrat **addons-egenskapen** _för azure-policy_ till false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Mer information finns i [AKS Engine - Disable Azure Policy Add-on (AKS-motor – inaktivera Azure Policy-tillägg).](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)

- Om du har installerat med Helm Charts kör du följande Helm-kommando:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostikdata som samlas Azure Policy av tillägg

Tillägget Azure Policy kubernetes samlar in begränsade klusterdiagnostikdata. Dessa diagnostikdata är viktiga tekniska data som rör programvara och prestanda. Det används på följande sätt:

- Håll Azure Policy-tillägget uppdaterat
- Håll Azure Policy ett tillägg säkert, tillförlitligt och presterande
- Förbättra Azure Policy tillägg – genom den sammanställda analysen av användningen av tillägget

Den information som samlas in av tillägget är inte personliga data. Följande information samlas in för närvarande:

- Azure Policy agentversion för tillägg
- Klustertyp
- Klusterregion
- Klusterresursgrupp
- Klusterresurs-ID
- Id för klusterprenumeration
- Klusteroperativsystem (exempel: Linux)
- Klusterstad (exempel: Seattle)
- Klustertillstånd eller provins (exempel: Washington)
- Klusterland eller -region (exempel: USA)
- Undantag/fel som uppstår Azure Policy tillägg under agentinstallationen vid principutvärdering
- Antal Gatekeeper-principdefinitioner som inte har installerats Azure Policy-tillägget

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [programmässigt skapar principer.](../how-to/programmatically-create.md)
- Lär dig hur du [hämtar efterlevnadsdata.](../how-to/get-compliance-data.md)
- Lär dig hur [du åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper.](../../management-groups/overview.md)
