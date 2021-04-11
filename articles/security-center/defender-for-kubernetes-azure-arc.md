---
title: Skydda Kubernetes-distributioner med hybrid-och flera moln med Azure Defender för Kubernetes
description: Använda Azure Defender för Kubernetes med dina lokala och Kubernetes-kluster med flera moln
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029207"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Försvara Azure Arc-aktiverade Kubernetes-kluster som körs i lokala miljöer och miljöer med flera moln

**Tillägget Azure Defender för Kubernetes Clusters** kan skydda dina lokala kluster med samma hot identifierings funktioner som erbjuds för Azure Kubernetes service-kluster. Aktivera [Azure Arc-aktiverade Kubernetes](../azure-arc/kubernetes/overview.md) i klustren och distribuera tillägget enligt beskrivningen på den här sidan. 

Tillägget kan också skydda Kubernetes-kluster på andra moln leverantörer, även om de inte är på deras hanterade Kubernetes-tjänster.

> [!TIP]
> Vi har lagt till några exempelfiler för att hjälpa dig med installations processen i [installations exempel på GitHub](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Tillgänglighet

| Aspekt | Information |
|--------|---------|
| Versions tillstånd | **Förhandsgranskning**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Nödvändiga roller och behörigheter | [Säkerhets administratören](../role-based-access-control/built-in-roles.md#security-admin) kan stänga aviseringar<br>[Säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader) kan visa resultat |
| Priser | Kräver [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md) |
| Kubernetes-distributioner som stöds | [Azure Kubernetes service på Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS-motor](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (version 4,6 eller senare) |
| Begränsningar | Azure Arc-aktiverade Kubernetes och Azure Defender-tillägget har **inte stöd** för hanterade Kubernetes-erbjudanden som Google Kubernetes Engine och elastisk Kubernetes-tjänst. [Azure Defender är internt tillgängligt för Azure Kubernetes service (AKS)](defender-for-kubernetes-introduction.md) och kräver inte att du ansluter klustret till Azure-bågen. |
| Miljöer och regioner | Tillgänglighet för det här tillägget är detsamma som [Azure Arc-aktiverade Kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Översikt över arkitekturen

För alla Kubernetes-kluster förutom AKS måste du ansluta klustret till Azure-bågen. När du är ansluten kan Azure Defender för Kubernetes distribueras på [Azure Arc-aktiverade Kubernetes](../azure-arc/kubernetes/overview.md) -resurser som ett [kluster tillägg](../azure-arc/kubernetes/extensions.md).

Tilläggs komponenterna samlar in Kubernetes gransknings loggar data från alla Control plan-noder i klustret och skickar dem till Azure Defender för Kubernetes-backend i molnet för ytterligare analys. Tillägget registreras med en Log Analytics arbets yta som används som en datapipeline, men Gransknings logg data lagras inte i arbets ytan Log Analytics.

Det här diagrammet visar interaktionen mellan Azure Defender för Kubernetes och Azure Arc-aktiverade Kubernetes-klustret:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Ett arkitektur diagram med hög nivå som beskriver interaktionen mellan Azure Defender för Kubernetes och ett Azure Arc-aktiverade Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Förutsättningar

- Azure Defender för Kubernetes har [Aktiver ATS i din prenumeration](enable-azure-defender.md)
- Ditt Kubernetes-kluster är [anslutet till Azure-bågen](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Du har uppfyllt de krav som anges i [dokumentationen för allmänna kluster tillägg](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>Distribuera Azure Defender-tillägget

Du kan distribuera Azure Defender-tillägget med flera olika metoder. För detaljerade steg väljer du relevant flik.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Använd alternativet "snabb korrigering" från Security Center rekommendation

En dedikerad rekommendation i Azure Security Center ger:

- **Synlighet** om vilka av dina kluster som har tillägget Defender för Kubernetes distribuerat
- **Ett "snabb korrigering"-alternativ** för att distribuera det till dessa kluster utan tillägget

1. På sidan rekommendationer från Azure Security Center öppnar du säkerhets kontrollen **Aktivera Azure Defender** .

1. Använd filtret för att hitta rekommendationen med namnet **Azure Arc-aktiverade Kubernetes-kluster ska ha Azure Defender-tillägget installerat**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center rekommendationen för att distribuera Azure Defender-tillägget för Azure Arc-aktiverade Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Observera ikonen snabb korrigering i kolumnen åtgärder

1. Välj tillägget för att se information om felfria och felaktiga resurser – kluster med och utan tillägget.

1. Från listan med felaktiga resurser väljer du ett kluster och väljer **åtgärda** för att öppna fönstret med reparations alternativen.

1. Välj den relevanta Log Analytics arbets ytan och välj **åtgärda x-resurs**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Distribuera Azure Defender-tillägget för Azure Arc med Security Center snabb korrigerings alternativ.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Använd Azure CLI för att distribuera Azure Defender-tillägget

1. Logga in på Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Se till att du använder samma prenumerations-ID för ``<your-subscription-id>`` som det som användes när du anslöt klustret till Azure-bågen.

1. Kör följande kommando för att distribuera tillägget ovanpå ditt Azure Arc-aktiverade Kubernetes-kluster:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    En beskrivning av alla konfigurations inställningar som stöds på Azure Defender-tilläggs typen anges nedan:

    | Egenskap | Beskrivning |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Valfritt**. Fullständigt resurs-ID för din egen Log Analytics-arbetsyta.<br>Om inget värde anges används standard arbets ytan för regionen.<br><br>Hämta det fullständiga resurs-ID: t genom att köra följande kommando för att visa listan över arbets ytor i dina prenumerationer i standardformatet JSON-format:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Resurs-ID för arbets ytan Log Analytics har följande syntax:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Läs mer i [Log Analytics arbets ytor](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Valfritt**. Den fullständiga sökvägen till Gransknings logg filen.<br>När det inte anges används standard Sök vägen ``/var/log/kube-apiserver/audit.log`` .<br>För AKS-motorn är standard Sök vägen ``/var/log/kubeaudit/audit.log`` |

    Kommandot nedan visar en exempel användning av alla valfria fält:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Använd Azure Resource Manager för att distribuera Azure Defender-tillägget

Om du vill använda Azure Resource Manager för att distribuera Azure Defender-tillägget behöver du en Log Analytics arbets yta för din prenumeration. Läs mer i [Log Analytics arbets ytor](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

Du kan använda **azure-defender-extension-arm-template.jsi** Resource Manager-mallen från Security Center s [installations exempel](https://aka.ms/kubernetes-extension-installation-examples).

> [!TIP]
> Börja här om du är nybörjare på Resource Manager-mallar: [Vad är Azure Resource Manager mallar?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Använd REST API för att distribuera Azure Defender-tillägget 

Om du vill använda REST API för att distribuera Azure Defender-tillägget behöver du en Log Analytics arbets yta för din prenumeration. Läs mer i [Log Analytics arbets ytor](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> Det enklaste sättet att använda API: et för att distribuera Azure Defender-tillägget är att använda JSON-exemplet för **Postman-samlingen** från Security Center [installations exempel](https://aka.ms/kubernetes-extension-installation-examples).
- Om du vill ändra JSON för Postman-samlingen eller manuellt distribuera tillägget med REST API kör du följande kommando:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Plats:

    | Name            | I   | Krävs | Typ   | Beskrivning                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Prenumerations-ID:t | path | True     | sträng | Din Azure-Arc aktiverade Kubernetes-resursens prenumerations-ID |
    | Resursgrupp  | path | True     | sträng | Namnet på resurs gruppen som innehåller din Azure Arc-aktiverad Kubernetes-resurs |
    | Klusternamn    | path | True     | sträng | Namnet på din Azure-Arc-aktiverad Kubernetes-resurs  |


    För **autentisering** måste ditt huvud ha en Bearer-token (precis som med andra Azure-API: er). Kör följande kommando för att hämta en Bearer-token:

    ```az account get-access-token --subscription <your-subscription-id>``` Använd följande struktur för bröd texten i meddelandet:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Beskrivning av egenskaperna anges nedan:

    | Egenskap | Beskrivning | 
    | -------- | ----------- |
    | logAnalytics. workspaceId | Arbetsyte-ID för Log Analytics resursen |
    | logAnalytics. Key         | Nyckel för Log Analytics resursen | 
    | auditLogPath             | **Valfritt**. Den fullständiga sökvägen till Gransknings logg filen. Standardvärdet är ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Verifiera distributionen

Kontrol lera att Azure Defender-tillägget är installerat på klustret genom att följa stegen på någon av flikarna nedan:

### <a name="azure-portal---security-center"></a>[**Azure Portal-Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Använd Security Center rekommendation för att verifiera tilläggets status

1. På sidan rekommendationer från Azure Security Center öppnar du säkerhets kontrollen  **Aktivera Azure Defender** .

1. Välj rekommendationen som heter **Azure Arc-aktiverade Kubernetes-kluster ska ha Azure Defender-tillägget installerat**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center rekommendationen för att distribuera Azure Defender-tillägget för Azure Arc-aktiverade Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Kontrol lera att det kluster där du distribuerade tillägget visas som **felfri**.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal – Azure-båge**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Använd Azures båg sidor för att kontrol lera status för tillägget

1. Öppna **Azure-bågen** från Azure Portal.
1. I listan infrastruktur väljer du **Kubernetes-kluster** och väljer sedan det aktuella klustret.
1. Öppna tilläggs sidan. Tilläggen i klustret visas i listan. Kontrol lera kolumnen **installations status** för att bekräfta att Azure Defender-tillägget har installerats korrekt.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Sidan Azure-båge för att kontrol lera status för alla installerade tillägg i ett Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Om du vill ha mer information väljer du tillägget.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Fullständig information om ett Azure Arc-tillägg i ett Kubernetes-kluster.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Använd Azure CLI för att kontrol lera att tillägget har distribuerats

1. Kör följande kommando i Azure CLI:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. I svaret söker du efter "extensionType": "Microsoft. azuredefender. Kubernetes" och "installState": "installerat".

    > [!NOTE]
    > Det kan Visa "installState": "väntar" under de första minuterna.
    
1. Om tillstånden visar **installerad** kör du följande kommando på datorn med `kubeconfig` filen som pekas på klustret för att kontrol lera att en pod som heter "azuredefender-xxxxx" är i körnings tillstånd:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Använd REST API för att kontrol lera att tillägget har distribuerats

För att bekräfta en lyckad distribution eller för att validera status för tillägget när som helst:

1. Kör följande GET-kommando:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. I svaret tittar du på "extensionType": "Microsoft. azuredefender. Kubernetes" för "installState": "installed".

    > [!TIP]
    > Det kan Visa "installState": "väntar" under de första minuterna.
    
1. Om tillstånden visar **installerad** kör du följande kommando på datorn med `kubeconfig` filen som pekas på klustret för att kontrol lera att en pod som heter "azuredefender-xxxxx" är i körnings tillstånd:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simulera säkerhets aviseringar från Azure Defender för Kubernetes

En fullständig lista över aviseringar som stöds finns i [referens tabellen för alla säkerhets aviseringar i Azure Security Center](alerts-reference.md#alerts-akscluster).

1. Kör följande kommando för att simulera en Azure Defender-avisering:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    Det förväntade svaret är "ingen resurs hittades".

    Inom 30 minuter kommer Azure Defender att identifiera den här aktiviteten och utlösa en säkerhets avisering.

1. Öppna Azure Security Center sidan säkerhets aviseringar på Azure Portal och leta efter aviseringen för den aktuella resursen:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Exempel avisering från Azure Defender for Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Tar bort Azure Defender-tillägget

Du kan ta bort tillägget med Azure Portal, Azure CLI eller REST API enligt beskrivningen i flikarna nedan.

### <a name="azure-portal---arc"></a>[**Azure Portal-båge**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Använd Azure Portal för att ta bort tillägget

1. Öppna Azure-bågen från Azure Portal.
1. I listan infrastruktur väljer du **Kubernetes-kluster** och väljer sedan det aktuella klustret.
1. Öppna tilläggs sidan. Tilläggen i klustret visas i listan.
1. Välj klustret och välj **Avinstallera**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Ta bort ett tillägg från ditt Arc-aktiverade Kubernetes-kluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Använd Azure CLI för att ta bort Azure Defender-tillägget

1. Ta bort Arc-tillägget för Azure Defender för Kubernetes med följande kommandon:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Det kan ta några minuter att ta bort tillägget. Vi rekommenderar att du väntar innan du försöker verifiera att det lyckades.

1. Kontrol lera att tillägget har tagits bort genom att köra följande kommandon:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Det får inte finnas någon fördröjning i tilläggs resursen som tas bort från Azure Resource Manager. Efter det kontrollerar du att det inte finns några poddar som heter "azuredefender-XXXXX" i klustret genom att köra följande kommando med `kubeconfig` filen som pekade på klustret: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Det kan ta några minuter innan poddar tas bort.

### <a name="rest-api"></a>[**REST-API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Använd REST API för att ta bort Azure Defender-tillägget 

Om du vill ta bort tillägget med REST API kör du följande kommando för borttagning:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | I   | Krävs | Typ   | Beskrivning                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Prenumerations-ID:t | path | True     | sträng | Ditt Arc-aktiverade Kubernetes-kluster prenumerations-ID |
| Resursgrupp  | path | True     | sträng | Ditt Arc-aktiverade Kubernetes-klusters resurs grupp  |
| Klusternamn    | path | True     | sträng | Din ARC-aktiverad Kubernetes-kluster namn            |

För **autentisering** måste ditt huvud ha en Bearer-token (precis som med andra Azure-API: er). Kör följande kommando för att hämta en Bearer-token:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Begäran kan ta flera minuter att slutföra.

---

## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras hur du distribuerar Azure Defender-tillägget för Azure Arc-aktiverade Kubernetes-kluster. Läs mer om Azure Defender och Azure Security Center behållar säkerhets funktioner på följande sidor:

- [Containersäkerhet i Security Center](container-security.md)
- [Introduktion till Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Skydda dina Kubernetes-arbetsbelastningar](kubernetes-workload-protections.md)
