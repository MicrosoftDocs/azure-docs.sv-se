---
title: Flytta data från SFTP-servern med Azure Data Factory
description: Lär dig mer om hur du flyttar data från en lokal eller en molnbaserad SFTP-server med hjälp av Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382658"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Flytta data från en SFTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-sftp-connector.md)
> * [Version 2 (aktuell version)](../connector-sftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [SFTPconnector i v2](../connector-sftp.md).

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en lokal/molnbaserad SFTP-server till ett mottagar data lager som stöds. Den här artikeln bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) som visar en allmän översikt över data förflyttning med kopierings aktivitet och listan över data lager som stöds som källor/mottagare.

Data Factory har för närvarande endast stöd för att flytta data från en SFTP-server till andra data lager, men inte för att flytta data från andra data lager till en SFTP-server. Det stöder både lokala och molnbaserade SFTP-servrar.

> [!NOTE]
> Kopierings aktiviteten tar inte bort käll filen när den har kopierats till målet. Om du behöver ta bort käll filen efter en lyckad kopiering skapar du en anpassad aktivitet för att ta bort filen och använder aktiviteten i pipelinen.

## <a name="supported-scenarios-and-authentication-types"></a>Scenarier och autentiseringstyper som stöds
Du kan använda den här SFTP-anslutningen för att kopiera data från **både Cloud SFTP-servrar och lokala SFTP-servrar**. **Basic** -och **SshPublicKey** -autentiseringstyper stöds när du ansluter till SFTP-servern.

När du kopierar data från en lokal SFTP-server måste du installera en Data Management Gateway i den lokala miljön/Azure VM. Se [Data Management Gateway](data-factory-data-management-gateway.md) för information om gatewayen. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner om hur du konfigurerar gatewayen och använder den.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från en SFTP-källa med hjälp av olika verktyg/API: er.

- Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . För JSON-exempel för att kopiera data från SFTP-servern till Azure Blob Storage, se [JSON-exempel: kopiera data från SFTP-server till Azure Blob](#json-example-copy-data-from-sftp-server-to-azure-blob) i den här artikeln.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Följande tabell innehåller en beskrivning av JSON-element som är begränsade till FTP-länkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ | Egenskapen Type måste anges till `Sftp` . |Ja |
| värd | Namn eller IP-adress för SFTP-servern. |Ja |
| port |Porten som SFTP-servern lyssnar på. Standardvärdet är: 21 |Inga |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Basic**, **SshPublicKey**. <br><br> Se [använda grundläggande autentisering](#using-basic-authentication) och [autentisering med offentliga SSH-nycklar](#using-ssh-public-key-authentication) på fler egenskaper respektive JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över validering av värd nycklar. | Nej. Standardvärdet: false |
| hostKeyFingerprint | Ange finger utskriften för värd nyckeln. | Ja om `skipHostKeyValidation` är inställt på falskt.  |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (username + Password) eller SshPublicKey-autentisering (användar namn + privat nyckel Sök väg eller innehåll) i guiden Kopiera eller dialog rutan för ClickOnce. | Nej. Gäller endast när du kopierar data från en lokal SFTP-server. |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering

Om du vill använda grundläggande autentisering anger du `authenticationType` som `Basic` och anger sedan följande egenskaper, förutom SFTP-anslutningen som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Användarens lösen ord (användar namn). | Ja |

#### <a name="example-basic-authentication"></a>Exempel: grundläggande autentisering
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: grundläggande autentisering med krypterade autentiseringsuppgifter

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Använda autentisering med offentlig SSH-nyckel

Om du vill använda autentisering med offentlig SSH-nyckel anger du `authenticationType` som `SshPublicKey` och anger följande egenskaper förutom SFTP-anslutningen som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-servern |Ja |
| privateKeyPath | Ange en absolut sökväg till den privata nyckel filen som gatewayen har åtkomst till. | Ange antingen `privateKeyPath` eller `privateKeyContent` . <br><br> Gäller endast när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserad sträng för innehållet i den privata nyckeln. Kopierings guiden kan läsa den privata nyckel filen och extrahera innehållet i den privata nyckeln automatiskt. Om du använder andra verktyg/SDK använder du egenskapen privateKeyPath i stället. | Ange antingen `privateKeyPath` eller `privateKeyContent` . |
| Fraser | Ange pass frasen/lösen ordet för att dekryptera den privata nyckeln om nyckel filen skyddas av en pass fras. | Ja om den privata nyckel filen skyddas av en pass fras. |

> [!NOTE]
> SFTP-anslutaren stöder RSA/DSA OpenSSH-nyckel. Kontrol lera att innehållet i nyckel filen börjar med "-----BEGIN [RSA/DSA] privat nyckel-----". Om den privata nyckel filen är en PPK-fil kan du använda verktyget SparaTillFil för att konvertera från. PPK till OpenSSH-format.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Exempel: SshPublicKey-autentisering med privat nyckel-sökväg

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: SshPublicKey-autentisering med privat nyckel innehåll

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar samma för alla data uppsättnings typer.

Avsnittet **typeProperties** är olika för varje typ av data uppsättning. Den innehåller information som är speciell för data uppsättnings typen. Avsnittet typeProperties för en data uppsättning av typen **fileshare** -datauppsättning har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Under Sök väg till mappen. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget fil namn har angetts för en data uppsättning för utdata skulle namnet på den genererade filen ha följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter är tillämpligt för en data uppsättning för en indata-FileShare. Den här egenskapen stöds inte med HDFS. |Inga |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath, fil namn för Time Series-data. Till exempel folderPath parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| useBinaryTransfer |Ange om du vill använda binärt överförings läge. Sant för binärt läge och falskt ASCII. Standardvärde: sant. Den här egenskapen kan endast användas när den associerade länkade tjänst typen är av typen: FtpServer. |Inga |

> [!NOTE]
> Det går inte att använda filename och fileFilter samtidigt.

### <a name="using-partionedby-property"></a>Använda egenskapen partionedBy
Som vi nämnt i föregående avsnitt kan du ange en dynamisk folderPath, ett fil namn för Time Series-data med partitionedBy. Det kan du göra med Data Factory-makron och systemvariabeln SliceStart, SliceEnd som anger den logiska tids perioden för en viss data sektor.

Information om data uppsättningar, schemaläggning och segment i Time Series finns i [skapa data uppsättningar](data-factory-create-datasets.md), [Schemalägga & körning](data-factory-scheduling-and-execution.md)och [skapa pipelines](data-factory-create-pipelines.md) -artiklar.

#### <a name="sample-1"></a>Exempel 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {slice} med värdet för Data Factory systemvariabeln SliceStart i det angivna formatet (YYYYMMDDHH). SliceStart refererar till Start tiden för sektorn. FolderPath är olika för varje sektor. Exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exempel 2:

```json
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

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktiviteten varierar typ egenskaperna beroende på typerna av källor och mottagare.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Se [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel om information.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-exempel: kopiera data från SFTP-server till Azure-Blob
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data från SFTP-källan till Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Det innehåller inga steg-för-steg-instruktioner för att skapa data fabriken. Se [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar för steg-för-steg-instruktioner.

Exemplet har följande data Factory-entiteter:

* En länkad tjänst av typen [SFTP](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [fileshare](#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en SFTP-server till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad SFTP-tjänst**

I det här exemplet används grundläggande autentisering med användar namn och lösen ord som oformaterad text. Du kan också använda något av följande sätt:

* Grundläggande autentisering med krypterade autentiseringsuppgifter
* Autentisering med offentlig SSH-nyckel

Se avsnittet [FTP-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage länkad tjänst**

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
**Data uppsättning för SFTP-indata**

Den här data uppsättningen refererar till mappen SFTP `mysharedfolder` och filen `test.csv` . Pipelinen kopierar filen till målet.

Inställningen "extern": "true" informerar den Data Factory tjänsten att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline med kopierings aktivitet**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **FileSystemSource** och **mottagar** typ är inställd på **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Själv studie kursen om kopierings aktiviteter](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopierings aktivitet.
