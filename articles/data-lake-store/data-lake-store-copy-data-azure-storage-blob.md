---
title: Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1
description: Använd AdlCopy-verktyget för att kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85980097"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Använda DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Använda AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 tillhandahåller ett kommando rads verktyg, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), för att kopiera data från följande källor:

* Från Azure Storage blobbar till Data Lake Storage Gen1. Du kan inte använda AdlCopy för att kopiera data från Data Lake Storage Gen1 till Azure Storage blobbar.
* Mellan två Data Lake Storage Gen1-konton.

Du kan också använda AdlCopy-verktyget i två olika lägen:

* **Fristående**, där verktyget använder data Lake Storage gen1 resurser för att utföra uppgiften.
* Genom att **använda ett data Lake Analytics konto**, där de enheter som tilldelats ditt data Lake Analytics-konto används för att utföra kopieringen. Du kanske vill använda det här alternativet när du vill utföra kopierings aktiviteterna på ett förutsägbart sätt.

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage blobs** -behållare med vissa data.
* **Ett data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)
* **Data Lake Analytics konto (valfritt)** – se [kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) för instruktioner om hur du skapar ett data Lake Analytics-konto.
* **AdlCopy-verktyget**. Installera [AdlCopy-verktyget](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntax för verktyget AdlCopy

Använd följande syntax för att arbeta med verktyget AdlCopy

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

Parametrarna i syntaxen beskrivs nedan:

| Alternativ | Beskrivning |
| --- | --- |
| Källa |Anger platsen för data källan i Azure Storage-blobben. Källan kan vara en BLOB-behållare, en BLOB eller ett annat Data Lake Storage Gen1 konto. |
| Måltransportadr |Anger Data Lake Storage Gen1 destination att kopiera till. |
| SourceKey |Anger lagrings åtkomst nyckeln för Azure Storage BLOB-källan. Detta krävs endast om källan är en BLOB-behållare eller en blob. |
| Konto |**Valfritt**. Använd det här om du vill använda Azure Data Lake Analytics kontot för att köra kopierings jobbet. Om du använder alternativet/Account i syntaxen men inte anger ett Data Lake Analytics konto använder AdlCopy ett standard konto för att köra jobbet. Om du använder det här alternativet måste du också lägga till källan (Azure Storage Blob) och mål (Azure Data Lake Storage Gen1) som data källor för ditt Data Lake Analytics-konto. |
| Enheter |Anger antalet Data Lake Analytics enheter som ska användas för kopierings jobbet. Det här alternativet är obligatoriskt om du använder alternativet **/Account** för att ange data Lake Analytics kontot. |
| Mönster |Anger ett regex-mönster som visar vilka blobbar eller filer som ska kopieras. AdlCopy använder SKIFT läges känslig matchning. Standard mönstret när inget mönster har angetts är att kopiera alla objekt. Det finns inte stöd för att ange flera fil mönster. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Använd AdlCopy (som fristående) för att kopiera data från en Azure Storage-BLOB

1. Öppna en kommando tolk och navigera till den katalog där AdlCopy är installerat, vanligt vis `%HOMEPATH%\Documents\adlcopy` .
1. Kör följande kommando för att kopiera en angiven BLOB från käll behållaren till en Data Lake Storage Gen1 mapp:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Exempel:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >Syntaxen ovan anger den fil som ska kopieras till en mapp i Data Lake Storage Gen1-kontot. AdlCopy-verktyget skapar en mapp om det angivna mappnamnet inte finns.

    Du uppmanas att ange autentiseringsuppgifterna för den Azure-prenumeration under vilken du har ditt Data Lake Storage Gen1-konto. Du kommer att se utdata som liknar följande:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Du kan också kopiera alla blobbar från en behållare till Data Lake Storage Gen1 kontot med hjälp av följande kommando:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Exempel:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Om du kopierar från ett Azure Blob Storage-konto kan du begränsas under kopiering på Blob Storage-sidan. Detta innebär att kopierings jobbets prestanda försämras. Mer information om gränserna för Azure Blob Storage finns i Azure Storage gränser för [Azure-prenumeration och tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Använd AdlCopy (som fristående) om du vill kopiera data från ett annat Data Lake Storage Gen1 konto

Du kan också använda AdlCopy för att kopiera data mellan två Data Lake Storage Gen1-konton.

1. Öppna en kommando tolk och navigera till den katalog där AdlCopy är installerat, vanligt vis `%HOMEPATH%\Documents\adlcopy` .
1. Kör följande kommando för att kopiera en enskild fil från ett Data Lake Storage Gen1-konto till ett annat.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Exempel:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > Syntaxen ovan anger den fil som ska kopieras till en mapp i mål Data Lake Storage Gen1s konto. AdlCopy-verktyget skapar en mapp om det angivna mappnamnet inte finns.
   >
   >

    Du uppmanas att ange autentiseringsuppgifterna för den Azure-prenumeration under vilken du har ditt Data Lake Storage Gen1-konto. Du kommer att se utdata som liknar följande:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. Följande kommando kopierar alla filer från en angiven mapp i käll Data Lake Storage Gen1s kontot till en mapp i mål Data Lake Storage Gen1 kontot.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du använder AdlCopy som ett fristående verktyg körs kopian på delade, Azure-hanterade resurser. Vilken prestanda du kan få i den här miljön beror på system belastning och tillgängliga resurser. Det här läget används bäst för små överföringar på en ad hoc-basis. Inga parametrar behöver justeras när AdlCopy används som fristående verktyg.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Använda AdlCopy (med Data Lake Analytics konto) för att kopiera data

Du kan också använda ditt Data Lake Analytics-konto för att köra jobbet AdlCopy för att kopiera data från Azure Storage-blobbar till Data Lake Storage Gen1. Du använder vanligt vis det här alternativet när data som ska flyttas är i intervallet gigabyte och terabyte och du vill ha bättre och förutsägbart prestanda data flöde.

Om du vill använda ditt Data Lake Analytics-konto med AdlCopy för att kopiera från en Azure Storage Blob måste källan (Azure Storage Blob) läggas till som en data källa för ditt Data Lake Analytics-konto. Anvisningar om hur du lägger till ytterligare data källor i Data Lake Analytics-kontot finns i [hantera data Lake Analytics konto data källor](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Om du kopierar från ett Azure Data Lake Storage Gen1 konto som källa med ett Data Lake Analytics konto behöver du inte associera Data Lake Storage Gen1-kontot med Data Lake Analytics-kontot. Kravet att associera käll arkivet med Data Lake Analytics-kontot är bara när källan är ett Azure Storage konto.
>
>

Kör följande kommando för att kopiera från en Azure Storage-blob till ett Data Lake Storage Gen1 konto med Data Lake Analytics-konto:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Exempel:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

På samma sätt kan du köra följande kommando för att kopiera alla filer från en angiven mapp i käll Data Lake Storage Gen1s kontot till en mapp i mål Data Lake Storage Gen1 kontot med hjälp av Data Lake Analytics konto:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

När du kopierar data i intervallet terabyte kan du använda AdlCopy med ditt eget Azure Data Lake Analytics-konto för att ge bättre och mer förutsägbar prestanda. Parametern som ska justeras är antalet Azure Data Lake Analytics enheter som ska användas för kopierings jobbet. Om du ökar antalet enheter kommer kopierings jobbets prestanda att öka. Varje fil som ska kopieras kan använda maximalt en enhet. Att ange fler enheter än antalet filer som kopieras ökar inte prestandan.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Använda AdlCopy för att kopiera data med hjälp av mönster matchning

I det här avsnittet får du lära dig hur du använder AdlCopy för att kopiera data från en källa (i vårt exempel använder vi Azure Storage Blob) till ett mål Data Lake Storage Gen1 konto med hjälp av mönster matchning. Du kan till exempel använda stegen nedan för att kopiera alla filer med fil namns tillägget. csv från käll-bloben till målet.

1. Öppna en kommando tolk och navigera till den katalog där AdlCopy är installerat, vanligt vis `%HOMEPATH%\Documents\adlcopy` .
1. Kör följande kommando för att kopiera alla filer med tillägget *. csv från en angiven BLOB från käll behållaren till en Data Lake Storage Gen1 mapp:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Exempel:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Fakturering

* Om du använder AdlCopy-verktyget som fristående debiteras du för utgående kostnader för att flytta data, om käll Azure Storages kontot inte finns i samma region som Data Lake Storage Gen1 kontot.
* Om du använder AdlCopy-verktyget med ditt Data Lake Analytics konto kommer standard [data Lake Analytics fakturerings priser](https://azure.microsoft.com/pricing/details/data-lake-analytics/) att gälla.

## <a name="considerations-for-using-adlcopy"></a>Att tänka på när du använder AdlCopy

* AdlCopy (för version 1.0.5) stöder kopiering av data från källor som samlat mer än tusentals filer och mappar. Men om det uppstår problem med att kopiera en stor data uppsättning kan du distribuera filerna/mapparna i olika undermappar och använda sökvägen till dessa undermappar som källa i stället.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestanda överväganden vid användning av AdlCopy

AdlCopy stöder kopiering av data som innehåller tusentals filer och mappar. Men om det uppstår problem med att kopiera en stor data uppsättning kan du distribuera filerna/mapparna i mindre undermappar. AdlCopy har skapats för ad hoc-kopior. Om du försöker kopiera data regelbundet bör du överväga att använda [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) som ger fullständig hantering runt kopierings åtgärderna.

## <a name="release-notes"></a>Viktig information

* 1.0.13 – om du kopierar data till samma Azure Data Lake Storage Gen1 konto över flera AdlCopy-kommandon behöver du inte ange dina autentiseringsuppgifter för varje körning längre. AdlCopy kommer nu att cachelagra den informationen i flera körningar.

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
