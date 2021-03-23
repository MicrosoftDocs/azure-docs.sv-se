---
title: Introduktion till Data Factory, en data integrerings tjänst
description: 'Detta är vad Azure Data Factory är: en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data.'
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 220c1969055ffaa5d1bea41ab547efd4596f8a20
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786472"
---
# <a name="introduction-to-azure-data-factory"></a>introduktion till Azure Data Factory 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-introduction.md)
> * [Version 2 (aktuell version)](../introduction.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [introduktionen till Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?
Hur utnyttjas befintliga data i företag? Är det möjligt att utöka information som genereras i molnet med referensdata från lokala datakällor eller andra olika datakällor? 

Föreställ dig ett spelföretag som vill samla in en stor mängd loggar som producerats av spel i molnet. Företaget vill analysera loggarna för att få insikter om kundernas preferenser, demografi, beteende vid användning med mera. Det vill också hitta möjligheter till merförsäljning och korsförsäljning, utveckla intressanta nya funktioner, driva på affärstillväxten och ge en bättre kundupplevelse överlag. 

När företaget analyserar loggarna måste de använda referensdata, till exempel kundinformation, spelinformation och information från en marknadsföringskampanj som finns i ett lokalt datalager. Därför behöver företaget mata in loggdata från molndatalagret och referensdata från det lokala datalagret. 

Sedan bearbetar företaget dessa data med Hadoop i molnet (Azure HDInsight). De vill publicera resultat data i ett informations lager i molnet, till exempel Azure Synapse Analytics eller ett lokalt data lager, till exempel SQL Server. Företaget vill att arbetsflödet ska köras en gång i veckan. 

Därför behöver företaget en plattform där de kan skapa ett arbetsflöde som matar in data från både lokala och molnbaserade datalager. Företaget måste också kunna omvandla eller bearbeta data med hjälp av befintliga beräkningstjänster som exempelvis Hadoop och sedan publicera resultaten i ett lokalt eller molnbaserat datalager som kan användas av BI-programmen. 

![Data Factory-översikt](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory är plattformen som ofta används för sådana typer av scenarier. Det är en *molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling*. Du kan använda Azure Data Factory för att utföra följande uppgifter: 

- Skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan mata in data från olika datalager.

- Bearbeta och omvandla data med hjälp av beräkningstjänster, till exempel Azure HDInsight Hadoop, Apache Spark, Azure Data Lake Analytics och Azure Machine Learning.

-  Publicera utdata till data lager som Azure Synapse Analytics för Business Intelligence-program (BI) att använda.  

Det är snarare en EL-plattform (Extract-and-Load) och sedan en TL-plattform (Transform-and-Load), än en traditionell ETL-plattform (Extract-Transform-and-Load). Omvandlingarna bearbetar data med hjälp av beräkningstjänster i stället för att lägga till härledda kolumner, räkna antalet rader, sortera data och så vidare. 

För närvarande är data som används och produceras av arbetsflöden i Azure Data Factory *tidsuppdelade* (varje timme, dagligen, varje vecka osv.). En pipeline kan till exempel läsa indata, bearbeta data och skapa utdata en gång om dagen. Du kan också köra ett arbetsflöde bara en gång.  
  

## <a name="how-does-it-work"></a>Hur fungerar det? 
Pipelines (datadrivna arbetsflöden) i Azure Data Factory utför vanligen följande tre steg:

![Tre nivåer i Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Ansluta och samla in
Företag har olika typer av data lagrade i olika källor. Det första steget när man skapar ett informationsproduktionssystem är att upprätta anslutningar till alla datakällor och bearbetningskällor som krävs. Sådana källor omfattar SaaS-tjänster, filresurser, FTP- och webbtjänster. Därefter kan data flyttas efter behov till en central plats för senare bearbetning.

Utan Data Factory måste företag skapa egna dataöverföringskomponenter eller skriva anpassade tjänster för att integrera dessa data- och bearbetningskällor. Det är både dyrt och svårt att integrera och underhålla sådana system. Systemen saknar ofta övervakning, varningar och de kontroller i företagsklass som en helt hanterad tjänst kan erbjuda.

Med Data Factory kan du använda kopieringsaktiviteten i en datapipeline för att flytta data från datalager lokalt och molnet till ett centralt datalager i molnet där du kan analysera dem. 

Du kan till exempel samla in data i en Azure Data Lake Store och sedan omvandla dessa data med en Azure Data Lake Analytics-databearbetningstjänst. Eller också kan du samla in data i en Azure Blob Storage och sedan omvandla dem med ett Azure HDInsight Hadoop-kluster.

### <a name="transform-and-enrich"></a>Omvandla och berika
När data hamnar i ett centraliserat datalager i molnet kan du bearbeta eller överföra dem med beräkningstjänster som HDInsight Hadoop, Apache Spark, Data Lake Analytics och Machine Learning. Du kan producera omvandlade data på ett tillförlitligt sätt enligt ett anpassningsbart schema och fylla på med betrodda data i produktionsmiljöer. 

### <a name="publish"></a>Publicera 
Leverera omvandlade data från molnet till lokala källor som till exempel SQL Server. Alternativt kan du lagra dessa data i molnet där de kan användas av BI-verktyg, analysverktyg och andra program.

## <a name="key-components"></a>Nyckelkomponenter
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory består av fyra nyckelkomponenter. De här komponenterna samverkar för att tillhandahålla en plattform där du kan skapa datadrivna arbetsflöden med steg för att flytta och omvandla data. 

### <a name="pipeline"></a>Pipeline
En datafabrik kan ha en eller flera pipelines. En pipeline är en grupp av aktiviteter. Tillsammans utför aktiviteterna i en pipeline en uppgift. 

En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och sedan kör en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen med detta är att pipelinen låter dig hantera aktiviteterna som en grupp i stället för enskilt. Du kan till exempel distribuera och schemalägga en hel pipeline i stället för att göra detta separat för varje aktivitet. 

### <a name="activity"></a>Aktivitet
En pipeline kan innehålla en eller flera aktiviteter. Aktiviteter definierar de åtgärder som ska utföras på dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat. På samma sätt kan du också använda en Hive-aktivitet. En Hive-aktivitet kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering.

### <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data kan skrivas från valfri källa till valfri mottagare. Markera ett datalager om du vill veta hur du kopierar data till och från det datalagret. Data Factory har stöd för följande datalager:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Mer information finns i [Move data by using Copy Activity](data-factory-data-movement-activities.md) (Flytta data med kopieringsaktiviteten).

### <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Mer information finns i [Move data by using Copy Activity](data-factory-data-transformation-activities.md) (Flytta data med kopieringsaktiviteten).

### <a name="custom-net-activities"></a>Anpassa .NET-aktiviteter
Skapa en anpassad .NET-aktivitet om du behöver flytta data till eller från ett datalager som kopieringsaktiviteten inte stöder eller om du vill omvandla data med egen logik. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Använda anpassade aktiviteter i en Azure Data Factory-pipeline).

### <a name="datasets"></a>Datauppsättningar
En aktivitet har noll eller flera datauppsättningar som indata och en eller flera datauppsättningar som utdata. Datauppsättningar representerar datastrukturer i datalager. Dessa strukturer pekar eller refererar till de data som du vill använda i dina aktiviteter (till exempel indata eller utdata). 

En Azure Blob-datauppsättning anger till exempel blobcontainern och -mappen i Azure Blob Storage som pipelinen ska läsa data från. Eller också kan en Azure SQL-tabelldatauppsättning ange vilken tabell som utdata ska skrivas till av aktiviteten. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Man kan se det som att datamängden representerar strukturen för data och den länkade tjänsten definierar anslutningen till datakällan. 

Till exempel anger en länkad Azure Storage-tjänst en anslutningssträng som används för att ansluta till ett Azure Storage-konto. En Azure Blob-datauppsättning anger vilken blobcontainer och mapp som innehåller data.   

Länkade tjänster används i två syften i Data Factory:

* För att representera ett *data lager* som innehåller, men inte begränsat till, en SQL Server databas, Oracle-databas, fil resurs eller Azure Blob Storage-konto. I avsnittet [Dataförflyttningsaktiviteter](#data-movement-activities) finns en lista över datalager som stöds.

* Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. HDInsightHive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. Se avsnittet [Datatransformeringsaktiviteter](#data-transformation-activities) för en lista över vilka beräkningsmiljöer som stöds.

### <a name="relationship-between-data-factory-entities"></a>Förhållande mellan Data Factory-enheter

![Diagram: Data Factory, en molnbaserad dataintegreringstjänst – nyckelbegrepp](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Regioner som stöds
För närvarande kan du skapa datafabriker i regionerna USA, västra; USA, östra och Europa, norra. Dock kan en datafabrik använda datalager och beräkningstjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster.

Azure Data Factory lagrar inte själv några data. Du kan skapa datadrivna arbetsflöden som dirigerar förflyttning av data mellan [de datalager som stöds](#data-movement-activities). Du kan också bearbeta data med hjälp av [beräkningstjänster](#data-transformation-activities) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Data Factory är endast tillgänglig i regionerna USA, västra, USA, östra och Europa, norra. Den tjänst som används av dataflytten i Data Factory är dock tillgänglig [globalt](data-factory-data-movement-activities.md#global) i flera regioner. Om ett data lager finns bakom en brand vägg flyttar en [Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) som är installerad i din lokala miljö data i stället.

Exempelvis kan vi anta att dina beräkningsmiljöer, som till exempel Azure HDInsight-kluster och Azure Machine Learning körs i regionen Europa, västra. Du kan skapa och använda en Azure Data Factory-instans i Europa, norra. Du kan sedan använda den för att schemalägga jobb i dina beräkningsmiljöer i regionen Europa, västra. Det tar några millisekunder för Data Factory att utlösa jobbet i din beräkningsmiljö, men den tid det tar för att köra jobbet ändras inte.

## <a name="get-started-with-creating-a-pipeline"></a>Kom igång med att skapa en pipeline
Du kan använda något av dessa verktyg eller API:er för att skapa datapipelines i Azure Data Factory: 

- Visual Studio
- PowerShell
- .NET-API
- REST-API
- Azure Resource Manager-mall

Följ de stegvisa anvisningarna i följande självstudier om du vill lära dig hur du skapar datafabriker med datapipeliner:

| Självstudie | Beskrivning |
| --- | --- |
| [Flytta data mellan två molndatalager](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Skapa en data fabrik med en pipeline som flyttar data från Blob Storage till SQL Database. |
| [Omvandla data med Hadoop-kluster](data-factory-build-your-first-pipeline.md) |Skapa din första Azure Data Factory med en datapipeline som bearbetar data genom att köra Hive-skriptet på ett Azure HDInsight-kluster (Hadoop). |
| [Flytta data mellan ett lokalt datalager och ett molndatalager med hjälp av Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) |Bygg en data fabrik med en pipeline som flyttar data från en SQL Server-databas till en Azure-blob. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator. |
