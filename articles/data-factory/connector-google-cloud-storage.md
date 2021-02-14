---
title: Kopiera data från Google Cloud Storage med hjälp av Azure Data Factory
description: Läs om hur du kopierar data från Google Cloud Storage till mottagar data lager som stöds med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: jingwang
ms.openlocfilehash: 4d8ddb89055d318aaea9ada7f38f908223ff5a57
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388455"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Kopiera data från Google Cloud Storage med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från Google Cloud Storage (GC generationer). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Google Cloud Storage-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder den här Google Cloud Storage Connector kopiering av filer som är eller parsar filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md). Det drar nytta av GC generationer: s S3-kompatibla interoperabilitet.

## <a name="prerequisites"></a>Förutsättningar

Följande inställningar krävs på ditt Google Cloud Storage-konto:

1. Aktivera interoperabilitet för ditt Google Cloud Storage-konto
2. Ange standard projektet som innehåller de data som du vill kopiera från mål GC generationer Bucket.
3. Skapa ett tjänst konto och definiera rätt behörighets nivåer med hjälp av Cloud IAM på GCP. 
4. Generera åtkomst nycklar för det här tjänst kontot.

![Hämta åtkomst nyckel för Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Behörigheter som krävs

Om du vill kopiera data från Google Cloud Storage kontrollerar du att du har beviljats följande behörigheter för objekt åtgärder: ` storage.objects.get` och ` storage.objects.list` .

Om du använder Data Factory gränssnittet för att redigera ` storage.buckets.list` krävs ytterligare behörighet för åtgärder som att testa anslutningen till den länkade tjänsten och surfa från roten. Om du inte vill tilldela den här behörigheten kan du välja alternativen "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" från användar gränssnittet.

En fullständig lista över Google Cloud Storage-roller och tillhör ande behörigheter finns i [IAM-roller för moln lagring](https://cloud.google.com/storage/docs/access-control/iam-roles) på Google Cloud-webbplatsen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Google Cloud Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för länkade tjänster i Google Cloud Storage:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **GoogleCloudStorage**. | Ja |
| accessKeyId | ID för den hemliga åtkomst nyckeln. Se [krav](#prerequisites)för att hitta åtkomst nyckeln och hemligheten. |Ja |
| secretAccessKey | Den hemliga åtkomst nyckeln. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Ange den anpassade GC generationer-slutpunkten som `https://storage.googleapis.com` . | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |Inga |

Här är ett exempel:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Google Cloud Storage under `location` Inställningar i en formaterad data uppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen **Type** under `location` i data uppsättningen måste anges till **GoogleCloudStorageLocation**. | Ja      |
| bucketName | GC generationer-Bucket-namn.                                          | Ja      |
| folderPath | Sökvägen till mappen under den angivna Bucket. Om du vill använda ett jokertecken för att filtrera mappen, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Inga       |
| fileName   | Fil namnet under den angivna Bucket och sökvägen till mappen. Om du vill använda ett jokertecken för att filtrera filerna, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | Inga       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Google Cloud Storage-källan.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage som käll typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Google Cloud Storage under `storeSettings` Inställningar i en format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | **Typ** egenskapen under `storeSettings` måste anges till **GoogleCloudStorageReadSettings**. | Ja                                                         |
| ***Leta upp de filer som ska kopieras:*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från den angivna Bucket-eller mappen/fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla filer från en Bucket eller mapp, anger du också `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: GC generationer-prefix<br>-prefix | Prefix för nyckel namnet GC generationer under den angivna Bucket som kon figurer ATS i data uppsättningen för att filtrera källans GC generationer-filer. GC generationer-nycklar vars namn börjar med `bucket_in_dataset/this_prefix` väljs. Den använder GC generationer filter på service sidan, vilket ger bättre prestanda än ett Wildcard-filter. | Inga |
| ALTERNATIV 3: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken under den angivna Bucket som kon figurer ATS i en data uppsättning för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om ditt mappnamn har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Inga                                            |
| ALTERNATIV 3: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den angivna Bucket och mappsökvägen (eller sökvägen till jokertecken) för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om fil namnet har ett jokertecken eller detta escape-tecken i.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Ja |
| ALTERNATIV 3: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera, en fil per rad, som är den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen.<br/>När du använder det här alternativet ska du inte ange fil namnet i data uppsättningen. Se fler exempel i [fil List exempel](#file-list-examples). |Inga |
| ***Ytterligare inställningar:*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när **rekursivt** har angetts till **True** och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är **True** (standard) och **false**.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |Inga |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt fil kopierings scenario. Standardvärdet: false. |Inga |
| modifiedDatetimeStart    | Filerna filtreras baserat på attributet: senast ändrad. <br>Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null** väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | Inga                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | Inga                                                          |
| enablePartitionDiscovery | För filer som är partitionerade anger du om du vill parsa partitionerna från fil Sök vägen och lägga till dem som ytterligare käll kolumner.<br/>Tillåtna värden är **false** (standard) och **True**. | Inga                                            |
| partitionRootPath | När partitions identifiering har Aktiver ATS anger du den absoluta rot Sök vägen för att kunna läsa partitionerade mappar som data kolumner.<br/><br/>Om den inte anges, som standard,<br/>– När du använder fil Sök vägen i data uppsättningen eller en lista med filer på källan, är partitionens rot Sök väg den sökväg som kon figurer ATS i data uppsättningen.<br/>– När du använder mapp-filter med jokertecken är partitionens rot Sök väg den underordnade sökvägen före det första jokertecknet.<br/><br/>Anta till exempel att du konfigurerar sökvägen i dataset som "rot/mapp/år = 2020/månad = 08/Day = 27":<br/>– Om du anger partitionens rot Sök väg som "rot/mapp/år = 2020" genererar kopierings aktiviteten två kolumner `month` och `day` värdet "08" respektive "27", förutom kolumnerna inuti filerna.<br/>-Om partitionens rot Sök väg inte anges genereras ingen extra kolumn. | Inga                                            |
| maxConcurrentConnections | Antalet samtidiga anslutningar till lagringen. Ange bara när du vill begränsa samtidiga anslutningar till data lagret. | Inga                                                          |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| Bucket | key | rekursiva | Källans mappstruktur och filter resultat (filer i fetstil hämtas)|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | falskt | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I det här avsnittet beskrivs det resulterande beteendet för att använda en fil List Sök väg i kopierings aktivitets källan.

Antag att du har följande mappstruktur och vill kopiera filerna i fetstil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | Data Factory konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättning:**<br>Bucket `bucket`<br>– Mappsökväg: `FolderA`<br><br>**I kopierings aktivitets Källa:**<br>– Sökväg till fil lista: `bucket/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera, en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen. |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktiviteten](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md).

## <a name="legacy-models"></a>Äldre modeller

Om du använde en Amazon S3-koppling för att kopiera data från Google Cloud Storage finns det fortfarande stöd för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen som beskrivs ovan. Redigerings gränssnittet för Data Factory har växlat till att generera den nya modellen.

## <a name="next-steps"></a>Nästa steg
En lista över data lager som kopierings aktiviteten i Azure Data Factory stöder som källor och mottagare finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
