---
title: Flytta data med hjälp av kopierings aktivitet
description: 'Lär dig mer om data förflyttning i Data Factory pipelines: datamigrering mellan moln lager och mellan en lokal lagrings plats och ett moln lager. Använd kopierings aktivitet.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8a3fe99896ff244d2e35737919e6797141095acb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364026"
---
# <a name="move-data-by-using-copy-activity"></a>Flytta data med hjälp av kopierings aktivitet
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-data-movement-activities.md)
> * [Version 2 (aktuell version)](../copy-activity-overview.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [kopierings aktivitet i v2](../copy-activity-overview.md).

## <a name="overview"></a>Översikt
I Azure Data Factory kan du använda kopierings aktivitet för att kopiera data mellan lokala och molnbaserade data lager. När data har kopierats kan de transformeras och analyseras ytterligare. Du kan också använda kopierings aktivitet för att publicera omvandlings-och analys resultat för Business Intelligence (BI) och program förbrukning.

![Roll för kopierings aktivitet](media/data-factory-data-movement-activities/copy-activity.png)

Kopierings aktiviteten drivs av en säker, tillförlitlig, skalbar och [globalt tillgänglig tjänst](#global). Den här artikeln innehåller information om data förflyttning i Data Factory och kopierings aktivitet.

Först ska vi se hur datamigrering sker mellan två moln data lager och mellan ett lokalt data lager och ett moln data lager.

> [!NOTE]
> Mer information om aktiviteter i allmänhet finns i [förstå pipeliner och aktiviteter](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiera data mellan två moln data lager
När både käll-och mottagar data lager finns i molnet går kopierings aktiviteten igenom följande steg för att kopiera data från källan till mottagaren. Tjänsten som ger befogenhet att kopiera aktivitet:

1. Läser data från käll data lagret.
2. Utför serialisering/deserialisering, komprimering/expandering, kolumn mappning och typ konvertering. De här åtgärderna baseras på konfigurationerna av indata-DataSet, utdata-datauppsättning och kopierings aktivitet.
3. Skriver data till mål data lagret.

Tjänsten väljer automatiskt den optimala regionen för att utföra data flytten. Den här regionen är vanligt vis den som ligger närmast data lagret för mottagaren.

![Moln-till-moln-kopiering](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt data lager och ett moln data lager
Om du vill flytta data säkert mellan ett lokalt data lager och ett moln data lager, installerar du Data Management Gateway på den lokala datorn. Data Management Gateway är en agent som möjliggör hybrid data förflyttning och bearbetning. Du kan installera det på samma dator som själva data lagret eller på en annan dator som har åtkomst till data lagret.

I det här scenariot utför Data Management Gateway serialisering/deserialisering, komprimering/expandering, kolumn mappning och typ konvertering. Data flödar inte via den Azure Data Factory tjänsten. I stället skriver Data Management Gateway direkt data till mål arkivet.

![Kopiering lokalt till molnet](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Se [Flytta data mellan lokala och molnbaserade data lager](data-factory-move-data-between-onprem-and-cloud.md) för en introduktion och genom gång. Se [Data Management Gateway](data-factory-data-management-gateway.md) för detaljerad information om den här agenten.

Du kan också flytta data från/till data lager som stöds och som finns i Azure IaaS Virtual Machines (VM) med hjälp av Data Management Gateway. I det här fallet kan du installera Data Management Gateway på samma virtuella dator som själva data lagret, eller på en separat virtuell dator som har åtkomst till data lagret.

## <a name="supported-data-stores-and-formats"></a>Data lager och format som stöds
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

> [!NOTE] 
> Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder kan du använda en **anpassad aktivitet** i Data Factory med din egen logik för att kopiera/flytta data. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="supported-file-formats"></a>Filformat som stöds
Du kan använda kopierings aktiviteten för att **Kopiera filer som är** mellan två filbaserade data lager. du kan hoppa över [avsnittet format](data-factory-create-datasets.md) i både indata och utdata-datauppsättnings definitioner. Data kopieras effektivt utan serialisering/deserialisering.

Kopierings aktiviteten läser också från och skriver till filer i angivna format: **text, JSON, Avro, Orc och Parquet**, och komprimerings **-codec gzip, deflate, BZip2 och ZipDeflate** stöds. Se [fil-och komprimerings format som stöds](data-factory-supported-file-and-compression-formats.md) med information.

Du kan till exempel göra följande kopierings aktiviteter:

* Kopiera data i en SQL Server databas och skriva till Azure Data Lake Store i ORC-format.
* Kopiera filer i text format (CSV) från det lokala fil systemet och skriv till Azure blob i Avro-format.
* Kopiera zippade filer från det lokala fil systemet och expandera sedan land till Azure Data Lake Store.
* Kopiera data i formatet GZip-komprimerad text (CSV) från Azure blob och skriv till Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globalt tillgänglig data förflyttning
Azure Data Factory är endast tillgängligt i regionerna västra USA, östra USA och norra Europa. Tjänsten som har behörighet att kopiera aktiviteter är dock tillgänglig globalt i följande regioner och geografiska områden. Den globalt tillgängliga topologin garanterar effektiv data förflyttning som vanligt vis förhindrar hopp över flera regioner. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) för att få till gång till Data Factory och data förflyttning i en region.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiera data mellan moln data lager
När både käll-och mottagar data lager finns i molnet använder Data Factory en tjänst distribution i den region som ligger närmast mottagaren i samma geografiska område för att flytta data. Se tabellen nedan för mappning:

| Geografi för mål data lager | Region för mål data lagret | Region som används för data förflyttning |
|:--- |:--- |:--- |
| USA | East US | East US |
| &nbsp; | USA, östra 2 | USA, östra 2 |
| &nbsp; | Central US | Central US |
| &nbsp; | USA, norra centrala | USA, norra centrala |
| &nbsp; | USA, södra centrala | USA, södra centrala |
| &nbsp; | USA, västra centrala | USA, västra centrala |
| &nbsp; | USA, västra | USA, västra |
| &nbsp; | USA, västra 2 | USA, västra 2 |
| Kanada | Kanada, östra | Kanada, centrala |
| &nbsp; | Kanada, centrala | Kanada, centrala |
| Brasilien | Brasilien, södra | Brasilien, södra |
| Europa | Europa, norra | Europa, norra |
| &nbsp; | Europa, västra | Europa, västra |
| Storbritannien | Storbritannien, västra | Storbritannien, södra |
| &nbsp; | Storbritannien, södra | Storbritannien, södra |
| Asien och stillahavsområdet | Sydostasien | Sydostasien |
| &nbsp; | Asien, östra | Sydostasien |
| Australien | Australien, östra | Australien, östra |
| &nbsp; | Australien, sydöstra | Australien, sydöstra |
| Indien | Indien, centrala | Indien, centrala |
| &nbsp; | Indien, västra | Indien, centrala |
| &nbsp; | Indien, södra | Indien, centrala |
| Japan | Japan, östra | Japan, östra |
| &nbsp; | Japan, västra | Japan, östra |
| Korea | Sydkorea, centrala | Sydkorea, centrala |
| &nbsp; | Sydkorea, södra | Sydkorea, centrala |

Alternativt kan du uttryckligen ange regionen för Data Factory tjänst som ska användas för att utföra kopieringen genom att ange `executionLocation` egenskapen under kopierings aktivitet `typeProperties` . Värden som stöds för den här egenskapen visas i den **region som används för data förflyttnings** kolumnen. Observera att dina data går igenom den regionen över kabeln under kopieringen. Om du till exempel vill kopiera mellan Azure-butiker i Korea kan du ange `"executionLocation": "Japan East"` att den ska dirigeras genom den japanska regionen (se [exempel-JSON](#by-using-json-scripts) som referens).

> [!NOTE]
> Om regionen för mål data lagret inte finns i föregående lista eller inte kan identifieras som standard kopierings aktivitet Miss lyckas i stället för att gå igenom en alternativ region, om inte `executionLocation` har angetts. Listan region som stöds kommer att expanderas över tid.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt data lager och ett moln data lager
När data kopieras mellan lokala (eller Azure Virtual Machines/IaaS) och moln lager, utför [Data Management Gateway](data-factory-data-management-gateway.md) data förflyttning på en lokal dator eller virtuell dator. Data flödar inte genom tjänsten i molnet, om du inte använder funktionen för [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) . I det här fallet flödar data genom mellanlagringen av Azure Blob Storage innan det skrivs till data lagret för mottagare.

## <a name="create-a-pipeline-with-copy-activity"></a>Skapa en pipeline med kopierings aktivitet
Du kan skapa en pipeline med kopierings aktivitet på ett par olika sätt:

### <a name="by-using-the-copy-wizard"></a>Med hjälp av guiden Kopiera
Med guiden Data Factory kopiering kan du skapa en pipeline med kopierings aktivitet. Med den här pipelinen kan du kopiera data från källor som stöds till destinationer *utan att skriva JSON* -definitioner för länkade tjänster, data uppsättningar och pipeliner. Mer information om guiden finns i [Data Factory kopierings guiden](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>Med hjälp av JSON-skript
Du kan använda Data Factory Editor i Visual Studio eller Azure PowerShell för att skapa en JSON-definition för en pipeline (genom att använda kopierings aktiviteten). Sedan kan du distribuera den för att skapa pipelinen i Data Factory. Se [självstudie: Använd kopierings aktivitet i en Azure Data Factory pipeline](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för en själv studie kurs med stegvisa instruktioner.    

JSON-egenskaper (t. ex. namn, beskrivning, indata-och utdata tabeller och principer) är tillgängliga för alla typer av aktiviteter. Vilka egenskaper som är tillgängliga i `typeProperties` avsnittet av aktiviteten varierar beroende på varje aktivitets typ.

För kopierings aktiviteten `typeProperties` varierar avsnittet beroende på typerna av källor och mottagare. Klicka på en källa/mottagare i avsnittet [källor och mottagare som stöds och](#supported-data-stores-and-formats) lär dig mer om typ egenskaper som kopierings aktivitet stöder för det data lagret.

Här är ett exempel på en JSON-definition:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Schemat som definieras i data uppsättningen för utdata avgör när aktiviteten körs (till exempel: **daglig**, frekvens som **dag** och intervall som **1**). Aktiviteten kopierar data från en data uppsättning (**källa**) till en data uppsättning för utdata (**Sink**).

Du kan ange fler än en indata-datauppsättning för kopiering av aktivitet. De används för att verifiera beroenden innan aktiviteten körs. Men endast data från den första data uppsättningen kopieras till mål data uppsättningen. Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda-och justerings guiden för kopierings aktivitet](data-factory-copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för data flytten (kopierings aktiviteten) i Azure Data Factory. Den visar även observerade prestanda under intern testning och diskuterar olika sätt att optimera prestanda för kopierings aktiviteten.

## <a name="fault-tolerance"></a>Feltolerans
Som standard slutar kopierings aktiviteten att kopiera data och returnera fel när de stöter på inkompatibla data mellan källa och mottagare. även om du uttryckligen kan konfigurera för att hoppa över och logga de inkompatibla raderna och bara kopiera dessa kompatibla data så att kopieringen är klar. Mer information finns i [fel toleransen för kopierings aktiviteten](data-factory-copy-activity-fault-tolerance.md) .

## <a name="security-considerations"></a>Säkerhetsöverväganden
Se [säkerhets aspekter](data-factory-data-movement-security-considerations.md), som beskriver säkerhets infrastruktur som tjänster för data flyttning i Azure Data Factory använda för att skydda dina data.

## <a name="scheduling-and-sequential-copy"></a>Schemaläggning och sekventiell kopiering
Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) för detaljerad information om hur schemaläggning och körning fungerar i Data Factory. Det går att köra flera kopierings åtgärder en efter varandra på ett sekventiellt/ordnat sätt. Se avsnittet [Kopiera sekventiellt](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) .

## <a name="type-conversions"></a>Typkonverteringar
Olika data lager har olika typer av egna system. Kopierings aktiviteten utför automatiska typ konverteringar från käll typer till mottagar typer med följande två stegs metod:

1. Konvertera från interna käll typer till en .NET-typ.
2. Konvertera från en .NET-typ till en typ av intern mottagare.

Mappningen från ett internt typ system till en .NET-typ för ett data lager finns i respektive data lager artikel. (Klicka på länken för den aktuella länken i tabellen med data lager som stöds). Du kan använda dessa mappningar för att fastställa lämpliga typer när du skapar tabeller, så att kopierings aktiviteten utför rätt konverteringar.

## <a name="next-steps"></a>Nästa steg
* Mer information om kopierings aktiviteten finns i [Kopiera data från Azure Blob Storage till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Information om hur du flyttar data från ett lokalt data lager till ett moln data lager finns i [Flytta data från lokal plats till moln data lager](data-factory-move-data-between-onprem-and-cloud.md).
