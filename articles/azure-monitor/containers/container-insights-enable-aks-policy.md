---
title: Aktivera AKS Monitoring addon med Azure Policy
description: Beskriver hur du aktiverar AKS övervaknings tillägg med hjälp av en anpassad Azure-princip.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713906"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Aktivera AKS Monitoring addon med Azure Policy
I den här artikeln beskrivs hur du aktiverar AKS övervaknings tillägg med hjälp av en anpassad Azure-princip. Anpassad princip för övervaknings tillägg kan tilldelas antingen i prenumerations-eller resurs grupps omfånget. Om Azure Log Analytics-arbetsytan och AKS-klustret finns i olika prenumerationer, måste den hanterade identitet som används av princip tilldelningen ha de roll behörigheter som krävs för både prenumerationerna eller minst på resursen i arbets ytan Log Analytics. Om principen är begränsad till resurs gruppen, ska den hanterade identiteten ha de nödvändiga roll behörigheterna på arbets ytan Log Analytics om arbets ytan inte är i det valda resurs grupps omfånget.

Övervaknings tillägg kräver följande roller på den hanterade identitet som används av Azure Policy:

 - [Azure-Kubernetes-service-Contributor-Role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [Log-Analytics – Contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Skapa och tilldela princip definition med hjälp av Azure Portal

### <a name="create-policy-definition"></a>Skapa princip definition

1. Hämta den anpassade princip definitionen för Azure för att aktivera AKS övervaknings tillägg.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Navigera till https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions och skapa princip definition med följande information i dialog rutan skapa princip definition.
 
    - **Definitions plats**: Välj den Azure-prenumeration där princip definitionen ska lagras.
    - **Namn**: *(för hands version) AKS-Monitoring-addon*
    - **Beskrivning**: *Azure-anpassad princip för att aktivera övervaknings-tillägg till Azure Kubernetes-kluster i angivet omfång*
    - **Kategori**: Välj *Använd befintlig* och välj *Kubernetes* i list rutan.
    - **Princip regel**: ta bort de befintliga exempel reglerna och kopiera innehållet i *azurepolicy.jspå* hämtade i steg #1 ovan.

### <a name="assign-policy-definition-to-specified-scope"></a>Tilldela en princip definition till angivet omfång

> [!NOTE]
>  Hanterad identitet skapas automatiskt och tilldelas angivna roller i princip definitionen.

1. Välj princip definition *(för hands version) AKS övervaknings tillägg* som du nyss skapade.
4. Klicka på *tilldela** * och ange ett **omfång** för var principen ska tilldelas. 
5. Klicka på **Nästa** och ange resurs-ID för Azure Log Analytics-arbetsytan. Resurs-ID ska ha det här formatet `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Skapa en reparations uppgift om du vill tillämpa principen på befintliga AKS-kluster i det valda omfånget.
7. Klicka på **Granska + skapa** för att skapa princip tilldelningen.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Skapa och tilldela princip definition med Azure CLI

### <a name="create-policy-definition"></a>Skapa princip definition

1. Ladda ned Azures definitions regler och parametrar filer för anpassad princip med följande kommandon:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Skapa princip definitionen med följande kommando:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Tilldela en princip definition till angivet omfång

- Skapa princip tilldelningen med följande kommando:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure policy](../../governance/policy/overview.md).
- Lär dig hur [reparations säkerhet fungerar](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- Läs mer om [behållar insikter](./container-insights-overview.md).
- Installera [Azure CLI](/cli/azure/install-azure-cli).