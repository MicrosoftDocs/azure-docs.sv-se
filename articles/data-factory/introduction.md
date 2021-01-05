---
title: introduktion till Azure Data Factory
description: Lär dig mer om den molnbaserade dataintegreringstjänsten Azure Data Factory som samordnar och automatiserar förflyttning och omvandling av data.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 00ae812536e73f432b9bddb93fc1b4a958b379f5
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97812876"
---
# <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När det gäller stordata, lagras råa, oordnade data ofta i relationella, icke-relationella och andra lagringssystem. Men i sig självt så har rådata inte rätt kontext eller mening för att ge analytiker, dataforskare och beslutsfattare meningsfulla insikter. 

Big data kräver en tjänst som kan samordna och operationalisera processer för att förfina dessa enorma lager av rå data till användbara affärs insikter. Azure Data Factory är en hanterad molntjänst som skapats för dessa komplexa, hybrida, ETL- (extract-transform-load), ELT- (extract-load-transform) och dataintegreringsprojekt.

Tänk dig till exempel ett spelföretag som samlar in petabyte med spelloggar som produceras av spel i molnet. Företaget vill analysera dessa loggar för att få insikter om kundpreferenser, demografi och användningsbeteende. Det vill också identifiera möjligheter till merförsäljning och korsförsäljning, utveckla intressanta nya funktioner, driva affärstillväxten och ge en bättre kundupplevelse.

När företaget ska analysera loggarna måste de använda referensdata, till exempel kundinformation, spelinformation och marknadsföringskampanjinformation som finns i ett lokalt datalager. Företaget vill använda dessa data från det lokala datalagret och kombinera dem med ytterligare loggdata som de har i ett molndatalager. 

För att extrahera insikter kan IT-hoppas bearbeta kopplade data med hjälp av ett Spark-kluster i molnet (Azure HDInsight) och publicera transformerade data i ett moln informations lager, till exempel Azure Synapse Analytics, för att enkelt skapa en rapport ovanpå den. De vill automatisera det här arbetsflödet samt övervaka och hantera det enligt ett dagligt schema. De vill även köra det när filer landar i en bloblagringscontainer.

Azure Data Factory är en plattform som löser den här typen av datascenarier. Det är den *MOLNBASERAD ETL-och data integrerings tjänst som gör att du kan skapa data drivna arbets flöden för att dirigera data förflyttning och omvandla data i skala*. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (så kallade ”pipelines”) som kan mata in data från olika datalager. Du kan bygga komplexa ETL-processer som omvandlar data visuellt med data flöden eller med hjälp av beräknings tjänster som Azure HDInsight Hadoop, Azure Databricks och Azure SQL Database. 

Dessutom kan du publicera dina transformerade data till data lager som Azure Synapse Analytics för Business Intelligence-program (BI) att använda. Slutligen kan rådata ordnas, via Azure Data Factory, i meningsfulla datalager och datasjöar för att ge bättre beslutsunderlag.

![Toppnivåvy över Data Factory](media/data-flow/overview.png)

## <a name="how-does-it-work"></a>Hur fungerar det?

Data Factory innehåller en serie sammankopplade system som tillsammans utgör en komplett plattform för datatekniker.

### <a name="connect-and-collect"></a>Ansluta och samla in

Företag har olika typer av data som befinner sig på olika platser, lokalt, i molnet, strukterade, ostrukturerade och delvis strukturerade och alla dessa data anländer i olika intervall och med olika hastighet. 

Det första steget när det gäller att skapa ett informationsproduktionssystem är att ansluta till alla nödvändiga data- och bearbetningskällor, exempelvis SaaS-tjänster, databaser, filresurser och FTP-webbtjänster. Nästa steg är att flytta data efter behov till en central plats för senare bearbetning.

Utan Data Factory måste företag skapa egna dataöverföringskomponenter eller skriva anpassade tjänster för att integrera dessa data- och bearbetningskällor. Det är dyrt och svårt att integrera och underhålla sådana system. De saknar dessutom ofta övervakning, varningar och de kontroller i företagsklass som en helt hanterad tjänst kan erbjuda.

Med Data Factory kan du använda [kopieringsaktiviteten](copy-activity-overview.md) i en datapipeline för att flytta data från datalager lokalt och molnet till ett centralt datalager i molnet där du kan analysera dem. Du kan till exempel samla in data i Azure Data Lake Storage och transformera data senare med hjälp av en Azure Data Lake Analytics Compute-tjänst. Eller så kan du samla in data i en Azure Blob Storage och sedan omvandla de med ett Azure HDInsight Hadoop-kluster.

### <a name="transform-and-enrich"></a>Omvandla och berika
När data är tillgängliga i ett centraliserat data lager i molnet, bearbeta eller transformera du insamlade data med hjälp av data flöden för automatisk mappning. Data flöden gör det möjligt för data tekniker att bygga och underhålla data omvandlings diagram som körs i Spark utan att behöva förstå Spark-kluster eller Spark-programmering.

Om du föredrar att koda omvandlingar manuellt stöder ADF externa aktiviteter för att köra dina omvandlingar på beräknings tjänster som HDInsight Hadoop, Spark, Data Lake Analytics och Machine Learning.

### <a name="cicd-and-publish"></a>CI/CD och publicera
[Data Factory erbjuder fullt stöd för CI/CD-](continuous-integration-deployment.md) datapipeliner med Azure DevOps och GitHub. På så sätt kan du stegvis utveckla och leverera dina ETL-processer innan du publicerar den färdiga produkten. Efter att rådata har förfinats till en form som företaget kan använda, läser du in data i Azure Data Warehouse, Azure SQL Database, Azure CosmosDB eller den analysmotor som ditt företags användare kan peka till från sina business intelligence-verktyg.
### <a name="monitor"></a>Övervaka
När du har skapat och distribuerat din pipeline för dataintegrering och fått affärsvärde från förfinade data, kan du övervaka schemalagda aktiviteter och pipelines för att se hur många som lyckats respektive misslyckats. Azure Data Factory har ett inbyggt stöd för pipelineövervakning via Azure Monitor, API, PowerShell, Azure Monitor-loggar och hälsopaneler i Azure-portalen.

## <a name="top-level-concepts"></a>Toppnivåbegrepp
En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory består av nedanstående nyckel komponenter.
- Pipelines
- Aktiviteter
- Datauppsättningar
- Länkade tjänster
- Dataflöden
- Integreringskörningar

De här komponenterna samverkar för att tillhandahålla en plattform där du kan skapa datadrivna arbetsflöden med steg för att flytta och omvandla data.

### <a name="pipeline"></a>Pipeline
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter som utför en arbetsenhet. Tillsammans utför aktiviteterna i en pipeline en uppgift. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och sedan kör en Hive-fråga på ett HDInsight-kluster för att partitionera data. 

Fördelen med detta är att pipelinen låter dig hantera aktiviteter som en uppsättning istället för enskilt. Aktiviteter i en pipeline kan sammanlänkas för att köras sekventiellt eller så kan de köras fristående och parallellt.

### <a name="mapping-data-flows"></a>Mappa dataflöden
Skapa och hantera diagram över data omvandlings logik som du kan använda för att omvandla data i alla storlekar. Du kan skapa ett återanvändbart bibliotek med data omvandlings rutiner och köra dessa processer på ett utskalat sätt från dina ADF-pipeliner. Data Factory kommer att köra din logik på ett Spark-kluster som snurrar upp och snurrar ned när du behöver det. Du behöver inte någonsin hantera eller underhålla kluster.

### <a name="activity"></a>Aktivitet
Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från ett datalager till ett annat. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar
Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster
Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Man kan se det som att datamängden representerar strukturen för data och den länkade tjänsten definierar anslutningen till datakällan. Till exempel anger en länkad Azure Storage-tjänst en anslutningssträng för att ansluta till ett Azure Storage-konto. Och en Azure Blob-datauppsättning anger vilken blobcontainer och mapp som innehåller data.

Länkade tjänster används för två syften i Data Factory:

- För att representera ett **data lager** som innehåller, men inte begränsat till, en SQL Server databas, Oracle-databas, fil resurs eller Azure Blob Storage-konto. En lista över datalager som stöds finns i artikeln om [kopieringsaktiviteter](copy-activity-overview.md).

- Så här visar du en **beräkningsresurs** som kan vara värd för körningen av en aktivitet. HDInsightHive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. En lista över transformeringsaktiviteter och beräkningsmiljöer som stöds finns i artikeln om [omvandling av data](transform-data.md).

### <a name="triggers"></a>Utlösare
Utlösare representerar en bearbetningsenhet som avgör när en pipelinekörning måste startas. Det finns olika typer av utlösare för olika typer av händelser.

### <a name="pipeline-runs"></a>Pipelinekörningar
En pipelinekörning är en instans av en pipelinekörning. Pipelinekörningar initieras vanligen genom att skicka argumenten till de parametrar som definierats i pipelines. Argumenten kan skickas manuellt eller i en utlösardefinition.

### <a name="parameters"></a>Parametrar
Parametrar är nyckel/värde-par i en skrivskyddad konfiguration.Parametrar har definierats i pipelinen. Argumenten för de definierade parametrarna skickas vid körning från körningskontexten som skapats av en utlösare eller en pipeline som körs manuellt. Aktiviteter i pipelinen använder parametervärdena.

En datauppsättning är en starkt typifierad parameter och en återanvändbar/refererbar entitet. En aktivitet kan referera till datauppsättningar och kan använda egenskaperna som definierats i definitionen för datauppsättningen.

En länkad tjänst är också en starkt typifierad parameter som innehåller anslutningsinformationen till antingen ett datalager eller en beräkningsmiljö. Det är också en återanvändningsbar/refererbar entitet.

### <a name="control-flow"></a>Kontrollflöde
Kontrollflöde är en orkestrering av pipelineaktiviteter som innefattar kedjesammansättning av aktiviteter i en sekvens, branchning definiering av parametrar på pipelinenivå och argument som skickas vid anrop till pipelinen på begäran eller från en utlösare. Det innefattar även att skicka anpassade tillstånd och loopcontainer, d.v.s. for-each-iteratorer.

### <a name="variables"></a>Variabler
Variabler kan användas i pipelines för att lagra tillfälliga värden och kan också användas tillsammans med parametrar för att möjliggöra överföring av värden mellan pipelines, data flöden och andra aktiviteter.

## <a name="next-steps"></a>Nästa steg
Här är några viktiga nästa steg i dokument som du kan utforska:

- [Datamängder och länkade tjänster](concepts-datasets-linked-services.md)
- [Pipelines och aktiviteter](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md)
- [Mappa data flöden](concepts-data-flow-overview.md)
- [Data Factory-gränssnittet i Azure Portal](quickstart-create-data-factory-portal.md)
- [Verktyget för att kopiera data i Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager mall](quickstart-create-data-factory-resource-manager-template.md)
 
