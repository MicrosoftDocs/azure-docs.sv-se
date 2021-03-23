---
title: 'Snabb start: skapa en Azure Data Factory med python'
description: Använd en data fabrik för att kopiera data från en plats i Azure Blob Storage till en annan plats.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 01/15/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: c21956e47d2eb7634a9950751929f82875f490fb
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801646"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Snabbstart: Skapa en datafabrik och pipeline med Python

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här snabb starten skapar du en data fabrik med hjälp av python. Pipelinen i den här data fabriken kopierar data från en mapp till en annan mapp i Azure Blob Storage.

Azure Data Factory är en molnbaserad data integrerings tjänst som gör att du kan skapa data drivna arbets flöden för att dirigera och automatisera data förflyttning och data omvandling. Med hjälp av Azure Data Factory kan du skapa och schemalägga data drivna arbets flöden som kallas pipeliner.

Pipelines kan mata in data från olika data lager. Pipelines bearbetar eller transformerar data med hjälp av beräknings tjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning. Pipelines publicerar utdata till data lager som Azure Synapse Analytics för Business Intelligence-program (BI).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Ett Azure Storage konto](../storage/common/storage-account-create.md).

* [Azure Storage Explorer](https://storageexplorer.com/) (valfritt).

* [Ett program i Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Anteckna följande värden som du kan använda i senare steg: **program-ID**, **autentiseringsnyckel** och **klient-ID**. Tilldela program rollen **deltagare** genom att följa anvisningarna i samma artikel.

## <a name="create-and-upload-an-input-file"></a>Skapa och ladda upp en indatafil

1. Öppna Anteckningar. Kopiera följande text och spara den som **input.txt** på disken.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Använd verktyg som [Azure Storage Explorer](https://storageexplorer.com/) till att skapa containern **adfv2tutorial** och mappen **input** i containern. Ladda sedan upp filen **input.txt** till mappen **input**.

## <a name="install-the-python-package"></a>Installera Python-paketet

1. Öppna en terminal eller kommandotolk med administratörsbehörighet. 
2. Installera först Python-paketet för hanteringsresurser i Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Om du vill installera Python-paketet för Data Factory kör du följande kommando:

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Python SDK för Data Factory](https://github.com/Azure/azure-sdk-for-python) stöder python 2,7 och 3.6 +.

4. Kör följande kommando för att installera python-paketet för Azure Identity Authentication:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Paketet "Azure-Identity" kan ha konflikter med "Azure-CLI" på vissa vanliga beroenden. Om du uppfyller eventuella autentiseringsproblem tar du bort "Azure-CLI" och dess beroenden eller använder en ren dator utan att installera "Azure-CLI"-paketet så att det fungerar.
    
## <a name="create-a-data-factory-client"></a>Skapa en datafabriksklient

1. Skapa en fil med namnet **datafactory.py**. Lägg till följande instruktioner för att lägga till referenser till namnområden.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Lägg till följande funktioner som skriver ut information.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Lägg till följande kod till **Main**-metoden som skapar en instans av klassen DataFactoryManagementClient. Du använde det är objektet till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Du kan också använda det här objektet för att övervaka information om pipelinekörning. Ställ in variabeln **subscription_id** för ID:t för din Azure-prenumeration. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Lägg till följande kod som skapar en **datafabrik** till **Main**-metoden. Om din resursgrupp redan finns kommenterar du ut det första `create_or_update`-instruktionen.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst

Lägg till följande kod i **Main**-metoden som skapar en **länkad Azure Storage-tjänst**.

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här snabbstarten behöver du bara skapa en Azure Storage-länkad tjänst som både kopia på källa och mottagarlagring, med namnet "AzureStorageLinkedService" i exemplet. Byt ut `<storageaccountname>` och `<storageaccountkey>` mot namnet på och nyckeln för ditt Azure-lagringskonto.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Skapa datauppsättningar

I det här avsnittet skapar du två datauppsättningar: en för källan och en för mottagaren.

### <a name="create-a-dataset-for-source-azure-blob"></a>Skapa en datauppsättning för Azure Blob-källan

Lägg till följande kod till Main-metoden som skapar en Azure-blobdatauppsättning. Mer information om de här Azure-blobegenskaperna finns i artikeln [Azure Blob-anslutningsapp](connector-azure-blob-storage.md#dataset-properties).

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Skapa en datauppsättning för Azure Blob-mottagaren

Lägg till följande kod till Main-metoden som skapar en Azure-blobdatauppsättning. Mer information om de här Azure-blobegenskaperna finns i artikeln [Azure Blob-anslutningsapp](connector-azure-blob-storage.md#dataset-properties).

Du definierar en datauppsättning som representerar källdata i Azure Blob. Denna Blob-datauppsättning refererar till den Azure Storage-länkade tjänst som du skapar i föregående steg.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Skapa en pipeline

Lägg till följande kod som skapar och **aktiverar en pipeline** till **Main**-metoden.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning

Lägg till följande kod i **Main**-metoden för att **utlösa en pipelinekörning**.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Övervaka en pipelinekörning

Om du vill övervaka pipelinekörningen lägger du till följande kod för **Main**-metoden:

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Lägg nu till följande instruktion för att anropa **Main**-metoden när programmet körs:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Fullständigt skript

Här är den fullständiga Python-koden:

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och kontrollera sedan pipelinekörningen.

Konsolen skriver ut förloppet för skapandet av datafabriken, den länkade tjänsten, datauppsättningar, pipeline och pipelinekörning. Vänta tills du ser information om körningen av kopieringsaktiviteten med storlek för lästa/skrivna data. Använd sedan verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att kontrollera de blob(ar) som kopierats till outputBlobPath från inputBlobPath som du angav i variablerna.

Här är exempel på utdata:

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Rensa resurser

För att ta bort datafabriken lägger du till följande kod till programmet:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Nästa steg

Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-dot-net.md) om du vill lära dig hur du använder Data Factory i fler scenarier.
