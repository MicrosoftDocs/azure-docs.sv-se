---
title: Flytta data från en lokal HDFS
description: Lär dig mer om hur du flyttar data från en lokal HDFS med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 52e176e0fed85b649d482614667d695db539e5d1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383083"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Flytta data från en lokal HDFS med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-hdfs-connector.md)
> * [Version 2 (aktuell version)](../connector-hdfs.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [HDFS Connector i v2](../connector-hdfs.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal HDFS. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från HDFS till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från en lokal HDFS till andra data lager, men inte för att flytta data från andra data lager till en lokal HDFS.

> [!NOTE]
> Kopierings aktiviteten tar inte bort käll filen när den har kopierats till målet. Om du behöver ta bort käll filen efter en lyckad kopiering skapar du en anpassad aktivitet för att ta bort filen och använder aktiviteten i pipelinen. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory tjänsten stöder anslutning till lokal HDFS med hjälp av Data Management Gateway. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar om du vill lära dig mer om data Management Gateway och stegvisa anvisningar för hur du konfigurerar gatewayen. Använd gatewayen för att ansluta till HDFS även om den finns i en virtuell Azure IaaS-dator.

> [!NOTE]
> Se till att Data Management Gateway har åtkomst till **alla** [namnserver]: [namn Node port] och [datanode-servrar]: [datanode port] för Hadoop-klustret. Standard [Name Node port] är 50070 och standardvärdet för [datanode-port] är 50075.

Även om du kan installera gateway på samma lokala dator eller den virtuella Azure-datorn som HDFS, rekommenderar vi att du installerar gatewayen på en separat dator/Azure IaaS VM. Att ha en gateway på en annan dator minskar resurs konkurrens och förbättrar prestandan. När du installerar gatewayen på en separat dator ska datorn kunna komma åt datorn med HDFS.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från en HDFS-källa med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, **.NET API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett HDFS-datalager finns i [JSON-exempel: kopiera data från den lokala HDFS till Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för HDFS:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
En länkad tjänst länkar ett data lager till en data fabrik. Du skapar en länkad tjänst av typen **HDFS** för att länka en lokal HDFS till din data fabrik. Följande tabell innehåller en beskrivning av JSON-element som är speciella för den länkade tjänsten HDFS.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **HDFS** |Ja |
| url |URL till HDFS |Ja |
| authenticationType |Anonym eller Windows. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS Connector läser du [det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) för att konfigurera din lokala miljö. |Ja |
| userName |Användar namn för Windows-autentisering. För Kerberos-autentisering anger du `<username>@<domain>.com` . |Ja (för Windows-autentisering) |
| password |Lösen ord för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factorys tjänsten ska använda för att ansluta till HDFS. |Ja |
| encryptedCredential |[New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) utdata från autentiseringsuppgifterna för åtkomst. |Inga |

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Använda Windows-autentisering

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information om platsen för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **fileshare** (som innehåller HDFS-datauppsättningen) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet "\" för specialtecken i strängen. Exempel: för folder\subfolder, anger du \\ \\ undermappen mapp och för d:\samplefolder, anger du d: \\ \\ samplefolder.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: <br/><br/>`Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath, fil namn för Time Series-data. Exempel: folderPath-parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Det går inte att använda filename och fileFilter samtidigt.

### <a name="using-partionedby-property"></a>Använda egenskapen partionedBy
Som vi nämnt i föregående avsnitt kan du ange ett dynamiskt folderPath och ett fil namn för Time Series-data med egenskapen **partitionedBy** , [Data Factory Functions och systemvariablerna](data-factory-functions-variables.md).

Mer information om data uppsättningar för Time Series, schemaläggning och segment finns i [skapa data uppsättningar](data-factory-create-datasets.md), [Schemalägga & körning](data-factory-scheduling-and-execution.md)och [skapa pipelines](data-factory-create-pipelines.md) -artiklar.

#### <a name="sample-1"></a>Exempel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {slice} med värdet för Data Factory systemvariabeln SliceStart i det angivna formatet (YYYYMMDDHH). SliceStart refererar till Start tiden för sektorn. FolderPath är olika för varje sektor. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exempel 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
I det här exemplet extraheras år, månad, dag och tid för SliceStart till separata variabler som används av folderPath-och fileName-egenskaperna.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, in-och utdata-tabeller och principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

För kopierings aktiviteten, när källan är av typen **FileSystemSource** , finns följande egenskaper i avsnittet typeProperties:

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Se [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel om information.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-exempel: kopiera data från en lokal HDFS till Azure-Blob
Det här exemplet visar hur du kopierar data från en lokal HDFS till Azure Blob Storage. Data kan dock kopieras **direkt** till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.  

Exemplet innehåller JSON-definitioner för följande Data Factory entiteter. Du kan använda dessa definitioner för att skapa en pipeline för att kopiera data från HDFS till Azure Blob Storage med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. En länkad tjänst av typen [OnPremisesHdfs](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [fileshare](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en lokal HDFS till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

Som ett första steg konfigurerar du gatewayen för data hantering. Instruktionerna i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

**HDFS-länkad tjänst:** I det här exemplet används Windows-autentisering. Se avsnittet [HDFS-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage länkad tjänst:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**HDFS-indata-datamängd:** Den här data mängden refererar till HDFS-mappen DataTransfer/UnitTest/. Pipelinen kopierar alla filer i den här mappen till målet.

Inställningen "extern": "true" informerar den Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**En kopierings aktivitet i en pipeline med fil system källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som är konfigurerad för att använda dessa data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **FileSystemSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som anges för egenskapen **fråga** väljer data under den senaste timmen som ska kopieras.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Använd Kerberos-autentisering för HDFS-anslutning
Det finns två alternativ för att konfigurera den lokala miljön så att den använder Kerberos-autentisering i HDFS-anslutningen. Du kan välja den som passar bäst för ditt ärende.
* Alternativ 1: [Anslut gateway-datorn i Kerberos-sfären](#kerberos-join-realm)
* Alternativ 2: [Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>Alternativ 1: Anslut gateway-datorn i Kerberos-sfären

#### <a name="requirement"></a>Föreskrifter

* Gateway-datorn måste ansluta till Kerberos-sfären och kan inte ansluta till en Windows-domän.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

**På gateway-datorn:**

1. Kör **Ksetup** -verktyget för att konfigurera Kerberos KDC-servern och sfären.

   Datorn måste vara konfigurerad som medlem i en arbets grupp eftersom en Kerberos-sfär skiljer sig från en Windows-domän. Detta kan uppnås genom att du ställer in Kerberos-sfären och lägger till en KDC-server på följande sätt. Ersätt *REALM.com* med egen respektive sfär efter behov.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   **Starta om** datorn när du har kört dessa två kommandon.

2. Verifiera konfigurationen med kommandot **Ksetup** . Utdata bör vara som:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**I Azure Data Factory:**

* Konfigurera HDFS-anslutningen med **Windows-autentisering** tillsammans med ditt Kerberos-huvud namn och lösen ord för att ansluta till HDFS-datakällan. Kontrol lera [HDFS-avsnittet länkade tjänst egenskaper](#linked-service-properties) i konfigurations information.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Alternativ 2: Aktivera ömsesidigt förtroende mellan Windows-domän och Kerberos-sfär

#### <a name="requirement"></a>Föreskrifter
*    Gateway-datorn måste ansluta till en Windows-domän.
*    Du måste ha behörighet att uppdatera inställningarna för domänkontrollanten.

#### <a name="how-to-configure"></a>Så här konfigurerar du:

> [!NOTE]
> Ersätt REALM.COM och AD.COM i följande självstudie med din egen domän och domänkontrollant efter behov.

**På KDC-Server:**

1. Redigera KDC-konfigurationen i **krb5. conf** -filen för att tillåta att KDC-förtroende Windows-domänen refererar till följande konfigurations mall. Konfigurationen finns som standard på **/etc/krb5.conf**.

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

   **Starta om** KDC-tjänsten efter konfigurationen.

2. Förbered ett huvud namn med namnet **KRBTGT/REALM. COM- \@ AD.com** i KDC-servern med följande kommando:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. I konfigurations filen för **Hadoop.Security.auth_to_local** HDFS-tjänsten lägger du till `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**På domänkontrollant:**

1. Kör följande **Ksetup** -kommandon för att lägga till en sfär post:

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Upprätta förtroende från Windows-domän till Kerberos-sfär. [Password] är lösen ordet för det primära **KRBTGT/REALM. COM- \@ AD.com**.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Välj krypteringsalgoritm som används i Kerberos.

   1. Gå till Serverhanteraren > grupprincip hantering > domän > grupprincip objekt > standard princip eller aktiv domän princip och redigera.

   2. I popup-fönstret **redigeraren Grupprinciphantering** går du till dator konfiguration > principer > Windows-inställningar > säkerhets inställningar > lokala principer > säkerhets alternativ och konfigurerar **nätverks säkerhet: Konfigurera krypterings typer som tillåts för Kerberos**.

   3. Välj den krypteringsalgoritm som du vill använda när du ansluter till KDC. Vanligt vis kan du bara välja alla alternativ.

      ![Konfigurations krypterings typer för Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

   4. Använd **Ksetup** -kommandot för att ange vilken krypteringsalgoritm som ska användas för den aktuella sfären.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Skapa mappningen mellan domän kontot och Kerberos-huvudobjektet för att använda Kerberos-huvudobjektet i Windows-domänen.

   1. Starta administrations verktygen > **Active Directory användare och datorer**.

   2. Konfigurera avancerade funktioner genom att klicka på **Visa**  >  **avancerade funktioner**.

   3. Leta upp det konto som du vill skapa mappningar för och högerklicka för att visa **namn mappningar** > Klicka på fliken **Kerberos-namn** .

   4. Lägg till ett huvud konto från sfären.

      ![Mappa säkerhets identitet](media/data-factory-hdfs-connector/map-security-identity.png)

**På gateway-datorn:**

* Kör följande **Ksetup** -kommandon för att lägga till en resurs post.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**I Azure Data Factory:**

* Konfigurera HDFS-anslutningen med **Windows-autentisering** tillsammans med antingen ditt domän konto eller Kerberos-huvudobjektet för att ansluta till HDFS-datakällan. Kontrol lera [HDFS-avsnittet länkade tjänst egenskaper](#linked-service-properties) i konfigurations information.

> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
