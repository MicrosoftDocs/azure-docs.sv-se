---
title: Kopiera data från HDFS med Azure Data Factory
description: Lär dig hur du kopierar data från en moln-eller lokal HDFS-källa till mottagar data lager med stöd för kopierings aktivitet i en Azure Data Factory pipeline.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: 9c274bdfb5854529dbb82bd2d8b7cefdf07390b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588910"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Kopiera data från HDFS-servern med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av tjänsten Data Factory som du använder:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Aktuell version](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kopierar data från Hadoop Distributed File System-servern (HDFS). Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

HDFS-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-och mottagar mat ris som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [Ta bort aktivitet](delete-activity.md)

Mer specifikt stöder HDFS-anslutningen:

- Kopiera filer med hjälp av *Windows* (Kerberos) eller *Anonym* autentisering.
- Kopiera filer med hjälp av *webhdfs* -protokollet eller *inbyggt DistCp* -stöd.
- Kopiera filer som de är eller genom att parsa eller generera filer med de [fil format och komprimerings-codec som stöds](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Se till att integration Runtime har åtkomst till *alla* [namnserver]: [namn Node port] och [datanode-servrar]: [datanode port] för Hadoop-klustret. Standardvärdet [Name Node port] är 50070 och standardvärdet för [datanode-port] är 50075.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för HDFS.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten HDFS:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen *Type* måste anges till *HDFS*. | Yes |
| url |URL: en till HDFS |Yes |
| authenticationType | De tillåtna värdena är *anonyma* eller *Windows*. <br><br> Om du vill konfigurera din lokala miljö läser du avsnittet [använda Kerberos-autentisering för HDFS-anslutaren](#use-kerberos-authentication-for-the-hdfs-connector) . |Yes |
| userName |Användar namnet för Windows-autentisering. För Kerberos-autentisering anger du **\<username> @ \<domain> . com**. |Ja (för Windows-autentisering) |
| password |Lösen ordet för Windows-autentisering. Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i din data fabrik eller [referera till en hemlighet som lagras i ett Azure Key Vault](store-credentials-in-key-vault.md). |Ja (för Windows-autentisering) |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Mer information finns i avsnittet [krav](#prerequisites) . Om integration runtime inte har angetts använder tjänsten standard Azure Integration Runtime. |No |

**Exempel: använda anonym autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: använda Windows-autentisering**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns [i data uppsättningar i Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `location` Inställningar i den formatbaserade data uppsättningen:

| Egenskap   | Beskrivning                                                  | Krävs |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Egenskapen *Type* under `location` i data uppsättningen måste anges till *HdfsLocation*. | Yes      |
| folderPath | Sökvägen till mappen. Om du vill använda ett jokertecken för att filtrera mappen, hoppar du över den här inställningen och anger sökvägen i aktivitetens käll inställningar. | No       |
| fileName   | Fil namnet under den angivna folderPath. Om du vill använda ett jokertecken för att filtrera filer, hoppar du över den här inställningen och anger fil namnet i inställningarna för aktivitets källan. | No       |

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines och aktiviteter i Azure Data Factory](concepts-pipelines-activities.md). Det här avsnittet innehåller en lista över egenskaper som stöds av HDFS-källan.

### <a name="hdfs-as-source"></a>HDFS som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för HDFS under `storeSettings` Inställningar i den formatbaserade kopierings källan:

| Egenskap                 | Beskrivning                                                  | Krävs                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| typ                     | *Typ* egenskapen under `storeSettings` måste anges till **HdfsReadSettings**. | Yes                                           |
| ***Leta upp de filer som ska kopieras*** |  |  |
| ALTERNATIV 1: statisk sökväg<br> | Kopiera från mappen eller fil Sök vägen som anges i data uppsättningen. Om du vill kopiera alla filer från en mapp anger du även `wildcardFileName` som `*` . |  |
| ALTERNATIV 2: jokertecken<br>- wildcardFolderPath | Mappsökvägen med jokertecken för att filtrera källmappen. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken i. <br>Fler exempel finns i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | No                                            |
| ALTERNATIV 2: jokertecken<br>- wildcardFileName | Fil namnet med jokertecken under den angivna folderPath/wildcardFolderPath för att filtrera källfiler. <br>Tillåtna jokertecken är: `*` (matchar noll eller flera tecken) och `?` (matchar inget eller ett enskilt tecken). Använd `^` för att undanta om det faktiska fil namnet har ett jokertecken eller detta escape-tecken i.  Fler exempel finns i [exempel på mapp-och fil filter](#folder-and-file-filter-examples). | Yes |
| ALTERNATIV 3: en lista över filer<br>- fileListPath | Anger om du vill kopiera en angiven fil uppsättning. Peka på en textfil som innehåller en lista över filer som du vill kopiera (en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen).<br/>När du använder det här alternativet ska du inte ange fil namnet i data uppsättningen. Fler exempel finns i [exempel på fil lista](#file-list-examples). |No |
| ***Ytterligare inställningar*** |  | |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. Om `recursive` är inställt på *Sant* och mottagaren är en filbaserad lagring, kopieras eller skapas inte en tom mapp eller undermapp till mottagaren. <br>Tillåtna värden är *True* (standard) och *false*.<br>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . |No |
| deleteFilesAfterCompletion | Anger om de binära filerna kommer att tas bort från käll arkivet efter att du har flyttat till mål lagret. Filen som ska tas bort är per fil, så när kopierings aktiviteten Miss lyckas visas några filer som redan har kopierats till målet och tagits bort från källan, medan andra fortfarande är kvar på käll arkivet. <br/>Den här egenskapen är endast giltig i ett binärt fil kopierings scenario. Standardvärdet: false. |No |
| modifiedDatetimeStart    | Filerna filtreras baserat på det *senast ändrade* attributet. <br>Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.<br/>Den här egenskapen gäller inte när du konfigurerar `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Samma som ovan.  
| enablePartitionDiscovery | För filer som är partitionerade anger du om du vill parsa partitionerna från fil Sök vägen och lägga till dem som ytterligare käll kolumner.<br/>Tillåtna värden är **false** (standard) och **True**. | No                                            |
| partitionRootPath | När partitions identifiering har Aktiver ATS anger du den absoluta rot Sök vägen för att kunna läsa partitionerade mappar som data kolumner.<br/><br/>Om den inte anges, som standard,<br/>– När du använder fil Sök vägen i data uppsättningen eller en lista med filer på källan, är partitionens rot Sök väg den sökväg som kon figurer ATS i data uppsättningen.<br/>– När du använder mapp-filter med jokertecken är partitionens rot Sök väg den underordnade sökvägen före det första jokertecknet.<br/><br/>Anta till exempel att du konfigurerar sökvägen i dataset som "rot/mapp/år = 2020/månad = 08/Day = 27":<br/>– Om du anger partitionens rot Sök väg som "rot/mapp/år = 2020" genererar kopierings aktiviteten två kolumner `month` och `day` värdet "08" respektive "27", förutom kolumnerna inuti filerna.<br/>-Om partitionens rot Sök väg inte anges genereras ingen extra kolumn. | No                                            |
| maxConcurrentConnections | Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| No                                            |
| ***DistCp-inställningar*** |  | |
| distcpSettings | Egenskaps gruppen som ska användas när du använder HDFS DistCp. | No |
| resourceManagerEndpoint | GARN (ännu en annan resurs Negotiator) slut punkt | Ja, om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra Temp DistCp-kommando skriptet. Skript filen genereras av Data Factory och tas bort när kopierings jobbet är klart. | Ja, om du använder DistCp |
| distcpOptions | Ytterligare alternativ har angetts för DistCp-kommandot. | No |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
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

I det här avsnittet beskrivs det resulterande beteendet om du använder ett Wildcard-filter med mappsökvägen och fil namnet.

| folderPath | fileName             | rekursiva | Källans mappstruktur och filter resultat (filer i **fetstil** hämtas) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (tom, Använd standard) | falskt     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (tom, Använd standard) | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jspå**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | falskt     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Exempel på fil listor

I det här avsnittet beskrivs det beteende som är resultatet av att använda en fil List Sök väg i kopierings aktivitets källan. Det förutsätter att du har följande mappstruktur för en källa och vill kopiera filerna i fet stil:

| Exempel på käll struktur                                      | Innehåll i FileListToCopy.txt                             | Azure Data Factory konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| skogen<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jspå<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadatatjänst<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **I data uppsättningen:**<br>– Mappsökväg: `root/FolderA`<br><br>**I kopierings aktivitets källan:**<br>– Sökväg till fil lista: `root/Metadata/FileListToCopy.txt` <br><br>Sökvägen till fil listan pekar på en textfil i samma data lager som innehåller en lista över filer som du vill kopiera (en fil per rad, med den relativa sökvägen till den sökväg som kon figurer ATS i data uppsättningen). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Använda DistCp för att kopiera data från HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) är ett internt Hadoop-kommando rads verktyg för att utföra en distribuerad kopia i ett Hadoop-kluster. När du kör ett kommando i DistCp visas först alla filer som ska kopieras och sedan skapas flera kart jobb i Hadoop-klustret. Varje kart jobb gör en binär kopia från källan till mottagaren.

Kopierings aktiviteten stöder användning av DistCp för att kopiera filer som är i Azure Blob Storage (inklusive [mellanlagrad kopia](copy-activity-performance.md)) eller en Azure Data Lake Store. I det här fallet kan DistCp dra nytta av klustrets kraft i stället för att köras på den lokala integrerings körningen. Med DistCp får du bättre kopiering av data genom att i synnerhet om klustret är mycket kraftfullt. Baserat på konfigurationen i din data fabrik konstruerar kopierings aktiviteten automatiskt ett DistCp-kommando, skickar det till ditt Hadoop-kluster och övervakar kopierings statusen.

### <a name="prerequisites"></a>Förutsättningar

Om du vill använda DistCp för att kopiera filer från HDFS till Azure Blob Storage (inklusive mellanlagrad kopia) eller Azure Data Lake Store kontrollerar du att Hadoop-klustret uppfyller följande krav:

* MapReduce-och garn tjänsterna är aktiverade.  
* GARN versionen är 2,5 eller senare.  
* HDFS-servern är integrerad med mål data lagret: **Azure Blob Storage** eller **Azure Data Lake Store (ADLS gen1)**: 

    - Azure Blob-filsystem stöds internt sedan Hadoop 2,7. Du behöver bara ange JAR-sökvägen i Hadoop-miljöns konfiguration.
    - Azure Data Lake Store FileSystem paketeras från Hadoop 3.0.0-alpha1. Om din Hadoop-kluster version är tidigare än den versionen måste du manuellt importera Azure Data Lake Store-relaterade JAR-paket (Azure-datalake-Store. jar) till klustret härifrån, och ange sökvägen till [jar-filen](https://hadoop.apache.org/releases.html)i Hadoop-miljöns konfiguration.

* Förbered en Temp-mapp i HDFS. Den här Temp-mappen används för att lagra ett DistCp-gränssnitts skript så att det upptar utrymme på KB-nivå.
* Kontrol lera att det användar konto som har angetts i den länkade tjänsten HDFS har behörighet att:
   * Skicka in ett program i garn.
   * Skapa en undermapp och läsa/skriva filer under Temp-mappen.

### <a name="configurations"></a>Konfigurationer

DistCp-relaterade konfigurationer och exempel finns i avsnittet [HDFS som källa](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Använd Kerberos-autentisering för HDFS-anslutningen

Det finns två alternativ för att konfigurera den lokala miljön för att använda Kerberos-autentisering för HDFS-anslutningen. Du kan välja den som passar din situation bättre.
* Alternativ 1: [Anslut en egen värd för integration runtime-datorn i Kerberos-sfären](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domänen och Kerberos-sfären](#kerberos-mutual-trust)

För något av alternativen kontrollerar du att du aktiverar webhdfs för Hadoop-kluster:

1. Skapa HTTP-huvudobjektet och keytab för webhdfs.

    > [!IMPORTANT]
    > HTTP Kerberos-huvudobjektet måste börja med "**http/**" enligt KERBEROS-SPNEGO specifikation. Läs mer [här](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. HDFS konfigurations alternativ: Lägg till följande tre egenskaper i `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Alternativ 1: Anslut en egen värd för integration runtime-datorn i Kerberos-sfären

#### <a name="requirements"></a>Krav

* Den egen värdbaserade integration runtime-datorn måste ansluta till Kerberos-sfären och kan inte ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du

**På KDC-servern:**

Skapa ett huvud konto för Azure Data Factory som ska användas och ange lösen ordet.

> [!IMPORTANT]
> Användar namnet får inte innehålla värd namnet.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**På den lokala datorn för integration Runtime:**

1.  Kör Ksetup-verktyget för att konfigurera Kerberos-Key Distribution Center (KDC)-servern och sfären.

    Datorn måste vara konfigurerad som medlem i en arbets grupp, eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Du kan uppnå den här konfigurationen genom att ange Kerberos-sfären och lägga till en KDC-Server genom att köra följande kommandon. Ersätt *REALM.com* med ditt eget sfär namn.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    När du har kört dessa kommandon startar du om datorn.

2.  Verifiera konfigurationen med `Ksetup` kommandot. Utdata bör vara som:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**I din data fabrik:**

* Konfigurera HDFS-anslutningen genom att använda Windows-autentisering tillsammans med ditt Kerberos-huvud namn och lösen ord för att ansluta till HDFS-datakällan. Information om konfigurations information finns i avsnittet [HDFS länkad tjänst egenskaper](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domänen och Kerberos-sfären

#### <a name="requirements"></a>Krav

*   Den egen värdbaserade integration runtime-datorn måste ansluta till en Windows-domän.
*   Du måste ha behörighet att uppdatera inställningarna för domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med ditt eget sfärnamn och din domänkontrollant.

**På KDC-servern:**

1. Redigera KDC-konfigurationen i filen *krb5. conf* för att låta KDC lita på Windows-domänen genom att referera till följande konfigurations mall. Konfigurationen finns som standard på */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   När du har konfigurerat filen startar du om KDC-tjänsten.

2. Förbered ett huvud namn med namnet *KRBTGT/REALM. COM- \@ AD.com* i KDC-servern med följande kommando:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. I konfigurations filen för *Hadoop.Security.auth_to_local* HDFS-tjänsten lägger du till `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**På domänkontrollanten:**

1.  Kör följande `Ksetup` kommandon för att lägga till en sfär post:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Upprätta förtroende från Windows-domänen till Kerberos-sfären. [Password] är lösen ordet för det primära *KRBTGT/REALM. COM- \@ AD.com*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Välj krypteringsalgoritmen som används i Kerberos.

    a. Välj **Serverhanteraren**  >  **Grupprincip hanterings**  >  **domän**  >  **Grupprincip objekt**  >  **standard eller aktiv domän princip**, och välj **sedan redigera**.

    b. I fönstret **redigeraren Grupprinciphantering** väljer du **dator konfiguration**  >  **principer**  >  **Windows-inställningar**  >  **säkerhets inställningar**  >  **lokala principer**  >  **säkerhets alternativ** och konfigurerar sedan **nätverks säkerhet: Konfigurera krypterings typer som tillåts för Kerberos**.

    c. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC-servern. Du kan välja alla alternativ.

    ![Skärm bild av fönstret "nätverks säkerhet: Konfigurera krypterings typer som tillåts för Kerberos"](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Använd `Ksetup` kommandot för att ange krypteringsalgoritmen som ska användas på den angivna sfären.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Skapa mappningen mellan domän kontot och Kerberos-huvudobjektet så att du kan använda Kerberos-huvudobjektet i Windows-domänen.

    a. Välj **administrations verktyg**  >  **Active Directory användare och datorer**.

    b. Konfigurera avancerade funktioner genom att välja **Visa**  >  **avancerade funktioner**.

    c. I fönstret **avancerade funktioner** högerklickar du på det konto som du vill skapa mappningar för och i fönstret **namn mappningar** väljer du fliken **Kerberos-namn** .

    d. Lägg till ett huvud konto från sfären.

       ![Fönstret "säkerhets identitets mappning"](media/connector-hdfs/map-security-identity.png)

**På den lokala datorn för integration Runtime:**

* Kör följande `Ksetup` kommandon för att lägga till en resurs post.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**I din data fabrik:**

* Konfigurera HDFS-anslutningen genom att använda Windows-autentisering tillsammans med antingen ditt domän konto eller Kerberos-huvudobjektet för att ansluta till HDFS-datakällan. Konfigurations information finns i avsnittet [HDFS-länkade tjänst egenskaper](#linked-service-properties) .

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Information om egenskaper för söknings aktiviteter finns [i lookup-aktivitet i Azure Data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Ta bort aktivitets egenskaper

Information om hur du tar bort aktivitets egenskaper finns i [ta bort aktivitet i Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande som är för bakåtkompatibilitet. Vi rekommenderar att du använder den tidigare diskuterade nya modellen eftersom Azure Data Factory redigerings gränssnittet har växlat till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre data uppsättnings modell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens *typ* -egenskap måste anges till *fileshare* |Yes |
| folderPath | Sökvägen till mappen. Ett Wildcard-filter stöds. Tillåtna jokertecken är `*` (matchar noll eller flera tecken) och `?` (matchar inte noll eller ett enskilt tecken). används `^` för att undanta om det faktiska fil namnet har ett jokertecken eller detta escape-tecken i. <br/><br/>Exempel: RootFolder/undermapp/, se fler exempel i [mapp-och fil filter exempel](#folder-and-file-filter-examples). |Yes |
| fileName |  Namnet eller wildcard-filtret för filerna under den angivna "folderPath". Om du inte anger ett värde för den här egenskapen pekar data uppsättningen på alla filer i mappen. <br/><br/>För filter är tillåtna jokertecken `*` (matchar noll eller flera tecken) och `?` (matchar noll eller ett enskilt tecken).<br/>– Exempel 1: `"fileName": "*.csv"`<br/>– Exempel 2: `"fileName": "???20180427.txt"`<br/>Används `^` för att kringgå om det faktiska mappnamnet har ett jokertecken eller detta escape-tecken i. |No |
| modifiedDatetimeStart | Filerna filtreras baserat på det *senast ändrade* attributet. Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill tillämpa ett fil filter på ett stort antal filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| modifiedDatetimeEnd | Filerna filtreras baserat på det *senast ändrade* attributet. Filerna väljs om deras senaste ändrings tid ligger inom intervallet `modifiedDatetimeStart` till `modifiedDatetimeEnd` . Tiden tillämpas på UTC-tidszonen i formatet *2018-12-01T05:00:00Z*. <br/><br/> Tänk på att den övergripande prestandan för data förflyttning påverkas om du aktiverar den här inställningen när du vill tillämpa ett fil filter på ett stort antal filer. <br/><br/> Egenskaperna kan vara NULL, vilket innebär att inget filter för filattribut används för data uppsättningen.  När `modifiedDatetimeStart` har ett datetime-värde men `modifiedDatetimeEnd` är null, innebär det att filerna vars senast ändrade attribut är större än eller lika med värdet för datetime är markerade.  När `modifiedDatetimeEnd` har ett datetime-värde men `modifiedDatetimeStart` är null, innebär det att filerna vars senast ändrade attribut är mindre än värdet för datetime är markerat.| No |
| format | Om du vill kopiera filer som är mellan filbaserade butiker (binär kopia) hoppar du över avsnittet format i både indata och utdata-datauppsättnings definitionerna.<br/><br/>Om du vill parsa filer med ett speciellt format stöds följande fil format *typer: text* format, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. Ange egenskapen *Type* under format till något av dessa värden. Mer information finns i avsnitten [text format](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parquet format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nej (endast för binär kopierings scenario) |
| komprimering | Ange typ och nivå för komprimeringen för data. Mer information finns i [fil format och komprimerings-codecar som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>De typer som stöds är: *gzip*, *DEFLATE*, *bzip2* och *ZipDeflate*.<br/>De nivåer som stöds är: *optimalt* och *snabbast*. |No |

>[!TIP]
>Om du vill kopiera alla filer under en mapp anger du endast **folderPath** .<br>Om du vill kopiera en enskild fil med ett angivet namn, anger du **folderPath** med mapp-och **fil** namn med fil namn.<br>Om du vill kopiera en delmängd av filer under en mapp anger du **folderPath** med en mapp och ett **fil namns** filter med jokertecken.

**Exempel:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| typ | *Typ* egenskapen för kopierings aktivitets källan måste anges till *HdfsSource*. |Yes |
| rekursiva | Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. När rekursivt är inställt på *True* och sinken är en filbaserad lagring, kommer en tom mapp eller undermapp inte att kopieras eller skapas i mottagaren.<br/>Tillåtna värden är *True* (standard) och *false*. | No |
| distcpSettings | Egenskaps gruppen när du använder HDFS-DistCp. | No |
| resourceManagerEndpoint | GARN Resource Manager-slutpunkt | Ja, om du använder DistCp |
| tempScriptPath | En mappsökväg som används för att lagra Temp DistCp-kommando skriptet. Skript filen genereras av Data Factory och tas bort när kopierings jobbet är klart. | Ja, om du använder DistCp |
| distcpOptions | Det finns ytterligare alternativ för DistCp-kommandot. | No |
| maxConcurrentConnections | Den övre gränsen för samtidiga anslutningar som upprättats till data lagret under aktivitets körningen. Ange bara ett värde om du vill begränsa samtidiga anslutningar.| No |

**Exempel: HDFS-källa i kopierings aktivitet med DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
