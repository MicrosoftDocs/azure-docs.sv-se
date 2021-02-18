---
title: Uppdatera Azure Monitor för behållare för mått | Microsoft Docs
description: I den här artikeln beskrivs hur du uppdaterar Azure Monitor för behållare för att aktivera funktionen anpassade mått som stöder utforska och aviserar på sammansatta mått.
ms.topic: conceptual
ms.date: 10/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 37c19cd074e9ce1985d5d0e82137d8603913d4bd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622433"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Uppdatera Azure Monitor för containrar för att aktivera mått

Azure Monitor for containers introducerar stöd för insamling av mått från Azure Kubernetes Services (AKS) och Azure Arc-aktiverade Kubernetes klustrar noder och poddar och skriver dem till Azure Monitor statistik lagringen. Den här ändringen är avsedd att ge förbättrad tids linje när du presenterar agg regerings beräkningar (medelvärde, antal, Max, min, summa) i prestanda diagram, stöd för att fästa prestanda diagram i Azure Portal instrument paneler och stöd för mått aviseringar.

>[!NOTE]
>Den här funktionen stöder för närvarande inte Azure Red Hat OpenShift-kluster.
>

Följande mått är aktiverade som en del av den här funktionen:

| Mått namn område | Metric | Beskrivning |
|------------------|--------|-------------|
| Insights. container/Nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Som *Node* -mått innehåller de *värden* som en dimension. De innehåller också<br> nodens namn som värde för *värd* dimensionen. |
| Insights. container/poddar | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Som *Pod* mått inkluderar de följande som dimensioner – ControllerName, Kubernetes-namnrymd, namn, fas. |
| Insights. container/containers | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |
| Insights. container/persistentvolumes | pvUsageExceededPercentage | |

För att stödja dessa nya funktioner ingår en ny container agent i versionen, version **Microsoft/OMS: ciprod05262020** för AKS och version **Microsoft/OMS: Ciprod09252020** för Azure Arc-aktiverade Kubernetes-kluster. Nya distributioner av AKS inkluderar automatiskt den här konfigurations ändringen och-funktionerna. Uppdatering av klustret för att stödja den här funktionen kan utföras från Azure Portal, Azure PowerShell eller med Azure CLI. Med Azure PowerShell och CLI. Du kan aktivera detta per kluster eller för alla kluster i din prenumeration.

Antingen tilldelar den **övervaknings mått utgivar** rollen rollen som övervaknings mått till klustrets huvud namn för tjänsten eller användarens tilldelade MSI för övervaknings tillägget så att data som samlas in av agenten kan publiceras i kluster resursen. Övervaknings mått utgivare har bara behörighet att skicka mått till resursen, den kan inte ändra något tillstånd, uppdatera resursen eller läsa data. Mer information om rollen finns i [övervaknings mått utgivar rollen](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). Roll kraven för övervaknings mått utgivaret gäller inte för Azure Arc-aktiverade Kubernetes-kluster.

> [!IMPORTANT]
> Uppgraderingen krävs inte för Azure Arc-aktiverade Kubernetes-kluster eftersom de redan har den lägsta agent version som krävs.

## <a name="prerequisites"></a>Förutsättningar

Innan du uppdaterar klustret bör du kontrol lera följande:

* Anpassade mått är bara tillgängliga i en delmängd av Azure-regioner. En lista över regioner som stöds dokumenteras [här](../essentials/metrics-custom-overview.md#supported-regions).

* Du är medlem i **[ägar](../../role-based-access-control/built-in-roles.md#owner)** rollen på AKS-klusterresursen för att aktivera insamling av nod-och Pod anpassade prestanda mått. Detta krav gäller inte för Azure Arc-aktiverade Kubernetes-kluster.

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.59 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Uppgradera ett kluster från Azure Portal

För befintliga AKS-kluster som övervakas av Azure Monitor för behållare, när du har valt klustret för att Visa hälsan från vyn multi-cluster i Azure Monitor eller direkt från klustret genom att välja **insikter** från den vänstra rutan, bör du se en banderoll överst i portalen.

![Uppgradera AKS Cluster banderoll i Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

När du klickar på **Aktivera** startas processen för att uppgradera klustret. Den här processen kan ta flera sekunder att slutföra och du kan följa förloppet under meddelanden på menyn.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Uppgradera alla kluster med bash i Azure Command Shell

Utför följande steg för att uppdatera alla kluster i din prenumeration med bash i Azure Command Shell.

1. Kör följande kommando med hjälp av Azure CLI.  Redigera värdet för **subscriptionId** med värdet från **översikts sidan AKS** för AKS-klustret.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Konfigurations ändringen kan ta några sekunder att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Uppgradera per kluster med Azure CLI

Utför följande steg för att uppdatera ett särskilt kluster i din prenumeration med Azure CLI.

1. Kör följande kommando med hjälp av Azure CLI. Redigera värdena för **subscriptionId**, **resourceGroupName** och **kluster** namn med hjälp av värdena på **översikts** sidan för AKS för AKS-klustret.  För att hämta värdet för **clientIdOfSPN** returneras det när du kör kommandot `az aks show` som visas i exemplet nedan.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Om du vill hämta värdet för **clientIdOfSPNOrMsi** kan du köra kommandot `az aks show` som visas i exemplet nedan. Om **servicePrincipalProfile** -objektet har ett giltigt *ClientID* -värde kan du använda det. Annars, om det är inställt på *MSI*, måste du skicka in ClientID från `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Uppgradera alla kluster med Azure PowerShell

Utför följande steg för att uppdatera alla kluster i prenumerationen med hjälp av Azure PowerShell.

1. [Ladda ned](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) **mdm_onboarding_atscale.ps1** -skriptet och spara det i en lokal mapp från vår GitHub-lagrings platsen.
2. Kör följande kommando med hjälp av Azure PowerShell.  Redigera värdet för **subscriptionId** med värdet från **översikts sidan AKS** för AKS-klustret.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Konfigurations ändringen kan ta några sekunder att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Uppgradera per kluster med Azure PowerShell

Utför följande steg för att uppdatera ett enskilt kluster med hjälp av Azure PowerShell.

1. [Ladda ned](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) **mdm_onboarding.ps1** -skriptet och spara det i en lokal mapp från vår GitHub-lagrings platsen.

2. Kör följande kommando med hjälp av Azure PowerShell. Redigera värdena för **subscriptionId**, **resourceGroupName** och **kluster** namn med hjälp av värdena på **översikts** sidan för AKS för AKS-klustret.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Konfigurations ändringen kan ta några sekunder att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Verifiera uppdatering

När du har initierat uppdateringen med hjälp av någon av metoderna som beskrivs ovan kan du använda Azure Monitor Metrics Explorer och verifiera från **mått namn området** som **insikter** visas. Om så är fallet kan du gå vidare och börja konfigurera [mått varningar](../alerts/alerts-metric.md) eller fästa diagram på [instrument paneler](../../azure-portal/azure-portal-dashboards.md).  
