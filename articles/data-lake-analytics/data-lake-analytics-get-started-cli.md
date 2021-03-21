---
title: Skapa & fråga Azure Data Lake Analytics – Azure CLI
description: Lär dig hur du använder Azures kommando rads gränssnitt för att skapa ett Azure Data Lake Analytics-konto och skicka ett U-SQL-jobb.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 06/18/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9123ca6a1bfa90737bb1ce83ee365d1ecf514e1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221000"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Kom igång med Azure Data Lake Analytics med hjälp av Azure CLI

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Den här artikeln beskriver hur du använder kommando rads gränssnittet i Azure CLI för att skapa Azure Data Lake Analytics-konton, skicka USQL-jobb och kataloger. Jobbet läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du följande:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration:

```azurecli
az login
```

Du behöver öppna en webbadress och ange en autentiseringskod.  Därefter kan du följa anvisningarna för att ange dina uppgifter.

När du har loggat in visas dina prenumerationer.

Använda en viss prenumeration:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto

Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb. Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande objekt:

* **Azure-resursgrupp**. Ett Data Lake Analytics-konto måste skapas i en Azure-resursgrupp. Med [Azure Resource Manager](../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i programmet i en enda, samordnad åtgärd.  

Listar de befintliga resursgrupperna under din prenumeration:

```azurecli
az group list
```

Om du vill skapa en ny resursgrupp:

```azurecli
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Data Lake Analytics konto namn**. Varje Data Lake Analytics-konto har ett namn.
* **Plats**. Använd ett av de Azure-datacenter som stöder Data Lake Analytics.
* **Standard data Lake Stores konto**: varje data Lake Analytics konto har ett standard data Lake Store-konto.

Om du vill skapa en lista över befintliga Data Lake Store-konton:

```azurecli
az dls account list
```

Skapa ett nytt Data Lake Store-konto:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande syntax:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

När du har skapat ett konto kan du använda följande kommandon för att visa en lista över konton och visa kontoinformation:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"
```

## <a name="upload-data-to-data-lake-store"></a>Ladda upp data till Data Lake Store

I den här självstudien bearbetar du vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring.

Azure Portal innehåller ett användargränssnitt för att kopiera vissa exempeldatafiler till Data Lake Store-standardkontot, bland annat en sökloggfil. Se [Förbereda källdata](data-lake-analytics-get-started-portal.md) för att ladda upp data till Data Lake Store-standardkontot.

Använd följande kommandon för att ladda upp filer med Azure CLI:

```azurecli
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics kan också använda Azure Blob-lagring.  Information om att ladda upp data till Azure Blob-lagring finns i [Använda Azure CLI med Azure Storage](../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Skicka Data Lake Analytics-jobb

Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](/u-sql/).

### <a name="to-create-a-data-lake-analytics-job-script"></a>Skapa ett Data Lake Analytics-jobbskript

Skapa en textfil med följande U-SQL-skript och spara filen på din arbetsstation:

```usql
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**.

Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.

Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake Store-standardkonton. Du kan också använda absoluta sökvägar.  Exempel:

```usql
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:

```usql
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Azure Blob-container med offentliga blobar stöds inte.
> Azure Blob-container med offentliga containrar stöds inte.
>

### <a name="to-submit-jobs"></a>Skicka jobb

Använd följande syntax för att skicka ett jobb.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Exempel:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

### <a name="to-list-jobs-and-show-job-details"></a>Visa en lista över jobb och visa jobbinformation

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

### <a name="to-cancel-jobs"></a>Avbryta jobb

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Hämta jobbresultat

När jobbet har slutförts kan du använda följande kommandon för att visa och ladda ned filer med utdata:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Exempel:

```azurecli
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Nästa steg

* Om du vill se Data Lake Analytics Azure CLI-referens dokumentet, se [data Lake Analytics](/cli/azure/dla).
* Om du vill se Data Lake Store Azure CLI-referens dokumentet, se [data Lake Store](/cli/azure/dls).
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).