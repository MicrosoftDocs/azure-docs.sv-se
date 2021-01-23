---
title: Överför stor data uppsättning till Azure Data Lake Storage Gen1-offline-metoder
description: Använd import/export-tjänsten för att kopiera data från Azure Blob Storage till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98700847"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Använd Azures import-/export tjänst för att kopiera data offline till Data Lake Storage Gen1

I den här artikeln får du lära dig hur du kopierar enorma data uppsättningar (>200 GB) till Data Lake Storage Gen1 genom att använda kopierings metoder offline, t. ex. [Azure import/export-tjänsten](../import-export/storage-import-export-service.md). Mer specifikt är filen som används som ett exempel i den här artikeln 339 420 860 416 byte, eller cirka 319 GB på disken. Vi kallar den här filen 319GB. tsv.

Med tjänsten Azure import/export kan du överföra stora mängder data på ett säkrare sätt till Azure Blob Storage genom att leverera hård diskar till ett Azure-datacenter.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du ha följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Storage-konto**.
* **Ett Azure Data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Förbereda data

Innan du använder import/export-tjänsten kan du dela upp data filen som ska överföras **till kopior som är mindre än 200 GB** i storlek. Import verktyget fungerar inte med filer som är större än 200 GB. I den här artikeln delar vi upp filen i segment om 100 GB vardera. Du kan göra detta med hjälp av [Cygwin](https://cygwin.com/install.html). Cygwin stöder Linux-kommandon. I det här fallet använder du följande kommando:

```console
split -b 100m 319GB.tsv
```

Delnings åtgärden skapar filer med följande namn.

* *319GB. tsv-del-AA*
* *319GB. tsv-del-AB*
* *319GB. tsv-del-AC*
* *319GB. tsv-del-AD*

## <a name="get-disks-ready-with-data"></a>Hämta diskar som är klara med data

Följ anvisningarna i [använda Azure import/export-tjänsten](../import-export/storage-import-export-service.md) (under avsnittet **förbereda dina enheter** ) för att förbereda dina hård diskar. Här är den övergripande ordningen:

1. Skaffa en hård disk som uppfyller de krav som ska användas för Azure import/export-tjänsten.
2. Identifiera ett Azure Storage-konto där data ska kopieras efter att de skickats till Azure-datacentret.
3. Använd [Azure import/export-verktyget](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), ett kommando rads verktyg. Här är ett exempel på ett fragment som visar hur du använder verktyget.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Se [använda Azure import/export-tjänsten](../import-export/storage-import-export-service.md) för fler exempel kods tycken.
4. Föregående kommando skapar en journal fil på den angivna platsen. Använd den här Journal filen för att skapa ett import jobb från [Azure Portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Skapa ett import jobb

Nu kan du skapa ett import jobb genom att följa anvisningarna i [använda Azure import/export-tjänsten](../import-export/storage-import-export-service.md) (under avsnittet **skapa import jobb** ). För det här import jobbet, med annan information, anger du även Journal filen som skapades när du förbereder disk enheterna.

## <a name="physically-ship-the-disks"></a>Leverera diskarna fysiskt

Nu kan du fysiskt leverera diskarna till ett Azure-datacenter. Data kopieras till Azure Storage blobbar som du angav när du skapade import jobbet. Om du väljer att ange spårnings informationen senare kan du även gå tillbaka till import jobbet och uppdatera spårnings numret när du skapar jobbet.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Kopiera data från blobbar till Data Lake Storage Gen1

När import jobbets status är slutförd kan du kontrol lera om data är tillgängliga i de Azure Storage blobbar som du har angett. Du kan sedan använda en mängd olika metoder för att flytta data från blobbar till Azure Data Lake Storage Gen1. Alla tillgängliga alternativ för att ladda upp data finns i mata [in data i data Lake Storage gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

I det här avsnittet ger vi de JSON-definitioner som du kan använda för att skapa en Azure Data Factory pipeline för att kopiera data. Du kan använda dessa JSON-definitioner från [Azure Portal](../data-factory/tutorial-copy-data-portal.md) eller [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Länkad länkad tjänst (Azure Storage BLOB)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Länkad mål tjänst (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Inmatnings data uppsättning

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Utgående data uppsättning

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pipeline (kopierings aktivitet)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Mer information finns i [Flytta data från Azure Storage blob till Azure Data Lake Storage gen1 att använda Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Konstruera om datafilerna i Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vi började med en fil som var 319 GB och avbröt den i filer med mindre storlek så att den kan överföras med hjälp av Azure import/export-tjänsten. Nu när data är i Azure Data Lake Storage Gen1 kan vi återskapa filen till dess ursprungliga storlek. Du kan använda följande Azure PowerShell-cmdlets för att göra det.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
