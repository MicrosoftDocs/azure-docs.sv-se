---
title: Kopiera data från Amazon Simple Storage Service (S3)
description: Läs om hur du kopierar data från Amazon Simple Storage Service (S3) till mottagar data lager som stöds med hjälp av Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 03b0cd852f34e115cc5bbc60448e45fcbb680474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601235"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Kopiera data från tjänsten Amazon Simple Storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuell version](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från Amazon Simple Storage Service (Amazon S3). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

>[!TIP]
>Mer information om scenariot för data migrering från Amazon S3 till Azure Storage finns i [använda Azure Data Factory för att migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Amazon S3-kopplingen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder den här Amazon S3-anslutaren kopiering av filer som är eller parsar filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md). Du kan också välja att [bevara fil-metadata under kopieringen](#preserve-metadata-during-copy). Anslutningen använder [AWS Signature version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) för att autentisera begär anden till S3.

>[!TIP]
>Du kan använda den här Amazon S3-anslutningen för att kopiera data från *valfri S3-kompatibel lagringsprovider*, till exempel [Google Cloud Storage](connector-google-cloud-storage.md). Ange motsvarande tjänst-URL i den länkade tjänst konfigurationen.

## <a name="required-permissions"></a>Behörigheter som krävs

Om du vill kopiera data från Amazon S3 ser du till att du har beviljats följande behörigheter för Amazon S3-objekt åtgärder: `s3:GetObject` och `s3:GetObjectVersion` .

Om du använder Data Factory gränssnittet för att redigera `s3:ListAllMyBuckets` krävs ytterligare och `s3:ListBucket` / `s3:GetBucketLocation` behörigheter för åtgärder som att testa anslutningen till den länkade tjänsten och surfa från roten. Om du inte vill bevilja dessa behörigheter kan du välja alternativen "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" från användar gränssnittet.

En fullständig lista över Amazon S3-behörigheter finns i [Ange behörigheter i en princip](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) på AWS-webbplatsen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Amazon S3.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en Amazon S3-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **AmazonS3**. | Yes |
| authenticationType | Ange autentiseringstypen som används för att ansluta till Amazon S3. Du kan välja att använda åtkomst nycklar för ett AWS-konto för identitets-och åtkomst hantering (IAM) eller [tillfälliga säkerhets referenser](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).<br>Tillåtna värden är: `AccessKey` (standard) och `TemporarySecurityCredentials` . |No |
| accessKeyId | ID för den hemliga åtkomst nyckeln. |Yes |
| secretAccessKey | Den hemliga åtkomst nyckeln. Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| sessionToken | Gäller när du använder autentisering med [tillfälliga autentiseringsuppgifter](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) . Lär dig hur du [begär tillfälliga säkerhets referenser](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) från AWS.<br>Observera att AWS tillfälliga autentiseringsuppgifter upphör att gälla mellan 15 minuter och 36 timmar baserat på inställningar. Kontrol lera att autentiseringsuppgiften är giltig när aktiviteten körs, särskilt för drift arbets belastning – till exempel kan du uppdatera den regelbundet och lagra den i Azure Key Vault.<br>Markera det här fältet som **SecureString** för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |No |
| serviceUrl | Ange den anpassade S3-slutpunkten om du kopierar data från en S3-kompatibel lagringsprovider än den officiella Amazon S3-tjänsten. Om du till exempel vill kopiera data från Google Cloud Storage anger du `https://storage.googleapis.com` . | No |
| forcePathStyle | Anger om du vill använda S3 [-åtkomst till sökväg](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) i stället för en virtuell värd stils åtkomst. Tillåtna värden är: **falskt** (standard), **Sant**.<br>Om du ansluter till en S3-kompatibel lagringsprovider än den officiella Amazon S3-tjänsten, och detta data lager kräver åtkomst till åtkomst till sökväg (till exempel [Oracle-molnbaserad lagring](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)), ställer du in den här egenskapen på True. Se efter i dokumentationen för varje data lager om åtkomst till Sök vägs format krävs eller inte. |No |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller den lokala integrerings körningen (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges använder tjänsten standard Azure integration Runtime. |No |

>[!TIP]
>Ange URL: en för den anpassade S3-tjänsten om du kopierar data från en S3-kompatibel lagrings plats än den officiella Amazon S3-tjänsten.

**Exempel: använda autentisering med åtkomst nyckel**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: använda autentisering med tillfälliga säkerhets referenser**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon S3 under `location` Inställningar i en format-baserad data uppsättning:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | **Typ** egenskapen under `location` i en data mängd måste anges till **AmazonS3Location**. | Yes      |
| bucketName | S3-Bucket-namn.                                          | Yes      |
| folderPath | Sökvägen till mappen under den angivna Bucket. Om du vill använda ett jokertecken för att filtrera mappen, hoppar du över den här inställningen och anger det i inställningarna för aktivitets källan. | No       |
| fileName   | Fil namnet under den angivna Bucket och sökvägen till mappen. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger det i inställningarna för aktivitets källan. | No       |
| version | Versionen av S3-objektet, om S3-versioner är aktive rad. Om den inte anges kommer den senaste versionen att hämtas. |No |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som Amazon S3-källan stöder.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 som käll typ

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Amazon S3 under `storeSettings` Inställningar i en format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | **Typ** egenskapen under `storeSettings` måste anges till **AmazonS3ReadSettings**. | Yes                                                         |
| ***Leta upp de filer som ska kopieras:*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från den angivna Bucket-eller mappen/fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla filer från en Bucket eller mapp, anger du också `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: S3-prefix<br>-prefix | Prefix för S3-nyckel namnet under den angivna Bucket som kon figurer ATS i en data uppsättning för att filtrera källa S3-filer. S3-nycklar vars namn börjar med `bucket_in_dataset/this_prefix` väljs. Den använder S3's service filter, som ger bättre prestanda än ett Wildcard-filter.<br/><br/>När du använder prefix och väljer att kopiera till filbaserad mottagare med bevara hierarkin, noterar du under Sök vägen efter att det sista "/"-prefixet är bevarat. Du kan till exempel ha källa  `bucket/folder/subfolder/file.txt` och konfigurera prefixet som `folder/sub` , och den bevarade fil Sök vägen är `subfolder/file.txt` . | No |
| ALTERNATIV 3: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken under den angivna Bucket som kon figurer ATS i en data uppsättning för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om ditt mappnamn har ett jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | No                                            |
| ALTERNATIV 3: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den angivna Bucket och mappsökvägen (eller sökvägen till jokertecken) för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om fil namnet har ett jokertecken eller detta escape-tecken i.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Yes |
| ALTERNATIV 4: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera, en fil per rad, som är den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen.<br/>När du använder det här alternativet ska du inte ange ett fil namn i data uppsättningen. Se fler exempel i [fil List exempel](#file-list-examples). |No |
| ***Ytterligare inställningar:*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när **rekursivt** har angetts till **True** och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är **True** (standard) och **false**.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |No |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt fil kopierings scenario. Standardvärdet: false. |No |
| modifiedDatetimeStart    | Filerna filtreras baserat på attributet: senast ändrad. <br>Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på en UTC-tidszon i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null** väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | No                                                          |
| enablePartitionDiscovery | För filer som är partitionerade anger du om du vill parsa partitionerna från fil Sök vägen och lägga till dem som ytterligare käll kolumner.<br/>Tillåtna värden är **false** (standard) och **True**. | No                                            |
| partitionRootPath | När partitions identifiering har Aktiver ATS anger du den absoluta rot Sök vägen för att kunna läsa partitionerade mappar som data kolumner.<br/><br/>Om den inte anges, som standard,<br/>– När du använder fil Sök vägen i data uppsättningen eller en lista med filer på källan, är partitionens rot Sök väg den sökväg som kon figurer ATS i data uppsättningen.<br/>– När du använder mapp-filter med jokertecken är partitionens rot Sök väg den underordnade sökvägen före det första jokertecknet.<br/>– När du använder prefix är partitionens rot Sök väg under Sök väg före den sista "/". <br/><br/>Anta till exempel att du konfigurerar sökvägen i dataset som "rot/mapp/år = 2020/månad = 08/Day = 27":<br/>– Om du anger partitionens rot Sök väg som "rot/mapp/år = 2020" genererar kopierings aktiviteten två kolumner `month` och `day` värdet "08" respektive "27", förutom kolumnerna inuti filerna.<br/>-Om partitionens rot Sök väg inte anges genereras ingen extra kolumn. | No                                            |
| maxConcurrentConnections |Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| No                                                          |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

I det här avsnittet beskrivs det resulterande beteendet för att använda en fil List Sök väg i en kopierings aktivitets källa.

Antag att du har följande mappstruktur och vill kopiera filerna i fetstil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | Data Factory konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättning:**<br>Bucket `bucket`<br>– Mappsökväg: `FolderA`<br><br>**I kopierings aktivitets Källa:**<br>– Sökväg till fil lista: `bucket/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera, en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen. |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3 till Azure Data Lake Storage Gen2 eller Azure Blob Storage kan du välja att bevara filens metadata tillsammans med data. Läs mer om att [bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktiviteten](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande som är för bakåtkompatibilitet. Vi rekommenderar att du använder den nya modellen som beskrivs ovan. Redigerings gränssnittet för Data Factory har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **AmazonS3Object**. |Yes |
| bucketName | S3-Bucket-namn. Wildcard-filtret stöds inte. |Ja för kopierings-eller söknings aktiviteten, nej för GetMetadata-aktiviteten |
| key | Namnet eller wildcard-filtret för S3-objekt nyckeln under den angivna Bucket. Gäller endast när egenskapen **prefix** inte anges. <br/><br/>Wildcard-filtret stöds för både del-och fil namns delen. Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exempel 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). Används `^` för att kringgå om din faktiska mapp eller fil namn har ett jokertecken eller detta escape-tecken inuti. |No |
| protokollprefixet | Prefix för S3-objekt nyckeln. Objekt vars nycklar börjar med det här prefixet väljs. Gäller endast när **nyckel** egenskapen inte har angetts. |No |
| version | Versionen av S3-objektet, om S3-versioner är aktive rad. Om en version inte anges kommer den senaste versionen att hämtas. |No |
| modifiedDatetimeStart | Filerna filtreras baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att om du aktiverar den här inställningen kommer den övergripande prestandan för data förflyttning att påverkas när du vill filtrera enorma mängder filer. <br/><br/> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null** väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är null, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.| No |
| modifiedDatetimeEnd | Filerna filtreras baserat på attributet: senast ändrad. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Tänk på att om du aktiverar den här inställningen kommer den övergripande prestandan för data förflyttning att påverkas när du vill filtrera enorma mängder filer. <br/><br/> Egenskaperna kan vara **Null**, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är **Null** väljs de filer vars senast ändrade attribut är större än eller lika med värdet för DateTime.  När `modifiedDatetimeEnd` har ett datetime-värde `modifiedDatetimeStart` , men är **Null**, väljs filerna vars senast ändrade attribut är mindre än värdet för DateTime.| No |
| format | Om du vill kopiera filer som är mellan filbaserade arkiv (binär kopia) hoppar du över avsnittet format i definitionerna för både indata och utdata.<br/><br/>Om du vill parsa eller generera filer med ett speciellt format stöds följande fil format **typer: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**.<br/>De nivåer som stöds är **optimala** och **snabbaste**. |No |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du **bucketName** för Bucket och **prefixet** för delen av mappen.
>
>Om du vill kopiera en enskild fil med ett visst namn anger du **bucketName** för Bucket och **nyckeln** för mappen del plus fil namn.
>
>Om du vill kopiera en delmängd av filer under en mapp anger du **bucketName** för Bucket och **nyckeln** för mappen del plus filter för jokertecken.

**Exempel: använda prefix**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exempel: använda nyckel och version (valfritt)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-source-model-for-the-copy-activity"></a>Äldre käll modell för kopierings aktiviteten

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **FileSystemSource**. |Yes |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när **rekursivt** har angetts till **True** och sinken är en filbaserad lagring, kommer en tom mapp eller undermapp inte att kopieras eller skapas i mottagaren.<br/>Tillåtna värden är **True** (standard) och **false**. | No |
| maxConcurrentConnections |Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över data lager som kopierings aktiviteten i Azure Data Factory stöder som källor och mottagare finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
