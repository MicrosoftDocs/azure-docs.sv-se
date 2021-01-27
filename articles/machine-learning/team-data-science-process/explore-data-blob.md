---
title: Utforska data i Azure Blob Storage med Pandas-team data science process
description: Utforska data som lagras i Azure Blob-behållare med Pandas python-paketet.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805679"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Utforska data i Azure Blob Storage med Pandas

Den här artikeln beskriver hur du utforskar data som lagras i Azure Blob-behållare med hjälp av [Pandas](https://pandas.pydata.org/) python-paket.

Den här uppgiften är ett steg i [processen för team data vetenskap](overview.md).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapat ett Azure Storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Lagrat dina data i ett Azure Blob Storage-konto. Om du behöver instruktioner, se [Flytta data till och från Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Läs in data i en Pandas-DataFrame
Om du vill utforska och ändra en data uppsättning måste du först ladda ned den från BLOB-källan till en lokal fil som sedan kan läsas in i en Pandas-DataFrame. Följ anvisningarna nedan för den här proceduren:

1. Hämta data från Azure-bloben med följande python-kod exempel med hjälp av Blob Service. Ersätt variabeln i följande kod med dina egna värden:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Läs data till en Pandas-DataFrame från den nedladdade filen.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Nu är du redo att utforska data och generera funktioner för den här data uppsättningen.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Exempel på data utforskning med Pandas
Här följer några exempel på hur du kan utforska data med Pandas:

1. Kontrol lera **antalet rader och kolumner**

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Granska** de första eller sista **raderna** i följande data uppsättning:

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Kontrol lera **data typen** varje kolumn importerades med hjälp av följande exempel kod

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. Kontrol lera den **grundläggande statistiken** för kolumnerna i data uppsättningen enligt följande

    ```python
    dataframe_blobdata.describe()
    ```

1. Titta på antalet poster för varje kolumn värde enligt följande

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. **Räkna värden som saknas** respektive det faktiska antalet poster i varje kolumn med hjälp av följande exempel kod

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Om du saknar **värden** för en speciell kolumn i data kan du släppa dem på följande sätt:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    Ett annat sätt att ersätta saknade värden är med funktionen mode:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. Skapa en **histogram** -kurva med varierande antal lager platser för att rita fördelningen av en variabel

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Titta på **korrelationer** mellan variabler med en scatterplot eller med hjälp av den inbyggda funktionen korrelation

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
