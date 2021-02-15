---
title: Kopiera data till/från ett fil system med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data till och från ett lokalt fil system med hjälp av Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9acd2adb68347978bb123d2239eedbb3e5595bc3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387401"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiera data till och från ett lokalt fil system med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-file-system-connector.md)
> * [Version 2 (aktuell version)](../connector-file-system.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [fil system Connector i v2](../connector-file-system.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till/från ett lokalt fil system. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från ett lokalt fil system** till följande data lager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till ett lokalt fil system**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Kopierings aktiviteten tar inte bort käll filen när den har kopierats till målet. Om du behöver ta bort käll filen efter en lyckad kopiering skapar du en anpassad aktivitet för att ta bort filen och använder aktiviteten i pipelinen.

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory stöder anslutning till och från ett lokalt fil system via **Data Management Gateway**. Du måste installera Data Management Gateway i din lokala miljö för att tjänsten Data Factory ska kunna ansluta till alla lokala data lager som stöds, inklusive fil system. Mer information om Data Management Gateway och stegvisa anvisningar om hur du konfigurerar gatewayen finns i [Flytta data mellan lokala källor och molnet med data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). Förutom Data Management Gateway behöver inga andra binära filer installeras för att kommunicera med och från ett lokalt fil system. Du måste installera och använda Data Management Gateway även om fil systemet finns i Azure IaaS VM. Detaljerad information om gatewayen finns i [Data Management Gateway](data-factory-data-management-gateway.md).

Om du vill använda en Linux-filresurs installerar du [Samba](https://www.samba.org/) på Linux-servern och installerar data Management Gateway på en Windows Server. Det finns inte stöd för att installera Data Management Gateway på en Linux-server.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från ett fil system med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du t. ex. kopierar data från en Azure Blob Storage till ett lokalt fil system, skapar du två länkade tjänster för att länka ditt lokala fil system och Azure Storage-konto till data fabriken. Information om länkade tjänst egenskaper som är speciella för ett lokalt fil system finns i avsnittet [länkade tjänst egenskaper](#linked-service-properties) .
3. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange BLOB-behållaren och mappen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange mappen och fil namnet (valfritt) i fil systemet. För data uppsättnings egenskaper som är speciella för det lokala fil systemet, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
4. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du BlobSource som källa och FileSystemSink som mottagare för kopierings aktiviteten. På samma sätt kan du använda FileSystemSource och BlobSink i kopierings aktiviteten om du kopierar från det lokala fil systemet till Azure Blob Storage. Information om kopiera aktivitets egenskaper som är speciella för lokala fil system finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från ett fil system finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-file-system) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för fil system:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Du kan länka ett lokalt fil system till en Azure-datafabrik med den **lokala fil serverns** länkade tjänst. Följande tabell innehåller beskrivningar av JSON-element som är speciella för den lokala fil Server länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Se till att egenskapen type har angetts till **OnPremisesFileServer**. |Ja |
| värd |Anger rot Sök vägen till den mapp som du vill kopiera. Använd escape-tecknet "\" för specialtecken i strängen. Se [exempel på länkade tjänst-och data uppsättnings definitioner](#sample-linked-service-and-dataset-definitions) . |Ja |
| userid |Ange ID: t för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| password |Ange lösen ordet för användaren (UserID). |Nej (om du väljer encryptedCredential |
| encryptedCredential |Ange de krypterade autentiseringsuppgifter som du kan få genom att köra cmdleten New-AzDataFactoryEncryptValue. |Nej (om du väljer att ange användar-ID och lösen ord som oformaterad text) |
| gatewayName |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala fil servern. |Ja |


### <a name="sample-linked-service-and-dataset-definitions"></a>Exempel på länkade tjänst-och data uppsättnings definitioner
| Scenario | Värd i länkad tjänst definition | folderPath i data uppsättnings definition |
| --- | --- | --- |
| Lokal mapp på Data Management Gateway dator: <br/><br/>Exempel: D: \\ \* eller D:\folder\subfolder\\\* |D: \\ \\ (för data Management Gateway 2,0 och senare versioner) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2,0) |.\\\\ eller mapp \\ \\ -undermapp (för data Management Gateway 2,0 och senare versioner) <br/><br/>D: \\ \\ eller d: \\ \\ mapp \\ \\ -undermapp (för gateway-version under 2,0) |
| Fjärran sluten delad mapp: <br/><br/>Exempel: \\ \\ mappen Server \\ resurs \\ \* eller mapp för min \\ \\ Server- \\ delad \\ mapp \\\\\* |\\\\\\\\Server \\ \\ resurs |.\\\\ eller mapp \\ -undermapp \\ |

>[!NOTE]
>När du redigerar via användar gränssnittet behöver du inte ange dubbla omvänt snedstreck ( `\\` ) för att undvika att du gör via JSON, ange enkelt omvänt snedstreck.

### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: använda användar namn och lösen ord i oformaterad text

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Exempel: använda encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [skapa data uppsättningar](data-factory-create-datasets.md). Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar samma för alla data uppsättnings typer.

Avsnittet typeProperties är olika för varje typ av data uppsättning. Den innehåller information som plats och format för data i data lagret. Avsnittet typeProperties för data uppsättningen av typen **fileshare** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger under Sök vägen till mappen. Använd escape-tecknet \' för specialtecken i strängen. Wildcard-filtret stöds inte. Se [exempel på länkade tjänst-och data uppsättnings definitioner](#sample-linked-service-and-dataset-definitions) .<br/><br/>Du kan kombinera den här egenskapen med **partitionby kolumndefinitionerna** för att ha mappsökvägar baserat på sektors start/slutdatum-gånger. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en speciell fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>Om inget **fil namn** har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren, är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| fileFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer. <br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (ett tecken).<br/><br/>Exempel 1: "fileFilter": "*. log"<br/>Exempel 2: "fileFilter": 2014-1-?. format<br/><br/>Observera att fileFilter är tillämpligt för en data uppsättning för en indata-FileShare. |Inga |
| partitionedBy |Du kan använda partitionedBy för att ange ett dynamiskt folderPath/fileName för Time Series-data. Ett exempel är folderPath-parameter för varje timme med data. |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Se [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Du kan inte använda fileName och fileFilter samtidigt.

### <a name="using-partitionedby-property"></a>Använda egenskapen partitionedBy
Som vi nämnt i föregående avsnitt kan du ange ett dynamiskt folderPath och ett fil namn för Time Series-data med egenskapen **partitionedBy** , [Data Factory Functions och systemvariablerna](data-factory-functions-variables.md).

Mer information om data uppsättningar för tids serier, schemaläggning och segment finns i [skapa data uppsättningar](data-factory-create-datasets.md), [schemalägga och köra](data-factory-scheduling-and-execution.md)och [skapa pipeliner](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Exempel 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet ersätts {slice} med värdet för Data Factory system variabeln SliceStart i formatet (YYYYMMDDHH). SliceStart refererar till Start tiden för sektorn. FolderPath är olika för varje sektor. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

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

I det här exemplet extraheras år, månad, dag och tid för SliceStart till separata variabler som egenskaperna folderPath och fileName använder.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata och principer är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ.

För kopierings aktivitet varierar de beroende på typerna av källor och mottagare. Om du flyttar data från ett lokalt fil system anger du käll typen i kopierings aktiviteten till **FileSystemSource**. På samma sätt, om du flyttar data till ett lokalt fil system, ställer du in mottagar typen i kopierings aktiviteten till **FileSystemSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av FileSystemSource och FileSystemSink.

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant, falskt (standard) |Inga |

**FileSystemSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopierings beteendet när källan är BlobSource eller FileSystem. |**PreserveHierarchy:** Bevarar filens hierarki i målmappen. Det vill säga att käll filens relativa sökväg är samma som den relativa sökvägen till mål filen i målmappen.<br/><br/>**FlattenHierarchy:** Alla filer från källmappen skapas på den första nivån i målmappen. Målattribut skapas med ett automatiskt genererat namn.<br/><br/>**MergeFiles:** Sammanfogar alla filer från källmappen till en fil. Om fil namnet/blobb namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars är det ett automatiskt genererat fil namn. |Inga |

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior-exempel
I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av värden för egenskaperna recursive och copyBehavior.

| rekursivt värde | copyBehavior-värde | Resulterande beteende |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källan:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Innehållet i fil1 + Fil2 + File3 + File4 + File 5 sammanfogas till en fil med ett automatiskt genererat fil namn. |
| falskt |preserveHierarchy |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |flattenHierarchy |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |mergeFiles |För en källmapp Mapp1 med följande struktur,<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2-innehåll sammanfogas till en fil med ett automatiskt genererat fil namn.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Se [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel om information.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-exempel för att kopiera data till och från fil systemet
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från ett lokalt fil system och Azure Blob Storage. Du kan dock kopiera data *direkt* från någon av källorna till någon av de handfat som visas i [källor och mottagare som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopierings aktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exempel: kopiera data från ett lokalt fil system till Azure Blob Storage
Det här exemplet visar hur du kopierar data från ett lokalt fil system till Azure Blob Storage. Exemplet har följande Data Factory entiteter:

* En länkad tjänst av typen [OnPremisesFileServer](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [fileshare](#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [FileSystemSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

I följande exempel kopieras Time-Series-data från ett lokalt fil system till Azure Blob Storage varje timme. JSON-egenskaperna som används i dessa exempel beskrivs i avsnitten efter exemplen.

Som ett första steg konfigurerar du Data Management Gateway enligt anvisningarna i [Flytta data mellan lokala källor och molnet med data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Länkad tjänst för lokal fil Server:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Vi rekommenderar att du använder egenskapen **encryptedCredential** i stället för **UserID** -och **Password** -egenskaperna. Se [länkad tjänst för fil Server](#linked-service-properties) för information om den här länkade tjänsten.

**Azure Storage länkad tjänst:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Data uppsättning för lokal fil system indata:**

Data hämtas från en ny fil varje timme. Egenskaperna folderPath och fileName bestäms baserat på sektorns start tid.

Inställningen `"external": "true"` informerar Data Factory att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Data uppsättning för Azure Blob Storage-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder året, månaden, dagen och timvärdet i Start tiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**En kopierings aktivitet i en pipeline med fil system källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **FileSystemSource** och **mottagar** typen är inställd på **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exempel: kopiera data från Azure SQL Database till ett lokalt fil system
Följande exempel visar:

* En länkad tjänst av typen [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* En länkad tjänst av typen [OnPremisesFileServer](#linked-service-properties).
* En indata-datauppsättning av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* En data uppsättning av typen [fileshare](#dataset-properties).
* En pipeline med en kopierings aktivitet som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [FileSystemSink](#copy-activity-properties).

Exemplet kopierar Time Series-data från en Azure SQL-tabell till ett lokalt fil system varje timme. JSON-egenskaperna som används i dessa exempel beskrivs i avsnitt efter exemplen.

**Azure SQL Database länkad tjänst:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Länkad tjänst för lokal fil Server:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Vi rekommenderar att du använder egenskapen **encryptedCredential** i stället för att använda egenskaperna **UserID** och **Password** . Se [fil systemets länkade tjänst](#linked-service-properties) för information om den här länkade tjänsten.

**Data uppsättning för Azure SQL-indata:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i Azure SQL och att den innehåller en kolumn med namnet "timestampcolumn" för Time Series-data.

Inställningen ``"external": "true"`` informerar Data Factory att data uppsättningen är extern för data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Data uppsättning för det lokala fil systemet:**

Data kopieras till en ny fil varje timme. FolderPath och fileName för blobben bestäms utifrån start tiden för sektorn.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**En kopierings aktivitet i en pipeline med SQL-källa och fil Systems mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlSource** och **mottagar** typen är inställd på **FileSystemSink**. SQL-frågan som anges för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Du kan också mappa kolumner från käll data uppsättningen till kolumner från Sink-datauppsättningen i kopierings aktivitets definitionen. Mer information finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
 Information om viktiga faktorer som påverkar prestandan för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den finns i [guiden Kopiera aktivitets prestanda och justering](data-factory-copy-activity-performance.md).
