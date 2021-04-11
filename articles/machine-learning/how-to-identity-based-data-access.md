---
title: Identitets baserad data åtkomst till lagrings tjänster på Azure
titleSuffix: Machine Learning
description: Lär dig hur du använder identitetsbaserade data åtkomst för att ansluta till lagrings tjänster på Azure med Azure Machine Learning data lager och Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210660"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Ansluta till lagring med hjälp av Identity-baserad data åtkomst (för hands version)

>[!IMPORTANT]
> Funktionerna som visas i den här artikeln är i för hands version. De bör anses vara [experimentella](/python/api/overview/azure/ml/#stable-vs-experimental) för hands versions funktioner som kan ändras när som helst.

I den här artikeln får du lära dig hur du ansluter till lagrings tjänster i Azure med hjälp av identitetsbaserade data åtkomst och Azure Machine Learning data lager via [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro).  

Normalt använder data lager för autentiseringsuppgifter för att kontrol lera att du har behörighet att komma åt lagrings tjänsten. De behåller anslutnings informationen, t. ex. prenumerations-ID och token-auktorisering, i [nyckel valvet](https://azure.microsoft.com/services/key-vault/) som är associerat med arbets ytan. När du skapar ett data lager som använder Identity-baserad data åtkomst, används ditt Azure-konto ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) för att bekräfta att du har åtkomst behörighet till lagrings tjänsten. I det här scenariot sparas inga autentiseringsuppgifter för autentisering. Endast lagrings konto informationen lagras i data lagret. 

Information om hur du skapar data lager som använder autentisering baserad autentisering, t. ex. åtkomst nycklar eller tjänstens huvud namn, finns i [Anslut till lagrings tjänster på Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Identitets baserad data åtkomst i Azure Machine Learning

Det finns två scenarier där du kan tillämpa identitetsbaserade data åtkomst i Azure Machine Learning. De här scenarierna passar bra för identitetsbaserade åtkomst när du arbetar med konfidentiella data och behöver mer detaljerad data åtkomst hantering:

- Åtkomst till lagrings tjänster
- Träna maskin inlärnings modeller med privata data

### <a name="accessing-storage-services"></a>Åtkomst till lagrings tjänster

Du kan ansluta till lagrings tjänster via identitets-baserad data åtkomst med Azure Machine Learning data lager eller [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). 

Autentiseringsuppgifterna för autentisering behålls vanligt vis i ett data lager, vilket används för att se till att du har åtkomst behörighet till lagrings tjänsten. När de här autentiseringsuppgifterna registreras via data lager kan alla användare med rollen för arbets ytans läsare hämta dem. Den skalningen av åtkomst kan vara en säkerhets risk för vissa organisationer. [Läs mer om rollen för arbets ytans läsare.](how-to-assign-roles.md#default-roles) 

När du använder identitetsbaserade data Azure Machine Learning åtkomst uppmanas du att ange din Azure Active Directory token för autentisering av data åtkomst i stället för att behålla dina autentiseringsuppgifter i data lagret. Den metoden möjliggör data åtkomst hantering på lagrings nivå och bevarar autentiseringsuppgifterna konfidentiellt. 

Samma sak gäller när du:

* [Skapa en data uppsättning direkt från lagrings-URL: er](#use-data-in-storage). 
* Arbeta med data interaktivt via en Jupyter Notebook på din lokala dator eller [beräknings instans](concept-compute-instance.md).

> [!NOTE]
> Autentiseringsuppgifterna som lagras via Credential-baserad autentisering innehåller prenumerations-ID: n, token för delad åtkomst (SAS) och lagrings åtkomst nyckel och tjänstens huvud namn, t. ex. klient-ID och klient-ID.

### <a name="model-training-on-private-data"></a>Modell träning för privata data

Vissa Machine Learning-scenarier omfattar utbildnings modeller med privata data. I sådana fall behöver data forskare köra utbildnings arbets flöden utan att exponeras för konfidentiella indata. I det här scenariot används en hanterad identitet för inlärnings beräkningen för autentisering av data åtkomst. Med den här metoden kan lagrings administratörer ge åtkomst till lagrings-BLOB-dataläsaren åtkomst till den hanterade identitet som inlärningen använder för att köra utbildnings jobbet. Enskilda data forskare behöver inte beviljas åtkomst. Mer information finns i [Konfigurera hanterad identitet i ett beräknings kluster](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure Storage-konto med en lagrings typ som stöds. Dessa lagrings typer stöds i för hands versionen: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/install).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en [befintlig via python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Åtkomst behörigheter för lagring

För att säkerställa att du ansluter till lagrings tjänsten på ett säkert sätt i Azure måste Azure Machine Learning ha behörighet att komma åt motsvarande data lagring.

Identity-baserad data åtkomst stöder anslutningar till endast följande lagrings tjänster:

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

För att få åtkomst till dessa lagrings tjänster måste du ha minst åtkomst till [lagrings BLOB-dataläsaren](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Endast lagrings kontots ägare kan [ändra åtkomst nivån via Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Om du tränar en modell på ett fjärrberäknings mål måste beräknings identiteten beviljas minst rollen Storage BLOB data Reader från lagrings tjänsten. Lär dig hur du [konfigurerar hanterad identitet i ett beräknings kluster](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Arbeta med virtuella nätverk

Azure Machine Learning kan som standard inte kommunicera med ett lagrings konto som ligger bakom en brand vägg eller i ett virtuellt nätverk.

Du kan konfigurera lagrings konton så att de endast tillåter åtkomst i vissa virtuella nätverk. Den här konfigurationen kräver ytterligare åtgärder för att säkerställa att data inte läcker utanför nätverket. Det här beteendet är detsamma för autentisering baserad data åtkomst. Mer information finns i [så här konfigurerar du scenarier för virtuella nätverk](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en lagrings tjänst på Azure som ett data lager skapas och registreras data lagret automatiskt i en angiven arbets yta. Information om vilka behörighets typer som krävs finns i [åtkomst behörigheter för lagring](#storage-access-permissions) . Mer information om hur du ansluter till data lagring bakom virtuella nätverk finns i [arbeta med virtuella nätverk](#work-with-virtual-networks) .

I följande kod noterar du frånvaron av autentiseringsmetoder som,, `sas_token` `account_key` `subscription_id` och tjänstens huvud namn `client_id` . Detta kan betyda att Azure Machine Learning använder identitetsbaserade data åtkomst för autentisering. Skapandet av data lager sker vanligt vis interaktivt i en bärbar dator eller via Studio. Så din Azure Active Directory token används för autentisering av data åtkomst.

> [!NOTE]
> Data lager namn får endast bestå av gemena bokstäver, siffror och under streck. 

### <a name="azure-blob-container"></a>Azure Blob-behållare

Använd för att registrera en Azure Blob-behållare som ett data lager [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Följande kod skapar `credentialless_blob` data lagret, registrerar det på `ws` arbets ytan och tilldelar det till `blob_datastore` variabeln. Detta data lager har åtkomst till `my_container_name` BLOB-behållaren på `my-account-name` lagrings kontot.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Använd [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) för att registrera ett data lager som ansluter till Azure Data Lake Storage gen1.

Följande kod skapar `credentialless_adls1` data lagret, registrerar det på `workspace` arbets ytan och tilldelar det till `adls_dstore` variabeln. Det här data lagret har åtkomst till `adls_storage` Azure Data Lake Storage-kontot.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Använd [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) för att registrera ett data lager som ansluter till Azure Data Lake Storage Gen2.

Följande kod skapar `credentialless_adls2` data lagret, registrerar det på `ws` arbets ytan och tilldelar det till `adls2_dstore` variabeln. Detta data lager har åtkomst till fil systemet `tabular` i `myadls2` lagrings kontot.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Använda data i Storage

Vi rekommenderar att du använder [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md) när du interagerar med dina data i lagringen med Azure Machine Learning. 

Data uppsättningar paketerar dina data i ett Lazy utvärderat förbruknings Bart objekt för Machine Learning-uppgifter som träning. Med data uppsättningar kan du också [Hämta eller montera](how-to-train-with-datasets.md#mount-vs-download) filer i alla format från Azure Storage-tjänster som Azure Blob Storage och Azure Data Lake Storage till ett beräknings mål.


Om du vill skapa data uppsättningar med Identity-baserad data åtkomst har du följande alternativ. Den här typen av data uppsättnings skapande använder Azure Active Directory token för autentisering av data åtkomst. 

*  Referens Sök vägar från data lager som också använder identitets baserad data åtkomst. 
<br>I följande exempel `blob_datastore` finns redan och använder Identity-baserad data åtkomst.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Hoppa över skapande av data lager och skapa data uppsättningar direkt från lagrings-URL: er. Den här funktionen har för närvarande endast stöd för Azure-blobbar och Azure Data Lake Storage Gen1-och Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

När du skickar ett utbildnings jobb som använder en data uppsättning som har skapats med identitetsbaserade data åtkomst används den hanterade identiteten för inlärnings beräkningen för autentisering med data åtkomst. Din Azure Active Directory token används inte. I det här scenariot ser du till att den hanterade identiteten för data bearbetningen beviljas minst rollen Storage BLOB data Reader från lagrings tjänsten. Mer information finns i [Konfigurera hanterad identitet för beräknings kluster](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning data uppsättning](how-to-create-register-datasets.md)
* [Träna med datauppsättningar](how-to-train-with-datasets.md)
* [Skapa ett data lager med nyckelbaserad data åtkomst](how-to-access-data.md)
