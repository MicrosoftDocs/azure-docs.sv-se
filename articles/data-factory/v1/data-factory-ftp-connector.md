---
title: Flytta data från en FTP-server med hjälp av Azure Data Factory
description: Lär dig mer om hur du flyttar data från en FTP-server med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8a8ca44fbdb7610f85bc53c23d502d2efb01c8b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363958"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Flytta data från en FTP-server med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-ftp-connector.md)
> * [Version 2 (aktuell version)](../connector-ftp.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [FTP-anslutning i v2](../connector-ftp.md).

Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att flytta data från en FTP-server. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

Du kan kopiera data från en FTP-server till alla mottagar data lager som stöds. En lista över data lager som stöds som mottagare av kopierings aktiviteten finns i tabellen över [data lager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory har för närvarande endast stöd för att flytta data från en FTP-server till andra data lager, men inte flytta data från andra data lager till en FTP-server. Den stöder både lokala och molnbaserade FTP-servrar.

> [!NOTE]
> Kopierings aktiviteten tar inte bort käll filen när den har kopierats till målet. Om du behöver ta bort käll filen efter en lyckad kopiering skapar du en anpassad aktivitet för att ta bort filen och använder aktiviteten i pipelinen.

## <a name="enable-connectivity"></a>Aktivera anslutning
Om du flyttar data från en **lokal** FTP-server till ett moln data lager (till exempel till Azure Blob Storage) installerar du och använder data Management Gateway. Data Management Gateway är en klient agent som är installerad på den lokala datorn och som gör det möjligt för moln tjänster att ansluta till en lokal resurs. Mer information finns i [Data Management Gateway](data-factory-data-management-gateway.md). Stegvisa instruktioner om hur du konfigurerar gatewayen och använder den finns i [Flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md). Du använder gatewayen för att ansluta till en FTP-server, även om servern finns på en virtuell dator i Azure Infrastructure as a Service (IaaS).

Det är möjligt att installera gatewayen på samma lokala dator eller IaaS VM som FTP-servern. Vi rekommenderar dock att du installerar gatewayen på en separat dator eller virtuell IaaS-dator för att undvika resurs konkurrens och för bättre prestanda. När du installerar gatewayen på en annan dator ska datorn kunna komma åt FTP-servern.

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data från en FTP-källa genom att använda olika verktyg eller API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Data Factory kopiering**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna, utför följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg eller API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data från ett FTP-datalager finns i [JSON-exemplet: kopiera data från FTP-server till Azure Blob](#json-example-copy-data-from-ftp-server-to-azure-blob) i den här artikeln.

> [!NOTE]
> Mer information om vilka fil-och komprimerings format som stöds finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för FTP.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
I följande tabell beskrivs JSON-element som är begränsade till en länkad FTP-tjänst.

| Egenskap | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| typ |Ange till FtpServer. |Ja |&nbsp; |
| värd |Ange namnet eller IP-adressen för FTP-servern. |Ja |&nbsp; |
| authenticationType |Ange autentiseringstyp. |Ja |Grundläggande, anonym |
| användarnamn |Ange den användare som har åtkomst till FTP-servern. |Inga |&nbsp; |
| password |Ange lösen ordet för användaren (användar namn). |Inga |&nbsp; |
| encryptedCredential |Ange krypterade autentiseringsuppgifter för åtkomst till FTP-servern. |Inga |&nbsp; |
| gatewayName |Ange namnet på gatewayen i Data Management Gateway för att ansluta till en lokal FTP-server. |Inga |&nbsp; |
| port |Ange den port som FTP-servern lyssnar på. |Inga |21 |
| enableSsl |Ange om FTP ska användas över en SSL/TLS-kanal. |Inga |true |
| enableServerCertificateValidation |Ange om du vill aktivera verifiering av Server-TLS/SSL-certifikat när du använder FTP över SSL/TLS-kanal. |Inga |true |

>[!NOTE]
>FTP-anslutningen har stöd för åtkomst till FTP-servern med antingen ingen kryptering eller explicit SSL/TLS-kryptering. den har inte stöd för implicit SSL/TLS-kryptering.

### <a name="use-anonymous-authentication"></a>Använd anonym autentisering

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Använd användar namn och lösen ord som oformaterad text för grundläggande autentisering

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Använda port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Använda encryptedCredential för autentisering och gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [skapa data uppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar samma för alla data uppsättnings typer.

Avsnittet **typeProperties** är olika för varje typ av data uppsättning. Den innehåller information som är speciell för data uppsättnings typen. Avsnittet **typeProperties** för en data uppsättning av typen **fileshare** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Under Sök väg till mappen. Använd escape-tecknet "\" för specialtecken i strängen. Se exempel på länkade tjänst-och data uppsättnings definitioner.<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserade på sektor start-och slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När inget **fil namn** har angetts för en data uppsättning för utdata är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i **folderPath** i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** är tillämpligt för en data uppsättning för en indata-fileshare. Den här egenskapen stöds inte med Hadoop Distributed File System (HDFS). |Inga |
| partitionedBy |Används för att ange en dynamisk **folderPath** och ett **fil namn** för Time Series-data. Du kan till exempel ange en **folderPath** som är parameterstyrda för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i avsnitten [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Om du vill kopiera filer som de är mellan filbaserade arkiv (binär kopia) hoppar du över avsnittet format i definitionerna för både indata och utdata. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**, och de nivåer som stöds är **optimala** och **snabbaste**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| useBinaryTransfer |Ange om du vill använda binärt överförings läge. Värdena är true för binärt läge (detta är standardvärdet) och falskt för ASCII. Den här egenskapen kan endast användas när den associerade länkade tjänst typen är av typen: FtpServer. |Inga |

> [!NOTE]
> Det går inte att använda **filename** och **fileFilter** samtidigt.

### <a name="use-the-partionedby-property"></a>Använd egenskapen partionedBy
Som vi nämnt i föregående avsnitt kan du ange ett dynamiskt **folderPath** och ett **fil namn** för Time Series-data med egenskapen **partitionedBy** .

Information om data uppsättningar, schemaläggning och segment i Time Series finns i [skapa data uppsättningar](data-factory-create-datasets.md), [schemalägga och köra](data-factory-scheduling-and-execution.md)och [skapa pipeliner](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
I det här exemplet ersätts {slice} med värdet för Data Factory systemvariabeln SliceStart, i det format som anges (YYYYMMDDHH). SliceStart refererar till Start tiden för sektorn. Mappsökvägen är olika för varje sektor. (Till exempel wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>Exempel 2

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
I det här exemplet extraheras året, månaden, dagen och tiden för SliceStart till separata variabler som används av egenskaperna **folderPath** och **filename** .

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapa pipeliner](data-factory-create-pipelines.md). Egenskaper som namn, beskrivning, in-och utdata-tabeller och principer är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten, å andra sidan, varierar beroende på varje aktivitets typ. För kopierings aktiviteten varierar typ egenskaperna beroende på typerna av källor och mottagare.

När källan är av typen **FileSystemSource** i kopierings aktivitet är följande egenskap tillgängligt i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna, eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-exempel: kopiera data från FTP-server till Azure-Blob
Det här exemplet visar hur du kopierar data från en FTP-server till Azure Blob Storage. Data kan dock kopieras direkt till någon av de handfat som anges i de [data lager och format som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats), genom att använda kopierings aktiviteten i Data Factory.

I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)eller [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* En länkad tjänst av typen [ftpserver](#linked-service-properties)
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [fileshare](#dataset-properties)
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Exemplet kopierar data från en FTP-server till en Azure-Blob varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

### <a name="ftp-linked-service"></a>Länkad FTP-tjänst

I det här exemplet används grundläggande autentisering med användar namnet och lösen ordet som oformaterad text. Du kan också använda något av följande sätt:

* Anonym autentisering
* Grundläggande autentisering med krypterade autentiseringsuppgifter
* FTP över SSL/TLS (FTPS)

Se avsnittet [FTP-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

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
### <a name="ftp-input-dataset"></a>Data uppsättning för FTP-indata

Den här data mängden refererar till FTP-mappen `mysharedfolder` och-filen `test.csv` . Pipelinen kopierar filen till målet.

Om du anger **external** till **True** informerar Data Factory tjänsten att data uppsättningen är extern i data fabriken och inte skapas av en aktivitet i data fabriken.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Utdatauppsättning för Azure-blobb

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder de delar av start tiden för år, månad, dag och timmar.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>En kopierings aktivitet i en pipeline med fil system källa och blob-mottagare

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **FileSystemSource** och **mottagar** typen är inställd på **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* Information om viktiga faktorer som påverkar prestanda för data flytt (kopierings aktivitet) i Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitets prestanda och justering](data-factory-copy-activity-performance.md).

* Stegvisa instruktioner för hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
