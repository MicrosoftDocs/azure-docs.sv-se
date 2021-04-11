---
title: Hantera container Insights-agenten | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar de vanligaste underhålls aktiviteterna med den container Log Analytics-agent som används av behållar insikter.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 6b485f4d49f0dd80f712d96779098c26be3f6de1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442583"
---
# <a name="how-to-manage-the-container-insights-agent"></a>Hantera container Insights-agenten

Container Insights använder en behållar version av Log Analytics agent för Linux. Efter den första distributionen finns det rutiner eller valfria uppgifter som du kan behöva utföra under dess livs cykel. Den här artikeln beskriver hur du uppgraderar agenten manuellt och inaktiverar samlingen av miljövariabler från en viss behållare. 

## <a name="how-to-upgrade-the-container-insights-agent"></a>Uppgradera container Insights-agenten

Container Insights använder en behållar version av Log Analytics agent för Linux. När en ny version av agenten släpps uppgraderas agenten automatiskt på de hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS) och Azure Red Hat OpenShift version 3. x. För ett [hybrid Kubernetes-kluster](container-insights-hybrid-setup.md) och Azure Red Hat OpenShift version 4. x hanteras inte agenten och du måste uppgradera agenten manuellt.

Om agent uppgraderingen Miss lyckas för ett kluster som finns på AKS eller Azure Red Hat OpenShift version 3. x, beskriver den här artikeln även processen för att uppgradera agenten manuellt. Information om hur du följer de versioner som lanseras finns i [agent release-meddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>Uppgradera agenten på AKS-kluster

Processen för att uppgradera agenten på AKS-kluster består av två enkla steg. Det första steget är att inaktivera övervakning med behållar insikter med hjälp av Azure CLI. Följ stegen som beskrivs i artikeln [inaktivera övervakning](container-insights-optout.md?#azure-cli) . Med Azure CLI kan vi ta bort agenten från noderna i klustret utan att påverka lösningen och motsvarande data som lagras i arbets ytan. 

>[!NOTE]
>När du utför den här underhålls aktiviteten vidarebefordrar inte noderna i klustret insamlade data, och prestanda vyer visar inte data mellan den tidpunkt då du tar bort agenten och installerar den nya versionen. 
>

Om du vill installera den nya versionen av agenten följer du stegen som beskrivs i [Aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)för att slutföra processen.  

När du har aktiverat övervakningen igen kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälso mått för klustret. Du kan kontrol lera att agenten har uppgraderats genom att antingen:

* Kör kommandot: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . Observera värdet under **bild** för omsagent i avsnittet *behållare* i utdata i status returnerat.
* På fliken **noder** väljer du noden kluster och i rutan **Egenskaper** till höger, noterar du värdet under **agent avbildnings tag gen**.

Den version av agenten som visas ska matcha den senaste versionen på sidan [versions historik](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) .

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Uppgradera agenten på Hybrid Kubernetes-kluster

Utför följande steg för att uppgradera agenten på ett Kubernetes-kluster som körs på:

* Självhanterade Kubernetes-kluster som finns i Azure med hjälp av AKS-motorn.
* Självhanterade Kubernetes-kluster som finns på Azure Stack eller lokalt med AKS-motorn.
* Red Hat OpenShift version 4. x.

Kör följande kommando om Log Analytics arbets ytan är i kommersiell Azure-arbets yta:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Om arbets ytan Log Analytics är i Azure Kina 21Vianet kör du följande kommando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Om arbets ytan Log Analytics är i Azure amerikanska myndigheter kör du följande kommando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Uppgradera agenten på Azure Red Hat OpenShift v4

Utför följande steg för att uppgradera agenten på ett Kubernetes-kluster som körs på Azure Red Hat OpenShift version 4. x. 

>[!NOTE]
>Azure Red Hat OpenShift version 4. x stöder bara körning i det kommersiella Azure-molnet.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Så här inaktiverar du miljö variabel samling på en behållare

Container Insights samlar in miljövariabler från behållarna som körs i en POD och visar dem i egenskaps rutan för den valda behållaren i vyn **behållare** . Du kan styra det här beteendet genom att inaktivera samling för en speciell behållare antingen under distributionen av Kubernetes-klustret, eller genom att ange miljövariabeln *AZMON_COLLECT_ENV*. Den här funktionen är tillgänglig från agent versionen – ciprod11292018 och högre.  

Om du vill inaktivera insamling av miljövariabler på en ny eller befintlig behållare ställer du in variabeln **AZMON_COLLECT_ENV** med värdet **false** i yaml-konfigurationsfilen för Kubernetes-distributionen. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Kör följande kommando för att tillämpa ändringen på andra Kubernetes-kluster än Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>` . Om du vill redigera ConfigMap och tillämpa den här ändringen för kluster med ett OpenShift-kluster i Azure Red Hat kör du kommandot:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Detta öppnar standard text redigeraren. När du har angett variabeln sparar du filen i redigeraren.

Kontrol lera att konfigurations ändringen har genomförts genom att välja en behållare i **behållare** -vyn i behållar insikter och expandera **miljövariabler** i egenskaps panelen.  Avsnittet ska endast visa variabeln som skapades tidigare- **AZMON_COLLECT_ENV = false**. För alla andra behållare ska avsnittet miljövariabler lista alla miljövariabler som identifierats.

Om du vill återaktivera identifieringen av miljövariablerna använder du samma process tidigare och ändrar värdet från **falskt** till **Sant** och kör sedan `kubectl` kommandot för att uppdatera behållaren.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Nästa steg

Om du får problem när du uppgraderar agenten kan du läsa [fel söknings guiden](container-insights-troubleshoot.md) för support.
