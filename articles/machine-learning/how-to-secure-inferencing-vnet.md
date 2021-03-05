---
title: Skydda inferencing-miljöer med virtuella nätverk
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network för att skydda din Azure Machine Learning inferencing-miljö.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 8c47478f1fccfac0c59b4a58a2767013490f869d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209503"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Skydda en Azure Machine Learning inferencing-miljö med virtuella nätverk

I den här artikeln får du lära dig hur du skyddar inferencing-miljöer med ett virtuellt nätverk i Azure Machine Learning.

Den här artikeln är del fyra i en serie med fem delar som vägleder dig genom att skydda ett Azure Machine Learning arbets flöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt](how-to-network-security-overview.md) för att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  [skydda arbets ytan](how-to-secure-workspace-vnet.md)  >  [3. Skydda inlärnings miljö](how-to-secure-training-vnet.md)  >  **4. Skydda inferencing-miljön**  >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

I den här artikeln får du lära dig att skydda följande inferencing-resurser i ett virtuellt nätverk:
> [!div class="checklist"]
> - Standard Azure Kubernetes service-kluster (AKS)
> - Privat AKS-kluster
> - AKS-kluster med privat länk
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Förutsättningar

+ Läs artikeln [Översikt över nätverks säkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräknings resurser.

+ För att distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användar konto ha behörighet till följande åtgärder i rollbaserad åtkomst kontroll i Azure (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" på den virtuella nätverks resursen.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" på under näts resursen.

    Mer information om Azure RBAC med nätverk finns i [inbyggda nätverks roller](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Om du vill använda ett AKS-kluster i ett virtuellt nätverk måste följande nätverks krav uppfyllas:

> [!div class="checklist"]
> * Följ kraven i [Konfigurera Advanced Networking i Azure Kubernetes service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * AKS-instansen och det virtuella nätverket måste finnas i samma region. Om du skyddar de Azure Storage-konton som används av arbets ytan i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som AKS-instansen.

Använd följande steg för att lägga till AKS i ett virtuellt nätverk till din arbets yta:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/)och välj sedan din prenumeration och arbets yta.

1. Välj __Beräkna__ till vänster.

1. Välj __härlednings kluster__ från mitten och välj sedan __+__ .

1. I dialog rutan __nytt kluster__ för __överbelastnings__ kluster väljer du __Avancerat__ under nätverks konfiguration.

1. Utför följande åtgärder för att konfigurera beräknings resursen för att använda ett virtuellt nätverk:

    1. I list rutan __resurs grupp__ väljer du den resurs grupp som innehåller det virtuella nätverket.
    1. I list rutan __virtuellt nätverk__ väljer du det virtuella nätverk som innehåller under nätet.
    1. I list rutan __undernät__ väljer du under nätet.
    1. I rutan __Kubernetes service-adressintervall__ anger du tjänst adress intervallet för Kubernetes. I det här adress intervallet används CIDR-notation (Classless Inter-Domain routing) för att definiera de IP-adresser som är tillgängliga för klustret. Det får inte överlappa några IP-adressintervall för undernät (till exempel 10.0.0.0/16).
    1. I rutan __IP-adress för KUBERNETES DNS-tjänst__ anger du IP-adressen för DNS-tjänsten Kubernetes. Den här IP-adressen är tilldelad DNS-tjänsten Kubernetes. Det måste ligga inom Kubernetes-tjänstens adress intervall (till exempel 10.0.0.10).
    1. I rutan __Docker Bridge-adress__ anger du Docker-bryggans adress. Den här IP-adressen är tilldelad Docker-bryggan. Det får inte finnas i några IP-adressintervall för undernät eller Kubernetes-tjänstens adress intervall (till exempel 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning-beräkning inställningar för virtuellt nätverk](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. När du distribuerar en modell som en webb tjänst till AKS skapas en poäng slut punkt för att hantera inferencing-begäranden. Kontrol lera att NSG-gruppen som styr det virtuella nätverket har en inkommande säkerhets regel aktive rad för beräknings slut punktens IP-adress om du vill anropa den utanför det virtuella nätverket.

    Du hittar IP-adressen för bedömnings slut punkten genom att titta på bedömnings-URI: n för den distribuerade tjänsten. Information om hur du visar bedömnings-URI: n finns i [använda en modell som distribueras som en webb tjänst](how-to-consume-web-service.md#connection-information).

   > [!IMPORTANT]
   > Behåll de utgående standard reglerna för NSG. Mer information finns i standard säkerhets regler i [säkerhets grupper](../virtual-network/network-security-groups-overview.md#default-security-rules).

   [![En inkommande säkerhets regel](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > IP-adressen som visas i avbildningen för bedömnings slut punkten kommer att vara en annan för dina distributioner. Samma IP-adress delas av alla distributioner till ett AKS-kluster, men varje AKS-kluster har en annan IP-adress.

Du kan också använda Azure Machine Learning SDK för att lägga till Azure Kubernetes-tjänsten i ett virtuellt nätverk. Om du redan har ett AKS-kluster i ett virtuellt nätverk ansluter du det till arbets ytan enligt beskrivningen i [så här distribuerar du till AKS](how-to-deploy-and-where.md). Följande kod skapar en ny AKS-instans i `default` under nätet för ett virtuellt nätverk med namnet `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

När processen har skapats kan du köra en härledning eller modell bedömning i ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [så här distribuerar du till AKS](how-to-deploy-and-where.md).

Mer information om hur du använder Role-Based Access Control med Kubernetes finns i [använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Rollen nätverks deltagare

> [!IMPORTANT]
> Om du skapar eller ansluter ett AKS-kluster genom att tillhandahålla ett virtuellt nätverk som du skapade tidigare, måste du bevilja tjänstens huvud namn (SP) eller hanterad identitet för ditt AKS-kluster rollen _nätverks deltagare_ till den resurs grupp som innehåller det virtuella nätverket.
>
> Använd följande steg för att lägga till identiteten som en nätverks deltagare:

1. Använd följande Azure CLI-kommandon för att hitta tjänstens huvud namn eller hanterat identitets-ID för AKS. Ersätt `<aks-cluster-name>` med namnet på klustret. Ersätt `<resource-group-name>` med namnet på den resurs grupp som _innehåller AKS-klustret_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Om det här kommandot returnerar värdet `msi` använder du följande kommando för att identifiera ägar-ID: t för den hanterade identiteten:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Använd följande kommando för att hitta ID: t för den resurs grupp som innehåller ditt virtuella nätverk. Ersätt `<resource-group-name>` med namnet på den resurs grupp som _innehåller det virtuella nätverket_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Använd följande kommando om du vill lägga till tjänstens huvud namn eller hanterade identitet som en nätverks deltagare. Ersätt `<SP-or-managed-identity>` med det ID som returnerades för tjänstens huvud namn eller hanterad identitet. Ersätt `<resource-group-id>` med det ID som returnerades för resurs gruppen som innehåller det virtuella nätverket:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Mer information om hur du använder den interna belastningsutjämnaren med AKS finns i [använda intern belastningsutjämnare med Azure Kubernetes service](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Säker VNet-trafik

Det finns två metoder för att isolera trafik till och från AKS-klustret till det virtuella nätverket:

* __Privat AKS-kluster__: den här metoden använder Azures privata länk för att skydda kommunikationen med klustret för distribution/hantering.
* __Intern AKS-belastningsutjämnare__: den här metoden konfigurerar slut punkten för dina distributioner till AKS för att använda en privat IP-adress i det virtuella nätverket.

> [!WARNING]
> Den interna belastningsutjämnaren fungerar inte med ett AKS-kluster som använder Kubernetes. Om du vill använda en intern belastningsutjämnare och ett privat AKS-kluster samtidigt konfigurerar du ditt privata AKS-kluster med Azure Container Network Interface (CNI). Mer information finns i [Konfigurera Azure cni-nätverk i Azure Kubernetes-tjänsten](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Privat AKS-kluster

Som standard har AKS-kluster ett kontroll plan eller en API-server med offentliga IP-adresser. Du kan konfigurera AKS till att använda ett privat kontroll plan genom att skapa ett privat AKS-kluster. Mer information finns i [skapa ett privat Azure Kubernetes service-kluster](../aks/private-clusters.md).

När du har skapat det privata AKS-klustret [ansluter du klustret till det virtuella nätverket](how-to-create-attach-kubernetes.md) som ska användas med Azure Machine Learning.

> [!IMPORTANT]
> Innan du använder en privat länk som är aktive rad AKS-kluster med Azure Machine Learning måste du öppna en support incident för att aktivera den här funktionen. Mer information finns i [Hantera och öka kvoter](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

### <a name="internal-aks-load-balancer"></a>Intern belastningsutjämnare för AKS

Som standard använder AKS-distributioner en [offentlig belastningsutjämnare](../aks/load-balancer-standard.md). I det här avsnittet får du lära dig hur du konfigurerar AKS till att använda en intern belastningsutjämnare. En intern (eller privat) belastningsutjämnare används där endast privata IP-adresser tillåts som klient del. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk

En privat belastningsutjämnare är aktive rad genom att konfigurera AKS för att använda en _intern belastningsutjämnare_. 

#### <a name="enable-private-load-balancer"></a>Aktivera privat belastningsutjämnare

> [!IMPORTANT]
> Du kan inte aktivera privat IP när du skapar Azure Kubernetes service-klustret i Azure Machine Learning Studio. Du kan skapa en med hjälp av en intern belastningsutjämnare när du använder python SDK eller Azure CLI-tillägget för Machine Learning.

Följande exempel visar hur du __skapar ett nytt AKS-kluster med en privat IP/intern belastningsutjämnare__ med hjälp av SDK och CLI:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> Med CLI kan du bara skapa ett AKS-kluster med en intern belastningsutjämnare. Det finns inget AZ ml-kommando för att uppgradera ett befintligt kluster så att det använder en intern belastningsutjämnare.

Mer information finns i [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

---

När du __kopplar ett befintligt kluster__ till din arbets yta, måste du vänta tills efter åtgärden för att lägga till belastningsutjämnaren. Information om hur du kopplar ett kluster finns i [bifoga ett befintligt AKS-kluster](how-to-create-attach-kubernetes.md).

När du har kopplat det befintliga klustret kan du uppdatera klustret så att det använder en intern belastningsutjämnare/privat IP:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Aktivera Azure Container Instances (ACI)

Azure Container Instances skapas dynamiskt när du distribuerar en modell. Om du vill aktivera Azure Machine Learning att skapa ACI i det virtuella nätverket måste du aktivera __under näts delegering__ för under nätet som används av distributionen.

> [!WARNING]
> När du använder Azure Container Instances i ett virtuellt nätverk måste det virtuella nätverket vara:
> * I samma resurs grupp som din Azure Machine Learning-arbetsyta.
> * Om din arbets yta har en __privat slut punkt__ måste det virtuella nätverket som används för Azure Container instances vara samma som det som används av den privata slut punkten för arbets ytan.
>
> När du använder Azure Container Instances inuti det virtuella nätverket kan Azure Container Registry (ACR) för din arbets yta inte finnas i det virtuella nätverket.

Använd följande steg för att använda ACI i ett virtuellt nätverk på din arbets yta:

1. Om du vill aktivera under näts delegering i det virtuella nätverket använder du informationen i artikeln [Lägg till eller ta bort en under näts delegering](../virtual-network/manage-subnet-delegation.md) . Du kan aktivera delegering när du skapar ett virtuellt nätverk eller lägga till det i ett befintligt nätverk.

    > [!IMPORTANT]
    > När du aktiverar delegering ska `Microsoft.ContainerInstance/containerGroups` du använda som värde för __tjänsten delegera till tjänst__ .

2. Distribuera modellen med hjälp av [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), Använd `vnet_name` `subnet_name` parametrarna och. Ange de här parametrarna som namn på det virtuella nätverket och under nätet där du aktiverade delegering.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Begränsa utgående anslutningar från det virtuella nätverket

Om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk måste du tillåta åtkomst till Azure Container Registry. Kontrol lera till exempel att dina nätverks säkerhets grupper (NSG) innehåller en regel som tillåter åtkomst till __AzureContainerRegistry. RegionName__ service tag där {RegionName} är namnet på en Azure-region.

## <a name="next-steps"></a>Nästa steg

Den här artikeln är del fyra av en serie virtuella nätverks serier i fem delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: översikt över virtuella nätverk](how-to-network-security-overview.md)
* [Del 2: skydda arbets ytans resurser](how-to-secure-workspace-vnet.md)
* [Del 3: skydda tränings miljön](how-to-secure-training-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)