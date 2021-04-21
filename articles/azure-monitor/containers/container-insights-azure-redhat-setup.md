---
title: Konfigurera Azure Red Hat OpenShift v3.x med Container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du konfigurerar övervakning av ett Kubernetes-kluster med Azure Monitor som finns på Azure Red Hat OpenShift version 3 och senare.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784685"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Konfigurera Azure Red Hat OpenShift v3 med Container Insights

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 dras tillbaka i juni 2022.
>
> Från och med oktober 2020 kommer du inte längre att kunna skapa nya 3.11-kluster.
> Befintliga 3.11-kluster fortsätter att fungera fram till juni 2022 men kommer inte längre att stödjas efter det datumet.
>
> Följ den här guiden [för att skapa Azure Red Hat OpenShift 4-kluster](../../openshift/tutorial-create-cluster.md).
> Om du har specifika frågor kan [du kontakta oss](mailto:aro-feedback@microsoft.com).

Containerinsikter ger omfattande övervakningsupplevelse för Azure Kubernetes Service(AKS) och AKS Engine-kluster. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns på [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 3 och den senaste version av version 3 som stöds för att få en liknande övervakningsupplevelse.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig förhandsversion just nu.
>

Containerinsikter kan aktiveras för nya eller en eller flera befintliga distributioner Azure Red Hat OpenShift med hjälp av följande metoder som stöds:

- För ett befintligt kluster från Azure Portal eller med Azure Resource Manager mall.
- För ett nytt kluster med Azure Resource Manager mall eller när du skapar ett nytt kluster med hjälp av [Azure CLI.](/cli/azure/openshift#az_openshift_create)

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och inte stöds

Containerinsikter stöder Azure Red Hat OpenShift enligt beskrivningen i översiktsartikeln, förutom följande funktioner: [](container-insights-overview.md)

- Livedata (förhandsversion)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor måttdatabasen

## <a name="prerequisites"></a>Förutsättningar

- En [Log Analytics-arbetsyta](../logs/design-logs-deployment.md).

    Container insights stöder en Log Analytics-arbetsyta i de regioner som anges i [Azure-produkter efter region.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Om du vill skapa en egen arbetsyta kan [den skapas via Azure Resource Manager](../logs/resource-manager-workspace.md), via [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../logs/quick-create-workspace.md).

- Om du vill aktivera och få åtkomst till funktionerna i Container  Insights måste du åtminstone vara medlem i azure-deltagarrollen i Azure-prenumerationen och vara medlem i [*Log Analytics-deltagarrollen*](../logs/manage-access.md#manage-access-using-azure-permissions) för Log Analytics-arbetsytan som konfigurerats med Container Insights.

- Om du vill visa övervakningsdata är du medlem i [*log Analytics-läsarrollbehörigheten*](../logs/manage-access.md#manage-access-using-azure-permissions) med Log Analytics-arbetsytan som konfigurerats med Container Insights.

## <a name="identify-your-log-analytics-workspace-id"></a>Identifiera ID för Log Analytics-arbetsyta

 Om du vill integrera med en befintlig Log Analytics-arbetsyta börjar du med att identifiera det fullständiga resurs-ID:t för Log Analytics-arbetsytan. Resurs-ID för arbetsytan krävs för parametern när `workspaceResourceId` du aktiverar övervakning med hjälp av Azure Resource Manager-mallmetoden.

1. Visa en lista över alla prenumerationer som du har åtkomst till genom att köra följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopiera värdet för **SubscriptionId**.

1. Växla till den prenumeration som är värd för Log Analytics-arbetsytan genom att köra följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visa listan över arbetsytor i dina prenumerationer i JSON-standardformat genom att köra följande kommando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Leta reda på arbetsytans namn i utdata och kopiera sedan det fullständiga resurs-ID:t för Den Log Analytics-arbetsytan under **fält-ID:t**.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Aktivera för ett nytt kluster med en Azure Resource Manager mall

Utför följande steg för att distribuera ett Azure Red Hat OpenShift kluster med övervakning aktiverat. Innan du fortsätter kan du gå [igenom självstudien Skapa ett Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md) kluster för att förstå de beroenden som du behöver konfigurera så att din miljö är korrekt konfigurerad.

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att distribuera klustret med övervakning aktiverat, och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- Resurs-ID Azure Red Hat OpenShift kluster.

- Resursgruppen som klustret distribueras i.

- [Azure Active Directory klientorganisations-ID](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) som anges när du har utfört stegen för att skapa ett eller ett som redan har skapats.

- [Azure Active Directory klientprogram-ID som anges](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) när du har utfört stegen för att skapa ett eller ett som redan har skapats.

- [Azure Active Directory Klienthemlighet som](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) anges när du har utfört stegen för att skapa en eller en som redan har skapats.

- [Azure AD-säkerhetsgrupp](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) som anges när du har utfört stegen för att skapa en eller en som redan har skapats.

- Resurs-ID för en befintlig Log Analytics-arbetsyta. Information [om hur du hämtar den här informationen finns](#identify-your-log-analytics-workspace-id) i Identifiera id för Log Analytics-arbetsyta.

- Antalet huvudnoder som ska skapas i klustret.

- Antalet beräkningsnoder i agentpoolprofilen.

- Antalet infrastrukturnoder i agentpoolprofilen.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall kan du se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.65 eller senare. Kör för att identifiera din `az --version` version. Om du behöver installera eller uppgradera Azure CLI kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

1. Ladda ned och spara i en lokal mapp, Azure Resource Manager mallen och parameterfilen, för att skapa ett kluster med övervakningstillägget med hjälp av följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Logga in på Azure

    ```azurecli
    az login
    ```

    Om du har åtkomst till flera prenumerationer kör `az account set -s {subscription ID}` du ersätter `{subscription ID}` med den prenumeration som du vill använda.

3. Skapa en resursgrupp för klustret om du inte redan har en. En lista över Azure-regioner som stöder OpenShift på Azure finns i [Regioner som stöds.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Redigera JSON-parameterfilen **newClusterWithMonitoringParam.jspå** och uppdatera följande värden:

    - *Plats*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Följande steg distribuerar klustret med övervakning aktiverat med hjälp av Azure CLI.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Utdata liknar följande:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Aktivera för ett befintligt kluster

Utför följande steg för att aktivera övervakning av ett Azure Red Hat OpenShift kluster som distribuerats i Azure. Du kan göra detta från Azure Portal eller med hjälp av de tillhandahållna mallarna.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal eller på sidan Start väljer du **Azure Monitor**. Under avsnittet **Insikter** väljer du **Containrar.**

3. På sidan **Övervaka – containrar** väljer du **Icke-övervakade kluster.**

4. I listan över icke-övervakade kluster hittar du klustret i listan och klickar på **Aktivera.** Du kan identifiera resultaten i listan genom att leta efter värdet **ARO** under kolumnen **KLUSTERTYP**.

5. Om du **har en befintlig** Log Analytics-arbetsyta i samma prenumeration som klustret på sidan Publicera till containerinsikter väljer du den i listrutan.  
    Listan förväljer standardarbetsytan och platsen som klustret distribueras till i prenumerationen.

    ![Aktivera övervakning för icke-övervakade kluster](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervakningsdata från klustret följer du anvisningarna i [Skapa en Log Analytics-arbetsyta.](../logs/quick-create-workspace.md) Se till att skapa arbetsytan i samma prenumeration som RedHat OpenShift-klustret distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

### <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med en Azure Resource Manager mall

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- Resurs-ID för Azure RedHat OpenShift-kluster.

- Den resursgrupp som klustret distribueras i.

- En Log Analytics-arbetsyta. Information [om hur du hämtar den här informationen finns](#identify-your-log-analytics-workspace-id) i Identifiera id för Log Analytics-arbetsyta.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall kan du se:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.65 eller senare. Kör för att identifiera din `az --version` version. Om du behöver installera eller uppgradera Azure CLI kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

1. Ladda ned mallen och parameterfilen för att uppdatera klustret med övervakningstillägget med hjälp av följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Logga in på Azure

    ```azurecli
    az login
    ```

    Om du har åtkomst till flera prenumerationer kör `az account set -s {subscription ID}` du ersätter `{subscription ID}` med den prenumeration som du vill använda.

3. Ange prenumerationen på Azure RedHat OpenShift-klustret.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Kör följande kommando för att identifiera klusterplatsen och resurs-ID:t:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Redigera JSON-parameterfilen **existingClusterParam.jspå** och uppdatera värdena *aroResourceId* och *aroResourceLocation*. Värdet för **workspaceResourceId är** det fullständiga resurs-ID:t för Log Analytics-arbetsytan, som innehåller arbetsytans namn.

6. Kör följande kommandon för att distribuera med Azure CLI:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Utdata liknar följande:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Nästa steg

- När övervakning har aktiverats för att samla in hälsotillstånd och resursanvändning för ditt RedHat OpenShift-kluster och arbetsbelastningar som körs på dem kan du lära dig hur [du använder](container-insights-analyze.md) Container Insights.

- Som standard samlar containeragenten in stdout/stderr-containerloggarna för alla containrar som körs i alla namnområden utom kube-system. Om du vill konfigurera containerlogginsamling som är specifik för ett visst namnområde eller namnområden kan du läsa [Container Insights-agentkonfigurationen](container-insights-agent-config.md) för att konfigurera önskade inställningar för datainsamling i konfigurationsfilen för ConfigMap.

- Om du vill ta fram och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus-måttskrasning](container-insights-prometheus-integration.md)

- Information om hur du stoppar övervakningen av klustret med Container Insights finns i [How to Stop Monitoring Your Azure Red Hat OpenShift cluster](./container-insights-optout-openshift-v3.md).