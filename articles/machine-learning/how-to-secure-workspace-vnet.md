---
title: Skydda en Azure Machine Learning arbets yta med virtuella nätverk
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network för att skydda Azure Machine Learning-arbetsytan och associerade resurser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 6d23b0204cc597898eb2202a329d93ff349f8c13
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518542"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Skydda en Azure Machine Learning arbets yta med virtuella nätverk

I den här artikeln får du lära dig hur du skyddar en Azure Machine Learning arbets yta och dess tillhör ande resurser i ett virtuellt nätverk.

Den här artikeln är del två i en serie med fem delar som vägleder dig genom att skydda ett Azure Machine Learning-arbetsflöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt](how-to-network-security-overview.md) för att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  **2. Skydda arbets ytan**  >  [3. Skydda inlärnings miljö](how-to-secure-training-vnet.md)  >  [4. Skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)  >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

I den här artikeln får du lära dig hur du aktiverar följande arbets ytor resurser i ett virtuellt nätverk:
> [!div class="checklist"]
> - Azure Machine Learning-arbetsyta
> - Azure Storage-konton
> - Azure Machine Learning data lager och data uppsättningar
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Förutsättningar

+ Läs artikeln [Översikt över nätverks säkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräknings resurser.

+ För att distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användar konto ha behörighet till följande åtgärder i rollbaserad åtkomst kontroll i Azure (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" på den virtuella nätverks resursen.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" på under näts resursen.

    Mer information om Azure RBAC med nätverk finns i [inbyggda nätverks roller](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Skydda arbets ytan med privat slut punkt

Med Azures privata länk kan du ansluta till din arbets yta med en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i det virtuella nätverket. Du kan sedan begränsa åtkomsten till din arbets yta så att den bara sker över de privata IP-adresserna. Privat länk hjälper till att minska risken för data exfiltrering.

Mer information om hur du konfigurerar en privat länk arbets yta finns i [så här konfigurerar du en privat länk](how-to-configure-private-link.md).

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Skydda Azure Storage-konton med tjänst slut punkter

Azure Machine Learning stöder lagrings konton som kon figurer ATS att använda antingen tjänst slut punkter eller privata slut punkter. I det här avsnittet får du lära dig hur du skyddar ett Azure Storage-konto med hjälp av tjänst slut punkter. För privata slut punkter, se nästa avsnitt.

> [!IMPORTANT]
> Du kan placera både _standard lagrings kontot_ för Azure Machine Learning eller _andra lagrings konton som inte är standard_ i ett virtuellt nätverk.
>
> Standard lagrings kontot tillhandahålls automatiskt när du skapar en arbets yta.
>
> För lagrings konton som inte är standard `storage_account` kan du ange ett anpassat lagrings konto per Azure-resurs-ID i-parametern i [ `Workspace.create()` funktionen](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) .

Använd följande steg för att använda ett Azure Storage-konto för arbets ytan i ett virtuellt nätverk:

1. I Azure Portal går du till den lagrings tjänst som du vill använda i arbets ytan.

   [![Lagrings utrymmet som är kopplat till arbets ytan Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. På sidan Storage Service-konto väljer du __brand väggar och virtuella nätverk__.

   ![Avsnittet "brand väggar och virtuella nätverk" på sidan Azure Storage i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. På sidan __brand väggar och virtuella nätverk__ utför du följande åtgärder:
    1. Välj __Valda nätverk__.
    1. Under __virtuella nätverk__ väljer du länken __Lägg till befintligt virtuellt nätverk__ . Den här åtgärden lägger till det virtuella nätverk där din beräkning finns (se steg 1).

        > [!IMPORTANT]
        > Lagrings kontot måste finnas i samma virtuella nätverk och undernät som de beräknings instanser eller kluster som används för utbildning eller härledning.

    1. Markera kryss rutan __Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot__ . Detta ger inte alla Azure-tjänster åtkomst till ditt lagrings konto.
    
        * Resurser för vissa tjänster som har **registrerats i din prenumeration** kan komma åt lagrings kontot **i samma prenumeration** för Select-åtgärder. Skriv till exempel loggar eller skapar säkerhets kopior.
        * Resurser i vissa tjänster kan beviljas uttrycklig åtkomst till ditt lagrings konto genom att __tilldela en Azure-roll__ till sin systemtilldelade hanterade identitet.

        Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > När du arbetar med Azure Machine Learning SDK måste utvecklings miljön kunna ansluta till Azure Storage-kontot. När lagrings kontot finns i ett virtuellt nätverk måste brand väggen tillåta åtkomst från utvecklings miljöns IP-adress.
    >
    > Om du vill aktivera åtkomst till lagrings kontot går du till __brand väggarna och de virtuella nätverken__ för lagrings kontot *från en webbläsare på utvecklings klienten*. Använd sedan kryss rutan __Lägg till din klient-IP-adress__ för att lägga till KLIENTens IP-adress i __adress intervallet__. Du kan också använda fältet __adress intervall__ för att manuellt ange IP-adressen för utvecklings miljön. När IP-adressen för klienten har lagts till kan den komma åt lagrings kontot med hjälp av SDK.

   [![Fönstret "brand väggar och virtuella nätverk" i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Skydda Azure Storage-konton med privata slut punkter

Azure Machine Learning stöder lagrings konton som kon figurer ATS att använda antingen tjänst slut punkter eller privata slut punkter. Om lagrings kontot använder privata slut punkter måste du konfigurera två privata slut punkter för ditt standard lagrings konto:
1. En privat slut punkt med en **BLOB** Target-underresurs.
1. En privat slut punkt med en **fil** mål del resurs (fileshare).

![Skärm bild som visar konfigurations sidan för privat slut punkt med blob-och fil alternativ](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Om du vill konfigurera en privat slut punkt för ett lagrings konto som **inte** är standard lagring väljer du den **mål under resurs** typ som motsvarar det lagrings konto som du vill lägga till.

Mer information finns i [använda privata slut punkter för Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Säkra data lager och data uppsättningar

I det här avsnittet får du lära dig hur du använder data lager och data uppsättningar i SDK-upplevelsen med ett virtuellt nätverk. Mer information om Studio-upplevelsen finns i [använda Azure Machine Learning Studio i ett virtuellt nätverk](how-to-enable-studio-virtual-network.md).

Om du vill komma åt data med hjälp av SDK måste du använda autentiseringsmetoden som krävs av den enskilda tjänsten som data lagras i. Om du till exempel registrerar ett data lager för att komma åt Azure Data Lake Store Gen2, måste du fortfarande använda ett tjänst huvud namn som dokumenterat i [Anslut till Azure Storage Services](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Inaktivera data verifiering

Som standard utför Azure Machine Learning data giltighet och autentiseringsuppgifter vid försök att komma åt data med hjälp av SDK. Om data ligger bakom ett virtuellt nätverk kan Azure Machine Learning inte utföra de här kontrollerna. För att undvika detta måste du skapa data lager och data uppsättningar som hoppar över verifieringen.

### <a name="use-datastores"></a>Använda data lager

 Azure Data Lake Store gen1 och Azure Data Lake Store Gen2 hoppa över validering som standard, så ingen ytterligare åtgärd krävs. För följande tjänster kan du dock använda liknande syntax för att hoppa över validering av data lager:

- Azure Blob Storage
- Azure-fileshare
- PostgreSQL
- Azure SQL Database

I följande kod exempel skapas ett nytt Azure Blob-datalager och uppsättningar `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Använda data uppsättningar

Syntaxen för att hoppa över data uppsättnings verifiering är liknande för följande typer av data uppsättningar:
- Avgränsad fil
- JSON 
- Parquet
- SQL
- Fil

Följande kod skapar en ny JSON-datauppsättning och uppsättningar `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Säkra Azure Key Vault

Azure Machine Learning använder en associerad Key Vault instans för att lagra följande autentiseringsuppgifter:
* Den associerade anslutnings strängen för lagrings kontot
* Lösen ord till Azure Container databas instanser
* Anslutnings strängar till data lager

Använd följande steg för att använda Azure Machine Learning experiment funktioner med Azure Key Vault bakom ett virtuellt nätverk:

1. Gå till den Key Vault som är kopplad till arbets ytan.

1. På sidan __Key Vault__ väljer du __nätverk__ i det vänstra fönstret.

1. Utför följande åtgärder på fliken __brand väggar och virtuella nätverk__ :
    1. Under __Tillåt åtkomst från__ väljer du __privat slut punkt och valda nätverk__.
    1. Under __virtuella nätverk__ väljer du __Lägg till befintliga virtuella nätverk__ för att lägga till det virtuella nätverk där din experiment beräkning finns.
    1. Under __Tillåt betrodda Microsoft-tjänster att kringgå den här brand väggen? väljer du__ __Ja__.

   [![Avsnittet "brand väggar och virtuella nätverk" i fönstret Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Aktivera Azure Container Registry (ACR)

Om du vill använda Azure Container Registry inuti ett virtuellt nätverk måste du uppfylla följande krav:

* Din Azure Container Registry måste vara en Premium version. Mer information om hur du uppgraderar finns i [ändra SKU: er](../container-registry/container-registry-skus.md#changing-tiers).

* Ditt Azure Container Registry måste finnas i samma virtuella nätverk och undernät som lagrings kontot och beräknings målen som används för utbildning eller härledning.

* Din Azure Machine Learning-arbetsyta måste innehålla ett [Azure Machine Learning Compute-kluster](how-to-create-attach-compute-cluster.md).

    När ACR ligger bakom ett virtuellt nätverk kan Azure Machine Learning inte använda det för att direkt bygga Docker-avbildningar. I stället används beräknings klustret för att bygga avbildningarna.

När dessa krav är uppfyllda använder du följande steg för att aktivera Azure Container Registry.

1. Hitta namnet på Azure Container Registry för din arbets yta med någon av följande metoder:

    __Azure-portalen__

    I översikts avsnittet på arbets ytan länkar __registervärdet__ till Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry för arbets ytan" border="true":::

    __Azure CLI__

    Om du har [installerat Machine Learning-tillägget för Azure CLI](reference-azure-machine-learning-cli.md)kan du använda `az ml workspace show` kommandot för att visa information om arbets ytan.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Det här kommandot returnerar ett värde som liknar `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Den sista delen av strängen är namnet på Azure Container Registry för arbets ytan.

1. Begränsa åtkomsten till ditt virtuella nätverk genom att följa stegen i [Konfigurera nätverks åtkomst för registret](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). När du lägger till det virtuella nätverket väljer du det virtuella nätverket och under nätet för dina Azure Machine Learning-resurser.

1. Använd Azure Machine Learning python SDK för att konfigurera ett beräknings kluster för att bygga Docker-avbildningar. Följande kodfragment visar hur du gör detta:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Ditt lagrings konto, beräknings kluster och Azure Container Registry måste finnas i samma undernät i det virtuella nätverket.
    
    Mer information finns i metod referensen [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

## <a name="next-steps"></a>Nästa steg

Den här artikeln är del två i en serie virtuella nätverks serier i fem delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: översikt över virtuella nätverk](how-to-network-security-overview.md)
* [Del 3: skydda tränings miljön](how-to-secure-training-vnet.md)
* [Del 4: skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)