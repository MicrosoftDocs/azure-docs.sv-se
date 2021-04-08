---
title: Identitets baserad data åtkomst till lagrings tjänster på Azure
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Identity-baserad data åtkomst för att ansluta till lagrings tjänster på Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520021"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Ansluta till lagring med Identity-baserad data åtkomst (för hands version)

>[!IMPORTANT]
> De funktioner som presenteras i den här artikeln är i för hands version och bör [betraktas som en för hands version](/python/api/overview/azure/ml/#stable-vs-experimental) som kan ändras när som helst.

I den här artikeln får du lära dig hur du ansluter till lagrings tjänster i Azure med identitetsbaserade data åtkomst och Azure Machine Learning data lager via [Azure Machine Learning python SDK](/python/api/overview/azure/ml/intro).  

Normalt använder data lager för autentiseringsuppgifter för att kontrol lera att du har behörighet att komma åt lagrings tjänsten. De håller anslutnings information, t. ex. prenumerations-ID och token-auktorisering, i [Key Vault](https://azure.microsoft.com/services/key-vault/) som är kopplad till arbets ytan. När du skapar ett data lager som använder identitetsbaserade data åtkomst, används Azure-inloggningen ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) för att bekräfta att du har behörighet att komma åt lagrings tjänsten. I det här scenariot sparas inga autentiseringsuppgifter för autentisering och endast lagrings konto informationen lagras i data lagret. 

Information om hur du skapar data lager som använder autentisering baserad på autentisering, t. ex. åtkomst nycklar eller tjänstens huvud namn, finns i [Anslut till lagrings tjänster på Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Identitets baserad data åtkomst i Azure Machine Learning

Det finns två områden för att tillämpa identitetsbaserade data åtkomst i Azure Machine Learning. Särskilt när du arbetar med konfidentiella data och behöver mer detaljerad data åtkomst hantering. 

1. Åtkomst till lagrings tjänster.
1. Träna maskin inlärnings modeller med privata data.

### <a name="accessing-storage-services"></a>Åtkomst till lagrings tjänster

Du kan ansluta till lagrings tjänster via identitets-baserad data åtkomst med Azure Machine Learning data lager eller [Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). 

Normalt behålls dina autentiseringsuppgifter i ett data lager som används för att se till att du har åtkomst behörighet till lagrings tjänsten. När de här autentiseringsuppgifterna registreras med data lager kan alla användare med rollen arbets ytans *läsare* hämta dem – vilket kan vara en säkerhets risk för vissa organisationer. [Läs mer om rollen för arbets ytans *läsare*](how-to-assign-roles.md#default-roles). 

När du använder identitetsbaserade data Azure Machine Learning åtkomst uppmanas du att ange din Azure Active Directory token för autentisering av data åtkomst i stället för att behålla dina autentiseringsuppgifter i data lagret. Som möjliggör data åtkomst hantering på lagrings nivån och bevarar autentiseringsuppgifterna konfidentiellt. 

Samma sak gäller när du,

* [Skapa en data uppsättning direkt från lagrings-URL: er](#use-data-in-storage). 
* Arbeta med data interaktivt via en Jupyter Notebook på din lokala dator eller [beräknings instans](concept-compute-instance.md).

> [!NOTE]
> Autentiseringsuppgifterna som lagras med autentisering är: prenumerations-ID, signatur för delad åtkomst (SAS), lagrings åtkomst nycklar och tjänstens huvud namn, t. ex. klient-ID och klient-ID.

### <a name="model-training-on-private-data"></a>Modell träning för privata data

Vissa Machine Learning-scenarier omfattar utbildnings modeller med privata data. I sådana fall behöver data forskare köra utbildnings arbets flöden utan att exponera konfidentiella indata. I det här scenariot används en hanterad identitet för inlärnings beräkningen för autentisering av data åtkomst. På så sätt kan lagrings administratörer ge åtkomst till **lagrings-BLOB-dataläsaren** åtkomst till den hanterade identitet som inlärningen använder för att köra utbildnings jobbet, i stället för enskilda data experter. Lär dig hur du [konfigurerar hanterad identitet för en beräkning](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure Storage-konto med en lagrings typ som stöds. Följande lagrings typer stöds i för hands versionen. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL-databas](../azure-sql/database/sql-database-paas-overview.md)

- [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/install).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en [befintlig via python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Åtkomst behörigheter för lagring

För att säkerställa att du ansluter till lagrings tjänsten på ett säkert sätt, kräver Azure Machine Learning att du har behörighet att komma åt motsvarande data lagring.

Identitetsbaserade data åtkomst stöder endast anslutningar till följande lagrings tjänster:

* Azure Blob Storage
* Azure Data Lake generation 1
* Azure Data Lake generation 2
* Azure SQL-databas

För att få åtkomst till dessa lagrings tjänster måste du ha minst åtkomst till **lagrings BLOB-dataläsaren** . Läs mer om [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Endast lagrings kontots ägare kan [ändra åtkomst nivån via Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md).

Om du tränar en modell på ett fjärrberäknings mål måste beräknings identiteten beviljas med minst **Storage BLOB data Reader** -rollen från lagrings tjänsten. Lär dig hur du [konfigurerar hanterad identitet vid data bearbetning](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Arbeta med virtuella nätverk

Azure Machine Learning kan som standard inte kommunicera med ett lagrings konto som ligger bakom en brand vägg eller i ett virtuellt nätverk.

Lagrings konton kan konfigureras att bara tillåta åtkomst från vissa virtuella nätverk, vilket kräver ytterligare konfigurationer för att säkerställa att data inte läcker utanför nätverket. Det här beteendet är detsamma för autentisering baserad data åtkomst, se [vilka konfigurationer som behövs och hur de används för virtuella nätverks scenarier](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en lagrings tjänst på Azure som ett data lager skapas och registreras data lagret automatiskt i en angiven arbets yta. Granska följande avsnitt: [behörigheter för lagrings åtkomst](#storage-access-permissions) för vägledning om nödvändiga behörighets typer och [arbeta med virtuella nätverk](#work-with-virtual-networks) för information om hur du ansluter till data lagring bakom virtuella nätverk.

I följande kod noterar du frånvaron av autentiseringsmetoder, till exempel, `sas_token` , `account_key` `subscription_id` eller tjänstens huvud namn `client_id` . Detta utelämnande visar att Azure Machine Learning är att använda identitetsbaserade data åtkomst för autentisering. Eftersom skapandet av data lager vanligt vis sker interaktivt i en bärbar dator eller via Studio, används din Azure Active Directory token för autentisering av data åtkomst.

> [!NOTE]
> Data lager namn får endast bestå av gemena bokstäver, siffror och under streck. 

### <a name="azure-blob-container"></a>Azure Blob-behållare

Använd för att registrera en Azure Blob-behållare som ett data lager [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Följande kod skapar och registrerar `credentialless_blob` data lagret på `ws` arbets ytan och tilldelar det till variabeln `blob_datastore` . Detta data lager har åtkomst till `my_container_name` BLOB-behållaren på `my-account-name` lagrings kontot.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage generation 1

Använd [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) för att registrera ett data lager som ansluter till en Azure DataLake generation 1-lagring för en Azure Data Lake Storage generation 1 (ADLS gen 1) data lager.

Följande kod skapar och registrerar `credentialless_adls1` data lagret på `workspace` arbets ytan och tilldelar det till variabeln `adls_dstore` . Detta data lager har åtkomst till `adls_storage` Azure Data Lake Store lagrings konto.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generation 2

För en Azure Data Lake Storage generation 2 (ADLS gen 2) data lager använder du [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) för att registrera ett data lager som ansluter till en Azure DataLake gen 2-lagring.

Följande kod skapar och registrerar `credentialless_adls2` data lagret på `ws` arbets ytan och tilldelar det till variabeln `adls2_dstore` . Detta data lager har åtkomst till fil systemet `tabular` i `myadls2` lagrings kontot.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Använda data i Storage

[Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md) är det rekommenderade sättet att interagera med dina data i lagringen med Azure Machine Learning. 

Data uppsättningar paketerar dina data i ett Lazy utvärderat förbruknings Bart objekt för Machine Learning-uppgifter, till exempel träning. Med data uppsättningar kan du också [Ladda ned eller montera](how-to-train-with-datasets.md#mount-vs-download) filer i alla format från Azure Storage-tjänster som Azure Blob Storage och Azure Data sjöar, till ett beräknings mål.


**Om du vill skapa data uppsättningar med Identity-baserad data åtkomst** har du följande alternativ. Den här typen av data uppsättnings skapande använder din Azure Active Directory token för autentisering av data åtkomst. 

*  Referens Sök vägar från data lager som också använder identitets baserad data åtkomst. 
<br>I följande exempel `blob_datastore` har tidigare skapats med Identity-baserad data åtkomst.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Hoppa över skapande av data lager och skapa data uppsättningar direkt från lagrings-URL: er. För närvarande stöder den här funktionen endast Azure-blobbar och Azure Data Lake Storage generation 1 och 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Men när du skickar ett utbildnings jobb som använder en data uppsättning som har skapats med identitetsbaserade data åtkomst** används den hanterade identiteten för inlärnings beräkningen för autentisering av data åtkomst, i stället för din Azure Active Directory token. I det här scenariot ser du till att den hanterade identiteten för Compute beviljas med minst **Storage BLOB data Reader** -roll från lagrings tjänsten. Lär dig hur du [konfigurerar hanterad identitet vid data bearbetning](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md).
* [Träna med data uppsättningar](how-to-train-with-datasets.md).
* [Skapa ett data lager med nyckelbaserad data åtkomst](how-to-access-data.md).
