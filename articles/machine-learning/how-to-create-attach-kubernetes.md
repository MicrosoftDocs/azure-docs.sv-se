---
title: Skapa och bifoga Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar ett Azure Kubernetes Service kluster via Azure Machine Learning eller hur du kopplar ett befintligt AKS-kluster till din arbetsyta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 375a8f6613ff90edd3df635c8236196aab62b6ac
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861147"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Skapa och koppla ett Azure Kubernetes Service kluster

Azure Machine Learning kan distribuera tränade maskininlärningsmodeller till Azure Kubernetes Service. Du måste dock först antingen __skapa__ ett Azure Kubernetes Service(AKS)-kluster från Din Azure ML-arbetsyta eller koppla __ett__ befintligt AKS-kluster. Den här artikeln innehåller information om hur du både skapar och kopplar ett kluster.

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

- [Azure CLI-tillägget för Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- Om du planerar att använda en Azure Virtual Network för att skydda kommunikationen mellan din Azure ML-arbetsyta och AKS-klustret kan du läsa artikeln [Nätverksisolering under & härledning.](./how-to-network-security-overview.md)

## <a name="limitations"></a>Begränsningar

- Om du behöver distribuera **ett Standard Load Balancer(SLB)** i klustret i stället för en Basic Load Balancer(BLB) skapar du ett kluster  i AKS-portalen/CLI/SDK och kopplar det till AML-arbetsytan.

- Om du har en Azure Policy som begränsar skapandet av offentliga IP-adresser kommer AKS-kluster att misslyckas. AKS kräver en offentlig IP-adress [för utgående trafik](../aks/limit-egress-traffic.md). Artikeln om utgående trafik innehåller också vägledning för att låsa utgående trafik från klustret via den offentliga IP-adressen, förutom några få fullständigt kvalificerade domännamn. Det finns två sätt att aktivera en offentlig IP-adress:
    - Klustret kan använda den offentliga IP-adress som skapas som standard med BLB eller SLB, eller
    - Klustret kan skapas utan en offentlig IP-adress och sedan konfigureras en offentlig IP-adress med en brandvägg med en användardefinierad väg. Mer information finns i [Anpassa utgående kluster med en användardefinierad väg.](../aks/egress-outboundtype.md)
    
    AML-kontrollplanet pratar inte med denna offentliga IP-adress. Den pratar med AKS-kontrollplanet för distributioner. 

- Om du **kopplar ett** AKS-kluster som har ett auktoriserat IP-intervall aktiverat för åtkomst till [API-servern](../aks/api-server-authorized-ip-ranges.md)aktiverar du AML-kontrollplanets IP-intervall för AKS-klustret. AML-kontrollplanet distribueras i parkopplade regioner och distribuerar härledningspoddar i AKS-klustret. Utan åtkomst till API-servern går det inte att distribuera härledningspoddarna. Använd [IP-intervallen](https://www.microsoft.com/download/confirmation.aspx?id=56519) för båda [de parkopplade regionerna](../best-practices-availability-paired-regions.md) när du aktiverar IP-intervallen i ett AKS-kluster.

    Auktoriserade IP-intervall fungerar bara med Standard Load Balancer.

- När **du kopplar** ett AKS-kluster måste det finnas i samma Azure-prenumeration som din Azure Machine Learning arbetsyta.

- Om du vill använda ett privat AKS-kluster (med Azure Private Link) måste du först skapa klustret och **sedan** koppla det till arbetsytan. Mer information finns i Skapa [ett privat Azure Kubernetes Service kluster.](../aks/private-clusters.md)

- Beräkningsnamnet för AKS-klustret MÅSTE vara unikt på Azure ML-arbetsytan. Den kan innehålla bokstäver, siffror och bindestreck. Det måste börja med en bokstav, sluta med en bokstav eller siffra och vara mellan 3 och 24 tecken långt.
 
 - Om du vill distribuera modeller till **GPU-noder** eller **FPGA-noder** (eller någon specifik SKU) måste du skapa ett kluster med den specifika SKU:n. Det finns inget stöd för att skapa en sekundär nodpool i ett befintligt kluster och distribuera modeller i den sekundära nodpoolen.
 
- När du skapar eller kopplar ett kluster kan du välja om du vill skapa klustret för __dev-test__ eller __produktion.__ Om du vill skapa ett AKS-kluster  för __utveckling,__ validering och testning i stället för produktion anger du  __klustersyftet__ till __dev-test__. Om du inte anger klustersyftet skapas __ett__ produktionskluster. 

    > [!IMPORTANT]
    > Ett __dev-test-kluster__ lämpar sig inte för trafik på produktionsnivå och kan öka inferenstiden. Dev/test-kluster garanterar inte heller feltolerans.

- Om klustret ska användas för produktion när du skapar eller kopplar ett kluster __måste__ det innehålla minst 12 __virtuella processorer__. Antalet virtuella processorer kan __beräknas__ genom  att multiplicera antalet noder i klustret med antalet kärnor som tillhandahålls av den valda VM-storleken. Om du till exempel använder VM-storleken "Standard_D3_v2", som har 4 virtuella kärnor, bör du välja 3 eller större som antalet noder.

    För ett __dev-test-kluster__ recommand vi minst 2 virtuella processorer.

- Sdk Azure Machine Learning har inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användargränssnittet för ditt AKS-kluster i Azure Machine Learning-studio. Du kan bara ändra antalet noder, inte vm-storleken på klustret. Mer information om hur du skalar noderna i ett AKS-kluster finns i följande artiklar:

    - [Skala antalet noder manuellt i ett AKS-kluster](../aks/scale-cluster.md)
    - [Konfigurera autoskalning av kluster i AKS](../aks/cluster-autoscaler.md)

- __Uppdatera inte klustret direkt med hjälp av en YAML-konfiguration.__ Azure Kubernetes Services stöder uppdateringar via YAML-konfiguration, Azure Machine Learning distributioner åsidosätter dina ändringar. De enda två YAML-fälten som inte skrivs över är __begärandegränser__ och __processor och minne.__

- Det är inte idempotent att skapa ett AKS Azure Machine Learning-studio med hjälp av Azure Machine Learning-studio-gränssnittet, SDK __eller__ CLI-tillägget. Om du försöker skapa resursen igen resulterar det i ett fel om att ett kluster med samma namn redan finns.
    
    - Att använda Azure Resource Manager en mall och [resursen Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) för att skapa ett AKS-kluster är inte __heller__ idempotent. Om du försöker använda mallen igen för att uppdatera en befintlig resurs får du samma felmeddelande.

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service-version

Azure Kubernetes Service kan du skapa ett kluster med en mängd olika Kubernetes-versioner. Mer information om tillgängliga versioner finns i [Kubernetes-versioner som stöds i Azure Kubernetes Service](../aks/supported-kubernetes-versions.md).

När **du** Azure Kubernetes Service ett kluster med någon av följande metoder har du inget val i *den version* av klustret som skapas:

* Azure Machine Learning-studio eller Azure Machine Learning i Azure Portal.
* Machine Learning för Azure CLI.
* Azure Machine Learning SDK.

De här metoderna för att skapa ett AKS-kluster __använder standardversionen__ av klustret. *Standardversionen ändras med tiden när* nya Kubernetes-versioner blir tillgängliga.

När **du kopplar ett** befintligt AKS-kluster stöder vi alla AKS-versioner som stöds för närvarande.

> [!NOTE]
> Det kan finnas gränsfall där du har ett äldre kluster som inte längre stöds. I det här fallet returnerar attach-åtgärden ett fel och visar en lista över de versioner som stöds för närvarande.
>
> Du kan bifoga **förhandsversioner.** Förhandsversionsfunktioner tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Stödet för att använda förhandsversioner kan vara begränsat. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Tillgängliga versioner och standardversioner

Du hittar tillgängliga och förvalda AKS-versioner med hjälp av [Azure CLI-kommandot](/cli/azure/install-azure-cli) [az aks get-versions](/cli/azure/aks#az_aks_get_versions). Följande kommando returnerar till exempel de versioner som är tillgängliga i regionen USA, västra:

```azurecli-interactive
az aks get-versions -l westus -o table
```

Kommandots utdata liknar följande text:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Om du vill hitta standardversionen som **används** när du skapar ett kluster Azure Machine Learning kan du använda `--query` parametern för att välja standardversion:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Kommandots utdata liknar följande text:

```text
Result
--------
1.16.13
```

Om du vill kontrollera de tillgängliga **versionerna programmatiskt** använder du [Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) för Container Service Client - List REST API. Du hittar de tillgängliga versionerna genom att titta på posterna där `orchestratorType` är `Kubernetes` . De associerade `orchestrationVersion` posterna innehåller de tillgängliga versioner som kan **kopplas till** din arbetsyta.

Om du vill hitta standardversionen som används när **du** skapar ett kluster Azure Machine Learning du posten där `orchestratorType` är och är `Kubernetes` `default` `true` . Det associerade `orchestratorVersion` värdet är standardversionen. Följande JSON-kodfragment visar en exempelpost:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Skapa ett nytt AKS-kluster

**Tidsuppskattning:** Cirka 10 minuter.

Att skapa eller koppla ett AKS-kluster är en enda process för din arbetsyta. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resursgruppen som innehåller det måste du skapa ett nytt kluster nästa gång du behöver distribuera. Du kan ha flera AKS-kluster anslutna till din arbetsyta.

I följande exempel visas hur du skapar ett nytt AKS-kluster med hjälp av SDK och CLI:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i referensen [az ml computetarget create aks.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Information om hur du skapar ett AKS-kluster i portalen finns i [Skapa beräkningsmål i Azure Machine Learning-studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tidsuppskattning:** Cirka 5 minuter.

Om du redan har ett AKS-kluster i din Azure-prenumeration kan du använda det med din arbetsyta.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en annan Azure-region än din Azure Machine Learning arbetsyta.


> [!WARNING]
> Skapa inte flera samtidiga bifogade filer i samma AKS-kluster från din arbetsyta. Du kan till exempel koppla ett AKS-kluster till en arbetsyta med två olika namn. Varje ny bifogad fil bryter den eller de tidigare befintliga bifogade filer.
>
> Om du vill ansluta ett AKS-kluster på nytt, till exempel för att ändra TLS eller andra konfigurationsinställningar för klustret, måste du först ta bort den befintliga bifogade filen med [hjälp av AksCompute.detach()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--).

Mer information om hur du skapar ett AKS-kluster med hjälp av Azure CLI eller portalen finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [Skapa ett AKS-kluster (portalen)](../aks/kubernetes-walkthrough-portal.md)
* [Skapa ett AKS-kluster (ARM-mall på Azure-snabbstartsmallar)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Följande exempel visar hur du kopplar ett befintligt AKS-kluster till din arbetsyta:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ansluta ett befintligt kluster med hjälp av CLI måste du hämta resurs-ID:t för det befintliga klustret. Använd följande kommando för att hämta det här värdet. Ersätt `myexistingcluster` med namnet på ditt AKS-kluster. Ersätt `myresourcegroup` med resursgruppen som innehåller klustret:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Det här kommandot returnerar ett värde som liknar följande text:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Om du vill koppla det befintliga klustret till arbetsytan använder du följande kommando. Ersätt `aksresourceid` med det värde som returnerades av föregående kommando. Ersätt `myresourcegroup` med den resursgrupp som innehåller din arbetsyta. Ersätt `myworkspace` med namnet på din arbetsyta.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Mer information finns i referensen [az ml computetarget attach aks.](/cli/azure/ml/computetarget/attach#az_ml_computetarget_attach_aks)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Information om hur du kopplar ett AKS-kluster i portalen finns i [Skapa beräkningsmål i Azure Machine Learning-studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Skapa eller koppla ett AKS-kluster med TLS-avslutning
När du [skapar eller kopplar ett AKS-kluster](how-to-create-attach-kubernetes.md)kan du aktivera TLS-avslutning med **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** **[och AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** konfigurationsobjekt. Båda metoderna returnerar ett konfigurationsobjekt **som har enable_ssl** metod och du kan använda **enable_ssl** för att aktivera TLS.

Följande exempel visar hur du aktiverar TLS-avslutning med automatisk TLS-certifikatgenerering och konfiguration med hjälp av Microsoft-certifikat under huven.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
Följande exempel visar hur du aktiverar TLS-avslutning med anpassat certifikat och anpassat domännamn. Med anpassad domän och certifikat måste du uppdatera DNS-posten så att den pekar på IP-adressen för bedömningsslutpunkten. Mer information finns [i Uppdatera din DNS](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Mer information om hur du skyddar modelldistribution i AKS-kluster finns i Använda [TLS](how-to-secure-web-service.md) för att skydda en webbtjänst via Azure Machine Learning

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Skapa eller koppla ett AKS-kluster för att använda Load Balancer med privat IP
När du skapar eller kopplar ett AKS-kluster kan du konfigurera klustret så att det använder en intern Load Balancer. Med en intern Load Balancer använder bedömningsslutpunkter för dina distributioner till AKS en privat IP-adress i det virtuella nätverket. Följande kodfragment visar hur du konfigurerar en intern Load Balancer för ett AKS-kluster.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning stöder inte TLS-avslutning med internt Load Balancer. Interna Load Balancer har en privat IP-adress och den privata IP-adressen kan finnas i ett annat nätverk och certifikat kan användas. 

>[!NOTE]
> Mer information om hur du skyddar inferencingsmiljön finns i [Skydda en Azure Machine Learning inferencingsmiljö](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Koppla från ett AKS-kluster

Om du vill koppla från ett kluster från arbetsytan använder du någon av följande metoder:

> [!WARNING]
> Med hjälp Azure Machine Learning-studio, SDK eller Azure CLI-tillägget för maskininlärning för att koppla från ett AKS-kluster tas inte **AKS-klustret bort.** Information om hur du tar bort klustret [finns i Använda Azure CLI med AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill koppla från det befintliga klustret till arbetsytan använder du följande kommando. Ersätt `myaks` med namnet som AKS-klustret är kopplat till din arbetsyta som. Ersätt `myresourcegroup` med resursgruppen som innehåller din arbetsyta. Ersätt `myworkspace` med namnet på din arbetsyta.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Machine Learning-studio väljer du __Compute,__ __Härledningskluster__ och det kluster som du vill ta bort. Använd länken __Koppla från__ för att koppla från klustret.

---

## <a name="troubleshooting"></a>Felsökning
### <a name="update-the-cluster"></a>Uppdatera klustret

Uppdateringar av Azure Machine Learning som installerats i ett Azure Kubernetes Service kluster måste tillämpas manuellt. 

Du kan tillämpa dessa uppdateringar genom att koppla från klustret från Azure Machine Learning-arbetsytan och sedan återansluta klustret till arbetsytan. Om TLS är aktiverat i klustret måste du ange TLS/SSL-certifikatet och den privata nyckeln när du återansluter klustret. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Om du inte längre har TLS/SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som genererats av Azure Machine Learning, kan du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med och hämta `kubectl` hemligheten `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i base-64-kodat format. Du måste base-64 avkoda komponenterna `cert.pem` `key.pem` och för hemligheterna innan du ger dem till `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Webbtjänstfel

Många webbtjänstfel i AKS kan felsökas genom att ansluta till klustret med hjälp av `kubectl` . Du kan hämta för `kubeconfig.json` ett AKS-kluster genom att köra

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Nästa steg

* [Använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)
* [Hur och var du distribuerar en modell](how-to-deploy-and-where.md)
* [Distribuera en modell till ett Azure Kubernetes Service kluster](how-to-deploy-azure-kubernetes-service.md)