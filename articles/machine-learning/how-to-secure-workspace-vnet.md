---
title: Skydda en Azure Machine Learning arbetsyta med virtuella nätverk
titleSuffix: Azure Machine Learning
description: Använd en isolerad Azure-Virtual Network för att skydda Azure Machine Learning arbetsyta och associerade resurser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531034"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Skydda en Azure Machine Learning arbetsyta med virtuella nätverk

I den här artikeln får du lära dig hur du Azure Machine Learning en arbetsyta och dess associerade resurser i ett virtuellt nätverk.

Den här artikeln är del två i en serie om fem delar som steg för steg beskriver hur du Azure Machine Learning ett arbetsflöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt för](how-to-network-security-overview.md) att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. Översikt över virtuella nätverk](how-to-network-security-overview.md)  >  **2. Skydda arbetsytan**  >  [3. Skydda träningsmiljön](how-to-secure-training-vnet.md)  >  [4. Skydda inferencingmiljön](how-to-secure-inferencing-vnet.md)  >  [5. Aktivera studiofunktioner](how-to-enable-studio-virtual-network.md)

I den här artikeln får du lära dig hur du aktiverar följande arbetsyteresurser i ett virtuellt nätverk:
> [!div class="checklist"]
> - Azure Machine Learning-arbetsyta
> - Azure Storage-konton
> - Azure Machine Learning datalager och datauppsättningar
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Förutsättningar

+ Läs artikeln [Översikt över nätverkssäkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas med dina beräkningsresurser.

+ Om du vill distribuera resurser till ett virtuellt nätverk eller undernät måste ditt användarkonto ha behörighet till följande åtgärder i rollbaserad åtkomstkontroll i Azure (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" på den virtuella nätverksresursen.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" på undernätsresursen.

    Mer information om Azure RBAC med nätverk finns i [Inbyggda roller för nätverk](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Skydda arbetsytan med privat slutpunkt

Azure Private Link kan du ansluta till din arbetsyta med hjälp av en privat slutpunkt. Den privata slutpunkten är en uppsättning privata IP-adresser i ditt virtuella nätverk. Du kan sedan begränsa åtkomsten till din arbetsyta så att den endast sker via de privata IP-adresserna. Private Link minskar risken för data exfiltrering.

Mer information om hur du konfigurerar Private Link en arbetsyta [finns i Så här konfigurerar du Private Link](how-to-configure-private-link.md).

> [!Warning]
> Att skydda en arbetsyta med privata slutpunkter säkerställer inte säkerheten från slutpunkt till slutpunkt på egen hand. Du måste följa stegen i resten av den här artikeln och VNet-serien för att skydda enskilda komponenter i din lösning.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Skydda Azure Storage-konton med tjänstslutpunkter

Azure Machine Learning har stöd för lagringskonton som konfigurerats att använda antingen tjänstslutpunkter eller privata slutpunkter. I det här avsnittet får du lära dig hur du skyddar ett Azure Storage-konto med hjälp av tjänstslutpunkter. Information om privata slutpunkter finns i nästa avsnitt.

Använd följande steg om du vill använda ett Azure Storage-konto för arbetsytan i ett virtuellt nätverk:

1. I Azure Portal du till den lagringstjänst som du vill använda på din arbetsyta.

   [![Lagringen som är kopplad till Azure Machine Learning arbetsytan](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. På sidan för lagringstjänstkontot väljer du __Nätverk.__

   ![Nätverksområdet på Azure Storage i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. På __fliken Brandväggar och virtuella__ nätverk gör du följande:
    1. Välj __Valda nätverk__.
    1. Under __Virtuella nätverk__ väljer du länken Lägg till befintligt __virtuellt__ nätverk. Den här åtgärden lägger till det virtuella nätverk där din beräkning finns (se steg 1).

        > [!IMPORTANT]
        > Lagringskontot måste finnas i samma virtuella nätverk och undernät som beräkningsinstanserna eller klustren som används för träning eller slutsatsledning.

    1. Markera kryssrutan __Tillåt betrodda Microsoft-tjänster åtkomst till det här lagringskontot.__ Den här ändringen ger inte alla Azure-tjänster åtkomst till ditt lagringskonto.
    
        * Resurser för vissa tjänster, **registrerade i din prenumeration**, kan komma åt lagringskontot i samma prenumeration **för** utvalda åtgärder. Det kan till exempel vara att skriva loggar eller skapa säkerhetskopior.
        * Resurser i vissa tjänster kan beviljas explicit åtkomst till ditt lagringskonto genom att tilldela en __Azure-roll till__ dess system tilldelade hanterade identitet.

        Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > När du arbetar med Azure Machine Learning SDK måste din utvecklingsmiljö kunna ansluta till Azure Storage konto. När lagringskontot finns i ett virtuellt nätverk måste brandväggen tillåta åtkomst från utvecklingsmiljöns IP-adress.
    >
    > Om du vill ge åtkomst till __lagringskontot__ går du till Brandväggar och virtuella nätverk för lagringskontot från *en webbläsare på utvecklingsklienten*. Använd sedan kryssrutan __Lägg till klientens IP-adress__ för att lägga till klientens IP-adress i __ADRESSINTERVALL.__ Du kan också använda fältet __ADRESSINTERVALL__ för att manuellt ange IP-adressen för utvecklingsmiljön. När IP-adressen för klienten har lagts till kan den komma åt lagringskontot med hjälp av SDK:n.

   [![Fönstret "Brandväggar och virtuella nätverk" i Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Skydda Azure-lagringskonton med privata slutpunkter

Azure Machine Learning har stöd för lagringskonton som konfigurerats att använda antingen tjänstslutpunkter eller privata slutpunkter. Om lagringskontot använder privata slutpunkter måste du konfigurera två privata slutpunkter för ditt standardlagringskonto:
1. En privat slutpunkt med en **underkälla för** blobmålet.
1. En privat slutpunkt med en **underresurs** för filmål (filresurs).

![Skärmbild som visar sidan för konfiguration av privat slutpunkt med blob- och filalternativ](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Om du vill konfigurera en privat  slutpunkt för ett  lagringskonto som inte är standardlagringen väljer du den typ av målunderkälla som motsvarar det lagringskonto som du vill lägga till.

Mer information finns i [Använda privata slutpunkter för Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Skydda datalager och datauppsättningar

I det här avsnittet får du lära dig hur du använder datalager och datauppsättningar i SDK-upplevelsen med ett virtuellt nätverk. Mer information om Studio-upplevelsen finns i [Använda Azure Machine Learning-studio i ett virtuellt nätverk](how-to-enable-studio-virtual-network.md).

För att komma åt data med sdk:n måste du använda den autentiseringsmetod som krävs av den enskilda tjänst som data lagras i. Om du till exempel registrerar ett datalager för åtkomst till Azure Data Lake Store Gen2 måste du fortfarande använda tjänstens huvudnamn enligt dokumenten i Ansluta till [Azure Storage-tjänster.](how-to-access-data.md#azure-data-lake-storage-generation-2)

### <a name="disable-data-validation"></a>Inaktivera datavalidering

Som standard Azure Machine Learning att kontrollera datas giltighet och autentiseringsuppgifter när du försöker komma åt data med hjälp av SDK: n. Om data finns bakom ett virtuellt nätverk kan Azure Machine Learning slutföra dessa kontroller. Om du vill kringgå den här kontrollen måste du skapa datalager och datauppsättningar som hoppar över verifieringen.

### <a name="use-datastores"></a>Använda datalager

 Azure Data Lake Store Gen1 och Azure Data Lake Store Gen2 hoppar över valideringen som standard, så ingen ytterligare åtgärd krävs. För följande tjänster kan du dock använda liknande syntax för att hoppa över datalagringsverifiering:

- Azure Blob Storage
- Azure-filresurs
- PostgreSQL
- Azure SQL Database

Följande kodexempel skapar ett nytt Azure Blob-datalager och anger `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Använda datauppsättningar

Syntaxen för att hoppa över datauppsättningsverifiering liknar följande datamängdstyper:
- Avgränsad fil
- JSON 
- Parquet
- SQL
- Fil

Följande kod skapar en ny JSON-datauppsättning och anger `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Säker Azure Key Vault

Azure Machine Learning använder en associerad Key Vault för att lagra följande autentiseringsuppgifter:
* Anslutningssträngen för det associerade lagringskontot
* Lösenord till Azure Container Repository-instanser
* Anslutningssträngar till datalager

Om du Azure Machine Learning använda experimenteringsfunktioner med Azure Key Vault bakom ett virtuellt nätverk använder du följande steg:

1. Gå till Key Vault som är associerad med arbetsytan.

1. På __Key Vault__ väljer du Nätverk i den vänstra __rutan.__

1. På __fliken Brandväggar och virtuella__ nätverk gör du följande:
    1. Under __Tillåt åtkomst från__ väljer du Privat slutpunkt och valda __nätverk__.
    1. Under __Virtuella nätverk__ väljer du Lägg till befintliga virtuella nätverk __för__ att lägga till det virtuella nätverk där experimenteringsbearbetningen finns.
    1. Under __Tillåt betrodda Microsoft-tjänster kringgå brandväggen väljer__ du __Ja.__

   [![Avsnittet "Brandväggar och virtuella nätverk" i Key Vault fönster](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Aktivera Azure Container Registry (ACR)

Om du Azure Container Registry i ett virtuellt nätverk måste du uppfylla följande krav:

* Din Azure Container Registry måste vara Premium-version. Mer information om hur du uppgraderar finns i [Ändra SKU:er.](../container-registry/container-registry-skus.md#changing-tiers)

* Din Azure Container Registry i samma virtuella nätverk och undernät som lagringskontot och beräkningsmålen som används för träning eller slutsatsledning.

* Din Azure Machine Learning måste innehålla ett Azure Machine Learning [beräkningskluster.](how-to-create-attach-compute-cluster.md)

    När ACR finns bakom ett virtuellt nätverk kan Azure Machine Learning använda det för att direkt skapa Docker-avbildningar. I stället används beräkningsklustret för att skapa avbildningarna.

    > [!IMPORTANT]
    > Beräkningsklustret som används för att skapa Docker-avbildningar måste kunna komma åt paketdatabaserna som används för att träna och distribuera dina modeller. Du kan behöva lägga till nätverkssäkerhetsregler som tillåter åtkomst till offentliga lagringsplatsen, använda privata [Python-paket](how-to-use-private-python-packages.md)eller använda anpassade [Docker-avbildningar](how-to-train-with-custom-image.md) som redan innehåller paketen.

När dessa krav har uppfyllts använder du följande steg för att aktivera Azure Container Registry.

> [!TIP]
> Om du inte använder en befintlig Azure Container Registry när du skapar arbetsytan kanske det inte finns någon. Som standard skapar arbetsytan inte en ACR-instans förrän den behöver en. Du kan tvinga fram skapandet av en modell genom att träna eller distribuera den med hjälp av din arbetsyta innan du använder stegen i det här avsnittet.

1. Leta upp namnet på Azure Container Registry för din arbetsyta med någon av följande metoder:

    __Azure-portalen__

    Från översiktsavsnittet på arbetsytan länkar __registervärdet__ till Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry för arbetsytan" border="true":::

    __Azure CLI__

    Om du har [installerat Machine Learning för Azure CLI](reference-azure-machine-learning-cli.md)kan du använda kommandot för att visa `az ml workspace show` arbetsyteinformationen.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Det här kommandot returnerar ett värde som liknar `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Den sista delen av strängen är namnet på Azure Container Registry för arbetsytan.

1. Begränsa åtkomsten till ditt virtuella nätverk med hjälp av stegen [i Konfigurera nätverksåtkomst för registret](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). När du lägger till det virtuella nätverket väljer du det virtuella nätverket och undernätet för Azure Machine Learning resurser.

1. Konfigurera ACR för arbetsytan till Tillåt [åtkomst av betrodda tjänster.](../container-registry/allow-access-trusted-services.md)

1. Använd python-Azure Machine Learning för att konfigurera ett beräkningskluster för att skapa Docker-avbildningar. Följande kodfragment visar hur du gör detta:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Ditt lagringskonto, beräkningskluster och Azure Container Registry måste finnas i samma undernät i det virtuella nätverket.
    
    Mer information finns i [metodreferensen update().](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

## <a name="next-steps"></a>Nästa steg

Den här artikeln är del två i en serie med fem virtuella nätverk. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: Översikt över virtuellt nätverk](how-to-network-security-overview.md)
* [Del 3: Skydda träningsmiljön](how-to-secure-training-vnet.md)
* [Del 4: Skydda inferensmiljön](how-to-secure-inferencing-vnet.md)
* [Del 5: Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)

Se även artikeln om hur du använder [anpassad DNS för](how-to-custom-dns.md) namnmatchning.
