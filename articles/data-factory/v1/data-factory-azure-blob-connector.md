---
title: Kopiera data till/från Azure Blob Storage
description: 'Lär dig hur du kopierar BLOB-data i Azure Data Factory. Använd vårt exempel: så här kopierar du data till och från Azure Blob Storage och Azure SQL Database.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1343f900e12bff09c0436ca52d8b091fe48a181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393555"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Kopiera data till eller från Azure Blob Storage med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-blob-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-blob-storage.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory kan du läsa mer i [Azure Blob Storage Connector i v2](../connector-azure-blob-storage.md).


Den här artikeln förklarar hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data till och från Azure Blob Storage. Det bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som visar en översikt över data förflyttning med kopierings aktiviteten.

## <a name="overview"></a>Översikt
Du kan kopiera data från alla käll data lager som stöds till Azure Blob Storage eller från Azure Blob Storage till alla mottagar data lager som stöds. Följande tabell innehåller en lista över data lager som stöds som källor eller handfat av kopierings aktiviteten. Du kan till exempel flytta data **från** en SQL Server databas eller en databas i Azure SQL Database **till** en Azure Blob-lagring. Du kan också kopiera data **från** Azure Blob Storage **till** Azure Synapse Analytics eller en Azure Cosmos DB-samling.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Blob Storage** till följande data lager:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till Azure Blob Storage**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopierings aktiviteten stöder kopiering av data från/till både generella Azure Storage konton och frekvent/låg frekvent blob-lagring. Aktiviteten har stöd för **läsning från block-, tilläggs-eller sid-blobar**, men stöder **skrivning enbart till block-blobar**. Azure Premium Storage stöds inte som mottagare eftersom den backas upp av Page blobbar.
>
> Kopierings aktiviteten tar inte bort data från källan när data har kopierats till målet. Om du behöver ta bort källdata efter en lyckad kopiering skapar du en [anpassad aktivitet](data-factory-use-custom-activities.md) för att ta bort data och använder aktiviteten i pipelinen. Ett exempel finns i [ta bort BLOB-eller Folder-exempel på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en Azure-Blob Storage med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Den här artikeln innehåller en [genom gång](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) av hur du skapar en pipeline för att kopiera data från en Azure Blob Storage plats till en annan Azure Blob Storage plats. En själv studie kurs om hur du skapar en pipeline för att kopiera data från en Azure-Blob Storage till Azure SQL Database finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du till exempel kopierar data från en Azure Blob Storage till Azure SQL Database, skapar du två länkade tjänster för att länka ditt Azure Storage-konto och Azure SQL Database till din data fabrik. En länkad tjänst egenskap som är speciell för Azure Blob Storage finns i avsnittet [länkade tjänst egenskaper](#linked-service-properties) .
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange BLOB-behållaren och mappen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange SQL-tabellen i Azure SQL Database som innehåller data som kopieras från blob-lagringen. För data uppsättnings egenskaper som är speciella för Azure Blob Storage, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du BlobSource som källa och SqlSink som mottagare för kopierings aktiviteten. På samma sätt kan du använda SqlSource och BlobSink i kopierings aktiviteten om du kopierar från Azure SQL Database till Azure Blob Storage. Information om kopiera aktivitets egenskaper som är speciella för Azure Blob Storage finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från ett Azure-Blob Storage finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-blob-storage  ) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Blob Storage.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
Det finns två typer av länkade tjänster som du kan använda för att länka en Azure Storage till en Azure Data Factory. De är: **AzureStorage** länkade tjänst-och **AzureStorageSas** -länkade tjänster. Den länkade tjänsten Azure Storage tillhandahåller data fabriken med global åtkomst till Azure Storage. Den länkade tjänsten Azure Storage SAS (Shared Access Signature) tillhandahåller data fabriken med begränsad/tidsbunden åtkomst till Azure Storage. Det finns inga andra skillnader mellan dessa två länkade tjänster. Välj den länkade tjänst som passar dina behov. I följande avsnitt finns mer information om dessa två länkade tjänster.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en data uppsättning som representerar indata eller utdata i ett Azure-Blob Storage anger du egenskapen type för data uppsättningen till: **AzureBlob**. Ange egenskapen **linkedServiceName** för data uppsättningen till namnet på Azure Storage eller Azure Storage länkade SAS-tjänsten.  Typ egenskaperna för data uppsättningen anger BLOB- **behållaren** och **mappen** i blob-lagringen.

En fullständig lista över JSON-avsnitt & egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar alla typer av data uppsättningar (Azure SQL, Azure Blob, Azure Table osv.).

Data Factory stöder följande CLS-kompatibla .NET-baserade typ värden för att tillhandahålla typ information i "struktur" för data källor av typen Schema-on-Read, till exempel Azure Blob: Int16, Int32, Int64, enkel, Double, decimal, byte [], bool, sträng, GUID, DateTime, DateTimeOffset, TimeSpan. Data Factory utför automatiskt typ konverteringar när data flyttas från ett käll data lager till ett data lager för mottagare.

**TypeProperties** -avsnittet skiljer sig åt för varje typ av data uppsättning och ger information om platsen, formatet osv, för data i data lagret. Avsnittet typeProperties för data uppsättning av typen **AzureBlob** data uppsättning har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till behållaren och mappen i blob-lagringen. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namn på blobben. fileName är valfritt och Skift läges känsligt.<br/><br/>Om du anger ett fil namn fungerar aktiviteten (inklusive kopia) på den aktuella blobben.<br/><br/>Om inget fil namn har angetts innehåller kopian alla blobbar i folderPath för indata-datauppsättning.<br/><br/>Om inget **fil namn** har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren, skulle namnet på den genererade filen ha följande format: `Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett fil namn för Time Series-data. FolderPath kan till exempel vara parameterstyrda för varje timme med data. Mer information och exempel finns i avsnittet om att [använda partitionedBy-egenskapen](#using-partitionedby-property) . |Inga |
| format | Följande format typer **stöds: text** format, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange egenskapen **Type** under format till något av dessa värden. Mer information finns i [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-format](data-factory-supported-file-and-compression-formats.md#avro-format), Orc- [format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) -avsnitt. <br><br> Om du vill **Kopiera filer som är** mellan filbaserade butiker (binär kopia), hoppar du över avsnittet format i definitionerna för in-och utdata-datauppsättningar. |Inga |
| komprimering | Ange typ och nivå för komprimeringen för data. De typer som stöds är: **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är: **optimalt** och **snabbast**. Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

### <a name="using-partitionedby-property"></a>Använda egenskapen partitionedBy
Som vi nämnt i föregående avsnitt kan du ange ett dynamiskt folderPath och ett fil namn för Time Series-data med egenskapen **partitionedBy** , [Data Factory Functions och systemvariablerna](data-factory-functions-variables.md).

Mer information om data uppsättningar för Time Series, schemaläggning och segment finns i [skapa data uppsättningar](data-factory-create-datasets.md) och [Schemaläggning & körnings](data-factory-scheduling-and-execution.md) artiklar.

#### <a name="sample-1"></a>Exempel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I det här exemplet ersätts {slice} med värdet för Data Factory systemvariabeln SliceStart i det angivna formatet (YYYYMMDDHH). SliceStart refererar till Start tiden för sektorn. FolderPath är olika för varje sektor. Till exempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104

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

I det här exemplet extraheras år, månad, dag och tid för SliceStart till separata variabler som används av folderPath-och fileName-egenskaperna.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata och principer är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i avsnittet **typeProperties** i aktiviteten varierar beroende på varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare. Om du flyttar data från en Azure-Blob Storage anger du käll typen i kopierings aktiviteten till **BlobSource**. På samma sätt, om du flyttar data till en Azure-Blob Storage, ställer du in mottagar typen i kopierings aktiviteten till **BlobSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av BlobSource och BlobSink.

**BlobSource** stöder följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| rekursiva |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant (standardvärde), falskt |Inga |

**BlobSink** stöder följande egenskaper **typeProperties** avsnittet:

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopierings beteendet när källan är BlobSource eller FileSystem. |<b>PreserveHierarchy</b>: filens hierarki bevaras i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns på den första nivån i målmappen. Målfilen har ett namn som skapats automatiskt. <br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen/BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars skapas fil namnet automatiskt. |Inga |

**BlobSource** stöder också dessa två egenskaper för bakåtkompatibilitet.

* **treatEmptyAsNull**: anger om du vill behandla null-värden eller tomma strängar som null-värden.
* **skipHeaderLineCount** – anger hur många rader som behöver hoppas över. Den kan endast användas när indata-dataset använder text form.

På samma sätt stöder **BlobSink** följande egenskap för bakåtkompatibilitet.

* **blobWriterAddHeader**: anger om du vill lägga till ett kolumn definitions huvud vid skrivning till en data uppsättning för utdata.

Data uppsättningar stöder nu följande egenskaper som implementerar samma funktion: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Följande tabell innehåller vägledning om hur du använder de nya egenskaperna för data uppsättningen i stället för dessa egenskaper för BLOB-källan/mottagare.

| Kopiera aktivitets egenskap | Data uppsättnings egenskap |
|:--- |:--- |
| skipHeaderLineCount på BlobSource |skipLineCount och firstRowAsHeader. Rader hoppas över först och sedan läses den första raden in som en rubrik. |
| treatEmptyAsNull på BlobSource |treatEmptyAsNull för indata-datauppsättning |
| blobWriterAddHeader på BlobSink |firstRowAsHeader för data uppsättning för utdata |

Mer information om dessa egenskaper finns i avsnittet [Ange](data-factory-supported-file-and-compression-formats.md#text-format) text format.

### <a name="recursive-and-copybehavior-examples"></a>rekursiva och copyBehavior-exempel
I det här avsnittet beskrivs det resulterande beteendet för kopierings åtgärden för olika kombinationer av rekursiva och copyBehavior värden.

| rekursiva | copyBehavior | Resulterande beteende |
| --- | --- | --- |
| true |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med samma struktur som källan<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för File5 |
| true |mergeFiles |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>mål-Mapp1 skapas med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Innehållet i fil1 + Fil2 + File3 + File4 + File 5 sammanfogas till en fil med automatiskt genererat fil namn |
| falskt |preserveHierarchy |För en källmapp Mapp1 med följande struktur: <br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |flattenHierarchy |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatiskt genererat namn för Fil2<br/><br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |
| falskt |mergeFiles |För en källmapp Mapp1 med följande struktur:<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>målmappen Mapp1 skapas med följande struktur<br/><br/>Mapp1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fil1 + Fil2-innehåll sammanfogas till en fil med automatiskt genererat fil namn. automatiskt genererat namn för fil1<br/><br/>Subfolder1 med File3, File4 och File5 hämtas inte. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Genom gång: Använd kopiera guiden för att kopiera data till/från Blob Storage
Nu ska vi titta på hur du snabbt kopierar data till/från en Azure Blob Storage. I den här genom gången är både käll-och mål data lager av typen: Azure Blob Storage. Pipelinen i den här genom gången kopierar data från en mapp till en annan mapp i samma BLOB-behållare. Den här genom gången är avsiktligt enkel att Visa inställningar eller egenskaper när du använder Blob Storage som källa eller mottagare.

### <a name="prerequisites"></a>Förutsättningar
1. Skapa ett allmänt **Azure Storage konto** om du inte redan har ett. Du använder Blob Storage som både **käll** -och **mål** data lager i den här genom gången. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
2. Skapa en BLOB-behållare med namnet **adfblobconnector** i lagrings kontot.
4. Skapa en mapp med namnet **inmatade** i behållaren **adfblobconnector** .
5. Skapa en fil med namnet **emp.txt** med följande innehåll och ladda upp den till mappen **indata** med hjälp av verktyg som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Skapa data fabriken
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapa en resurs** i det övre vänstra hörnet, klicka på **information + analys** och klicka på **Data Factory**.
3. I fönstret **ny data fabrik** :  
    1. Ange **ADFBlobConnectorDF** som **namn**. Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: `*Data factory name "ADFBlobConnectorDF" is not available` ändrar du namnet på data fabriken (till exempel yournameADFBlobConnectorDF) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
    2. Välj din Azure- **prenumeration**.
    3. För resurs grupp väljer du **Använd befintlig** för att välja en befintlig resurs grupp (eller) Välj **Skapa ny** för att ange ett namn för en resurs grupp.
    4. Välj **plats** för datafabriken.
    5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.
    6. Klicka på **Skapa**.
3. När den har skapats visas **Data Factory** bladet som visas på följande bild:  ![ Data Factory-Startsida](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Guiden Kopiera
1. På Data Factory start sida klickar du på panelen **Kopiera data** för att starta **Kopiera data guiden** på en separat flik.  

    > [!NOTE]
    > Om du ser att webbläsaren har fastnat på "auktoriserar...", inaktiverar/avmarkerar du inställningen **blockera cookies från tredje part och plats data** (eller) behåller den aktive rad och skapar ett undantag för **login.microsoftonline.com** . försök sedan starta guiden igen.
2. På sidan **Egenskaper**:
    1. Ange **CopyPipeline** som **uppgifts namn**. Uppgifts namnet är namnet på pipelinen i din data fabrik.
    2. Ange en **Beskrivning** av uppgiften (valfritt).
    3. För **aktivitets takt eller aktivitets schema** bör du behålla alternativet **kör regelbundet på schema** . Om du bara vill köra den här uppgiften en gång i stället för att köras upprepade gånger enligt ett schema väljer du **kör en gång nu**. Om du väljer alternativet **kör en gång nu** skapas en [engångs pipeline](data-factory-create-pipelines.md#onetime-pipeline) .
    4. Behåll inställningarna för **återkommande mönster**. Den här aktiviteten kör dagligen mellan start-och slut tiderna som du anger i nästa steg.
    5. Ändra **start datum tiden** till **04/21/2017**.
    6. Ändra **slutdatumets tid** till **04/25/2017**. Du kanske vill ange datumet i stället för att bläddra i kalendern.
    8. Klicka på **Nästa**.
        ![Verktyget Kopiera – sidan Egenskaper](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. På sidan **Källans datalager** klickar du på ikonen **Azure blobblagring**. Du kan använda den här sidan till att ange källans datalager för kopieringsaktiviteten. Du kan använda en länkad tjänst för ett befintligt datalager (eller) ange ett nytt datalager. Om du vill använda en befintlig länkad tjänst väljer du **från befintliga länkade tjänster** och väljer rätt länkad tjänst.
    ![Verktyget Kopiera – sidan Källans datalager](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. På sidan **Ange konto för Azure blobblagring**:
    1. Behåll det automatiskt genererade namnet för **anslutnings namnet**. Anslutnings namnet är namnet på den länkade tjänsten av typen: Azure Storage.
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din Azure-prenumeration eller Behåll **Välj alla** för **Azure-prenumeration**.
    4. Välj ett **Azure-lagringskonto** i listan med Azure-lagringskonton som är tillgängliga för den prenumeration som du har valt. Du kan också välja att ange inställningar för lagrings kontot manuellt genom att välja alternativet **ange manuellt** för **val metoden konto**.
    5. Klicka på **Nästa**.  
        ![Verktyget Kopiera – Ange konto för Azure blobblagring](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. På sidan **Välj indatafil eller mapp**:
    1. Dubbelklicka på **adfblobcontainer**.
    2. Välj **inmatade** och klicka på **Välj**. I den här genom gången väljer du mappen indatamängden. Du kan också välja emp.txt filen i mappen i stället.
        ![Verktyget kopiera – Välj indatafil eller mapp 1](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. På sidan **Välj indatafil eller mapp** :
    1. Bekräfta att **filen eller mappen** har angetts till **adfblobconnector/indata**. Om filerna finns i undermappar, till exempel 2017/04/01, 2017/04/02 och så vidare, anger du adfblobconnector/indata/{year}/{month}/{Day} för filen eller mappen. När du trycker på TABB av text rutan visas tre List rutor för att välja format för år (åååå), månad (MM) och dag (dd).
    2. Ange inte **kopierings filen rekursivt**. Välj det här alternativet för att rekursivt bläddra igenom mappar efter filer som ska kopieras till målet.
    3. Gör inte alternativet för **binär kopia** . Välj det här alternativet om du vill göra en binär kopia av käll filen till målet. Välj inte för den här genom gången så att du kan se fler alternativ på nästa sida.
    4. Bekräfta att **komprimerings typen** är inställd på **ingen**. Välj ett värde för det här alternativet om källfilerna komprimeras i något av de format som stöds.
    5. Klicka på **Nästa**.
    ![Verktyget kopiera – Välj indatafil eller mapp 2](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. På den **formatinställningar** kan du se avgränsare och det schema som upptäcks automatiskt i guiden genom att parsa filen.
    1. Bekräfta följande alternativ:  
        a. **Fil formatet** är inställt på **text format**. Du kan se alla format som stöds i list rutan. Till exempel: JSON, Avro, ORC, Parquet.
       b. **Kolumn avgränsaren** är inställd på `Comma (,)` . Du kan se de andra kolumn avgränsarna som stöds av Data Factory i den nedrullningsbara listan. Du kan också ange en anpassad avgränsare.
       c. **Rad avgränsaren** är inställd på `Carriage Return + Line feed (\r\n)` . Du kan se de andra rad avgränsarna som stöds av Data Factory i den nedrullningsbara listan. Du kan också ange en anpassad avgränsare.
       d. **Antalet rader för Skip** är inställt på **0**. Om du vill att några rader ska hoppas över överst i filen anger du numret här.
       e. Den **första data raden innehåller kolumn namn** som inte har angetts. Om källfilerna innehåller kolumn namn på den första raden väljer du det här alternativet.
       f. Alternativet **behandla tomt kolumn värde som null** är inställt.
    2. Expandera **Avancerade inställningar** om du vill se avancerade alternativ som är tillgängliga.
    3. Längst ned på sidan, se **förhands granskningen** av data från emp.txts filen.
    4. Klicka på fliken **schema** längst ned för att se schemat som kopierings guiden härledde genom att titta på data i käll filen.
    5. Klicka på **Nästa** när du har granskat avgränsarna och förhandsgranskat data.
    ![Verktyget Kopiera – Filformatinställningar](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. På **sidan mål data lager** väljer du **Azure Blob Storage** och klickar på **Nästa**. Du använder Azure-Blob Storage som både käll-och mål data lager i den här genom gången.  
    ![Verktyget kopiera – Välj mål data lager](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. På sidan **Ange Azure Blob Storage-konto** :  
    1. Ange **AzureStorageLinkedService** i fältet **anslutnings namn** .
    2. Kontrollera att alternativet **Från Azure-prenumerationer** har valts för **Metod för kontoval**.
    3. Välj din Azure- **prenumeration**.
    4. Välj ditt Azure Storage-konto.
    5. Klicka på **Nästa**.
10. På sidan **Välj utdatafil eller mapp** :  
    1. Ange **mappsökväg** som **adfblobconnector/output/{year}/{month}/{Day}**. **Fliken** ange.
    1. För **året** väljer du **åååå**.
    1. I **månaden** bekräftar du att den är inställd på **mm**.
    1. Bekräfta att den är inställd på **DD** för **dagen**.
    1. Bekräfta att **komprimerings typen** är inställd på **ingen**.
    1. Bekräfta att **kopierings beteendet** är inställt på **sammanfoga filer**. Om utdatafilen med samma namn redan finns, läggs det nya innehållet till i samma fil i slutet.
    1. Klicka på **Nästa**.
       ![Verktyget kopiera – Välj utdatafil eller mapp](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. På sidan **fil formats inställningar** granskar du inställningarna och klickar på **Nästa**. Ett av de ytterligare alternativen här är att lägga till en rubrik i utdatafilen. Om du väljer det alternativet läggs en rubrik rad till med namnen på kolumnerna från källans schema. Du kan byta namn på standard kolumn namnen när du visar schemat för källan. Du kan till exempel ändra den första kolumnen till förnamnet och andra kolumnen till efter namn. Sedan genereras utdatafilen med ett huvud med dessa namn som kolumn namn.
    ![Verktyget Kopiera – fil format inställningar för mål](media/data-factory-azure-blob-connector/file-format-destination.png)
12. På sidan **prestanda inställningar** bekräftar du att **moln enheter** och **parallella kopior** är inställda på **Auto** och klickar på Nästa. Mer information om de här inställningarna finns i [Kopiera aktivitets prestanda och justerings guide](data-factory-copy-activity-performance.md#parallel-copy).
    ![Verktyget kopiera – prestanda inställningar](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. På sidan **Sammanfattning** granskar du alla inställningar (aktivitets egenskaper, inställningar för källa och mål och kopiera inställningar) och klickar på **Nästa**.
    ![Verktyget kopiera – sammanfattnings sida](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Granska informationen på sidan **Sammanfattning** och klicka på **Slutför**. Guiden skapar två länkade tjänster, två datauppsättningar (indata och utdata) och en pipeline i datafabriken (från den plats där du startade guiden Kopiera).
    ![Verktyget kopiera – sidan distribution](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>Övervaka pipelinen (kopierings uppgift)

1. Klicka på länken `Click here to monitor copy pipeline` på sidan **distribution** .
2. Du bör se **programmet övervaka och hantera** på en separat flik.  ![Övervaka och hantera app](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Ändra **Start** tiden överst till `04/19/2017` och **slut** tid till och `04/27/2017` klicka sedan på **Använd**.
4. Du bör se fem aktivitets fönster i listan **AKTIVITETS fönster** . **WindowStart** -tiderna bör gälla alla dagar från pipelinen från början till pipelinens slut tider.
5. Klicka på knappen **Uppdatera** för **aktivitets Windows** -listan några gånger tills du ser att status för alla aktivitets fönster är inställd på klar.
6. Kontrol lera nu att utdatafilerna genereras i mappen utdata i adfblobconnector container. Du bör se följande mappstruktur i mappen utdata:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Detaljerad information om övervakning och hantering av data fabriker finns i artikeln [övervaka och hantera data Factory pipeline](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Data Factory entiteter
Gå nu tillbaka till fliken med Data Factory start sida. Observera att det finns två länkade tjänster, två data uppsättningar och en pipeline i din Data Factory nu.

![Data Factory start sida med entiteter](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Klicka på **Redigera och distribuera** för att starta Data Factory redigeraren.

![Data Factory-redigeraren](media/data-factory-azure-blob-connector/data-factory-editor.png)

Du bör se följande Data Factory entiteter i din data fabrik:

- Två länkade tjänster. En för källan och den andra för målet. Både de länkade tjänsterna refererar till samma Azure Storage konto i den här genom gången.
- Två data uppsättningar. En data uppsättning och en data uppsättning för utdata. I den här genom gången ska du använda samma BLOB-behållare men referera till olika mappar (indata och utdata).
- En pipeline. Pipelinen innehåller en kopierings aktivitet som använder en BLOB-källa och en BLOB-mottagare för att kopiera data från en Azure Blob-plats till en annan Azure Blob-plats.

I följande avsnitt finns mer information om dessa entiteter.

#### <a name="linked-services"></a>Länkade tjänster
Du bör se två länkade tjänster. En för källan och den andra för målet. I den här genom gången ser båda definitionerna likadana ut förutom namnen. Den länkade tjänstens **typ** är inställd på **AzureStorage**. Den viktigaste egenskapen för den länkade tjänst definitionen är **ConnectionString**, som används av Data Factory för att ansluta till ditt Azure Storage-konto vid körning. Ignorera egenskapen hubName i definitionen.

##### <a name="source-blob-storage-linked-service"></a>Länkad källa för blob-lagring
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Länkad Blob Storage-tjänst

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Mer information om Azure Storage länkade tjänsten finns i avsnittet [länkade tjänst egenskaper](#linked-service-properties) .

#### <a name="datasets"></a>Datauppsättningar
Det finns två data uppsättningar: en indata-datauppsättning och en utgående data uppsättning. Data uppsättningens typ är inställd på **AzureBlob** för båda.

Indata-datauppsättningen pekar på **adfblobconnector** BLOB-behållarens **indata** -mapp. Den **externa** egenskapen har angetts till **True** för den här data uppsättningen eftersom data inte produceras av pipelinen med den kopierings aktivitet som använder den här data uppsättningen som indata.

Data uppsättningen för utdata pekar på mappen **utdata** i samma BLOB-behållare. Data uppsättningen för utdata använder också året, månaden och dagen för **SliceStart** -systemvariabeln för att dynamiskt utvärdera sökvägen till utdatafilen. En lista över funktioner och systemvariabler som stöds av Data Factory finns i [Data Factory funktioner och systemvariabler](data-factory-functions-variables.md). Egenskapen **external** har angetts till **false** (standardvärde) eftersom den här data uppsättningen produceras av pipelinen.

Mer information om egenskaper som stöds av Azure Blob-datauppsättningen finns i avsnittet [Egenskaper för data uppsättning](#dataset-properties) .

##### <a name="input-dataset"></a>Indata-datauppsättning

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Data uppsättning för utdata

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Pipeline
Pipelinen har bara en aktivitet. Aktivitetens **typ** är inställd på **Kopiera**. I typ egenskaperna för aktiviteten finns det två avsnitt, en för källa och den andra för mottagare. Käll typen är inställd på **BlobSource** när aktiviteten kopierar data från en blob-lagring. Mottagar typen är inställd på **BlobSink** som aktivitet som kopierar data till en blob-lagring. Kopierings aktiviteten tar InputDataset-z4y som indata-och OutputDataset-z4y som utdata.

Mer information om egenskaper som stöds av BlobSource och BlobSink finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) .

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-exempel för att kopiera data till och från Blob Storage
I följande exempel finns exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Blob Storage och Azure SQL Database. Data kan dock kopieras **direkt** från någon av källorna till någon av de handfat som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med kopierings aktiviteten i Azure Data Factory.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-exempel: kopiera data från Blob Storage till SQL Database
Följande exempel visar:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [BlobSource](#copy-activity-properties) och [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

Exemplet kopierar Time Series-data från en Azure-blob till en Azure SQL-tabell varje timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure SQL-tjänst:**

```json
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
**Azure Storage länkad tjänst:**

```json
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
Azure Data Factory stöder två typer av Azure Storage länkade tjänster: **AzureStorage** och **AzureStorageSas**. För det första anger du anslutnings strängen som innehåller konto nyckeln och för den senare, anger du URL: en för signatur för delad åtkomst (SAS). Mer information finns i avsnittet [länkade tjänster](#linked-service-properties) .

**Data uppsättning för Azure Blob-indata:**

Data hämtas från en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dag delen av start tiden och fil namnet använder Tim delen av start tiden. inställningen "extern": "true" informerar Data Factory att tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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
**Data uppsättning för Azure SQL-utdata:**

Exemplet kopierar data till en tabell med namnet "min tabell" i Azure SQL Database. Skapa tabellen i din SQL Database med samma antal kolumner som du förväntar sig att BLOB CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**En kopierings aktivitet i en pipeline med BLOB-källa och SQL-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **BlobSource** och **mottagar** typ är inställd på **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-exempel: kopiera data från Azure SQL till Azure-Blob
Följande exempel visar:

1. En länkad tjänst av typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopierings aktivitet som använder [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) och [BlobSink](#copy-activity-properties).

Exemplet kopierar Time Series-data från en Azure SQL-tabell till en Azure-Blob per timme. De JSON-egenskaper som används i de här exemplen beskrivs i avsnitten som följer efter exemplen.

**Länkad Azure SQL-tjänst:**

```json
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
**Azure Storage länkad tjänst:**

```json
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
Azure Data Factory stöder två typer av Azure Storage länkade tjänster: **AzureStorage** och **AzureStorageSas**. För det första anger du anslutnings strängen som innehåller konto nyckeln och för den senare, anger du URL: en för signatur för delad åtkomst (SAS). Mer information finns i avsnittet [länkade tjänster](#linked-service-properties) .

**Data uppsättning för Azure SQL-indata:**

Exemplet förutsätter att du har skapat en tabell "Tabell" i Azure SQL och innehåller en kolumn med namnet "timestampcolumn" för Time Series-data.

Inställningen "External": "true" informerar Data Factory tjänst som tabellen är extern i data fabriken och produceras inte av en aktivitet i data fabriken.

```json
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

**Data uppsättning för Azure Blob-utdata:**

Data skrivs till en ny BLOB varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappens sökväg använder år, månad, dag och timmar delar av start tiden.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
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

**En kopierings aktivitet i en pipeline med SQL-källa och blob-mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlSource** och **mottagar** typ är inställd på **BlobSink**. SQL-frågan som angetts för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [!NOTE]
> Information om hur du mappar kolumner från käll data uppsättning till kolumner från mottagar data uppsättningen finns [i mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitets prestanda & justerings guide](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) i Azure Data Factory och olika sätt att optimera den.
