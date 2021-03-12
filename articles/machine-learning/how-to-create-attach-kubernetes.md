---
title: Skapa och ansluta Azure Kubernetes-tjänsten
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar ett nytt Azure Kubernetes service-kluster via Azure Machine Learning eller hur du ansluter ett befintligt AKS-kluster till din arbets yta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.openlocfilehash: bc8f7aa6827ce251799acd0673d43344c0833c3a
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149332"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Skapa och ansluta ett Azure Kubernetes service-kluster

Azure Machine Learning kan distribuera utbildade maskin inlärnings modeller till Azure Kubernetes-tjänsten. Du måste dock först __skapa__ ett Azure Kubernetes service-kluster (AKS) från din Azure ml-arbetsyta eller __ansluta__ ett befintligt AKS-kluster. Den här artikeln innehåller information om hur du både skapar och bifogar ett kluster.

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- Om du planerar att använda en Azure-Virtual Network för att skydda kommunikationen mellan din Azure ML-arbetsyta och AKS-klustret kan du läsa [nätverks isoleringen under övnings &](./how-to-network-security-overview.md) artikeln.

## <a name="limitations"></a>Begränsningar

- Om du behöver ett **standard Load Balancer (SLB)** distribuerat i klustret i stället för en grundläggande load BALANCER (BLB) skapar du ett kluster i AKS-portalen/CLI/SDK och **kopplar** det sedan till AML-arbetsytan.

- Om du har en Azure Policy som begränsar skapandet av offentliga IP-adresser kommer AKS-kluster inte att kunna skapas. AKS kräver en offentlig IP-adress för [utgående trafik](../aks/limit-egress-traffic.md). Artikeln om utgående trafik ger också vägledning för att låsa utgående trafik från klustret via den offentliga IP-adressen, förutom några få fullständigt kvalificerade domän namn. Det finns två sätt att aktivera en offentlig IP-adress:
    - Klustret kan använda den offentliga IP-adressen som skapas som standard med BLB eller SLB, eller
    - Klustret kan skapas utan en offentlig IP-adress och en offentlig IP-adress konfigureras med en brand vägg med en användardefinierad väg. Mer information finns i [Anpassa klustret utgående med en användardefinierad väg](../aks/egress-outboundtype.md).
    
    Kontroll planet för AML pratar inte med den här offentliga IP-adressen. Den pratar med AKS-kontroll planet för distributioner. 

- Om du **ansluter** ett AKS-kluster, som har ett [auktoriserat IP-adressintervall som är aktiverat för att få åtkomst till API-servern](../aks/api-server-authorized-ip-ranges.md), aktiverar du IP-intervallen för AML Control plan för AKS-klustret. Kontroll planet för AML distribueras i kopplade regioner och distribuerar poddar på AKS-klustret. Utan åtkomst till API-servern går det inte att distribuera poddar. Använd [IP-intervallen](https://www.microsoft.com/download/confirmation.aspx?id=56519) för båda [kopplade regionerna](../best-practices-availability-paired-regions.md) när du aktiverar IP-intervall i ett AKS-kluster.

    Auktoriserade IP-intervall fungerar endast med Standard Load Balancer.

- När du **kopplar** ett AKS-kluster måste det finnas i samma Azure-prenumeration som din Azure Machine Learning-arbetsyta.

- Om du vill använda ett privat AKS-kluster (med Azures privata länk) måste du först skapa klustret och sedan **koppla** det till arbets ytan. Mer information finns i [skapa ett privat Azure Kubernetes service-kluster](../aks/private-clusters.md).

- Compute-namnet för AKS-klustret måste vara unikt i din Azure ML-arbetsyta.
    - Namnet måste vara mellan 3 och 24 tecken långt.
    - Giltiga tecken är versaler, gemener, siffror och tecknet-Character.
    - Namnet måste börja med en bokstav.
    - Namnet måste vara unikt för alla befintliga beräkningar i en Azure-region. En avisering visas om det namn du väljer inte är unikt.
   
 - Om du vill distribuera modeller till **GPU** -noder eller **FPGA** -noder (eller vissa SKU: er) måste du skapa ett kluster med den angivna SKU: n. Det finns inget stöd för att skapa en sekundär Node-pool i ett befintligt kluster och distribuera modeller i den sekundära noden.
 
- När du skapar eller ansluter ett kluster kan du välja om du vill skapa klustret för __utveckling och testning__ eller __produktion__. Om du vill skapa ett AKS-kluster för __utveckling__, __verifiering__ och __testning__ i stället för produktion, ställer du in __kluster syftet__ med __utveckling och testning__. Om du inte anger klustrets syfte skapas ett __produktions__ kluster. 

    > [!IMPORTANT]
    > Ett __dev-test-__ kluster lämpar sig inte för trafik på produktions nivå och kan öka eventuella härlednings tider. Utvecklings-och test kluster garanterar inte heller fel tolerans.

- Om klustret ska användas för __produktion__ när du skapar eller ansluter ett kluster måste det innehålla minst 12 __virtuella processorer__. Antalet virtuella processorer kan beräknas genom att multiplicera __antalet noder__ i klustret med __antalet kärnor__ som anges av den virtuella dator storleken som valts. Om du till exempel använder en VM-storlek på "Standard_D3_v2", som har 4 virtuella kärnor, väljer du 3 eller större som antalet noder.

    För ett __dev-test-__ kluster ska vi på minst 2 virtuella processorer.

- Azure Machine Learning SDK ger inte stöd för skalning av ett AKS-kluster. Om du vill skala noderna i klustret använder du användar gränssnittet för ditt AKS-kluster i Azure Machine Learning Studio. Du kan bara ändra antalet noder, inte klustrets virtuella dator storlek. Mer information om hur du skalar noderna i ett AKS-kluster finns i följande artiklar:

    - [Skala antalet noder manuellt i ett AKS-kluster](../aks/scale-cluster.md)
    - [Konfigurera autoskalning för kluster i AKS](../aks/cluster-autoscaler.md)

- __Uppdatera inte klustret direkt genom att använda en yaml-konfiguration__. Även om Azure Kubernetes Services stöder uppdateringar via YAML-konfiguration kommer Azure Machine Learning distributioner att åsidosätta dina ändringar. De enda två YAML-fält som inte skrivs över är __begär ande begränsningar__ och __CPU och minne__.

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service-version

Med Azure Kubernetes-tjänsten kan du skapa ett kluster med hjälp av en rad olika Kubernetes-versioner. Mer information om tillgängliga versioner finns [i Kubernetes-versioner som stöds i Azure Kubernetes-tjänsten](../aks/supported-kubernetes-versions.md).

När du **skapar** ett Azure Kubernetes service-kluster med någon av följande metoder *har du inte något val i den version* av klustret som skapas:

* Azure Machine Learning Studio eller Azure Machine Learning avsnittet i Azure Portal.
* Machine Learning tillägget för Azure CLI.
* Azure Machine Learning SDK.

Dessa metoder för att skapa ett AKS-kluster använder __standard__ versionen av klustret. *Standard versionen ändras med tiden* när nya Kubernetes-versioner blir tillgängliga.

När du **kopplar** ett befintligt AKS-kluster, stöder vi alla AKS-versioner som stöds för närvarande.

> [!NOTE]
> Det kan finnas Edge-fall där du har ett äldre kluster som inte längre stöds. I det här fallet returnerar åtgärden Attach ett fel och listar de versioner som stöds för tillfället.
>
> Du kan koppla för **hands** versioner. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Stöd för att använda för hands versioner kan vara begränsat. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Tillgängliga och standard versioner

Använd [Azure CLI](/cli/azure/install-azure-cli) [-kommandot AZ AKS get-versions](/cli/azure/aks#az_aks_get_versions)för att hitta tillgängliga och standard AKS-versioner. Följande kommando returnerar till exempel de versioner som är tillgängliga i regionen Västra USA:

```azurecli-interactive
az aks get-versions -l westus -o table
```

Utdata från det här kommandot liknar följande text:

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

Om du vill hitta standard versionen som används när du **skapar** ett kluster via Azure Machine Learning kan du använda- `--query` parametern för att välja standard versionen:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Utdata från det här kommandot liknar följande text:

```text
Result
--------
1.16.13
```

Om du vill **kontrol lera om det finns tillgängliga versioner program mässigt** kan du använda [behållar tjänstens klient list Dirigerings](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Ta reda på vilka versioner som är tillgängliga genom att titta på posterna där `orchestratorType` är `Kubernetes` . De associerade `orchestrationVersion` posterna innehåller de tillgängliga versioner som kan **kopplas** till din arbets yta.

Om du vill hitta standard versionen som används när du **skapar** ett kluster via Azure Machine Learning söker du efter posten där `orchestratorType` är `Kubernetes` och `default` `true` . Det associerade `orchestratorVersion` värdet är standard versionen. Följande JSON-kodfragment visar ett exempel på en post:

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

**Tids uppskattning**: cirka 10 minuter.

Att skapa eller ansluta ett AKS-kluster är en process för arbets ytan. Du kan återanvända det här klustret för flera distributioner. Om du tar bort klustret eller resurs gruppen som innehåller den måste du skapa ett nytt kluster nästa gången du behöver distribuera. Du kan ha flera AKS-kluster kopplade till din arbets yta.

Följande exempel visar hur du skapar ett nytt AKS-kluster med hjälp av SDK och CLI:

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

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Mer information finns i [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Information om hur du skapar ett AKS-kluster i portalen finns i [skapa beräknings mål i Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Koppla ett befintligt AKS-kluster

**Tids uppskattning:** Cirka 5 minuter.

Om du redan har AKS-kluster i din Azure-prenumeration kan du använda det med din arbets yta.

> [!TIP]
> Det befintliga AKS-klustret kan finnas i en annan Azure-region än din Azure Machine Learning-arbetsyta.


> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma AKS-kluster från din arbets yta. Du kan till exempel koppla ett AKS-kluster till en arbets yta med två olika namn. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.
>
> Om du vill koppla ett AKS-kluster på nytt, t. ex. ändra TLS eller en annan kluster konfigurations inställning, måste du först ta bort den befintliga bilagan med hjälp av [AksCompute. Detached ()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--).

Mer information om hur du skapar ett AKS-kluster med hjälp av Azure CLI eller portalen finns i följande artiklar:

* [Skapa ett AKS-kluster (CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az-aks-create)
* [Skapa ett AKS-kluster (portal)](../aks/kubernetes-walkthrough-portal.md)
* [Skapa ett AKS-kluster (ARM-mall i Azure snabb starts mallar)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Följande exempel visar hur du kopplar ett befintligt AKS-kluster till din arbets yta:

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

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute. attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill koppla ett befintligt kluster med CLI måste du hämta resurs-ID för det befintliga klustret. Använd följande kommando för att hämta det här värdet. Ersätt `myexistingcluster` med namnet på ditt AKS-kluster. Ersätt `myresourcegroup` med resurs gruppen som innehåller klustret:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Det här kommandot returnerar ett värde som liknar följande text:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Använd följande kommando för att koppla det befintliga klustret till din arbets yta. Ersätt `aksresourceid` med det värde som returnerades av föregående kommando. Ersätt `myresourcegroup` med den resurs grupp som innehåller din arbets yta. Ersätt `myworkspace` med namnet på din arbets yta.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Mer information finns i [AZ ml computetarget Attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Information om hur du kopplar ett AKS-kluster i portalen finns i [skapa beräknings mål i Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Skapa eller koppla ett AKS-kluster med TLS-terminering
När du [skapar eller ansluter ett AKS-kluster](how-to-create-attach-kubernetes.md)kan du Aktivera TLS-avslutning med **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** och **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** konfigurations objekt. Båda metoderna returnerar ett konfigurations objekt som har en **enable_ssl** -Metod och du kan använda **enable_ssl** -metoden för att aktivera TLS.

Följande exempel visar hur du aktiverar TLS-uppsägning med automatisk generering och konfiguration av TLS-certifikat med hjälp av Microsoft-certifikat under huven.
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
Följande exempel visar hur du aktiverar TLS-avslutning med anpassat certifikat och anpassat domän namn. Med anpassad domän och certifikat måste du uppdatera DNS-posten så att den pekar på IP-adressen för bedömnings slut punkten. se [Uppdatera DNS](how-to-secure-web-service.md#update-your-dns)

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
> Mer information om hur du skyddar modell distribution i AKS-kluster finns i [använda TLS för att skydda en webb tjänst via Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Skapa eller koppla ett AKS-kluster för att använda interna Load Balancer med privat IP
När du skapar eller ansluter ett AKS-kluster kan du konfigurera klustret så att det använder en intern Load Balancer. Med ett internt Load Balancer använder bedömnings slut punkter för dina distributioner av AKS en privat IP-adress i det virtuella nätverket. Följande kodfragment visar hur du konfigurerar ett internt Load Balancer för ett AKS-kluster.
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
> Azure Machine Learning stöder inte TLS-avslutning med interna Load Balancer. Interna Load Balancer har en privat IP-adress och att den privata IP-adressen kan finnas i ett annat nätverk och certifikat kan vara recused. 

>[!NOTE]
> Mer information om hur du skyddar inferencing-miljön finns i [skydda en Azure Machine Learning inferencing-miljö](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>Koppla från ett AKS-kluster

Använd någon av följande metoder för att koppla från ett kluster från din arbets yta:

> [!WARNING]
> Om du använder Azure Machine Learning Studio, SDK eller Azure CLI-tillägget för Machine Learning för att koppla från ett AKS-kluster **tas inte AKS-klustret bort**. Information om hur du tar bort klustret finns i [använda Azure CLI med AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande kommando för att koppla bort det befintliga klustret till din arbets yta. Ersätt `myaks` med namnet som AKS-klustret är kopplat till din arbets yta som. Ersätt `myresourcegroup` med den resurs grupp som innehåller din arbets yta. Ersätt `myworkspace` med namnet på din arbets yta.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

I Azure Machine Learning Studio väljer du __beräknings__-, __härlednings kluster__ och det kluster som du vill ta bort. Använd __Koppla från__ -länken för att koppla från klustret.

---

## <a name="troubleshooting"></a>Felsökning
### <a name="update-the-cluster"></a>Uppdatera klustret

Uppdateringar av Azure Machine Learning-komponenter som är installerade i ett Azure Kubernetes service-kluster måste appliceras manuellt. 

Du kan tillämpa dessa uppdateringar genom att koppla från klustret från arbets ytan Azure Machine Learning och sedan återansluta klustret till arbets ytan. Om TLS är aktiverat i klustret måste du ange TLS/SSL-certifikatet och den privata nyckeln när du återansluter klustret. 

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

Om du inte längre har TLS/SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som har genererats av Azure Machine Learning, kan du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med hjälp av `kubectl` och hämta hemligheten `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i Base-64-kodat format. Du behöver Base-64-avkoda- `cert.pem` och- `key.pem` komponenterna i hemligheterna innan du ger dem `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Problem med webservice

Många WebService-fel i AKS kan felsökas genom att ansluta till klustret med hjälp av `kubectl` . Du kan hämta `kubeconfig.json` ett AKS-kluster genom att köra

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Nästa steg

* [Använd Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md)
* [Hur och var du distribuerar en modell](how-to-deploy-and-where.md)
* [Distribuera en modell till ett Azure Kubernetes service-kluster](how-to-deploy-azure-kubernetes-service.md)