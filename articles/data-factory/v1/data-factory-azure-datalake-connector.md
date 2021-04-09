---
title: Kopiera data till och från Azure Data Lake Storage Gen1
description: Lär dig hur du kopierar data till och från Data Lake Store med hjälp av Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 8bdfceff562ae2501a9d95774f7134669e929b7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379411"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiera data till och från Data Lake Storage Gen1 med Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-datalake-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Azure Data Lake Storage gen1 Connector i v2](../connector-azure-data-lake-store.md).

Den här artikeln förklarar hur du använder kopierings aktivitet i Azure Data Factory för att flytta data till och från Azure Data Lake Storage Gen1 (tidigare kallat Azure Data Lake Store). Den bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , en översikt över data förflyttning med kopierings aktivitet.

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure Data Lake Store** till följande data lager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande data lager **till Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Skapa ett Data Lake Store konto innan du skapar en pipeline med kopierings aktivitet. Mer information finns i [Kom igång med Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Autentiseringstyper som stöds
Data Lake Store-anslutningen stöder följande typer av autentisering:
* Autentisering av tjänstens huvudnamn
* OAuth-autentisering (User Credential)

Vi rekommenderar att du använder tjänstens huvud namns autentisering, särskilt för schemalagda data kopior. Förfallo beteende för token kan uppstå med autentisering av användarautentisering. Konfigurations information finns i avsnittet [Egenskaper för länkad tjänst](#linked-service-properties) .

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med en kopierings aktivitet som flyttar data till/från en Azure Data Lake Store med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline för att kopiera data är att använda **guiden Kopiera**. En själv studie kurs om hur du skapar en pipeline med hjälp av guiden Kopiera finns i [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md).

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API** och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa en **data fabrik**. En data fabrik kan innehålla en eller flera pipeliner.
2. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik. Om du t. ex. kopierar data från en Azure Blob Storage till en Azure Data Lake Store, skapar du två länkade tjänster för att länka ditt Azure Storage-konto och Azure Data Lake lagra till din data fabrik. För länkade tjänst egenskaper som är speciella för Azure Data Lake Store, se avsnittet [länkade tjänst egenskaper](#linked-service-properties) .
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden. I exemplet som nämns i det sista steget skapar du en data uppsättning för att ange BLOB-behållaren och mappen som innehåller indata. Du kan också skapa en annan data uppsättning för att ange mappen och sökvägen i Data Lake Store som innehåller data som kopieras från blob-lagringen. För data uppsättnings egenskaper som är speciella för Azure Data Lake Store, se avsnittet [Egenskaper för data mängd](#dataset-properties) .
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata. I exemplet ovan använder du BlobSource som källa och AzureDataLakeStoreSink som mottagare för kopierings aktiviteten. På samma sätt kan du använda AzureDataLakeStoreSource och BlobSink i kopierings aktiviteten om du kopierar från Azure Data Lake Store till Azure Blob Storage. Information om kopierings aktiviteter som är speciell för Azure Data Lake Store finns i avsnittet [Kopiera aktivitets egenskaper](#copy-activity-properties) . Om du vill ha mer information om hur du använder ett data lager som källa eller mottagare klickar du på länken i föregående avsnitt för ditt data lager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet. Exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till/från en Azure Data Lake Store finns i avsnittet om [JSON-exempel](#json-examples-for-copying-data-to-and-from-data-lake-store) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Data Lake Store.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst
En länkad tjänst länkar ett data lager till en data fabrik. Du skapar en länkad tjänst av typen **AzureDataLakeStore** för att länka dina data Lake Store data till din data fabrik. I följande tabell beskrivs JSON-element som är speciella för Data Lake Store länkade tjänster. Du kan välja mellan tjänstens huvud namn och autentisering av autentiseringsuppgifter för användare.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **bastyp** | Egenskapen Type måste anges till **AzureDataLakeStore**. | Ja |
| **dataLakeStoreUri** | Information om Azure Data Lake Store-kontot. Den här informationen tar något av följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/` . | Ja |
| **subscriptionId** | ID för Azure-prenumeration som Data Lake Stores kontot tillhör. | Krävs för Sink |
| **resourceGroupName** | Namnet på den Azure-resurs grupp som Data Lake Store kontot tillhör. | Krävs för Sink |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvud namn (rekommenderas)
Om du vill använda tjänstens huvud namns autentisering registrerar du en program enhet i Azure Active Directory (Azure AD) och ger den åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID
* Program nyckel
* Klient-ID:t

> [!IMPORTANT]
> Se till att ge tjänstens huvud behörighet rätt behörighet i Azure Data Lake Store:
>- **Om du vill använda data Lake Store som källa**, bevilja du minst **läsa + kör** data åtkomst behörighet för att visa och kopiera innehållet i en mapp, eller **Läs** behörighet för att kopiera en enskild fil. Inget krav på åtkomst kontroll på konto nivå.
>- **Om du vill använda data Lake Store som mottagare** ger du minst behörigheten **Skriv + kör** data åtkomst för att skapa underordnade objekt i mappen. Och om du använder Azure IR för att öka kopieringen (både källa och mottagare finns i molnet), så att du kan Data Factory identifiera Data Lake Store region, bevilja minst **läsar** roll i konto åtkomst kontroll (IAM). Om du vill undvika den här IAM-rollen [anger du executionLocation](data-factory-data-movement-activities.md#global) med platsen för data Lake Store i kopierings aktiviteten.
>- Om du **använder kopierings guiden för att redigera pipeliner ger du** minst **läsar** roll i konto åtkomst kontroll (IAM). Du kan också bevilja minst **Läs** -och kör behörighet till data Lake Store roten ("/") och dess underordnade. Annars kan du se meddelandet "de angivna autentiseringsuppgifterna är ogiltiga."

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **tenant** | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |

**Exempel: autentisering av tjänstens huvud namn**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autentisering av autentiseringsuppgifter för användare
Du kan också använda autentisering med användarens autentiseringsuppgifter för att kopiera från eller till Data Lake Store genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **auktoriseringsregeln** | Klicka på knappen **auktorisera** i Data Factory redigeraren och ange autentiseringsuppgifterna som tilldelar URL: en automatiskt genererad auktorisering till den här egenskapen. | Ja |
| **sessionId** | OAuth-sessions-ID från OAuth-auktoriseringsbegäran. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory redigeraren. | Ja |

> [!IMPORTANT]
> Se till att ge användaren rätt behörighet i Azure Data Lake Store:
>- **Om du vill använda data Lake Store som källa**, bevilja du minst **läsa + kör** data åtkomst behörighet för att visa och kopiera innehållet i en mapp, eller **Läs** behörighet för att kopiera en enskild fil. Inget krav på åtkomst kontroll på konto nivå.
>- **Om du vill använda data Lake Store som mottagare** ger du minst behörigheten **Skriv + kör** data åtkomst för att skapa underordnade objekt i mappen. Och om du använder Azure IR för att öka kopieringen (både källa och mottagare finns i molnet), så att du kan Data Factory identifiera Data Lake Store region, bevilja minst **läsar** roll i konto åtkomst kontroll (IAM). Om du vill undvika den här IAM-rollen [anger du executionLocation](data-factory-data-movement-activities.md#global) med platsen för data Lake Store i kopierings aktiviteten.
>- Om du **använder kopierings guiden för att redigera pipeliner ger du** minst **läsar** roll i konto åtkomst kontroll (IAM). Du kan också bevilja minst **Läs** -och kör behörighet till data Lake Store roten ("/") och dess underordnade. Annars kan du se meddelandet "de angivna autentiseringsuppgifterna är ogiltiga."

**Exempel: autentisering med användar behörighet**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Förfallo datum för token
Den auktoriseringskod som du genererar med hjälp av knappen **auktorisera** upphör att gälla efter en viss tid. Följande meddelande innebär att autentiseringstoken har upphört att gälla:

Åtgärds fel för autentiseringsuppgift: invalid_grant-AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: den angivna åtkomst tilldelningen har förfallit eller återkallats. Spårnings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelations-ID: fac30a0c-6BE6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21-09-31Z.

Följande tabell visar förfallo tiderna för olika typer av användar konton:

| Användar typ | Upphör att gälla efter |
|:--- |:--- |
| Användar konton som *inte* hanteras av Azure Active Directory (till exempel @hotmail.com eller @live.com ) |12 timmar |
| Användar konton som hanteras av Azure Active Directory |14 dagar efter den senaste sektor körningen <br/><br/>90 dagar, om en sektor som baseras på en OAuth-baserad länkad tjänst körs minst en gång var 14: e dag |

Om du ändrar ditt lösen ord innan giltighets tiden för token upphör att gälla, förfaller token omedelbart. Meddelandet som nämns ovan visas i det här avsnittet.

Du kan auktorisera kontot med hjälp av knappen **auktorisera** när token upphör att distribuera om den länkade tjänsten. Du kan också skapa värden för **SessionID** -och **Authorization** -egenskaperna program mässigt med hjälp av följande kod:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Mer information om de Data Factory klasser som används i koden finns i avsnittet om klassen [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)och [AuthorizationSessionGetResponse Class](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) . Lägg till en referens till versionen `2.9.10826.1824` av `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` för den `WindowsFormsWebAuthenticationDialog` klass som används i koden.

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** När du kopierar data **till** Azure Data Lake Store, om kopierings aktiviteten inte fungerar med följande fel:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Rotor saken:** Det finns två möjliga orsaker:

1. `resourceGroupName`Och/eller `subscriptionId` har angetts i Azure Data Lake Store länkade tjänsten är felaktigt.
2. Användaren eller tjänstens huvud namn har inte den behörighet som krävs.

**Lösning:**

1. Kontrol lera att `subscriptionId` och `resourceGroupName` du anger i den länkade tjänsten `typeProperties` verkligen är de som ditt data Lake-konto tillhör.

2. Se till att du beviljar minst **läsar** roll till användaren eller tjänstens huvud namn på data Lake-kontot. Så här gör du det:

    1. Gå till Azure Portal-> ditt Data Lake Store-konto
    2. Klicka på **åtkomst kontroll (IAM)** på bladet i data Lake Store
    3. Klicka på **Lägg till roll tilldelning**
    4. Ange **roll** som **läsare** och välj användaren eller tjänstens huvud namn som du vill använda för kopiering för att ge åtkomst

3. Om du inte vill ge **läsar** rollen till användarens eller tjänstens huvud namn, är det ett alternativ att [uttryckligen ange en körnings plats](data-factory-data-movement-activities.md#global) i kopierings aktiviteten med platsen för data Lake Store. Exempel:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Egenskaper för datamängd
Om du vill ange en data uppsättning som representerar indata i en Data Lake Store anger du egenskapen **Type** för data uppsättningen till **AzureDataLakeStore**. Ange egenskapen **linkedServiceName** för data uppsättningen till namnet på den länkade tjänsten Data Lake Store. En fullständig lista över JSON-avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt i en data uppsättning i JSON, till exempel **struktur**, **tillgänglighet** och **policy**, liknar alla typer av data uppsättningar (till exempel Azure SQL Database, Azure blob och Azure Table). Avsnittet **typeProperties** är olika för varje typ av data uppsättning och innehåller information som till exempel plats och format för data i data lagret.

Avsnittet **typeProperties** för en data uppsättning av typen **AzureDataLakeStore** innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **folderPath** |Sökväg till behållaren och mappen i Data Lake Store. |Ja |
| **fileName** |Namnet på filen i Azure Data Lake Store. Egenskapen **filename** är valfri och Skift läges känslig. <br/><br/>Om du anger **filename** fungerar aktiviteten (inklusive kopia) på den aktuella filen.<br/><br/>Om inget **fil namn** har angetts innehåller Copy alla filer i **folderPath** i data uppsättningen för indata.<br/><br/>Om inget **fil namn** har angetts för en data uppsättning för utdata och **preserveHierarchy** inte har angetts i aktivitets mottagaren, är namnet på den genererade filen i formatet `Data._Guid_.txt` . Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Inga |
| **partitionedBy** |Egenskapen **partitionedBy** är valfri. Du kan använda den för att ange en dynamisk sökväg och ett fil namn för Time Series-data. **FolderPath** kan till exempel vara parameterstyrda för varje timme med data. Mer information och exempel finns i egenskapen partitionedBy. |Inga |
| **formatering** | Följande format typer stöds: text **Forms**, **JsonFormat**, **AvroFormat**, **OrcFormat** och **ParquetFormat**. Ange egenskapen **Type** under **format** till något av dessa värden. Mer information finns i avsnitten [text format](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet format](data-factory-supported-file-and-compression-formats.md#parquet-format) i fil- [och komprimerings format som stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel. <br><br> Om du vill kopiera filer "i befintligt skick" mellan filbaserade butiker (binär kopia) hoppar du över `format` avsnittet i både indata och utdata-datauppsättnings definitioner. |Inga |
| **komprimering** | Ange typ och nivå för komprimeringen för data. Typer som stöds är **gzip**, **DEFLATE**, **BZip2** och **ZipDeflate**. De nivåer som stöds är **optimala** och **snabbaste**. Mer information finns i [fil-och komprimerings format som stöds av Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

### <a name="the-partitionedby-property"></a>Egenskapen partitionedBy
Du kan ange dynamiska **folderPath** -och **filename** -egenskaper för Time Series-data med egenskapen **partitionedBy** , Data Factory Functions och systemvariabler. Mer information finns i artikeln [Azure Data Factory-Functions och system Variables](data-factory-functions-variables.md) .


I följande exempel `{Slice}` ersätts med värdet för variabeln Data Factory system `SliceStart` i det angivna formatet ( `yyyyMMddHH` ). Namnet `SliceStart` avser start tiden för sektorn. `folderPath`Egenskapen skiljer sig åt för varje sektor, som i `wikidatagateway/wikisampledataout/2014100103` eller `wikidatagateway/wikisampledataout/2014100104` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

I följande exempel extraheras året, månaden, dagen och tiden `SliceStart` till separata variabler som används av- `folderPath` och- `fileName` egenskaperna:
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
Mer information om data uppsättningar för Time-serien, schemaläggning och segment finns i [data uppsättningarna i Azure Data Factory](data-factory-create-datasets.md) och [Data Factory schemaläggning och körning av](data-factory-scheduling-and-execution.md) artiklar.


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och policy är tillgängliga för alla typer av aktiviteter.

Egenskaperna som är tillgängliga i avsnittet **typeProperties** i en aktivitet varierar beroende på varje aktivitets typ. För en kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

**AzureDataLakeStoreSource** stöder följande egenskap i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| **rekursiva** |Anger om data ska läsas rekursivt från undermapparna eller endast från den angivna mappen. |Sant (standardvärde), falskt |Inga |

**AzureDataLakeStoreSink** stöder följande egenskaper i avsnittet **typeProperties** :

| Egenskap | Beskrivning | Tillåtna värden | Obligatorisk |
| --- | --- | --- | --- |
| **copyBehavior** |Anger kopierings beteendet. |<b>PreserveHierarchy</b>: filens hierarki bevaras i målmappen. Den relativa sökvägen till käll filen till källmappen är identisk med den relativa sökvägen till mål filen i målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas på den första nivån i målmappen. Målattribut skapas med namn som skapats automatiskt.<br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om filen eller BLOB-namnet anges, är det sammanslagna fil namnet det angivna namnet. Annars genereras fil namnet automatiskt. |Inga |

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

## <a name="supported-file-and-compression-formats"></a>Fil- och komprimeringsformat som stöds
Mer information finns i [fil-och komprimerings format i Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artikel.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-exempel för att kopiera data till och från Data Lake Store
I följande exempel finns exempel på JSON-definitioner. Du kan använda dessa exempel definitioner för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). I exemplen visas hur du kopierar data till och från Data Lake Store och Azure Blob Storage. Data kan dock kopieras _direkt_ från någon av källorna till någon av de mottagare som stöds. Mer information finns i avsnittet "data lager och format som stöds" i artikeln [Flytta data med hjälp av kopiera aktivitet](data-factory-data-movement-activities.md) .

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Exempel: kopiera data från Azure Blob Storage till Azure Data Lake Store
Exempel koden i det här avsnittet visar:

* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [AzureDataLakeStoreSink](#copy-activity-properties).

I exemplen visas hur Time Series-data från Azure Blob Storage kopieras till Data Lake Store varje timme.

**Azure Storage länkad tjänst**

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

**Azure Data Lake Store länkad tjänst**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Konfigurations information finns i avsnittet [Egenskaper för länkad tjänst](#linked-service-properties) .
>

**Data uppsättning för Azure Blob-indata**

I följande exempel hämtas data från en ny BLOB varje timme ( `"frequency": "Hour", "interval": 1` ). Mappsökvägen och fil namnet för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder året, månaden och dags delen av start tiden. Fil namnet använder Tim delen av start tiden. `"external": true`Inställningen informerar Data Factory tjänsten om att tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Azure Data Lake Store data uppsättning för utdata**

I följande exempel kopieras data till Data Lake Store. Nya data kopieras till Data Lake Store varje timme.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med en BLOB-källa och en Data Lake Store mottagare**

I följande exempel innehåller pipelinen en kopierings aktivitet som är konfigurerad för att använda data uppsättningar för indata och utdata. Kopierings aktiviteten är schemalagd att köras varje timma. I JSON-definitionen för pipelinen `source` är typen inställt på `BlobSource` och `sink` typen är inställd på `AzureDataLakeStoreSink` .

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Exempel: kopiera data från Azure Data Lake Store till en Azure-Blob
Exempel koden i det här avsnittet visar:

* En länkad tjänst av typen [AzureDataLakeStore](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En indata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureDataLakeStore](#dataset-properties).
* En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [AzureDataLakeStoreSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Koden kopierar Time Series-data från Data Lake Store till en Azure-Blob varje timme.

**Azure Data Lake Store länkad tjänst**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Konfigurations information finns i avsnittet [Egenskaper för länkad tjänst](#linked-service-properties) .
>

**Azure Storage länkad tjänst**

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
**Azure Data Lake indata-datauppsättning**

I det här exemplet anger `"external"` att `true` informerar den Data Factory tjänsten som tabellen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
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
**Data uppsättning för Azure Blob-utdata**

I följande exempel skrivs data till en ny BLOB varje timme ( `"frequency": "Hour", "interval": 1` ). Mappsökvägen för blobben utvärderas dynamiskt baserat på Start tiden för den sektor som bearbetas. Mappsökvägen använder en del av året, månaden, dagen och timmarna i Start tiden.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**En kopierings aktivitet i en pipeline med en Azure Data Lake Store källa och en BLOB-mottagare**

I följande exempel innehåller pipelinen en kopierings aktivitet som är konfigurerad för att använda data uppsättningar för indata och utdata. Kopierings aktiviteten är schemalagd att köras varje timma. I JSON-definitionen för pipelinen `source` är typen inställt på `AzureDataLakeStoreSource` och `sink` typen är inställd på `BlobSink` .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

I definitionen för kopierings aktiviteten kan du också mappa kolumner från käll data uppsättningen till kolumner i mottagar data uppsättningen. Mer information finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Mer information om faktorer som påverkar prestanda för kopierings aktiviteter och hur du optimerar den finns i artikeln [Kopiera aktivitets prestanda och justerings guide](data-factory-copy-activity-performance.md) .