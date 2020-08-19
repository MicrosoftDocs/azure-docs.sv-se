---
title: Kopiera data från och till SFTP-server
description: Lär dig hur du kopierar data från och till SFTP-servern med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/18/2020
ms.openlocfilehash: 8ec950ddabd3844618c878471d2e1391979e2056
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88521381"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Kopiera data från och till SFTP-servern med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av tjänsten Data Factory som du använder:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Aktuell version](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från och till servern för säker FTP (SFTP). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

SFTP-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder SFTP-anslutningen:

- Kopiera filer från och till SFTP-servern med *Basic* -eller *SshPublicKey* -autentisering.
- Kopiera filer som de är eller genom att parsa eller generera filer med de [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SFTP.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade SFTP-tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till *SFTP*. |Yes |
| värd | Namnet eller IP-adressen för SFTP-servern. |Yes |
| port | Porten som SFTP-servern lyssnar på.<br/>Det tillåtna värdet är ett heltal och standardvärdet är *22*. |No |
| skipHostKeyValidation | Ange om du vill hoppa över validering av värd nycklar.<br/>Tillåtna värden är *True* och *false* (standard).  | No |
| hostKeyFingerprint | Ange finger avtrycket för värd nyckeln. | Ja, om "skipHostKeyValidation" är inställt på false.  |
| authenticationType | Ange autentiseringstyp.<br/>Tillåtna värden är *Basic* -och *SshPublicKey*. Mer egenskaper finns i avsnittet [använda grundläggande autentisering](#use-basic-authentication) . JSON-exempel finns i avsnittet [använda autentisering med offentlig SSH-nyckel](#use-ssh-public-key-authentication) . |Yes |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Mer information finns i avsnittet [krav](#prerequisites) . Om integration runtime inte har angetts använder tjänsten standard Azure Integration Runtime. |No |

### <a name="use-basic-authentication"></a>Använd grundläggande autentisering

Om du vill använda grundläggande autentisering ställer du in egenskapen *authenticationType* på *Basic*och anger följande egenskaper utöver de allmänna egenskaperna för SFTP-anslutningen som introducerades i föregående avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användaren som har åtkomst till SFTP-servern. |Yes |
| password | Användarens lösen ord (användar namn). Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i din data fabrik eller [referera till en hemlighet som lagras i ett Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Exempel:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Använd autentisering med offentlig SSH-nyckel

Om du vill använda autentisering med offentlig SSH-nyckel anger du egenskapen "authenticationType" som **SshPublicKey**och anger följande egenskaper, förutom de allmänna SFTP-anslutningarna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användaren som har åtkomst till SFTP-servern. |Yes |
| privateKeyPath | Ange den absoluta sökvägen till den privata nyckel filen som integration Runtime har åtkomst till. Detta gäller endast när den egen värdbaserade typen av integration runtime anges i "connectVia". | Ange antingen `privateKeyPath` eller `privateKeyContent` .  |
| privateKeyContent | Base64-kodat innehåll i SSH-privat nyckel. Den privata SSH-nyckeln måste vara OpenSSH-format. Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i din data fabrik eller [referera till en hemlighet som lagras i ett Azure Key Vault](store-credentials-in-key-vault.md). | Ange antingen `privateKeyPath` eller `privateKeyContent` . |
| Fraser | Ange pass frasen eller lösen ordet för att dekryptera den privata nyckeln om nyckel filen skyddas av en pass fras. Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i din data fabrik eller [referera till en hemlighet som lagras i ett Azure Key Vault](store-credentials-in-key-vault.md). | Ja, om den privata nyckel filen skyddas av en pass fras. |

> [!NOTE]
> SFTP-anslutningen har stöd för en RSA/DSA-OpenSSH-nyckel. Kontrol lera att innehållet i nyckel filen börjar med "-----BEGIN [RSA/DSA] privat nyckel-----". Om den privata nyckel filen är en PPK-fil, använder du verktyget SparaTillFil för att konvertera från PPK till OpenSSH-format. 

**Exempel 1: SshPublicKey-autentisering med privat nyckel-sökväg**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: SshPublicKey-autentisering med privat nyckel innehåll**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för SFTP under `location` Inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | *Typ* egenskapen under `location` i data mängden måste anges till *SftpLocation*. | Yes      |
| folderPath | Sökvägen till mappen. Om du vill använda ett jokertecken för att filtrera mappen, hoppar du över den här inställningen och anger sökvägen i aktivitetens käll inställningar. | No       |
| fileName   | Fil namnet under den angivna folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger fil namnet i inställningarna för aktivitets källan. | No       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SFTP-källan.

### <a name="sftp-as-source"></a>SFTP som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för SFTP under `storeSettings` inställningarna i den formatbaserade kopierings källan:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | *Typ* egenskapen under `storeSettings` måste anges till *SftpReadSettings*. | Yes                                           |
| ***Leta upp de filer som ska kopieras*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från den mapp-/fil Sök väg som anges i data uppsättningen. Om du vill kopiera alla filer från en mapp anger du även `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inte noll eller ett enskilt tecken). används `^` för att undanta om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken inuti. <br>Fler exempel finns i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | No                                            |
| ALTERNATIV 2: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den angivna folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inte noll eller ett enskilt tecken). Använd `^` för att undanta om det faktiska mappnamnet har jokertecken eller det här Escape-tecknet inuti.  Fler exempel finns i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Yes |
| ALTERNATIV 3: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera (en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen).<br/>När du använder det här alternativet ska du inte ange fil namnet i data uppsättningen. Fler exempel finns i [exempel på fil lista](#file-list-examples). |No |
| ***Ytterligare inställningar*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på True och sinken är en filbaserad lagring, kopieras inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är *True* (standard) och *false*.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |No |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt kopierings scenario där data källor är BLOB, ADLS Gen1, ADLS Gen2, S3, Google Cloud Storage, File, Azure File, SFTP eller FTP. Standardvärdet: false. |No |
| modifiedDatetimeStart    | Filerna filtreras baserat på det *senast ändrade*attributet. <br>Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Samma som ovan.                                               | No                                            |
| maxConcurrentConnections | Antalet anslutningar som kan ansluta till lagrings lagret samtidigt. Ange bara ett värde om du vill begränsa den samtidiga anslutningen till data lagret. | No                                            |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-a-sink"></a>SFTP som mottagare

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Följande egenskaper stöds för SFTP under `storeSettings` Inställningar i en format-baserad kopierings mottagare:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | *Typ* egenskapen under `storeSettings` måste anges till *SftpWriteSettings*. | Yes      |
| copyBehavior             | Definierar kopierings beteendet när källan är filer från ett filbaserat data lager.<br/><br/>Tillåtna värden är:<br/><b>-PreserveHierarchy (standard)</b>: bevarar filens hierarki i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><b>-FlattenHierarchy</b>: alla filer från källmappen är på den första nivån i målmappen. Filerna har automatiskt genererade namn. <br/><b>-MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. | No       |
| maxConcurrentConnections | Antalet anslutningar som kan ansluta till lagrings lagret samtidigt. Ange bara ett värde om du vill begränsa den samtidiga anslutningen till data lagret. | No       |
| useTempFileRename | Ange om du vill överföra till temporära filer och byta namn på dem eller Skriv direkt till målmappen eller filens plats. Som standard skriver Azure Data Factory först till temporära filer och byter sedan namn på dem när överföringen är färdig. Den här sekvensen hjälper till (1) att undvika konflikter som kan resultera i en skadad fil om du har andra processer som skriver till samma fil och (2) kontrol lera att den ursprungliga versionen av filen finns under överföringen. Om SFTP-servern inte stöder en namnbytes åtgärd inaktiverar du det här alternativet och kontrollerar att du inte har en samtidig skrivning till målfilen. Mer information finns i fel söknings tipset i slutet av den här tabellen. | Nej. Standardvärdet är *True*. |
| operationTimeout | Vänte tiden innan varje skrivbegäran till SFTP-servern orsakar timeout. Standardvärdet är 60 min (01:00:00).|No |

>[!TIP]
>Om du får fel meddelandet "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" eller "SftpOperationFail" när du skriver data till SFTP, och den SFTP-användare som du använder har rätt behörigheter, kontrollerar du om din SFTP- *Server stöder fil* namnbytes åtgärden. Om den inte är det inaktiverar du alternativet **Ladda upp med temporär fil** ( `useTempFileRename` ) och försöker igen. Mer information om den här egenskapen finns i tabellen ovan. Om du använder en lokal integration runtime för kopierings aktiviteten, måste du använda version 4,6 eller senare.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exempel på mapp-och fil filter

I det här avsnittet beskrivs det beteende som beror på att jokertecken används med mappsökvägar och fil namn.

| folderPath | fileName | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (tom, Använd standard) | falskt | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (tom, Använd standard) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | falskt | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I den här tabellen beskrivs det beteende som är resultatet av att använda en fil List Sök väg i kopierings aktivitets källan. Det förutsätter att du har följande mappstruktur för en källa och vill kopiera filerna i fet stil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | Azure Data Factory konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| skogen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättningen:**<br>– Mappsökväg: `root/FolderA`<br><br>**I kopierings aktivitets källan:**<br>– Sökväg till fil lista: `root/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera (en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen). |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Information om egenskaper för söknings aktiviteter finns [i lookup-aktivitet i Azure Data Factory](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Information om GetMetadata-aktivitets egenskaper finns [i getMetaData-aktivitet i Azure Data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Information om hur du tar bort aktivitets egenskaper finns i [ta bort aktivitet i Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande som är för bakåtkompatibilitet. Vi rekommenderar att du använder den tidigare diskuterade nya modellen eftersom Azure Data Factory redigerings gränssnittet har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens *typ* -egenskap måste anges till *fileshare*. |Yes |
| folderPath | Sökvägen till mappen. Ett Wildcard-filter stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inte noll eller ett enskilt tecken). används `^` för att undanta om det faktiska fil namnet har ett jokertecken eller detta escape-tecken inuti. <br/><br/>Exempel: RootFolder/undermapp/, se fler exempel i [mapp-och fil filter exempel](#folder-and-file-filter-examples). |Yes |
| fileName |  **Namn eller Wildcard-filter** för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna jokertecken `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska mappnamnet har jokertecken eller detta escape-tecken inuti. |No |
| modifiedDatetimeStart | Filerna filtreras baserat på det *senast ändrade*attributet. Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från ett stort antal filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| modifiedDatetimeEnd | Filerna filtreras baserat på det *senast ändrade*attributet. Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br/><br/> Den övergripande prestandan för data förflyttning påverkas genom att aktivera den här inställningen när du vill göra fil filter från ett stort antal filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| format | Om du vill kopiera filer som är mellan filbaserade arkiv (binär kopia) hoppar du över avsnittet format i definitionerna för både indata och utdata.<br/><br/>Om du vill parsa filer med ett särskilt format stöds följande fil format typer: *Autoformat*, *JsonFormat*, *AvroFormat*, *OrcFormat*och *ParquetFormat*. Ange egenskapen *Type* under format till något av dessa värden. Mer information finns i [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), Orc- [format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) -avsnitt. |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Typer som stöds är *gzip*, *DEFLATE*, *BZip2*och *ZipDeflate*.<br/>De nivåer som stöds är *optimala* och *snabbaste*. |No |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast *folderPath* .<br>Om du vill kopiera en enskild fil med ett angivet namn, anger du *folderPath* med mappnamnet *och fil namnet med* fil namnet.<br>Om du vill kopiera en delmängd av filerna under en mapp, anger du *folderPath* med mappen del och *filename* med wildcard-filtret.

>[!NOTE]
>Om du använder egenskapen *fileFilter* för fil filtret, stöds den fortfarande, men vi rekommenderar att du använder den nya filter funktionen som har lagts till i *fil namnet* från nu.

**Exempel:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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
| typ | *Typ* egenskapen för kopierings aktivitets källan måste anges till *FileSystemSource* |Yes |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på *True* och sinken är ett filbaserat Arkiv, kopieras inte tomma mappar och undermappar till mottagaren.<br/>Tillåtna värden är *Sant* (standard) och *falskt* | No |
| maxConcurrentConnections | Antalet anslutningar som kan ansluta till ett lagrings lager samtidigt. Ange bara ett nummer när du vill begränsa samtidiga anslutningar till data lagret. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
