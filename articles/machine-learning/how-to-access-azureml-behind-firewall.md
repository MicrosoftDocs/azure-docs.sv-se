---
title: Använd en brand vägg
titleSuffix: Azure Machine Learning
description: Kontrol lera åtkomsten till Azure Machine Learning arbets ytor med Azure-brandväggar. Läs om de värdar som du måste tillåta genom brand väggen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 295228e9eaa3529b05055869bd46f9aefc938a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212781"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Använd arbets ytan bakom en brand vägg för Azure Machine Learning

I den här artikeln lär du dig hur du konfigurerar Azure-brandväggen för att kontrol lera åtkomsten till din Azure Machine Learning-arbetsyta och det offentliga Internet. Mer information om hur du skyddar Azure Machine Learning finns i [företags säkerhet för Azure Machine Learning](concept-enterprise-security.md).

> [!WARNING]
> Åtkomst till data lagring bakom en brand vägg stöds bara i första kod. Det går inte att komma åt data bakom en brand vägg med hjälp av [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) . Om du vill arbeta med data lagring i ett privat nätverk med Studio måste du först [Konfigurera ett virtuellt nätverk](../virtual-network/quick-create-portal.md) och [ge Studio åtkomst till data som lagras i ett virtuellt nätverk](how-to-enable-studio-virtual-network.md).

## <a name="azure-firewall"></a>Azure Firewall

När du använder Azure Firewall använder du __mål Network Address Translation (DNAT)__ för att skapa NAT-regler för inkommande trafik. Skapa __nätverks__ -och/eller __program__ regler för utgående trafik. De här regel samlingarna beskrivs mer detaljerat i [Vad är vissa Azure Firewall-begrepp](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Inkommande konfiguration

Om du använder en Azure Machine Learning __beräknings instans__ eller __beräknings kluster__, lägger du till en [användardefinierad routning (UDR)](../virtual-network/virtual-networks-udr-overview.md) för det undernät som innehåller Azure Machine Learning-resurserna. Den här vägen tvingar trafik __från__ IP-adresserna för- `BatchNodeManagement` och- `AzureMachineLearning` resurserna till den offentliga IP-adressen för din beräknings instans och beräknings kluster.

Dessa UDR gör att batch-tjänsten kan kommunicera med datornoder för schemaläggning av aktiviteter. Lägg också till IP-adressen för den Azure Machine Learning tjänsten, eftersom detta krävs för att få åtkomst till beräknings instanser. När du lägger till IP-adressen för tjänsten Azure Machine Learning måste du lägga till IP för både de __primära och sekundära__ Azure-regionerna. Den primära regionen är den plats där din arbets yta finns.

Du hittar den sekundära regionen genom att se till [att verksamhets kontinuiteten & haveri beredskap med Azure-kopplade regioner](../best-practices-availability-paired-regions.md#azure-regional-pairs). Om din Azure Machine Learning-tjänst till exempel finns i USA, östra 2, är den sekundära regionen Central USA. 

Använd någon av följande metoder för att hämta en lista över IP-adresser för batch-tjänsten och Azure Machine Learning tjänsten:

* Hämta [Azure IP-intervall och service märken](https://www.microsoft.com/download/details.aspx?id=56519) och Sök efter `BatchNodeManagement.<region>` och `AzureMachineLearning.<region>` , där `<region>` är din Azure-region.

* Använd [Azure CLI](/cli/azure/install-azure-cli) för att hämta informationen. I följande exempel hämtas IP-adressinformation och filtreras bort informationen för regionen USA, östra 2 (primär) och USA, centrala (sekundär):

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

Mer information finns i [skapa en Azure Batch pool i ett virtuellt nätverk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Utgående konfiguration

1. Lägg till __nätverks regler__ som tillåter trafik __till__ och __från__ följande service märken:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Lagring. region
    * Nyckel valv. region
    * ContainerRegistry. region

    Om du planerar att använda de standard Docker-avbildningar som tillhandahålls av Microsoft och aktiverar användar hanterade beroenden måste du också lägga till följande service märken:

    * MicrosoftContainerRegistry. region
    * AzureFrontDoor.FirstParty

    För poster som innehåller `region` ersätter du med den Azure-region som du använder. Till exempel `keyvault.westus`.

    För __protokollet__ väljer du `TCP` . För käll-och mål __portarna__ väljer du `*` .

1. Lägg till __program regler__ för följande värdar:

    > [!NOTE]
    > Det här är inte en fullständig lista över de värdar som krävs för alla python-resurser på Internet, bara de som används oftast. Om du till exempel behöver åtkomst till en GitHub-lagringsplats eller annan värd måste du identifiera och lägga till de värdar som krävs för det scenariot.

    | **Värdnamn** | **Syfte** |
    | ---- | ---- |
    | **graph.windows.net** | Används av Azure Machine Learning beräknings instans/kluster. |
    | **anaconda.com**</br>**\*. anaconda.com** | Används för att installera standard paket. |
    | **\*. anaconda.org** | Används för att hämta lagrings platsen-data. |
    | **pypi.org** | Används för att Visa beroenden från standard index, om det finns några, och indexet skrivs inte över av användar inställningarna. Om indexet skrivs över måste du också tillåta **\* . pythonhosted.org**. |
    | **cloud.r-project.org** | Används vid installation av CRAN-paket för R-utveckling. |
    | **\*pytorch.org** | Används i några exempel baserat på PyTorch. |
    | **\*. tensorflow.org** | Används i några exempel baserat på Tensorflow. |

    För __protokoll: port__ väljer du Använd __http, https__.

    Mer information om hur du konfigurerar program regler finns i [distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Information om hur du begränsar åtkomsten till modeller som distribueras till Azure Kubernetes service (AKS) finns i [begränsa utgående trafik i Azure Kubernetes-tjänsten](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Andra brand väggar

Vägledningen i det här avsnittet är generisk, eftersom varje brand vägg har egna terminologier och specifika konfigurationer. Om du har frågor om hur du tillåter kommunikation via brand väggen kan du läsa dokumentationen för den brand vägg som du använder.

Om konfigurationen inte är korrekt konfigurerad kan brand väggen orsaka problem med din arbets yta. Det finns flera olika värdnamn som används både i Azure Machine Learning-arbetsytan. I följande avsnitt listas värdar som krävs för Azure Machine Learning.

### <a name="microsoft-hosts"></a>Microsoft-värdar

Värdarna i det här avsnittet ägs av Microsoft och tillhandahåller tjänster som krävs för att din arbets yta ska fungera korrekt. I följande tabeller visas värd namnen för Azures offentliga, Azure Government och Azure Kina 21Vianet-regioner.

**Allmänna Azure-värdar**

| **Krävs för** | **Azure, offentlig** | **Azure Government** | **Azure Kina 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning värdar**

| **Krävs för** | **Azure, offentlig** | **Azure Government** | **Azure Kina 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning-studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Experimentering, historik, HyperDrive, etikettering | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Modellhantering | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Pipeline | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Designer (Studio-tjänst) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Integrerad Notebook | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Integrerad Notebook | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Integrerad Notebook | \*. dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Integrerad Notebook | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Integrerad Notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integrerad Notebook | \*. aznbcontent.net |  | |

**Azure Machine Learning beräknings instans och beräknings kluster värdar**

| **Krävs för** | **Azure, offentlig** | **Azure Government** | **Azure Kina 21Vianet** |
| ----- | ----- | ----- | ----- |
| Beräknings kluster/instans | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Beräknings kluster/instans | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Beräkninsinstans | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Beräkninsinstans | \*. instances.azureml.ms |  |  |

**Associerade resurser som används av Azure Machine Learning**

| **Krävs för** | **Azure, offentlig** | **Azure Government** | **Azure Kina 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage-konto | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Om du planerar att använda federerade identiteter följer du [metod tips för att skydda Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) artikel.

Använd också informationen i [Tvingad tunnel trafik](how-to-secure-training-vnet.md#forced-tunneling) för att lägga till IP-adresser för `BatchNodeManagement` och `AzureMachineLearning` .

Information om hur du begränsar åtkomsten till modeller som distribueras till Azure Kubernetes service (AKS) finns i [begränsa utgående trafik i Azure Kubernetes-tjänsten](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python-värdar

Värdarna i det här avsnittet används för att installera python-paket. De krävs vid utveckling, utbildning och distribution. 

> [!NOTE]
> Det här är inte en fullständig lista över de värdar som krävs för alla python-resurser på Internet, bara de som används oftast. Om du till exempel behöver åtkomst till en GitHub-lagringsplats eller annan värd måste du identifiera och lägga till de värdar som krävs för det scenariot.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Används för att installera standard paket. |
| **\*. anaconda.org** | Används för att hämta lagrings platsen-data. |
| **pypi.org** | Används för att Visa beroenden från standard index, om det finns några, och indexet skrivs inte över av användar inställningarna. Om indexet skrivs över måste du också tillåta **\* . pythonhosted.org**. |
| **\*pytorch.org** | Används i några exempel baserat på PyTorch. |
| **\*. tensorflow.org** | Används i några exempel baserat på Tensorflow. |

### <a name="r-hosts"></a>R-värdar

Värdarna i det här avsnittet används för att installera R-paket. De krävs vid utveckling, utbildning och distribution.

> [!NOTE]
> Det här är inte en fullständig lista över de värdar som krävs för alla R-resurser på Internet, bara de som används oftast. Om du till exempel behöver åtkomst till en GitHub-lagringsplats eller annan värd måste du identifiera och lägga till de värdar som krävs för det scenariot.

| **Värdnamn** | **Syfte** |
| ---- | ---- |
| **cloud.r-project.org** | Används vid installation av CRAN-paket. |

> [!IMPORTANT]
> R SDK för Azure Machine Learning använder python-paket internt. Så du måste också tillåta python-värdar genom brand väggen.
## <a name="next-steps"></a>Nästa steg

* [Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Skydda Azure ML-experimenterings- och slutsatsdragningsjobb i ett virtuellt Azure-nätverk](how-to-network-security-overview.md)
