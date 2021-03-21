---
title: Bearbeta Azure blob-data med avancerad analys – team data science process
description: Utforska data och generera funktioner från data som lagras i Azure Blob Storage med hjälp av avancerad analys.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93307168"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Bearbeta Azure-blobbdata med avancerade analyser
Det här dokumentet beskriver hur du utforskar data och genererar funktioner från data som lagras i Azure Blob Storage. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Läsa in data i en Pandas data ram
För att kunna utforska och ändra en data uppsättning måste den laddas ned från BLOB-källan till en lokal fil som sedan kan läsas in i en Pandas data ram. Följ anvisningarna nedan för den här proceduren:

1. Hämta data från Azure-bloben med följande exempel på python-kod med Blob Service. Ersätt variabeln i koden nedan med dina egna värden: 
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```
2. Läs informationen i en Pandas data-bildruta från den nedladdade filen.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Nu är du redo att utforska data och generera funktioner för den här data uppsättningen.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Data utforskning
Här följer några exempel på hur du kan utforska data med Pandas:

1. Kontrol lera antalet rader och kolumner 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Kontrol lera de första eller sista raderna i data uppsättningen enligt nedan:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Kontrol lera data typen varje kolumn importerades med hjälp av följande exempel kod
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. Kontrol lera den grundläggande statistiken för kolumnerna i data uppsättningen enligt följande
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Titta på antalet poster för varje kolumn värde enligt följande
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. Räkna värden som saknas respektive det faktiska antalet poster i varje kolumn med hjälp av följande exempel kod
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Om du saknar värden för en speciell kolumn i data kan du släppa dem på följande sätt:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   Ett annat sätt att ersätta saknade värden är med funktionen mode:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Skapa en histogram-kurva med varierande antal lager platser för att rita fördelningen av en variabel    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Titta på korrelationer mellan variabler med en scatterplot eller med hjälp av den inbyggda funktionen korrelation
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Generering av funktioner
Vi kan generera funktioner med hjälp av python på följande sätt:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Generering av indikator värde-baserad funktion
Kategoriska-funktioner kan skapas på följande sätt:

1. Kontrol lera fördelningen av kategoriska-kolumnen:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Generera indikator värden för varje kolumn värden
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Koppla kolumnen indikator till den ursprungliga data ramen 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Ta bort själva den ursprungliga variabeln:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Generering av diskretisering-funktioner
För att skapa diskretiseras-funktioner går vi vidare enligt följande:

1. Lägg till en sekvens med kolumner till bin en numerisk kolumn
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Konvertera diskretisering till en sekvens med booleska variabler
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Slutligen kan du koppla in provdockans variabler tillbaka till den ursprungliga data ramen
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Skriva tillbaka data till Azure blob och förbruka i Azure Machine Learning
När du har utforskat data och skapat de nödvändiga funktionerna kan du överföra data (sampled eller bearbetas) till en Azure-blob och använda den i Azure Machine Learning med hjälp av följande steg: ytterligare funktioner kan skapas i Azure Machine Learning Studio (klassisk) också. 

1. Skriva data ramen till en lokal fil
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Ladda upp data till Azure Blob enligt följande:
   
    ```python
    from azure.storage.blob import BlobService
    import tables
   
    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>
   
    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
    try:
   
    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. Nu kan data läsas från bloben med hjälp av modulen Azure Machine Learning [Importera data][import-data] som visas på skärmen nedan:

![läsar BLOB][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data