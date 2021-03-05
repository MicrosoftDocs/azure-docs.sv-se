---
title: Säkra utbildnings miljöer med virtuella nätverk
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network för att skydda din Azure Machine Learning utbildnings miljö.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: cb034c42ceee870ed7be72eb5ff7d29c42b72f36
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183598"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Skydda en Azure Machine Learning utbildnings miljö med virtuella nätverk

I den här artikeln får du lära dig hur du skyddar utbildnings miljöer med ett virtuellt nätverk i Azure Machine Learning.

Den här artikeln är del tre i en serie med fem delar som vägleder dig genom att skydda ett Azure Machine Learning-arbetsflöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt](how-to-network-security-overview.md) för att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  [2. Skydda arbets ytan](how-to-secure-workspace-vnet.md)  >  **3. Skydda inlärnings miljö**  >  [4. Skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)   >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

I den här artikeln får du lära dig att skydda följande utbildnings beräknings resurser i ett virtuellt nätverk:
> [!div class="checklist"]
> - Azure Machine Learning beräknings kluster
> - Azure Machine Learning-beräkningsinstans
> - Azure Databricks
> - Virtuell dator
> - HDInsight-kluster

## <a name="prerequisites"></a>Förutsättningar

+ Läs artikeln [Översikt över nätverks säkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräknings resurser.

+ För att distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användar konto ha behörighet till följande åtgärder i rollbaserad åtkomst kontroll i Azure (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" på den virtuella nätverks resursen.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" på under näts resursen.

    Mer information om Azure RBAC med nätverk finns i [inbyggda nätverks roller](../role-based-access-control/built-in-roles.md#networking)


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Beräknings kluster & instanser 

Om du vill använda en [hanterad Azure Machine Learning __beräknings mål__](concept-compute-target.md#azure-machine-learning-compute-managed) eller en [Azure Machine Learning beräknings __instans__](concept-compute-instance.md) i ett virtuellt nätverk måste följande nätverks krav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
> * Under nätet som anges för beräknings instansen eller klustret måste ha tillräckligt många otilldelade IP-adresser för att rymma antalet virtuella datorer som är riktade. Om under nätet inte har tillräckligt med otilldelade IP-adresser, tilldelas ett beräknings kluster delvis.
> * Kontrol lera om dina säkerhets principer eller lås på det virtuella nätverkets prenumeration eller resurs grupp begränsar behörigheter för hantering av det virtuella nätverket. Om du planerar att skydda det virtuella nätverket genom att begränsa trafiken lämnar du vissa portar öppna för beräknings tjänsten. Mer information finns i avsnittet [nödvändiga portar](#mlcports) .
> * Om du ska lagra flera beräknings instanser eller kluster i ett virtuellt nätverk kan du behöva begära en kvot ökning för en eller flera av dina resurser.
> * Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Machine Learning beräknings instans eller kluster. 
> * För att Compute instance Jupyter-funktionen ska fungera kontrollerar du att WebSocket-kommunikation inte är inaktiverat. Kontrol lera att nätverket tillåter WebSocket-anslutningar till *. instances.azureml.net och *. instances.azureml.ms. 
> * När beräknings instansen distribueras i en privat länk arbets yta kan den bara nås från det virtuella nätverket. Om du använder en anpassad DNS-eller Hosts-fil lägger du till en post för `<instance-name>.<region>.instances.azureml.ms` med privat IP-adress för arbets ytans privata slut punkt. Mer information finns i den [anpassade DNS-](./how-to-custom-dns.md) artikeln.
> * Det undernät som används för att distribuera beräknings kluster/instans ska inte delegeras till någon annan tjänst som ACI
> * Slut punkts principer för virtuella nätverks tjänster fungerar inte för beräknings kluster/instans system lagrings konton

    
> [!TIP]
> Machine Learning beräknings instans eller kluster allokerar automatiskt ytterligare nätverks resurser __i resurs gruppen som innehåller det virtuella nätverket__. För varje beräknings instans eller kluster allokerar tjänsten följande resurser:
> 
> * En nätverks säkerhets grupp
> * En offentlig IP-adress. Om du har en Azure-princip som förhindrar att offentliga IP skapas, kommer distributionen av kluster/instanser att Miss Miss sen
> * En belastningsutjämnare
> 
> I kluster är de här resurserna borttagna (och återskapas) varje gång klustret skalar ned till 0 noder, men för en instans är resurserna kvar på till instansen helt borttagna (stoppa tar inte bort resurserna). 
> Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-resource-manager/management/azure-subscription-service-limits.md). Om den virtuella nätverks resurs gruppen är låst går det inte att ta bort beräknings kluster/instanser. Det går inte att ta bort belastningsutjämnaren förrän beräknings klustret/instansen har tagits bort. Kontrol lera också att det inte finns någon Azure-princip som förhindrar skapande av nätverks säkerhets grupper.


### <a name="required-ports"></a><a id="mlcports"></a> Portar som krävs

Om du planerar att skydda det virtuella nätverket genom att begränsa nätverks trafiken till/från det offentliga Internet måste du tillåta inkommande kommunikation från tjänsten Azure Batch.

Batch-tjänsten lägger till nätverks säkerhets grupper (NSG: er) på nivån nätverks gränssnitt (NIC) som är anslutna till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

- Inkommande TCP-trafik på portarna 29876 och 29877 från en __service tag__ i __BatchNodeManagement__. Trafik över dessa portar är krypterad och används av Azure Batch för kommunikation i Scheduler/Node.

    ![En regel för inkommande trafik som använder BatchNodeManagement-tjänst tag gen](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Valfritt Inkommande TCP-trafik på port 22 för att tillåta fjärråtkomst. Använd bara den här porten om du vill ansluta med SSH på den offentliga IP-adressen.

- Utgående trafik på vilken port som helst till det virtuella nätverket.

- Utgående trafik på vilken port som helst till Internet.

- För Compute instance inkommande TCP-trafik på port 44224 från en __service tag__ i __AzureMachineLearning__. Trafik över den här porten krypteras och används av Azure Machine Learning för kommunikation med program som körs på beräknings instanser.

> [!IMPORTANT]
> Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om en NSG blockerar kommunikation till datornoderna, anger beräknings tjänsten status för datornoderna till oanvändbar.
>
> Du behöver inte ange NSG: er på under näts nivån, eftersom Azure Batch tjänsten konfigurerar sin egen NSG: er. Men om det undernät som innehåller Azure Machine Learning Compute har tillhör ande NSG: er eller en brand vägg, måste du också tillåta trafiken som anges ovan.

Regel konfigurationen för NSG i Azure Portal visas i följande avbildningar:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Regler för inkommande NSG för Machine Learning-beräkning" border="true":::



![Ingående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Begränsa utgående anslutning från det virtuella nätverket

Använd följande steg om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk:

- Neka utgående Internet anslutning med NSG-reglerna.

- För en __beräknings instans__ eller ett __beräknings kluster__ begränsar du utgående trafik till följande objekt:
   - Azure Storage med hjälp av __tjänst tag gen__ för __Storage. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
   - Azure Container Registry med hjälp av __service tag gen__ för __AzureContainerRegistry. RegionName__. Där `{RegionName}` är namnet på en Azure-region.
   - Azure Machine Learning med hjälp av __service tag gen__ för __AzureMachineLearning__
   - Azure Resource Manager med hjälp av __service tag gen__ för __AzureResourceManager__
   - Azure Active Directory med hjälp av __service tag gen__ för __AzureActiveDirectory__

Regel konfigurationen för NSG i Azure Portal visas i följande bild:

[![Utgående NSG-regler för Machine Learning-beräkning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Om du planerar att använda standard Docker-avbildningar som tillhandahålls av Microsoft och aktiverar hanterade beroenden, måste du också använda följande __service märken__:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Den här konfigurationen krävs när du har kod som liknar följande kodfragment som en del av dina utbildnings skript:
>
> __RunConfig-utbildning__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Uppskattnings utbildning__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Tvingad tunneltrafik

Om du använder [Tvingad tunnel trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) med Azure Machine Learning Compute måste du tillåta kommunikation med det offentliga Internet från det undernät som innehåller beräknings resursen. Den här kommunikationen används för schemaläggning av aktiviteter och åtkomst till Azure Storage.

Du kan göra detta på två sätt:

* Använd en [Virtual Network NAT](../virtual-network/nat-overview.md). En NAT-gateway ger utgående Internet anslutning för ett eller flera undernät i det virtuella nätverket. Mer information finns i [utforma virtuella nätverk med NAT-gateway-resurser](../virtual-network/nat-gateway-resource.md).

* Lägg till [användardefinierade vägar (UDR)](../virtual-network/virtual-networks-udr-overview.md) till det undernät som innehåller beräknings resursen. Upprätta en UDR för varje IP-adress som används av tjänsten Azure Batch i den region där dina resurser finns. Dessa UDR gör att batch-tjänsten kan kommunicera med datornoder för schemaläggning av aktiviteter. Lägg också till IP-adressen för den Azure Machine Learning tjänsten, eftersom detta krävs för att få åtkomst till beräknings instanser. När du lägger till IP-adressen för tjänsten Azure Machine Learning måste du lägga till IP för både de __primära och sekundära__ Azure-regionerna. Den primära regionen är den plats där din arbets yta finns.

    Du hittar den sekundära regionen genom att se till [att verksamhets kontinuiteten & haveri beredskap med Azure-kopplade regioner](../best-practices-availability-paired-regions.md#azure-regional-pairs). Om din Azure Machine Learning-tjänst till exempel finns i USA, östra 2, är den sekundära regionen Central USA. 

    Använd någon av följande metoder för att hämta en lista över IP-adresser för batch-tjänsten och Azure Machine Learning tjänsten:

    * Hämta [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) och Sök efter `BatchNodeManagement.<region>` och `AzureMachineLearning.<region>` , där `<region>` är din Azure-region.

    * Använd [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) för att hämta informationen. I följande exempel hämtas IP-adressinformation och filtreras bort informationen för regionen USA, östra 2 (primär) och USA, centrala (sekundär):

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Om du använder regionerna US-Virginia, US-Arizona regioner eller Kina – öst – 2, returnerar dessa kommandon inga IP-adresser. Använd i stället någon av följande länkar för att hämta en lista över IP-adresser:
        >
        > * [Azure IP-intervall och service märken för Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure IP-intervall och service märken för Azure Kina](https://www.microsoft.com//download/details.aspx?id=57062)
    
    När du lägger till UDR definierar du vägen för varje relaterat batch-IP-adressprefix och anger __nästa hopp typ__ till __Internet__. Följande bild visar ett exempel på den här UDR i Azure Portal:

    ![Exempel på en UDR för ett adressprefix](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > IP-adresserna kan ändras med tiden.

    Förutom de UDR som du definierar måste utgående trafik till Azure Storage tillåtas via den lokala nätverks enheten. Mer specifikt är URL: erna för den här trafiken i följande format: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` . 

    Mer information finns i [skapa en Azure Batch pool i ett virtuellt nätverk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Skapa ett beräknings kluster i ett virtuellt nätverk

Använd följande steg för att skapa ett Machine Learning-beräkning kluster:

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/)och välj sedan din prenumeration och arbets yta.

1. Välj __Beräkna__ till vänster.

1. Välj __utbildnings kluster__ från mitten och välj sedan __+__ .

1. I dialog rutan __nytt utbildnings kluster__ expanderar du avsnittet __Avancerade inställningar__ .

1. Om du vill konfigurera den här beräknings resursen för att använda ett virtuellt nätverk utför du följande åtgärder i avsnittet __Konfigurera virtuellt nätverk__ :

    1. I list rutan __resurs grupp__ väljer du den resurs grupp som innehåller det virtuella nätverket.
    1. I list rutan __virtuellt nätverk__ väljer du det virtuella nätverk som innehåller under nätet.
    1. I list rutan __undernät__ väljer du det undernät som ska användas.

   ![Inställningarna för virtuella nätverk för Machine Learning-beräkning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Du kan också skapa ett Machine Learning-beräkning-kluster med hjälp av Azure Machine Learning SDK. Följande kod skapar ett nytt Machine Learning-beräkning-kluster i `default` under nätet för ett virtuellt nätverk med namnet `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

När du har skapat processen tränar du din modell genom att använda klustret i ett experiment. Mer information finns i [Välj och använda ett beräknings mål för utbildning](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Få åtkomst till data i en beräknings instans antecknings bok

Om du använder antecknings böcker på en Azure-beräknings instans måste du se till att din bärbara dator körs på en beräknings resurs bakom samma virtuella nätverk och undernät som dina data. 

Du måste konfigurera beräknings instansen så att den är i samma virtuella nätverk när du skapar under **Avancerade inställningar**  >  **Konfigurera virtuellt nätverk**. Det går inte att lägga till en befintlig beräknings instans i ett virtuellt nätverk.

## <a name="azure-databricks"></a>Azure Databricks

Om du vill använda Azure Databricks i ett virtuellt nätverk med din arbets yta måste följande krav uppfyllas:

> [!div class="checklist"]
> * Det virtuella nätverket måste finnas i samma prenumeration och region som Azure Machine Learning-arbetsytan.
> * Om Azure Storage kontona för arbets ytan också är skyddade i ett virtuellt nätverk måste de finnas i samma virtuella nätverk som Azure Databricks-klustret.
> * Förutom de __databricks-privata__ och __databricks-offentliga__ undernät som används av Azure Databricks, krävs även det __standard__ -undernät som skapats för det virtuella nätverket.

För detaljerad information om hur du använder Azure Databricks med ett virtuellt nätverk, se [distribuera Azure Databricks i Azure-Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuell dator eller HDInsight-kluster

> [!IMPORTANT]
> Azure Machine Learning stöder bara virtuella datorer som kör Ubuntu.

I det här avsnittet får du lära dig hur du använder en virtuell dator eller ett Azure HDInsight-kluster i ett virtuellt nätverk med din arbets yta.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Skapa VM-eller HDInsight-klustret

Skapa ett virtuellt dator kluster eller HDInsight-kluster med hjälp av Azure Portal eller Azure CLI och Lägg klustret i ett virtuellt Azure-nätverk. Mer information finns i följande artiklar:
* [Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer](../virtual-machines/linux/tutorial-virtual-network.md)

* [Utöka HDInsight med ett virtuellt Azure-nätverk](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Konfigurera nätverks portar 

Tillåt Azure Machine Learning att kommunicera med SSH-porten på den virtuella datorn eller klustret och konfigurera en käll post för nätverks säkerhets gruppen. SSH-porten är vanligt vis port 22. Utför följande åtgärder för att tillåta trafik från den här källan:

1. I list rutan __källa__ väljer du __service tag__.

1. I list rutan __käll tjänst tag__ väljer du __AzureMachineLearning__.

    ![Regler för inkommande trafik för att utföra experimentering i ett virtuellt nätverk eller HDInsight-kluster i ett virtuellt nätverk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. I list rutan __käll port intervall__ väljer du __*__ .

1. I list rutan __mål__ väljer du __valfri__.

1. I list rutan __mål Port intervall__ väljer du __22__.

1. Under __protokoll__ väljer du __valfri__.

1. Under __åtgärd__ väljer du __Tillåt__.

Behåll standard reglerna för utgående trafik för nätverks säkerhets gruppen. Mer information finns i standard säkerhets regler i [säkerhets grupper](../virtual-network/network-security-groups-overview.md#default-security-rules).

Om du inte vill använda de utgående standard reglerna och du vill begränsa den utgående åtkomsten för ditt virtuella nätverk kan du läsa avsnittet [begränsa utgående anslutningar från det virtuella nätverket](#limiting-outbound-from-vnet) .

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Anslut VM-eller HDInsight-klustret

Anslut den virtuella datorn eller HDInsight-klustret till din Azure Machine Learning-arbetsyta. Mer information finns i [Konfigurera beräknings mål för modell träning](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Nästa steg

Den här artikeln är del tre i en serie virtuella nätverks serier i fem delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: översikt över virtuella nätverk](how-to-network-security-overview.md)
* [Del 2: skydda arbets ytans resurser](how-to-secure-workspace-vnet.md)
* [Del 4: skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)
