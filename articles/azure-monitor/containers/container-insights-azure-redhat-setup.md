---
title: Konfigurera Azure Red Hat OpenShift v3. x med container Insights | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar övervakning av ett Kubernetes-kluster med Azure Monitor som finns i Azure Red Hat OpenShift version 3 och senare.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b46dfda0bdb0f3b582aa751786187a4d74524f75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708381"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Konfigurera Azure Red Hat OpenShift v3 med container Insights

>[!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka juni 2022.
>
> Från och med oktober 2020 kommer du inte längre att kunna skapa nya 3,11-kluster.
> Befintliga 3,11-kluster fortsätter att köras fram till den 2022 juni men kommer inte längre att kunna användas efter det datumet.
>
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](../../openshift/tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:aro-feedback@microsoft.com).

Behållar insikter ger en omfattande övervaknings upplevelse för Azure Kubernetes service (AKS) och AKS-motor kluster. Den här artikeln beskriver hur du aktiverar övervakning av Kubernetes-kluster som finns i [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 3 och den senaste version som stöds av version 3, för att uppnå en liknande övervaknings upplevelse.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
>

Behållar insikter kan aktive ras för nya eller en eller flera befintliga distributioner av Azure Red Hat OpenShift med följande metoder som stöds:

- För ett befintligt kluster från Azure Portal eller med hjälp av Azure Resource Manager-mall.
- För ett nytt kluster med Azure Resource Manager mall, eller när du skapar ett nytt kluster med hjälp av [Azure CLI](/cli/azure/openshift#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och som inte stöds

Behållar insikter har stöd för övervakning av Azure Red Hat OpenShift enligt beskrivningen i [översikts](container-insights-overview.md) artikeln, förutom följande funktioner:

- Real tids data (förhands granskning)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor Metrics-databasen

## <a name="prerequisites"></a>Förutsättningar

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Behållar insikter stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbets yta kan den skapas via [Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

- Om du vill aktivera och komma åt funktionerna i behållar insikter måste du minst vara medlem i Azure *Contributor* -rollen i Azure-prenumerationen och en medlem i rollen [*Log Analytics Contributor*](../logs/manage-access.md#manage-access-using-azure-permissions) för den Log Analytics arbets yta som kon figurer ATS med behållar insikter.

- Om du vill visa övervaknings data är du medlem i behörighets rollen [*Log Analytics läsare*](../logs/manage-access.md#manage-access-using-azure-permissions) med den Log Analytics arbets yta som kon figurer ATS med behållar insikter.

## <a name="identify-your-log-analytics-workspace-id"></a>Identifiera ditt Log Analytics arbetsyte-ID

 Börja med att identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta om du vill integrera med en befintlig Log Analytics arbets yta. Resurs-ID för arbets ytan krävs för parametern `workspaceResourceId` när du aktiverar övervakning med hjälp av metoden Azure Resource Manager mall.

1. Lista alla prenumerationer som du har åtkomst till genom att köra följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata kommer att se ut så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopiera värdet för **SubscriptionId**.

1. Växla till den prenumeration som är värd för Log Analytics arbets ytan genom att köra följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visa listan över arbets ytor i dina prenumerationer i standardvärdet JSON-format genom att köra följande kommando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Aktivera för ett nytt kluster med en Azure Resource Manager mall

Utför följande steg för att distribuera ett Azure Red Hat OpenShift-kluster med övervakning aktiverat. Innan du fortsätter bör du gå igenom självstudien [skapa ett Azure Red Hat OpenShift-kluster](../../openshift/tutorial-create-cluster.md) för att förstå de beroenden som du behöver konfigurera så att din miljö är korrekt konfigurerad.

Den här metoden inkluderar två JSON-mallar. En mall anger konfigurationen för att distribuera klustret med övervakning aktiverat, och det andra innehåller parameter värden som du konfigurerar för att ange följande:

- Resurs-ID för Azure Red Hat OpenShift-klustret.

- Resurs gruppen som klustret har distribuerats i.

- [Azure Active Directory klient-ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) anges efter att du har utfört stegen för att skapa en eller ett redan skapat.

- [Azure Active Directory klient program-ID](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) anges efter att du har utfört stegen för att skapa en eller ett redan skapat.

- [Azure Active Directory klient hemligheten](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) som du angav när du utförde stegen för att skapa en eller en redan har skapats.

- [Azure AD-säkerhetsgruppen](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) har antecknats efter att ha utfört stegen för att skapa en eller en redan skapad.

- Resurs-ID för en befintlig Log Analytics-arbetsyta. Se [identifiera din Log Analytics arbetsyte-ID](#identify-your-log-analytics-workspace-id) för att lära dig mer om att hämta den här informationen.

- Antalet huvud noder som ska skapas i klustret.

- Antalet Compute-noder i agentens profil för poolen.

- Antalet infrastruktur noder i agentens profil för poolen.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Hämta och spara till en lokal mapp, Azure Resource Manager mall och parameter fil, för att skapa ett kluster med övervaknings tillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Logga in på Azure

    ```azurecli
    az login
    ```

    Om du har åtkomst till flera prenumerationer kan `az account set -s {subscription ID}` du köra Ersätt `{subscription ID}` med den prenumeration som du vill använda.

3. Skapa en resurs grupp för klustret om du inte redan har en. En lista över Azure-regioner som stöder OpenShift på Azure finns i [regioner som stöds](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Redigera JSON-parameter filen **newClusterWithMonitoringParam.jspå** och uppdatera följande värden:

    - *sökvägen*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. I följande steg distribueras klustret med övervakning aktiverat med hjälp av Azure CLI.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Resultatet ser ut ungefär så här:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Aktivera för ett befintligt kluster

Utför följande steg för att aktivera övervakning av ett Azure Red Hat OpenShift-kluster som distribuerats i Azure. Du kan göra detta från Azure Portal eller använda de angivna mallarna.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På Azure Portal-menyn eller på Start sidan väljer du **Azure Monitor**. Under avsnittet **insikter** väljer du **behållare**.

3. På sidan **Monitor-containers** väljer du **icke-övervakade kluster**.

4. I listan över icke-övervakade kluster letar du reda på klustret i listan och klickar på **Aktivera**. Du kan identifiera resultaten i listan genom att leta efter värdet **Aro** under kolumn **kluster typen**.

5. På sidan **onboarding to container Insights** , om du har en befintlig Log Analytics arbets yta i samma prenumeration som klustret, väljer du den i list rutan.  
    I listan förväljs standard arbets ytan och platsen som klustret distribueras till i prenumerationen.

    ![Aktivera övervakning för icke-övervakade kluster](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervaknings data från klustret, följer du anvisningarna i [skapa en Log Analytics arbets yta](../logs/quick-create-workspace.md). Se till att skapa arbets ytan i samma prenumeration som RedHat OpenShift-klustret distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager mall

Den här metoden inkluderar två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parameter värden som du konfigurerar för att ange följande:

- Resurs-ID för Azure RedHat OpenShift-klustret.

- Resurs gruppen som klustret har distribuerats i.

- En Log Analytics-arbetsyta. Se [identifiera din Log Analytics arbetsyte-ID](#identify-your-log-analytics-workspace-id) för att lära dig mer om att hämta den här informationen.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Hämta mallen och parameter filen för att uppdatera klustret med övervaknings tillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Logga in på Azure

    ```azurecli
    az login
    ```

    Om du har åtkomst till flera prenumerationer kan `az account set -s {subscription ID}` du köra Ersätt `{subscription ID}` med den prenumeration som du vill använda.

3. Ange prenumerationen för ett OpenShift-kluster i Azure RedHat.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Kör följande kommando för att identifiera kluster platsen och resurs-ID:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Redigera JSON-parameter filen **existingClusterParam.jspå** och uppdatera värdena *aroResourceId* och *aroResourceLocation*. Värdet för **workspaceResourceId** är det fullständiga resurs-ID: t för din Log Analytics-arbetsyta, som innehåller namnet på arbets ytan.

6. Om du vill distribuera med Azure CLI kör du följande kommandon:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Resultatet ser ut ungefär så här:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Nästa steg

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt RedHat OpenShift-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) behållar insikter.

- Som standard samlar den behållareade agenten STDOUT/stderr-behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera samling av behållar logg samlingar som är specifika för en viss namnrymd eller namnrymder granskar du [konfiguration av container Insights](container-insights-agent-config.md) för att konfigurera önskade inställningar för data insamling till ConfigMap-konfigurations filen.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått-kassationing](container-insights-prometheus-integration.md)

- Information om hur du stoppar övervakningen av klustret med behållar insikter finns i [så här slutar du övervaka ditt Azure Red Hat OpenShift-kluster](./container-insights-optout-openshift-v3.md).