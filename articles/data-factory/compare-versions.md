---
title: Jämför Azure Data Factory med Data Factory version 1
description: Den här artikeln jämför Azure Data Factory med Azure Data Factory version 1.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: overview
ms.date: 04/09/2018
ms.openlocfilehash: dc5a4c92ee4ac0acd4a69ef94fec0981e328d829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100393725"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Jämför Azure Data Factory med Data Factory version 1

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln jämför Data Factory med Data Factory version 1. En introduktion till Data Factory finns i [Introduktion till Data Factory](introduction.md). En introduktion till Data Factory version 1 finns i [Introduktion till Azure Data Factory](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Jämför funktioner
I följande tabell jämförs funktionerna i Data Factory med funktionerna i Data Factory version 1. 

| Funktion | Version 1 | Aktuell version | 
| ------- | --------- | --------- | 
| Datauppsättningar | En namngiven vy över data som refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. En Azure Blob-datauppsättning anger till exempel blobcontainern och mappen i Azure Blob Storage som aktiviteten ska läsa data från.<br/><br/>**Tillgänglighet** anger segmenteringsmodellen i bearbetningsfönstret för datauppsättningen (till exempel varje timme, varje dag, o.s.v.). | Datauppsättningarna är samma i den aktuella versionen. Men du behöver inte definiera scheman för **tillgänglighet** för datauppsättningar. Du kan definiera en utlösarresurs som kan schemalägga pipeliner från en clock scheduler-paradigm. Mer information finns i [Utlösare](concepts-pipeline-execution-triggers.md#trigger-execution) och [Datauppsättningar](concepts-datasets-linked-services.md). | 
| Länkade tjänster | Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. | Länkade tjänster är samma som Data Factory V1, men med en ny **connectVia**-egenskap för att använda Integration Runtime-beräkningsmiljön för den aktuella versionen av Data Factory. Mer information finns i [Integration Runtimes i Azure Data Factory](concepts-integration-runtime.md) och [Länkade tjänstegenskaper för Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties). |
| Pipelines | En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter som tillsammans utför en uppgift. Du använde startTime, endTime och isPaused för att schemalägga och köra pipeliner. | Pipeliner är grupper av aktiviteter som ska utföras på data. Schemaläggning av aktiviteter i pipelinen har dock separerats till nya utlösarresurser. Du kan tänka på pipelines i den aktuella versionen av Data Factory mer som "arbets flödes enheter" som du schemalägger separat via utlösare. <br/><br/>Pipelines har inte "Windows"-körnings tid i den aktuella versionen av Data Factory. Begreppet startTime, endTime och isPaused från Data Factory V1 finns inte längre i den aktuella versionen av Data Factory. Mer information finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md) och [Pipeline och aktiviteter](concepts-pipelines-activities.md). |
| Aktiviteter | Aktiviteter definierar åtgärder som ska utföras för dina data i en pipeline. Dataflytt (kopieringsaktivitet) och datatransformeringsaktiviteter (till exempel Hive, Pig och MapReduce) stöds. | I den aktuella versionen av Data Factory är aktiviteter fortfarande definierade åtgärder inom en pipeline. Den aktuella versionen av Data Factory introducerar nya [kontroll flödes aktiviteter](concepts-pipelines-activities.md#control-flow-activities). Du kan använda de här aktiviteterna i ett kontrollflöde (loopning och branchning). Aktiviteter för dataflytt och datatransformering som hade stöd i V1 har även stöd i den aktuella versionen. Du kan definiera transformeringsaktiviteter utan att använda datauppsättningar i den aktuella versionen. |
| Hybriddataflytt och aktivitetssändning | Det som nu kallas Integration Runtime, [Datahanteringsgateway](v1/data-factory-data-management-gateway.md), gav stöd för dataflytt mellan en lokal plats och molnet.| Datahanteringsgateway kallas nu lokal installation av Integration Runtime. Den innehåller samma funktioner som i V1. <br/><br/> Azure-SSIS Integration Runtime i den aktuella versionen har också stöd för distribution och körning av SQL Server Integration Services (SSIS)-paket i molnet. Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).|
| Parametrar | NA | Parametrar är nyckel-/värdepar i skrivskyddade konfigurationsinställningar som definieras i pipeliner. Du kan skicka argument för parametrar när du kör pipelinen manuellt. Om du använder en Scheduler-utlösare kan utlösaren även skicka värden för parametrarna. Aktiviteter i pipelinen använder parametervärdena.  |
| Uttryck | Med Data Factory V1 kan du använda funktioner och systemvariabler i datamarkeringsfrågor och egenskaper för aktiviteter/datauppsättningar. | I den aktuella versionen av Data Factory kan du använda uttryck var som helst i ett JSON-strängvärde. Mer information finns i avsnittet om [uttryck och funktioner i den aktuella versionen av Data Factory](control-flow-expression-language-functions.md).|
| Pipelinekörningar | NA | En enskild instans av en pipelinekörning. Anta till exempel att du har en pipeline som körs kl. 8, 9 och 10. Det skulle i det här fallet vara tre s.k. pipelinekörningar. Varje pipelinekörning har ett unikt pipelinekörnings-ID. Pipelinekörnings-ID är ett GUID som unikt definierar den specifika pipelinekörningen. Pipelinekörningar instansieras normalt genom att skicka argument till parametrar som definieras i pipelines. |
| Aktivitetskörningar | NA | En instans av en aktivitetskörning i en pipeline. | 
| Utlösarkörning | NA | En instans av en utlösarkörning. Mer information finns i [Utlösare](concepts-pipeline-execution-triggers.md). |
| Schemaläggning | Schemaläggning baseras på start-och sluttider för pipelinen samt tillgänglighet för datauppsättningar. | Scheduler-utlösare eller körning via extern schemaläggare. Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md). |

Följande avsnitt innehåller mer information om funktionerna för den aktuella versionen. 

## <a name="control-flow"></a>Kontrollflöde  
För att kunna använda olika integreringsflöden och mönster i ett modernt informationslager har den aktuella versionen av Data Factory en ny flexibel datapipelinemodell som inte längre är knuten till Time Series-data. Några vanliga flöden som tidigare inte var möjliga är nu aktiverade. De beskrivs i följande avsnitt.   

### <a name="chaining-activities"></a>Länkning av aktiviteter
I V1 var du tvungen att konfigurera utdata för en aktivitet som indata för en annan aktivitet för att länka dem. I den aktuella versionen kan du länka efterföljande aktiviteter i en pipeline. Du kan använda egenskapen **dependsOn** i en aktivitetsdefinition för att länka den med en överordnad aktivitet. Mer information och ett exempel finns i [Pipeliner och aktiviteter](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) samt [branchning och länkning av aktiviteter](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Branchning av aktiviteter
I den aktuella versionen kan du förgrena aktiviteter i en pipeline. [If-villkoret](control-flow-if-condition-activity.md) fungerar på samma sätt som en `if`-sats i ett programmeringsspråk. Det utvärderar en uppsättning aktiviteter om villkoret utvärderas till `true` och en annan uppsättning aktiviteter när villkoret utvärderas till `false`. Exempel på branchning av aktiviteter finns i självstudien om [branchning och länkning av aktiviteter](tutorial-control-flow.md).

### <a name="parameters"></a>Parametrar 
Du kan definiera parametrar på pipelinenivå och skicka argument när du anropar din pipeline på begäran eller via en utlösare. Aktiviteter kan använda argumenten som skickas till pipelinen. Mer information finns i avsnittet om [pipeliner och utlösare](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Skicka anpassade tillstånd
Aktivitetsutdata inklusive tillstånd kan användas av en efterföljande aktivitet i pipelinen. I JSON-definitionen för en aktivitet kan du till exempel komma åt utdata från den föregående aktiviteten med hjälp av följande syntax: `@activity('NameofPreviousActivity').output.value`. Genom att använda den här funktionen kan du skapa arbetsflöden där värden kan skickas genom aktiviteter.

### <a name="looping-containers"></a>Loopningscontainrar
[ForEach-aktiviteten](control-flow-for-each-activity.md) definierar ett upprepat kontrollflöde i din pipeline. Den här aktiviteten itererar över en samling och kör angivna aktiviteter i en slinga. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk. 

Aktiviteten [Until](control-flow-until-activity.md) fungerar på samma sätt som en do-until-loopstruktur i ett programmeringsspråk. En uppsättning aktiviteter körs i en loop tills det villkor som är associerat med aktiviteten utvärderas till `true`. Du kan ange en tidsgräns för Until-aktiviteten i Data Factory.  

### <a name="trigger-based-flows"></a>Utlösningsbaserade flöden
Pipeliner kan utlösas på begäran (händelsebaserade, dvs. blob-publicering) eller wall clock-tid. Mer information om utlösare finns i artikeln om [Pipeliner och utlösare](concepts-pipeline-execution-triggers.md). 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Anropa en pipeline från en annan pipeline
Med [aktiviteten Kör pipeline](control-flow-execute-pipeline-activity.md) kan en Data Factory-pipeline anropa en annan pipeline.

### <a name="delta-flows"></a>Deltaflöden
Ett nyckel användnings fall i ETL-mönster är "delta inläsningar" där endast data som har ändrats sedan den senaste iterationen av en pipeline har lästs in. Nya funktioner i den aktuella versionen, som till exempel [lookup-aktivitet](control-flow-lookup-activity.md), flexibel schemaläggning och kontrollflöde gör det här användningsfallet möjligt på ett naturligt sätt. En självstudie med stegvisa instruktioner finns i [Tutorial: Incremental copy](tutorial-incremental-copy-powershell.md) (Självstudie: Inkrementell kopiering).

### <a name="other-control-flow-activities"></a>Andra kontrollflödesaktiviteter
Här följer några fler kontrollflödesaktiviteter som stöds av den aktuella versionen av Data Factory. 

Kontrollaktivitet | Beskrivning
---------------- | -----------
[ForEach-aktivitet](control-flow-for-each-activity.md) | Definierar ett upprepat kontrollflöde i din pipeline. Den här aktiviteten används till att iterera över en samling och kör angivna aktiviteter i en loop. Implementeringen av loopen för den här aktiviteten liknar Foreach-loopstrukturen i programmeringsspråk.
[Webb aktivitet](control-flow-web-activity.md) | Anropar en anpassad REST-slutpunkt från Data Factory-pipelinen. Du kan överföra datauppsättningar och länkade tjänster så att de förbrukas och används av aktiviteten. 
[Söknings aktivitet](control-flow-lookup-activity.md) | Läser eller söker upp en post eller ett tabellnamnvärde från en extern källa. Dessa utdata kan vidare refereras av efterföljande aktiviteter. 
[Hämta metadata-aktivitet](control-flow-get-metadata-activity.md) | Hämtar metadata för alla data i Azure Data Factory. 
[Vänta-aktivitet](control-flow-wait-activity.md) | Pausar pipelinen för en angiven tidsperiod.

## <a name="deploy-ssis-packages-to-azure"></a>Distribuera SSIS-paket till Azure 
Använd Azure-SSIS om du vill flytta dina SIS-arbetsbelastningar till molnet, skapa en Data Factory genom att använda den aktuella versionen och etablera en Azure-SSIS Integration Runtime.

Azure-SSIS Integration Runtime är ett fullständigt hanterat kluster av virtuella Azure-datorer (noder) dedikerade för att köra dina SSIS-paket i molnet. När du etablerar Azure SSIS Integration Runtime kan du använda samma verktyg som du använde för att distribuera SSIS-paket till en lokal SSIS-miljö. 

Du kan till exempel använda SQL Server Data Tools eller SQL Server Management Studio för att distribuera SSIS-paket till den här körningen i Azure. Stegvisa instruktioner finns i guiden [distribuera SQL Server Integration Services-paket till Azure](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Flexibel schemaläggning
I den aktuella versionen av Data Factory behöver du inte definiera scheman för tillgänglighet för datauppsättningar. Du kan definiera en utlösarresurs som kan schemalägga pipeliner från en clock scheduler-paradigm. Du kan även skicka parametrar till en pipeline från en utlösare för en flexibel modell för schemaläggning och körning. 

Pipelines har inte "Windows"-körnings tid i den aktuella versionen av Data Factory. Begreppet startTime, endTime och isPaused från Data Factory V1 finns inte längre i den aktuella versionen av Data Factory. Du hittar mer information om hur du skapar och schemalägger en pipeline i den aktuella versionen av Data Factory i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Stöd för flera datalager
Den aktuella versionen har stöd för att kopiera data till och från flera datalager än V1. En lista över datalager som stöds finns i följande artiklar:

- [Version 1 – datalager som stöds](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Den aktuella versionen – datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Stöd för Spark-kluster på begäran
Den aktuella versionen har stöd för skapandet av ett Azure HDInsight Spark-kluster på begäran. Ange Spark som klustertyp i din länkade HDInsight-tjänstdefinition på begäran för att skapa ett Spark-kluster på begäran. Sedan kan du konfigurera Spark-aktiviteten i din pipeline för att använda den här länkade tjänsten. 

När aktiviteten körs skapar Data Factory-tjänsten automatiskt Spark-klustret åt dig. Mer information finns i följande artiklar:

- [Spark-aktivitet i den aktuella versionen av Data Factory](transform-data-using-spark.md)
- [Länkad tjänst för Azure HDInsight på begäran](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Anpassade aktiviteter
I V1 implementerar du en (anpassad) DotNet-aktivitetskod genom att skapa ett projekt för .NET-klassbiblioteket med en klass som implementerar körningsmetoden i IDotNetActivity-gränssnittet. Därför måste du skriva din anpassade kod i .NET Framework 4.5.2 och köra den på Windows-baserade Azure Batch-poolnoder. 

I en anpassad aktivitet i den aktuella versionen behöver du inte implementera ett .NET-gränssnitt. Du kan köra kommandon, skript och din egen anpassade kod direkt, kompilerad som en körbar fil. 

Mer information finns i [Difference between custom activity in Data Factory and version 1](transform-data-using-dotnet-custom-activity.md#compare-v2-v1) (Skillnaden mellan anpassade aktiviteter i Data Factory och version 1).

## <a name="sdks"></a>SDK:er
 Den aktuella versionen av Data Factory ger en mer omfattande uppsättning SDK:er som kan användas för att skapa, hantera och övervaka pipelines.

- **.NET SDK**: .NET SDK har uppdaterats för den aktuella versionen.

- **PowerShell**: PowerShell-cmdletarna har uppdaterats för den aktuella versionen. Cmdletarna för den aktuella versionen har **DataFactoryV2** i namnet, till exempel: get-AzDataFactoryV2. 

- **Python SDK**: Den här SDK:n är ny i den aktuella versionen .

- **REST API**: REST API:et har uppdaterats för den aktuella versionen. 

SDK:erna som har uppdaterats i den aktuella versionen är inte bakåtkompatibla med V1-klienter. 

## <a name="authoring-experience"></a>Redigeringsupplevelse

| | Version 2 | Version 1 |
| ------ | -- | -- | 
| **Azure-portalen** | [Ja](quickstart-create-data-factory-portal.md) | Inga |
| **Azure PowerShell** | [Ja](quickstart-create-data-factory-powershell.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-powershell.md) |
| **.NET SDK** | [Ja](quickstart-create-data-factory-dot-net.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-vs.md) |
| **REST-API** | [Ja](quickstart-create-data-factory-rest-api.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-rest-api.md) |
| **Python SDK** | [Ja](quickstart-create-data-factory-python.md) | Inga |
| **Resource Manager-mall** | [Ja](quickstart-create-data-factory-resource-manager-template.md) | [Ja](./v1/data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Roller och behörigheter

Deltagarrollen i version 1 av Data Factory kan inte användas till att skapa och hantera Data Factory-resurser i den aktuella versionen. Mer information finns i [Data Factory-deltagare](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Övervakningsupplevelse
I den aktuella versionen kan du även övervaka datafabriker genom att använda [Azure Monitor](monitor-using-azure-monitor.md). De nya PowerShell-cmdletarna har stöd för övervakning av [Integration Runtimes](monitor-integration-runtime.md). Både V1 och V2 har stöd för visuell övervakning via ett övervakningsprogram som kan startas från Azure Portal.


## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar en data fabrik genom att följa steg-för-steg-instruktioner i följande snabb starter: [PowerShell](quickstart-create-data-factory-powershell.md), [.net](quickstart-create-data-factory-dot-net.md), [python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md).