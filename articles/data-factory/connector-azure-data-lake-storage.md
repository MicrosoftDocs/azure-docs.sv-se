---
title: Kopiera och transformera data i Azure Data Lake Storage Gen2
description: Lär dig hur du kopierar data till och från Azure Data Lake Storage Gen2 och hur du omvandlar data i Azure Data Lake Storage Gen2 genom att använda Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: 9eed71f5a264a6308a49ac594563c746bb8ea5b7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544782"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiera och transformera data i Azure Data Lake Storage Gen2 med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) är en uppsättning funktioner som är avsedda för stor data analys som är inbyggda i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Med den här lösningen kan du interagera med data med hjälp av både filsystem- och objektlagringsparadigm.

I den här artikeln beskrivs hur du använder kopieringsaktivitet i Azure Data Factory till att kopiera data från och till Azure Data Lake Storage Gen2 samt hur du använder dataflöden till att transformera data i Azure Data Lake Storage Gen2. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

>[!TIP]
>För migrering av data Lake eller Data Warehouse kan du läsa mer i [använda Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Data Lake Storage Gen2 anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

För kopierings aktivitet kan du med den här anslutningen:

- Kopiera data från/till Azure Data Lake Storage Gen2 genom att använda konto nyckel, tjänstens huvud namn eller hanterade identiteter för Azure-resursers autentiseringar.
- Kopiera filer som-är eller parsa eller generera filer med [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).
- [Bevara filens metadata under kopieringen](#preserve-metadata-during-copy).
- [Bevara ACL: er](#preserve-acls) vid kopiering från Azure Data Lake Storage gen1-/Gen2.

>[!IMPORTANT]
>Om du aktiverar alternativet **Tillåt betrodda Microsoft-tjänster för att komma åt det här lagrings kontot** på Azure Storage brand Väggs inställningar och vill använda Azure integration runtime för att ansluta till din data Lake Storage Gen2 måste du använda [hanterad identitets autentisering](#managed-identity) för ADLS Gen2.


## <a name="get-started"></a>Kom igång

>[!TIP]
>En genom gång av hur du använder Data Lake Storage Gen2-anslutningen finns i [läsa in data i Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Azure Data Lake Storage Gen2-anslutningen har stöd för följande typer av autentisering. Mer information finns i motsvarande avsnitt:

- [Autentisering av konto nyckel](#account-key-authentication)
- [Autentisering av tjänstens huvud namn](#service-principal-authentication)
- [Hanterade identiteter för Azure-resurser-autentisering](#managed-identity)

>[!NOTE]
>När du använder PolyBase för att läsa in data i SQL Data Warehouse måste du använda hanterad identitetsautentisering som krävs för PolyBase om din käll Data Lake Storage Gen2 har kon figurer ATS med Virtual Network-slutpunkt. Se avsnittet [hanterad identitets autentisering](#managed-identity) med fler konfigurations krav.

### <a name="account-key-authentication"></a>Autentisering av konto nyckel

Följande egenskaper stöds för att använda autentisering av lagrings konto nycklar:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Yes |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net` . | Yes |
| accountKey | Konto nyckel för Data Lake Storage Gen2. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om den här egenskapen inte anges används standard körningen av Azure integration. |No |

>[!NOTE]
>Sekundär slut punkt för fil system för ADLS stöds inte när du använder autentisering med konto nycklar. Du kan använda andra typer av autentisering.

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda autentisering av tjänstens huvud namn.

1. Registrera en program enhet i Azure Active Directory (Azure AD) genom att följa stegen i [Registrera ditt program med en Azure AD-klient](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

    - Program-ID
    - Program nyckel
    - Klientorganisations-ID

2. Ge tjänstens huvud behörighet rätt behörighet. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Som källa**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du använder Data Factory gränssnittet för att redigera och tjänstens huvud namn inte är inställt med rollen "Storage BLOB data Reader/Contributor" i IAM, när du gör en test anslutning eller bläddrar/navigerar mappar, väljer du "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" och anger en sökväg med behörigheten **läsa + kör** för att fortsätta.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Yes |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net` . | Yes |
| servicePrincipalId | Ange programmets klient-ID. | Yes |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en `SecureString` för att lagra det på ett säkert sätt i Data Factory. Eller så kan du [referera till en hemlighet som lagrats i Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Yes |
| azureCloudType | För tjänstens huvud namns autentisering anger du vilken typ av Azure-moln miljö som Azure Active Directory programmet ska registreras i. <br/> Tillåtna värden är **AzurePublic**, **AzureChina**, **azureusgovernment eller**och **AzureGermany**. Som standard används data fabrikens moln miljö. | No |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |No |

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md)som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten direkt för Data Lake Storage Gen2 autentisering, på samma sätt som du använder ditt eget tjänst huvud namn. Det gör att den här fabriken kan komma åt och kopiera data till eller från din Data Lake Storage Gen2.

Följ dessa steg om du vill använda hanterade identiteter för Azure Resource Authentication.

1. [Hämta data Factory hanterad identitets information](data-factory-service-identity.md#retrieve-managed-identity) genom att kopiera värdet för det **hanterade ID-objekt-ID: t** som genererats tillsammans med din fabrik.

2. Ge behörigheten hanterad identitet korrekt. Se exempel på hur behörigheten fungerar i Data Lake Storage Gen2 från [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Som källa**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Läs** behörighet för de filer som ska kopieras. I åtkomst kontroll (IAM) beviljar du minst rollen **Storage BLOB data Reader** .
    - **Som mottagare**: i Storage Explorer beviljar du minst **Kör** behörighet för alla överordnade mappar och fil systemet, tillsammans med **Skriv** behörighet för mappen mottagare. Alternativt, i åtkomst kontroll (IAM), beviljar du minst rollen **Storage BLOB data Contributor** .

>[!NOTE]
>Om du använder Data Factory användar gränssnitt för att redigera och den hanterade identiteten inte har angetts med rollen "Storage BLOB data Reader/Contributor" i IAM, vid test anslutning eller bläddring/navigera mappar, väljer du "Testa anslutning till fil Sök väg" eller "Bläddra från angiven sökväg" och anger en sökväg med behörigheten **läsa + kör** för att fortsätta.

>[!IMPORTANT]
>Om du använder PolyBase för att läsa in data från Data Lake Storage Gen2 till SQL Data Warehouse, ska du, när du använder hanterad identitetsautentisering för Data Lake Storage Gen2, kontrol lera att du också följer steg 1 och 2 i [den här vägledningen](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) till 1) registrera dig med Azure Active Directory (Azure AD) och 2) tilldela rollen Storage BLOB data Contributor till servern. resten hanteras av Data Factory. Om din Data Lake Storage Gen2 har kon figurer ATS med en Azure Virtual Network-slutpunkt, för att använda PolyBase för att läsa in data från den, måste du använda hanterad identitetsautentisering som krävs av PolyBase.

De här egenskaperna stöds för den länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **AzureBlobFS**. |Yes |
| url | Slut punkt för Data Lake Storage Gen2 med mönstret för `https://<accountname>.dfs.core.windows.net` . | Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure integration runtime eller en lokal integration Runtime om ditt data lager finns i ett privat nätverk. Om inget värde anges används standard Azure integration Runtime. |No |

**Exempel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för Data Lake Storage Gen2 under `location` Inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen Type under `location` i data uppsättningen måste anges till **AzureBlobFSLocation**. | Yes      |
| Fil Systems | Namnet på filen med Data Lake Storage Gen2-systemet.                              | No       |
| folderPath | Sökvägen till en mapp under det angivna fil systemet. Om du vill använda ett jokertecken för att filtrera mappar, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | No       |
| fileName   | Fil namnet under angivet fileSystem + folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger den i aktivitets källans inställningar. | No       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Kopiera aktivitets konfigurationer](copy-activity-overview.md#configuration) och [pipeliner och aktiviteter](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av Data Lake Storage Gen2 källa och mottagare.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 som typ av källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Det finns flera alternativ för att kopiera data från ADLS Gen2:

- Kopiera från den angivna sökvägen som anges i data uppsättningen.
- Wildcard-filter mot mappsökväg eller fil namn, se `wildcardFolderPath` och `wildcardFileName` .
- Kopiera filerna som definieras i en viss textfil som fil uppsättning, se `fileListPath` .

Följande egenskaper stöds för Data Lake Storage Gen2 under `storeSettings` Inställningar i format-baserad kopierings Källa:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **AzureBlobFSReadSettings**. | Yes                                           |
| ***Leta upp de filer som ska kopieras:*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från det angivna fil systemet eller mappen/fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla filer från ett fil system/en mapp, anger du också `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken under det angivna fil systemet som kon figurer ATS i data uppsättningen för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). Använd `^` om du vill använda Escape om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. <br>Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | No                                            |
| ALTERNATIV 2: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under angivet fil system + folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller enstaka tecken). Använd `^` om du vill använda Escape om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti.  Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Yes |
| ALTERNATIV 3: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera, en fil per rad, som är den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen.<br/>När du använder det här alternativet ska du inte ange fil namn i data uppsättning. Se fler exempel i [fil List exempel](#file-list-examples). |No |
| ***Ytterligare inställningar:*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Observera att när rekursivt har angetts till true och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är **True** (standard) och **false**.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |No |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt kopierings scenario där data källor är BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, File, Azure File, SFTP eller FTP. Standardvärdet: false. |No |
| modifiedDatetimeStart    | Filter för filer baserat på attributet: senast ändrad. <br>Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen.  När `modifiedDatetimeStart` har datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime väljs.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime väljs.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | No                                            |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till lagrings lager samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | No                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 som typ av mottagare

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Följande egenskaper stöds för Data Lake Storage Gen2 under `storeSettings` Inställningar i format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Typ egenskapen under `storeSettings` måste anges till **AzureBlobFSWriteSettings**. | Yes      |
| copyBehavior             | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | No       |
| blockSizeInMB | Ange den block storlek i MB som används för att skriva data till ADLS Gen2. Läs mer [om block-blobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Det tillåtna värdet är **mellan 4 MB och 100 MB**. <br/>Som standard fastställer ADF automatiskt block storleken baserat på källans lagrings typ och data. För icke-binär kopia i ADLS Gen2 är standard block storleken 100 MB så att den passar i högst 4,95 TB data. Det kanske inte är optimalt om dina data inte är stora, särskilt när du använder självbetjänings Integration Runtime med dåligt nätverk som resulterar i drift tids gräns eller prestanda problem. Du kan uttryckligen ange en block storlek, men se till att blockSizeInMB * 50000 är tillräckligt stor för att lagra data, annars Miss kopie ras kopierings aktivitets körningen. | No |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | No       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det resulterande beteendet hos mappsökvägen och fil namnet med filter för jokertecken.

| folderPath | fileName | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Tom, Använd standard) | falskt | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Tom, Använd standard) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | falskt | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I det här avsnittet beskrivs det resulterande beteendet för att använda fil List Sök vägen i kopierings aktivitets källan.

Vi antar att du har följande mappstruktur och vill kopiera filerna i fetstil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | ADF-konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| fil Systems<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättning:**<br>-Fil system: `filesystem`<br>– Mappsökväg: `FolderA`<br><br>**I kopierings aktivitets Källa:**<br>– Sökväg till fil lista: `filesystem/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera, en fil per rad med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen. |


### <a name="some-recursive-and-copybehavior-examples"></a>Några rekursiva och copyBehavior-exempel

I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| rekursiva | copyBehavior | Källans mappstruktur | Resulterande mål |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2 + File3 + File4 + File5-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. |
| falskt |preserveHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| falskt |flattenHierarchy | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |
| falskt |mergeFiles | Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn. automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 har inte hämtats. |

## <a name="preserve-metadata-during-copy"></a>Bevara metadata under kopiering

När du kopierar filer från Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 till Azure Data Lake Storage Gen2/Azure-Blob kan du välja att bevara filens metadata tillsammans med data. Läs mer om att [bevara metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Bevara ACL: er från Data Lake Storage Gen1/Gen2

När du kopierar filer från Azure Data Lake Storage Gen1-Gen2 till Gen2 kan du välja att behålla POSIX-åtkomst kontrol listor (ACL: er) tillsammans med data. Läs mer [om att bevara ACL: er från data Lake Storage gen1/Gen2 till Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 i allmänhet finns i [Kopiera data från Azure Data Lake Storage gen1 till Gen2 med Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) för en genom gång och bästa praxis.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flöden kan du läsa och skriva filer från Azure Data Lake Storage Gen2 i följande format:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common data Model (för hands version)](format-common-data-model.md#mapping-data-flow-properties)
* [Avgränsad text](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Formatera vissa inställningar finns i dokumentationen för det formatet. Mer information finns i [käll omvandling i konvertering av data flöde](data-flow-source.md) och [omvandling av mottagare i data flödet](data-flow-sink.md).

### <a name="source-transformation"></a>Käll omvandling

I käll omvandlingen kan du läsa från en behållare, mapp eller en enskild fil i Azure Data Lake Storage Gen2. På fliken **käll alternativ** kan du hantera hur filerna får läsas. 

![Käll alternativ](media/data-flow/sourceOptions1.png "Käll alternativ")

**Sökväg till jokertecken:** Genom att använda jokertecken instrueras ADF att loopa igenom varje matchande mapp och fil i en enda käll omvandling. Detta är ett effektivt sätt att bearbeta flera filer i ett enda flöde. Lägg till flera matchnings mönster för jokertecken med +-tecknet som visas när du hovrar över ditt befintliga mönster i jokertecken.

Från din käll behållare väljer du en serie filer som matchar ett mönster. Det går endast att ange container i data uppsättningen. Sökvägen till jokertecken måste därför även innehålla sökvägen till din mapp från rotmappen.

Jokertecken exempel:

* ```*``` Representerar en uppsättning tecken
* ```**``` Representerar rekursiv katalog kapsling
* ```?``` Ersätter ett Character
* ```[]``` Matchar ett eller flera tecken inom hakparenteserna

* ```/data/sales/**/*.csv``` Hämtar alla CSV-filer under/data/Sales
* ```/data/sales/20??/**/``` Hämtar alla filer i 20-talet
* ```/data/sales/*/*/*.csv``` Hämtar CSV-filer två nivåer under/data/Sales
* ```/data/sales/2004/*/12/[XY]1?.csv``` Hämtar alla CSV-filer i 2004 i december från och med X eller Y som föregås av ett tvåsiffrigt tal

**Partitionens rot Sök väg:** Om du har partitionerade mappar i fil källan med ett ```key=value``` format (till exempel Year = 2019) kan du tilldela den översta nivån i det partitionens mappträd till ett kolumn namn i data flödet för data flödet.

Ange först ett jokertecken för att inkludera alla sökvägar som är de partitionerade mapparna plus de löv-filer som du vill läsa.

![Inställningar för partitionens källfil](media/data-flow/partfile2.png "Fil inställning för partition")

Använd inställningen för partitionens rot Sök väg för att definiera vad mappstrukturen är. När du visar innehållet i dina data via en data förhands granskning kommer du att se att ADF lägger till de matchade partitioner som finns i alla mappar.

![Partitionens rot Sök väg](media/data-flow/partfile1.png "För hands version av partitionens rot Sök väg")

**Lista över filer:** Detta är en fil uppsättning. Skapa en textfil som innehåller en lista över relativa Sök vägs filer som ska bearbetas. Peka på den här text filen.

**Kolumn att lagra fil namn på:** Lagra namnet på käll filen i en kolumn i dina data. Ange ett nytt kolumn namn här för att lagra fil namn strängen.

**Efter slut för ande:** Välj att inte göra något med käll filen när data flödet körts, ta bort käll filen eller flytta käll filen. Sök vägarna för flytten är relativa.

Om du vill flytta källfilerna till en annan plats efter bearbetning väljer du först flytta för fil åtgärd. Ange sedan "från"-katalogen. Om du inte använder jokertecken för sökvägen, kommer inställningen från att vara samma mapp som källmappen.

Om du har en käll Sök väg med jokertecken så ser din syntax ut så här:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som har ursprung under/data/Sales till/backup/priorSales.

> [!NOTE]
> Fil åtgärder körs bara när du startar data flödet från en pipeline-körning (en pipeline för pipeline-fel eller körning) som använder aktiviteten kör data flöde i en pipeline. Fil åtgärder körs *inte* i fel söknings läge för data flöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datum intervall när de senast ändrades. Alla datum-gånger är i UTC-tid. 

### <a name="sink-properties"></a>Egenskaper för mottagare

I omvandling av mottagare kan du skriva till antingen en behållare eller mapp i Azure Data Lake Storage Gen2. på fliken **Inställningar** kan du hantera hur filerna skrivs.

![mottagar alternativ](media/data-flow/file-sink-settings.png "mottagar alternativ")

**Rensa mappen:** Anger om målmappen ska rensas innan data skrivs.

**Fil namns alternativ:** Anger hur målfiler namnges i målmappen. Fil namns alternativen är:
   * **Standard**: Tillåt Spark att namnge filer baserat på del standarder.
   * **Mönster**: Ange ett mönster som räknar upp dina utdatafiler per partition. Till exempel kommer **lån [n]. csv** att skapa loans1.csv, loans2.csv och så vidare.
   * **Per partition**: Ange ett fil namn per partition.
   * **Som data i kolumnen**: Ange utdatafilen till värdet för en kolumn. Sökvägen är relativ i förhållande till data uppsättnings behållaren, inte målmappen. Om du har en mappsökväg i din data uppsättning kommer den att åsidosättas.
   * **Utdata till en enda fil**: kombinera de partitionerade utdatafilerna till en enda namngiven fil. Sökvägen är relativ i förhållande till dataset-mappen. Tänk på att det kan hända att te-sammanfognings åtgärder inte fungerar baserat på Node-storlek. Det här alternativet rekommenderas inte för stora data uppsättningar.

**Offert alla:** Anger om alla värden ska anges i citat tecken

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Om du vill veta mer om egenskaperna kontrollerar du [ta bort aktivitet](delete-activity.md)

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande för bakåtkompatibilitet. Du rekommenderar att du använder den nya modellen som anges ovan och fortsätter, och redigerings gränssnittet för ADF har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **AzureBlobFSFile**. |Yes |
| folderPath | Sökväg till mappen i Data Lake Storage Gen2. Om detta inte anges pekar den på roten. <br/><br/>Wildcard-filtret stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller om detta escape-tecken finns inuti. <br/><br/>Exempel: filesystem/Folder/. Se fler exempel i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). |No |
| fileName | Namn eller Wildcard-filter för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna jokertecken `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska fil namnet har ett jokertecken eller om detta escape-tecken är inuti.<br/><br/>När fil namnet inte har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren genererar kopierings aktiviteten automatiskt fil namnet med följande mönster: "*data. [ aktivitetens körnings-ID GUID]. [GUID om FlattenHierarchy]. [format om det är konfigurerat]. [komprimering om konfigurerad]*", till exempel" Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz ". Om du kopierar från en tabell källa med ett tabell namn i stället för en fråga, är namn mönstret "*[tabell namn]. [ format]. [komprimering om konfigurerad]*", till exempel" MyTable.csv ". |No |
| modifiedDatetimeStart | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| modifiedDatetimeEnd | Filter för filer baserat på det senast ändrade attributet. Filerna väljs om deras senaste ändrings tid ligger inom tidsintervallet mellan `modifiedDatetimeStart` och `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet "2018-12-01T05:00:00Z". <br/><br/> Den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill göra fil filter till stora mängder filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget attribut filter används för data uppsättningen. När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade. När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| format | Om du vill kopiera filer som är mellan filbaserade butiker (binär kopia) hoppar du över avsnittet format i både indata och utdata-datauppsättnings definitionerna.<br/><br/>Om du vill parsa eller generera filer med ett särskilt format stöds följande fil format **typer: text**format, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är **gzip**, **DEFLATE**, **BZip2**och **ZipDeflate**.<br/>De nivåer som stöds är **optimala** och **snabbaste**. |No |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enskild fil med ett visst namn anger du **folderPath** med en mapp **och ett fil namn** med ett fil namn.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namn** med ett Wildcard-filter. 

**Exempel:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Käll modell för äldre kopierings aktiviteter

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **AzureBlobFSSource**. |Yes |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren.<br/>Tillåtna värden är **True** (standard) och **false**. | No |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Legacy kopierings aktivitet, mottagar modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **AzureBlobFSSink**. |Yes |
| copyBehavior | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | No |
| maxConcurrentConnections | Antalet anslutningar för att ansluta till data lagret samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till data lagret. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
