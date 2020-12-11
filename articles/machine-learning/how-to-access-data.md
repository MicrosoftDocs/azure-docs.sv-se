---
title: Ansluta till lagrings tjänster på Azure
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder data lager för att säkert ansluta till Azure Storage-tjänster under utbildning med Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: bb63ac6de6c48bb3853bd235d908ee745ff5279d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032855"
---
# <a name="connect-to-storage-services-on-azure"></a>Ansluta till lagrings tjänster på Azure

I den här artikeln får du lära dig hur du **ansluter till lagrings tjänster på Azure via Azure Machine Learning data lager**. Data lagringen ansluter säkert till Azure Storage-tjänsten utan att dina autentiseringsuppgifter för autentisering och integritet för den ursprungliga data källan bevaras i risk zonen. De lagrar anslutnings information, t. ex. prenumerations-ID och token-auktorisering i [Key Vault](https://azure.microsoft.com/services/key-vault/) som är kopplade till arbets ytan, så att du kan komma åt lagringen på ett säkert sätt utan att behöva hårdkoda dem i dina skript. Du kan använda [Azure Machine Learning python SDK](#python) eller [Azure Machine Learning Studio](how-to-connect-data-ui.md) för att skapa och registrera data lager.

Om du föredrar att skapa och hantera data lager med hjälp av Azure Machine Learning VS Code-tillägget; Mer information finns i [vs Code Resource Management instruktions guide](how-to-manage-resources-vscode.md#datastores) .

Du kan skapa data lager från [dessa Azure Storage-lösningar](#matrix). **För lagrings lösningar som inte stöds** och för att spara utgångs kostnad under ml experiment, [flytta dina data](#move) till en Azure Storage-lösning som stöds.  

Information om var data lagret får plats i Azure Machine Learning det totala arbets flödet för data åtkomst finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

## <a name="prerequisites"></a>Förutsättningar

Du behöver:
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- Ett Azure Storage-konto med en [lagrings typ som stöds](#matrix).

- [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig via python SDK. 

    Importera `Workspace` och `Datastore` -klassen och Läs in din prenumerations information från filen `config.json` med hjälp av funktionen `from_config()` . Detta söker efter JSON-filen i den aktuella katalogen som standard, men du kan också ange en Sök vägs parameter som pekar på filen med hjälp av `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    När du skapar en arbets yta registreras en Azure Blob-behållare och en Azure-filresurs automatiskt som data lager till arbets ytan. De heter `workspaceblobstore` `workspacefilestore` respektive. `workspaceblobstore`Används för att lagra arbets ytans artefakter och dina experiment loggar för Machine Learning. Den har också angetts som **standard data lager** och kan inte tas bort från arbets ytan. `workspacefilestore`Används för att lagra antecknings böcker och R-skript som har auktoriserats via [beräknings instanser](./concept-compute-instance.md#accessing-files).
    
    > [!NOTE]
    > Azure Machine Learning designer skapar ett data lager med namnet **azureml_globaldatasets** automatiskt när du öppnar ett exempel på design sidan för designern. Detta data lager innehåller bara exempel data uppsättningar. Använd **inte** det här data lagret för någon konfidentiell data åtkomst.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Typer av data lagrings tjänster som stöds

Data lager har för närvarande stöd för lagring av anslutnings information till de lagrings tjänster som anges i följande matris.

| Lagrings &nbsp; typ | Autentiseringstyp &nbsp; | [Azure &nbsp; Machine &nbsp; Learning Studio](https://ml.azure.com/) | [Azure &nbsp; Machine &nbsp; Learning &nbsp; python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) |  [Azure &nbsp; Machine &nbsp; Learning cli](reference-azure-machine-learning-cli.md) | [REST-API för Azure &nbsp; Machine &nbsp; Learning &nbsp;](/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure &nbsp; BLOB &nbsp; Storage](../storage/blobs/storage-blobs-overview.md)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓ |✓
[Azure &nbsp; - &nbsp; filresurs](../storage/files/storage-files-introduction.md)| Kontonyckel <br> SAS-token | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; data Lake &nbsp; Storage gen &nbsp; 1](../data-lake-store/index.yml)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; data Lake &nbsp; Storage gen &nbsp; 2](../storage/blobs/data-lake-storage-introduction.md)| Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; SQL &nbsp; Database](../azure-sql/database/sql-database-paas-overview.md)| SQL-autentisering <br>Tjänstens huvudnamn| ✓ | ✓ | ✓ |✓|
[Azure- &nbsp; postgresql](../postgresql/overview.md) | SQL-autentisering| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Database &nbsp; för &nbsp; mysql](../mysql/overview.md) | SQL-autentisering|  | ✓* | ✓* |✓*|
[Databricks &nbsp; fil &nbsp; system](/azure/databricks/data/databricks-file-system)| Ingen autentisering | | ✓** | ✓ ** |✓** |

\* MySQL stöds endast för pipeline- [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py)<br />
\*\* Databricks stöds endast för pipeline- [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?preserve-view=true&view=azure-ml-py)


### <a name="storage-guidance"></a>Minnesriktlinjer

Vi rekommenderar att du skapar ett data lager för en [Azure Blob-behållare](../storage/blobs/storage-blobs-introduction.md). Både standard-och Premium lagring är tillgängliga för blobbar. Även om Premium Storage är dyrare, kan snabbare data flödes hastigheter förbättra hastigheten på din utbildning, särskilt om du tränar mot en stor data uppsättning. Information om kostnaden för lagrings konton finns i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) bygger på Azure Blob Storage och är utformat för företags Big data Analytics. En grundläggande del av Data Lake Storage Gen2 är att lägga till ett [hierarkiskt namn område](../storage/blobs/data-lake-storage-namespace.md) till Blob Storage. Det hierarkiska namn området ordnar objekt/filer i en hierarki med kataloger för effektiv data åtkomst.

## <a name="storage-access-and-permissions"></a>Lagrings åtkomst och behörigheter

För att säkerställa att du ansluter till Azure Storage-tjänsten på ett säkert sätt, kräver Azure Machine Learning att du har behörighet att komma åt motsvarande data lagrings behållare. Den här åtkomsten beror på de autentiseringsreferenser som används för att registrera data lagret. 

### <a name="virtual-network"></a>Virtuellt nätverk 

Om ditt data lagrings konto finns i ett **virtuellt nätverk** krävs ytterligare konfigurations steg för att säkerställa att Azure Machine Learning har åtkomst till dina data. Se [använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk](how-to-enable-studio-virtual-network.md) för att se till att lämpliga konfigurations steg tillämpas när du skapar och registrerar ditt data lager.  

### <a name="access-validation"></a>Åtkomst verifiering

**Som en del av den första processen för skapande och registrering av data lagret** validerar Azure Machine Learning automatiskt att den underliggande lagrings tjänsten finns och den användare som angavs huvud namn, tjänstens huvud namn eller SAS-token har åtkomst till den angivna lagrings platsen.

**När data lagret har skapats** utförs den här verifieringen bara för metoder som kräver åtkomst till den underliggande lagrings behållaren, **inte** varje gång som data lager objekt hämtas. Verifieringen sker till exempel om du vill hämta filer från ditt data lager. men om du bara vill ändra ditt standard data lager sker inte verifieringen.

Om du vill autentisera din åtkomst till den underliggande lagrings tjänsten kan du ange antingen din konto nyckel, token för signaturer för delad åtkomst (SAS) eller tjänstens huvud namn i motsvarande `register_azure_*()` metod för den data lager typ som du vill skapa. I [matrisen lagrings typ](#matrix) visas de autentiseringstyper som stöds och som motsvarar varje data lager typ.

Du hittar konto nyckel, SAS-token och information om tjänstens huvud namn på din [Azure Portal](https://portal.azure.com).

* Om du planerar att använda en konto nyckel eller SAS-token för autentisering väljer du **lagrings konton** i den vänstra rutan och väljer det lagrings konto som du vill registrera. 
  * **Översikts** sidan innehåller information som konto namn, behållare och fil resurs namn. 
      1. För konto nycklar går du till **åtkomst nycklar** i fönstret **Inställningar** . 
      1. För SAS-token, gå till **signaturer för delad åtkomst** i fönstret **Inställningar** .

* Om du planerar att använda ett huvud namn för tjänsten för autentisering går du till din **Appregistreringar** och väljer vilken app du vill använda. 
    * Dess motsvarande **översikts** sida innehåller nödvändig information, t. ex. klient-ID och klient-ID.

> [!IMPORTANT]
> * Om du behöver ändra åtkomst nycklarna för ett Azure Storage konto (konto nyckel eller SAS-token) måste du synkronisera de nya autentiseringsuppgifterna med din arbets yta och de data lager som är anslutna till den. Lär dig hur du [synkroniserar dina uppdaterade autentiseringsuppgifter](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Behörigheter

För Azure Blob-behållare och Azure Data Lake gen 2-lagring, se till att dina autentiseringsuppgifter har åtkomst till **lagrings-BLOB-dataläsaren** . Läs mer om [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Ett konto SAS-token som standard saknar behörighet. 
* För **Läs behörighet** för data måste autentiseringsuppgifterna för autentisering ha minst list-och Läs behörighet för behållare och objekt. 

* För data **Skriv åtkomst** krävs även Skriv-och tilläggs behörigheter.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Skapa och registrera data lager

När du registrerar en Azure Storage-lösning som ett data lager skapar och registrerar du automatiskt data lagret till en speciell arbets yta. Läs avsnittet [lagrings åtkomst & behörigheter](#storage-access-and-permissions) för vägledning om virtuella nätverks scenarier och var du hittar nödvändiga autentiseringsuppgifter för autentisering. 

I det här avsnittet finns exempel på hur du skapar och registrerar ett data lager via python SDK för följande lagrings typer. De parametrar som anges i de här exemplen är de **parametrar som krävs** för att skapa och registrera ett data lager.

* [Azure Blob-behållare](#azure-blob-container)
* [Azure-filresurs](#azure-file-share)
* [Azure Data Lake Storage generation 2](#azure-data-lake-storage-generation-2)

 Information om hur du skapar data lager för andra lagrings tjänster som stöds finns i [referens dokumentationen för tillämpliga `register_azure_*` metoder](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=truemethods).

Om du föredrar en låg kod upplevelse, se [Anslut till data med Azure Machine Learning Studio](how-to-connect-data-ui.md).
>[!IMPORTANT]
> Om du avregistrerar och omregistrerar ett data lager med samma namn, och det Miss lyckas, kan det hända att Azure Key Vault för din arbets yta inte har mjuk borttagning aktive rad. Som standard aktive ras mjuk borttagning för Key Vault-instansen som skapats av din arbets yta, men den är kanske inte aktive rad om du använde ett befintligt nyckel valv eller har en arbets yta som skapats före oktober 2020. Information om hur du aktiverar mjuk borttagning finns i [Aktivera mjuk borttagning för ett befintligt nyckel valv]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault). "

> [!NOTE]
> Data lager namnet får bara bestå av gemena bokstäver, siffror och under streck. 

### <a name="azure-blob-container"></a>Azure Blob-behållare

Använd för att registrera en Azure Blob-behållare som ett data lager [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Följande kod skapar och registrerar `blob_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till `my-container-name` BLOB-behållaren på `my-account-name` lagrings kontot med hjälp av den angivna konto åtkomst nyckeln. Läs avsnittet [lagrings åtkomst & behörigheter](#storage-access-and-permissions) för vägledning om virtuella nätverks scenarier och var du hittar nödvändiga autentiseringsuppgifter för autentisering. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure-filresurs

Använd för att registrera en Azure-filresurs som ett data lager [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Följande kod skapar och registrerar `file_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till `my-fileshare-name` fil resursen på `my-account-name` lagrings kontot med hjälp av den angivna konto åtkomst nyckeln. Läs avsnittet [lagrings åtkomst & behörigheter](#storage-access-and-permissions) för vägledning om virtuella nätverks scenarier och var du hittar nödvändiga autentiseringsuppgifter för autentisering. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generation 2

För en Azure Data Lake Storage generation 2 (ADLS gen 2) data lager använder du [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) för att registrera ett data lager för autentiseringsuppgifter som är anslutet till en Azure DataLake gen 2-lagring med [tjänstens huvud namn](../active-directory/develop/howto-create-service-principal-portal.md).  

För att kunna använda tjänstens huvud namn måste du [Registrera ditt program](../active-directory/develop/app-objects-and-service-principals.md) och bevilja tjänstens huvud data åtkomst via antingen Azure-rollbaserad åtkomst kontroll (Azure RBAC) eller åtkomst kontrol listor (ACL). Lär dig mer om [åtkomst kontroll som har kon figurer ATS för ADLS gen 2](../storage/blobs/data-lake-storage-access-control-model.md). 

Följande kod skapar och registrerar `adlsgen2_datastore_name` data lagret på `ws` arbets ytan. Detta data lager har åtkomst till fil systemet `test` i `account_name` lagrings kontot med hjälp av de angivna autentiseringsuppgifterna för tjänstens huvud namn. Läs avsnittet [lagrings åtkomst & behörigheter](#storage-access-and-permissions) för vägledning om virtuella nätverks scenarier och var du hittar nödvändiga autentiseringsuppgifter för autentisering. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>Skapa data lager med hjälp av Azure Resource Manager

Det finns ett antal mallar i [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) som kan användas för att skapa data lager.

Information om hur du använder dessa mallar finns i [använda en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Använda data i dina data lager

När du har skapat ett data lager [skapar du en Azure Machine Learning data uppsättning](how-to-create-register-datasets.md) för att interagera med dina data. Data uppsättningar paketerar dina data i ett Lazy utvärderat förbruknings Bart objekt för Machine Learning-uppgifter, till exempel träning. De ger också möjlighet att [Ladda ned eller montera](how-to-train-with-datasets.md#mount-vs-download) filer i alla format från Azure Storage-tjänster som Azure Blob Storage och ADLS gen 2. Du kan också använda dem för att läsa in tabell data i en Pandas-eller Spark-DataFrame.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Hämta data lager från din arbets yta

Om du vill hämta ett särskilt data lager som registrerats i den aktuella arbets ytan använder du den [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-workspace--datastore-name-) statiska metoden i `Datastore` klassen:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Om du vill hämta listan över data lager som registrerats med en specifik arbets yta kan du använda- [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedatastores) egenskapen på ett objekt i arbets ytan:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Använd den här raden för att hämta arbets ytans standard data lager:

```Python
datastore = ws.get_default_datastore()
```
Du kan också ändra standard data lagret med följande kod. Den här funktionen stöds bara via SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Åtkomst till data under Poängsättning

Azure Machine Learning tillhandahåller flera olika sätt att använda dina modeller för att beräkna poäng. Några av dessa metoder ger inte åtkomst till data lager. Använd följande tabell för att förstå vilka metoder du kan använda för att komma åt data lager under poängsättningen:

| Metod | Åtkomst till data lager | Description |
| ----- | :-----: | ----- |
| [Batchförutsägelse](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Göra förutsägelser kring stora mängder data asynkront. |
| [Webb tjänst](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller som en webb tjänst. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Distribuera modeller till IoT Edge enheter. |

I situationer där SDK inte ger åtkomst till data lager kan du kanske skapa anpassad kod med hjälp av relevanta Azure SDK för att få åtkomst till data. Till exempel är [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python) ett klient bibliotek som du kan använda för att komma åt data som lagras i blobbar eller filer.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Flytta data till Azure Storage-lösningar som stöds

Azure Machine Learning stöder åtkomst till data från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. Om du använder lagring som inte stöds rekommenderar vi att du flyttar dina data till Azure Storage-lösningar som stöds med hjälp av [Azure Data Factory och de här stegen](../data-factory/quickstart-create-data-factory-copy-data-tool.md). Genom att flytta data till lagring som stöds kan du spara data utgående kostnader under Machine Learning-experiment. 

Azure Data Factory ger effektiv och flexibel data överföring med fler än 80 färdiga kopplingar utan extra kostnad. Dessa anslutningar omfattar Azure Data Services, lokala data källor, Amazon S3 och RedShift och Google BigQuery.

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md)
* [Träna en modell](how-to-set-up-training-targets.md)
* [Distribuera en modell](how-to-deploy-and-where.md)