---
title: 'Azure Data Factory: vanliga frågor och svar '
description: Få svar på vanliga frågor om Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 2027e3555a7eb616ad024ec00bf6b0f8f452167c
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258528"
---
# <a name="azure-data-factory-faq"></a>Vanliga frågor och svar om Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln innehåller svar på vanliga frågor om Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Vad är Azure Data Factory?

Data Factory är en fullständigt hanterad, molnbaserad tjänst för data integrering som automatiserar flytt och transformering av data. Som en fabrik som kör utrustning för att transformera råmaterial till färdiga varor, Azure Data Factory dirigerar befintliga tjänster som samlar in rå data och omvandlar dem till information som är klar att använda. 

Med hjälp av Azure Data Factory kan du skapa data drivna arbets flöden för att flytta data mellan lokala och molnbaserade data lager. Och du kan bearbeta och transformera data med data flöden. ADF stöder även externa beräknings motorer för manuella omvandlingar genom att använda beräknings tjänster som Azure HDInsight, Azure Databricks och integrerings körningen SQL Server Integration Services (SSIS).

Med Data Factory kan du köra din data behandling antingen på en Azure-baserad moln tjänst eller i din egen dator med egen värd miljö, till exempel SSIS, SQL Server eller Oracle. När du har skapat en pipeline som utför den åtgärd du behöver kan du schemalägga att den ska köras regelbundet (varje timme, varje dag eller varje vecka, till exempel), tids periods schemaläggning eller utlösa pipelinen från en händelse förekomst. Mer information finns i [Introduktion till Azure Data Factory](introduction.md).

## <a name="compliance-and-security-considerations"></a>Efterlevnad och säkerhets aspekter

Azure Data Factory är certifierat för ett antal certifierings certifieringar, inklusive _SOC 1, 2, 3_, _HIPAA Baa_ och _HiTRUST_. En fullständig och växande lista över certifieringar finns [här](data-movement-security-considerations.md). Digitala kopior för gransknings rapporter och certifieringar för regelefterlevnad finns i [service Trust Center](https://servicetrust.microsoft.com/)

### <a name="control-flows-and-scale"></a>Kontrol lera flöden och skala

För att stödja olika integrerings flöden och mönster i det moderna informations lagret kan Data Factory aktivera flexibel data pipeline-modellering. Detta omfattar programmerings paradigm för full kontroll flöde, som omfattar villkorlig körning, förgrening i datapipeliner och möjligheten att explicit skicka parametrar inom och mellan dessa flöden. Kontroll flödet omfattar även omvandling av data via aktivitets sändning till externa körnings motorer och data flödes funktioner, inklusive data förflyttning i stor skala, via kopierings aktiviteten.

Data Factory ger friheten att modellera alla flödes format som krävs för data integrering och som kan skickas på begäran eller upprepade gånger enligt ett schema. Några vanliga flöden som den här modellen möjliggör är:

- Kontroll flöden:
    - Aktiviteter kan länkas samman i en sekvens i en pipeline.
    - Aktiviteter kan förgrenas i en pipeline.
    - Parametrar:
        * Parametrar kan definieras på pipeline-nivå och argument kan skickas när du anropar pipelinen på begäran eller från en utlösare.
        * Aktiviteter kan använda argumenten som skickas till pipelinen.
    - Anpassad tillstånds överföring:
        * Uppgiftsutdata, inklusive State, kan förbrukas av en efterföljande aktivitet i pipelinen.
    - Upprepnings behållare:
        * Förgrunds aktiviteten itererar över en angiven samling aktiviteter i en slinga. 
- Utlös ande flöden:
    - Pipelines kan utlösas på begäran, efter Time-of-Time eller som svar på de drivna händelse rutnäts ämnena
- Delta flöden:
    - Du kan använda parametrar för att definiera ett vatten märke för delta kopian samtidigt som du flyttar dimensions-eller referens tabeller från ett relationellt lager, antingen lokalt eller i molnet, för att läsa in data i sjön.

Mer information finns i [Självstudier: styr flöden](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Data omvandlas i skala med kod fria pipeliner

Den nya webbläsarbaserade verktygs upplevelsen ger kod fri pipeline-redigering och-distribution med en modern, interaktiv webbaserad upplevelse.

För utvecklare av visuella data och data tekniker är Data Factory Web UI den kod fria design miljön som du kommer att använda för att skapa pipeliner. Den är helt integrerad med Visual Studio Online git och ger integrering för CI/CD och iterativ utveckling med fel söknings alternativ.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Omfattande plattforms oberoende SDK: er för avancerade användare

Data Factory v2 innehåller en omfattande uppsättning SDK: er som kan användas för att skapa, hantera och övervaka pipelines med hjälp av din favorit-IDE, inklusive:

* Python SDK
* PowerShell CLI
* C#-SDK

Användare kan också använda de dokumenterade REST API: erna för gränssnitt med Data Factory v2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativ utveckling och fel sökning med hjälp av visuella verktyg

Azure Data Factory visuella verktyg möjliggör iterativ utveckling och fel sökning. Du kan skapa dina pipelines och utföra test körningar med hjälp av **fel söknings** funktionen i pipeline-arbetsytan utan att skriva en enda rad med kod. Du kan visa resultatet av dina test körningar i fönstret **utdata** för din pipeline-arbetsyta. När test körningen lyckas kan du lägga till fler aktiviteter i din pipeline och fortsätta fel sökningen på ett iterativt sätt. Du kan också avbryta test körningarna när de pågår.

Du behöver inte publicera dina ändringar i Data Factory-tjänsten innan du väljer **Felsök**. Detta är användbart i scenarier där du vill se till att de nya tilläggen eller ändringarna fungerar som förväntat innan du uppdaterar data Factory-arbetsflöden i utvecklings-, test-eller produktions miljöer.

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Möjlighet att distribuera SSIS-paket till Azure

Om du vill flytta dina SSIS-arbetsbelastningar kan du skapa en Data Factory och etablera en Azure-SSIS integration Runtime. En Azure-SSIS integration runtime är ett fullständigt hanterat kluster med virtuella Azure-datorer (noder) som är dedikerade att köra dina SSIS-paket i molnet. Stegvisa instruktioner finns i själv studie kursen [distribuera SSIS-paket till Azure](./tutorial-deploy-ssis-packages-azure.md) . 

### <a name="sdks"></a>SDK:er

Om du är en avancerad användare och söker efter ett programmerings gränssnitt innehåller Data Factory en omfattande uppsättning SDK: er som du kan använda för att skapa, hantera eller övervaka pipelines med hjälp av din favorit-IDE. Språk stöd omfattar .NET, PowerShell, python och REST.

### <a name="monitoring"></a>Övervakning

Du kan övervaka dina data fabriker via PowerShell, SDK eller visuella övervaknings verktyg i webbläsarens användar gränssnitt. Du kan övervaka och hantera på begäran, Utlös ande och klock baserade anpassade flöden på ett effektivt och effektivt sätt. Avbryt befintliga uppgifter, se fel snabbt, öka detalj nivån för att få detaljerade fel meddelanden och Felsök problemen, allt från en enda ruta utan Sammanhangs beroende eller navigering fram och tillbaka mellan skärmar.

### <a name="new-features-for-ssis-in-data-factory"></a>Nya funktioner för SSIS i Data Factory

Eftersom den första offentliga för hands versionen i 2017 har Data Factory lagt till följande funktioner för SSIS:

-    Stöd för tre fler konfigurationer/varianter av Azure SQL Database som är värdar för SSIS-databasen (SSISDB) för projekt/paket:
-    SQL Database med tjänst slut punkter för virtuellt nätverk
-    SQL-hanterad instans
-    Elastisk pool
-    Stöd för ett Azure Resource Manager virtuellt nätverk ovanpå ett klassiskt virtuellt nätverk för att bli inaktuellt i framtiden, vilket gör att du kan mata in/ansluta din Azure-SSIS integration runtime till ett virtuellt nätverk som har kon figurer ATS för SQL Database med tjänst slut punkter för virtuella nätverk/MI/lokal data åtkomst. Mer information finns även i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).
-    Stöd för Azure Active Directory (Azure AD)-autentisering och SQL-autentisering för att ansluta till SSISDB, vilket ger Azure AD-autentisering med din Data Factory hanterade identitet för Azure-resurser
-    Stöd för att sätta din befintliga SQL Server-licens för att få avsevärda kostnads besparingar från Azure Hybrid-förmåns alternativet
-    Stöd för Enterprise-utgåvan av Azure-SSIS integration runtime som gör att du kan använda avancerade/Premium-funktioner, ett anpassat installations gränssnitt för att installera ytterligare komponenter/tillägg och ett eko system för partner. Mer information finns i [Enterprise Edition, anpassad installation och utökning från tredje part för SSIS i ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Djupare integrering av SSIS i Data Factory som låter dig anropa/utlösa den första klassens kör SSIS-programpaket aktiviteter i Data Factory pipelines och Schemalägg dem via SSMS. Mer information finns även i [modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF-pipeline](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).

## <a name="what-is-the-integration-runtime"></a>Vad är integrerings körningen?

Integrerings körningen är den beräknings infrastruktur som Azure Data Factory använder för att tillhandahålla följande funktioner för data integrering i olika nätverks miljöer:

- **Data förflyttning**: vid data förflyttning flyttar integration runtime data mellan käll-och mål data lager, samtidigt som stöd för inbyggda anslutnings program, format konvertering, kolumn mappning och utförd och skalbar data överföring.
- **Sändnings aktiviteter**: för omvandling ger integrerings körningen möjlighet att internt köra SSIS-paket.
- **Köra SSIS-paket**: integrerings körningen exekverar internt SSIS-paket i en hanterad Azure Compute-miljö. Integrerings körningen har även stöd för att skicka och övervaka omvandlings aktiviteter som körs på en mängd olika beräknings tjänster, till exempel Azure HDInsight, Azure Machine Learning, SQL Database och SQL Server.

Du kan distribuera en eller flera instanser av integration runtime efter behov för att flytta och transformera data. Integrerings körningen kan köras på ett offentligt Azure-nätverk eller i ett privat nätverk (lokalt, Azure Virtual Network eller Amazon Web Services virtuellt privat moln [VPC]).

Mer information finns i [Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Vad är gränsen för antalet integrerings körningar?

Det finns ingen hård gräns för antalet integration runtime-instanser som du kan ha i en data fabrik. Det finns dock en gräns för antalet virtuella dator kärnor som integration runtime kan använda per prenumeration för körning av SSIS-paket. Mer information finns i [Data Factory gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Vilka är de viktigaste begreppen i Azure Data Factory?

En Azure-prenumeration kan ha en eller flera Azure Data Factory-instanser (eller datafabriker). Azure Data Factory innehåller fyra viktiga komponenter som fungerar tillsammans som en plattform där du kan skapa data drivna arbets flöden med steg för att flytta och transformera data.

### <a name="pipelines"></a>Pipelines

En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering av aktiviteter för att utföra en arbets enhet. Tillsammans utför aktiviteterna i en pipeline en uppgift. En pipeline kan till exempel innehålla en grupp med aktiviteter som matar in data från en Azure-blob och sedan kör en Hive-fråga på ett HDInsight-kluster för att partitionera data. Fördelen är att du kan använda en pipeline för att hantera aktiviteterna som en uppsättning i stället för att behöva hantera varje aktivitet individuellt. Du kan kedja samman aktiviteterna i en pipeline för att kunna använda dem i tur och ordning, eller så kan du använda dem oberoende av varandra parallellt.

### <a name="data-flows"></a>Dataflöden

Data flöden är objekt som du skapar visuellt i Data Factory som transformerar data i skala på backend-tjänster. Du behöver inte förstå programmering eller Spark-internt. Du behöver bara utforma data omvandlings avsikten med grafer (mappning) eller kalkyl blad (Datatransformering).

### <a name="activities"></a>Aktiviteter

Aktiviteter representerar ett bearbetningssteg i en pipeline. Du kan till exempel använda en kopierings aktivitet för att kopiera data från ett data lager till ett annat data lager. På samma sätt kan du använda en Hive-aktivitet som kör en Hive-fråga på ett Azure HDInsight-kluster för att transformera eller analysera dina data. Data Factory stöder tre typer av aktiviteter: dataförflyttning, datatransformering och kontroll.

### <a name="datasets"></a>Datauppsättningar

Datauppsättningar representerar datastrukturer i datalager som pekar på eller refererar till de data som du vill använda i dina aktiviteter som indata eller utdata. 

### <a name="linked-services"></a>Länkade tjänster

Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det på det här sättet: en länkad tjänst definierar anslutningen till data källan och en data uppsättning representerar data strukturen. Till exempel anger en länkad tjänst en Azure Storage anslutnings strängen för att ansluta till Azure Storage-kontot. Och en Azure Blob-datauppsättning anger BLOB-behållaren och mappen som innehåller data.

Länkade tjänster har två syfte i Data Factory:

- För att representera ett *data lager* som inkluderar, men inte är begränsat till, en SQL Server instans, en Oracle Database-instans, en fil resurs eller ett Azure Blob Storage-konto. En lista över data lager som stöds finns i [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md).
- Så här visar du en *beräkningsresurs* som kan vara värd för körningen av en aktivitet. HDInsight Hive-aktiviteten körs till exempel på ett HDInsight Hadoop-kluster. En lista över omvandlings aktiviteter och beräknings miljöer som stöds finns i [transformera data i Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Utlösare

Utlösare representerar bearbetnings enheter som avgör när en pipeline-körning startas. Det finns olika typer av utlösare för olika typer av händelser. 

### <a name="pipeline-runs"></a>Pipelinekörningar

En pipeline-körning är en instans av en pipeline-körning. Du instansierar vanligt vis en pipeline-körning genom att skicka argument till de parametrar som har definierats i pipelinen. Du kan skicka argumenten manuellt eller inom utlösnings definitionen.

### <a name="parameters"></a>Parametrar

Parametrar är nyckel/värde-par i en skrivskyddad konfiguration.Du definierar parametrar i en pipeline och du skickar argumenten för de definierade parametrarna under körningen från en körnings kontext. Körnings kontexten skapas av en utlösare eller från en pipeline som du kör manuellt. Aktiviteter i pipelinen använder parametervärdena.

En data uppsättning är en starkt skriven parameter och en entitet som du kan återanvända eller referera till. En aktivitet kan referera till data uppsättningar och kan använda de egenskaper som har definierats i data uppsättnings definitionen.

En länkad tjänst är också en starkt skriven parameter som innehåller anslutnings information till antingen ett data lager eller en beräknings miljö. Det är också en entitet som du kan återanvända eller referera till.

### <a name="control-flows"></a>Kontroll flöden

Kontroll flöden dirigerar pipeline-aktiviteter som innefattar länkning av aktiviteter i en sekvens, förgrening, parametrar som du definierar på pipelinen och argument som du skickar när du anropar pipelinen på begäran eller från en utlösare. Kontroll flöden omfattar också anpassade tillstånds pass och upprepnings behållare (det vill säga, förfallna iteratorer).


Mer information om Data Factory-begrepp finns i följande artiklar:

- [Datamängder och länkade tjänster](concepts-datasets-linked-services.md)
- [Pipelines och aktiviteter](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Vad är pris sättnings modellen för Data Factory?

Azure Data Factory pris information finns i [Data Factory pris information](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hur kan jag hålla dig uppdaterad med information om Data Factory?

För den senaste informationen om Azure Data Factory går du till följande webbplatser:

- [Blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Dokumentations start sida](./index.yml)
- [Produktens start sida](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Teknisk djupgående

### <a name="how-can-i-schedule-a-pipeline"></a>Hur kan jag schemalägga en pipeline?

Du kan använda utlösaren för Scheduler eller tids perioden för att schemalägga en pipeline. Utlösaren använder ett schema med en klock kalender som kan schemalägga pipeliner regelbundet eller i kalenderbaserade omaktuella mönster (till exempel på måndagar kl. 6:00 PM och torsdag på 9:00 PM). Mer information finns i [pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan jag skicka parametrar till en pipeline-körning?

Ja, parametrarna är ett första klass koncept på översta nivån i Data Factory. Du kan definiera parametrar på pipeline-nivå och skicka argument när du kör pipelinen körs på begäran eller genom att använda en utlösare.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan jag definiera standardvärden för pipeline-parametrarna?

Ja. Du kan definiera standardvärden för parametrarna i pipelinen.

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan en aktivitet i en pipeline förbruka argument som skickas till en pipeline-körning?

Ja. Varje aktivitet i pipelinen kan använda det parameter värde som skickas till pipelinen och köras med `@parameter` konstruktionen. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan en aktivitets utmatnings egenskap användas i en annan aktivitet?

Ja. En aktivitets utdata kan förbrukas i en efterföljande aktivitet med `@activity` konstruktionen.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hur gör jag för att hantera null-värden korrekt i en aktivitets utdata?

Du kan använda `@coalesce` konstruktionen i uttrycken för att hantera null-värden på ett smidigt sätt.

## <a name="mapping-data-flows"></a>Mappa dataflöden

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Jag behöver hjälp med att felsöka min data flödes logik. Vilken information behöver jag tillhandahålla för att få hjälp?

Ange data flödes skriptet när Microsoft tillhandahåller hjälp eller fel sökning med data flöden. Det här är skriptet bakomliggande kod i data flödes diagrammet. Öppna data flödet från ADF-ANVÄNDARGRÄNSSNITTET och klicka sedan på knappen "skript" i det övre högra hörnet. Kopiera och klistra in det här skriptet eller spara det i en textfil.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Hur gör jag för att åtkomst till data genom att använda de andra 90 data uppsättnings typerna i Data Factory?

Funktionen mappa data flöde tillåter för närvarande Azure SQL Database, Azure Synapse Analytics, avgränsade textfiler från Azure Blob Storage eller Azure Data Lake Storage Gen2 och Parquet-filer från Blob Storage eller Data Lake Storage Gen2 internt för källa och mottagare. 

Använd kopierings aktiviteten till att mellanlagra data från någon av de andra kopplingarna och kör sedan en data flödes aktivitet för att transformera data när de har mellanlagrats. Till exempel kommer din pipeline först att kopieras till Blob Storage och sedan använder en data flödes aktivitet en data uppsättning i källan för att transformera dessa data.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Är den egna värdbaserade integrerings körningen tillgänglig för data flöden?

IR med egen värd är en ADF-baserad pipeline-konstruktion som du kan använda med kopierings aktiviteten för att hämta eller flytta data till och från lokal eller VM-baserade data källor och mottagare. De virtuella datorer som du använder för en IR med egen värd kan också placeras inuti samma VNET som dina skyddade data lager för åtkomst till dessa data lager från ADF. Med data flöden uppnår du samma slut resultat med hjälp av Azure IR med hanterat VNET i stället.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Betjänar data flödets beräknings motor flera klienter?

Kluster delas aldrig. Vi garanterar isolering för varje jobb körning i produktions körningar. I händelse av ett fel söknings scenario får en person ett kluster och alla fel kommer att gå till det klustret som initieras av användaren.

## <a name="next-steps"></a>Nästa steg

Steg-för-steg-instruktioner för att skapa en data fabrik finns i följande Självstudier:

- [Snabb start: skapa en data fabrik](quickstart-create-data-factory-dot-net.md)
- [Självstudie: kopiera data i molnet](tutorial-copy-data-dot-net.md)
