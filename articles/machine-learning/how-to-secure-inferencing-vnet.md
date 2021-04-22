---
title: Skydda inferensmiljöer med virtuella nätverk
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network för att Azure Machine Learning din inferensmiljö.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872559"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Skydda en Azure Machine Learning en inferenterande miljö med virtuella nätverk

I den här artikeln får du lära dig hur du skyddar inferensmiljöer med ett virtuellt nätverk i Azure Machine Learning.

Den här artikeln är del fyra i en serie om fem delar som steg för steg beskriver hur du Azure Machine Learning ett arbetsflöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt för](how-to-network-security-overview.md) att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  [Skydda arbetsytan](how-to-secure-workspace-vnet.md)  >  [3. Skydda träningsmiljön](how-to-secure-training-vnet.md)  >  **4. Skydda inferensmiljön**  >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

I den här artikeln får du lära dig hur du skyddar följande inferensresurser i ett virtuellt nätverk:
> [!div class="checklist"]
> - AKS Azure Kubernetes Service kluster (Default Azure Kubernetes Service)
> - Privat AKS-kluster
> - AKS-kluster med privat länk
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Förutsättningar

+ Läs artikeln [Översikt över nätverkssäkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräkningsresurser.

+ Om du vill distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användarkonto ha behörighet till följande åtgärder i rollbaserad åtkomstkontroll i Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" på den virtuella nätverksresursen.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" på undernätsresursen.

    Mer information om Azure RBAC med nätverk finns i [Inbyggda roller för nätverk](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Om du vill använda ett AKS-kluster i ett virtuellt nätverk måste följande nätverkskrav vara uppfyllda:

> [!div class="checklist"]
> * Följ förutsättningarna i [Konfigurera avancerade nätverk i Azure Kubernetes Service (AKS).](../aks/configure-azure-cni.md#prerequisites)
> * AKS-instansen och det virtuella nätverket måste finnas i samma region. Om du skyddar Azure Storage-kontona som används av arbetsytan i ett virtuellt nätverk måste de också finnas i samma virtuella nätverk som AKS-instansen.

Använd följande steg för att lägga till AKS i ett virtuellt nätverk på din arbetsyta:

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com/)och välj sedan din prenumeration och arbetsyta.

1. Välj __Compute__ till vänster.

1. Välj __Härledningskluster__ från mitten och välj sedan __+__ .

1. I dialogrutan __Nytt härledningskluster__ väljer du __Avancerat__ under __Nätverkskonfiguration__.

1. Utför följande åtgärder för att konfigurera beräkningsresursen så att den använder ett virtuellt nätverk:

    1. I __listrutan__ Resursgrupp väljer du den resursgrupp som innehåller det virtuella nätverket.
    1. I __listrutan Virtuellt__ nätverk väljer du det virtuella nätverk som innehåller undernätet.
    1. I __listrutan Undernät__ väljer du undernätet.
    1. I rutan __Kubernetes Service-adressintervall__ anger du Kubernetes-tjänstens adressintervall. Det här adressintervallet använder ett CIDR-notationsintervall (Classless Inter-Domain Routing) för att definiera de IP-adresser som är tillgängliga för klustret. Det får inte överlappa med några IP-intervall för undernätet (till exempel 10.0.0.0/16).
    1. I rutan __IP-adress för Kubernetes DNS-tjänst__ anger du Kubernetes DNS-tjänstens IP-adress. Den här IP-adressen tilldelas till Kubernetes DNS-tjänsten. Det måste vara inom Kubernetes-tjänstens adressintervall (till exempel 10.0.0.10).
    1. I rutan __Docker-bryggadress__ anger du Docker-bryggadressen. Den här IP-adressen tilldelas Docker Bridge. Det får inte finnas i något undernäts IP-intervall eller Kubernetes-tjänstens adressintervall (till exempel 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning-beräkning för virtuella nätverk](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. När du distribuerar en modell som en webbtjänst till AKS skapas en bedömningsslutpunkt för att hantera inferensbegäranden. Kontrollera att NSG-gruppen som styr det virtuella nätverket har en inkommande säkerhetsregel aktiverad för IP-adressen för bedömningsslutpunkten om du vill anropa den utanför det virtuella nätverket.

    Du hittar IP-adressen för bedömningsslutpunkten genom att titta på bedömnings-URI för den distribuerade tjänsten. Information om hur du visar bedömnings-URI finns [i Använda en modell som distribuerats som en webbtjänst.](how-to-consume-web-service.md#connection-information)

   > [!IMPORTANT]
   > Behåll standardreglerna för utgående trafik för NSG:n. Mer information finns i standardsäkerhetsreglerna i [Säkerhetsgrupper.](../virtual-network/network-security-groups-overview.md#default-security-rules)

   [![En inkommande säkerhetsregel](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > IP-adressen som visas i bilden för bedömningsslutpunkten skiljer sig åt för dina distributioner. Samma IP-adress delas av alla distributioner till ett AKS-kluster, men varje AKS-kluster har olika IP-adresser.

Du kan också använda Azure Machine Learning SDK för att lägga Azure Kubernetes Service i ett virtuellt nätverk. Om du redan har ett AKS-kluster i ett virtuellt nätverk kopplar du det till arbetsytan enligt beskrivningen i Så [här distribuerar du till AKS](how-to-deploy-and-where.md). Följande kod skapar en ny AKS-instans i `default` undernätet för ett virtuellt nätverk med namnet `mynetwork` :

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

När skapandeprocessen är klar kan du köra slutsatsledning, eller modellbedömning, i ett AKS-kluster bakom ett virtuellt nätverk. Mer information finns i [Så här distribuerar du till AKS.](how-to-deploy-and-where.md)

Mer information om hur du använder Role-Based Access Control med Kubernetes finns i [Använda Azure RBAC för Kubernetes-auktorisering](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Nätverksdeltagareroll

> [!IMPORTANT]
> Om du skapar eller kopplar ett AKS-kluster genom att tillhandahålla ett virtuellt nätverk som du skapade  tidigare, måste du tilldela tjänstens huvudnamn (SP) eller den hanterade identiteten för ditt AKS-kluster rollen Nätverksdeltagare till den resursgrupp som innehåller det virtuella nätverket.
>
> Använd följande steg för att lägga till identiteten som nätverksdeltagare:

1. Använd följande Azure CLI-kommandon för att hitta tjänstens huvudnamn eller ID för hanterad identitet för AKS. Ersätt `<aks-cluster-name>` med namnet på klustret. Ersätt `<resource-group-name>` med namnet på resursgruppen som innehåller _AKS-klustret:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Om det här kommandot returnerar värdet `msi` använder du följande kommando för att identifiera huvudnamns-ID:t för den hanterade identiteten:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Använd följande kommando för att hitta ID:t för resursgruppen som innehåller ditt virtuella nätverk. Ersätt `<resource-group-name>` med namnet på resursgruppen som innehåller det virtuella _nätverket_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Om du vill lägga till tjänstens huvudnamn eller hanterade identitet som nätverksdeltagare använder du följande kommando. Ersätt `<SP-or-managed-identity>` med det ID som returneras för tjänstens huvudnamn eller hanterade identitet. Ersätt `<resource-group-id>` med det ID som returneras för resursgruppen som innehåller det virtuella nätverket:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Mer information om hur du använder den interna lastbalanseraren med AKS finns i [Använda intern lastbalanserare med Azure Kubernetes Service](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Säker VNet-trafik

Det finns två sätt att isolera trafik till och från AKS-klustret till det virtuella nätverket:

* __Privat AKS-kluster:__ Den här metoden använder Azure Private Link för att skydda kommunikationen med klustret för distribution/hanteringsåtgärder.
* __Intern AKS-lastbalanserare:__ Den här metoden konfigurerar slutpunkten för dina distributioner till AKS för att använda en privat IP-adress i det virtuella nätverket.

> [!WARNING]
> Intern lastbalanserare fungerar inte med ett AKS-kluster som använder kubenet. Om du vill använda en intern lastbalanserare och ett privat AKS-kluster samtidigt konfigurerar du ditt privata AKS-kluster med Azure Container Networking Interface (CNI). Mer information finns i [Konfigurera Azure CNI nätverk i Azure Kubernetes Service](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Privat AKS-kluster

Som standard har AKS-kluster ett kontrollplan eller en API-server med offentliga IP-adresser. Du kan konfigurera AKS att använda ett privat kontrollplan genom att skapa ett privat AKS-kluster. Mer information finns i Skapa [ett privat Azure Kubernetes Service kluster.](../aks/private-clusters.md)

När du har skapat det privata AKS-klustret [kopplar du klustret till det virtuella nätverket som](how-to-create-attach-kubernetes.md) ska användas med Azure Machine Learning.

> [!IMPORTANT]
> Innan du använder ett AKS-kluster med privat länk Azure Machine Learning måste du öppna ett supportincident för att aktivera den här funktionen. Mer information finns i [Hantera och öka kvoter.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

### <a name="internal-aks-load-balancer"></a>Intern AKS-lastbalanserare

Som standard använder AKS-distributioner en [offentlig lastbalanserare](../aks/load-balancer-standard.md). I det här avsnittet får du lära dig hur du konfigurerar AKS för att använda en intern lastbalanserare. En intern (eller privat) lastbalanserare används där endast privata IP-adresser tillåts som frontend. Interna lastbalanserare används för att belastningsutjämna trafik i ett virtuellt nätverk

En privat lastbalanserare aktiveras genom att konfigurera AKS att använda en _intern lastbalanserare_. 

#### <a name="enable-private-load-balancer"></a>Aktivera privat lastbalanserare

> [!IMPORTANT]
> Du kan inte aktivera privat IP när du skapar Azure Kubernetes Service klustret i Azure Machine Learning-studio. Du kan skapa en med en intern lastbalanserare när du använder Python SDK eller Azure CLI-tillägget för maskininlärning.

Följande exempel visar hur du skapar __ett nytt AKS-kluster med en privat IP-adress/intern lastbalanserare__ med SDK och CLI:

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
> Med CLI kan du bara skapa ett AKS-kluster med en intern lastbalanserare. Det finns inget az ml-kommando för att uppgradera ett befintligt kluster för att använda en intern lastbalanserare.

Mer information finns i referensen [az ml computetarget create aks.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks)

---

När __du kopplar ett befintligt kluster till__ arbetsytan måste du vänta tills efter anslutningåtgärden för att konfigurera lastbalanseraren. Information om hur du kopplar ett kluster finns i [Koppla ett befintligt AKS-kluster.](how-to-create-attach-kubernetes.md)

När du har bifogat det befintliga klustret kan du uppdatera klustret så att det använder en intern lastbalanserare/privat IP-adress:

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

Azure Container Instances skapas dynamiskt när du distribuerar en modell. Om du Azure Machine Learning skapa ACI i det virtuella nätverket måste du aktivera __delegering__ av undernät för det undernät som används av distributionen.

> [!WARNING]
> När du Azure Container Instances i ett virtuellt nätverk måste det virtuella nätverket vara:
> * I samma resursgrupp som din Azure Machine Learning arbetsyta.
> * Om arbetsytan har en privat __slutpunkt__ måste det virtuella nätverk som används för Azure Container Instances vara samma som det som används av arbetsytans privata slutpunkt.
>
> När du Azure Container Instances i det virtuella nätverket kan inte Azure Container Registry (ACR) för din arbetsyta finnas i det virtuella nätverket.

Använd följande steg om du vill använda ACI i ett virtuellt nätverk till din arbetsyta:

1. Om du vill aktivera delegering av undernät i ditt virtuella nätverk använder du informationen i artikeln [Lägga till eller ta bort en undernätsdelegering.](../virtual-network/manage-subnet-delegation.md) Du kan aktivera delegering när du skapar ett virtuellt nätverk eller lägga till det i ett befintligt nätverk.

    > [!IMPORTANT]
    > När du aktiverar delegering använder `Microsoft.ContainerInstance/containerGroups` du som värdet Delegera __undernät till__ tjänst.

2. Distribuera modellen med [hjälp AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), använd `vnet_name` `subnet_name` parametrarna och . Ange de här parametrarna till det virtuella nätverkets namn och undernät där du har aktiverat delegering.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Begränsa utgående anslutningar från det virtuella nätverket

Om du inte vill använda standardreglerna för utgående trafik och vill begränsa utgående åtkomst för ditt virtuella nätverk måste du tillåta åtkomst till Azure Container Registry. Kontrollera till exempel att dina nätverkssäkerhetsgrupper (NSG) innehåller en regel som tillåter åtkomst till __tjänsttaggen AzureContainerRegistry.RegionName__ där {RegionName} är namnet på en Azure-region.

## <a name="next-steps"></a>Nästa steg

Den här artikeln är del fyra i en serie med fem virtuella nätverk. I resten av artiklarna får du lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: Översikt över virtuellt nätverk](how-to-network-security-overview.md)
* [Del 2: Skydda arbetsytans resurser](how-to-secure-workspace-vnet.md)
* [Del 3: Skydda träningsmiljön](how-to-secure-training-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

Se även artikeln om hur du använder [anpassad DNS för](how-to-custom-dns.md) namnmatchning.