---
title: Integration runtime
description: Mer information om Integration Runtime i Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389951"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime i Azure Data Factory 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) är beräkningsinfrastrukturen som används av Azure Data Factory för att ge olika nätverksmiljöer integrationsfunktioner:

- **Data flöde**: kör ett [data flöde](concepts-data-flow-overview.md) i en hanterad Azure Compute-miljö.  
- **Data förflyttning**: kopiera data mellan data lager i offentliga nätverk och data lager i privata nätverk (lokalt eller virtuellt privat nätverk). Den ger stöd åt inbyggda anslutningsappar, konvertering av format, kolumnmappning och bättre och skalbar dataöverföring.
- **Aktivitets sändning**: sändnings-och övervaknings omvandlings aktiviteter som körs på en mängd olika beräknings tjänster, till exempel Azure Databricks, Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server med mera.
- **SSIS paketkörning**: Internt köra SQL Server Integration Services-paket (SSIS) i en hanterad Azure-beräkningsmiljö.

I Data Factory definierar en aktivitet åtgärden som ska utföras. En länkad tjänst definierar ett datalager som mål eller en beräkningstjänst. Integration Runtime utgör bryggan mellan aktiviteten och länkade tjänster.  Det refereras av den länkade tjänsten eller aktiviteten och tillhandahåller den beräknings miljö där aktiviteten antingen körs eller skickas från. På så sätt kan aktiviteten utföras i regionen som är den närmaste möjliga till måldatalagret eller beräkningstjänsten på det bästa sättet samtidigt som den uppfyller säkerhets- och efterlevnadsbehoven.

Integrerings körningar kan skapas i Azure Data Factory UX via [hanterings hubben](author-management-hub.md) och eventuella aktiviteter, data uppsättningar eller data flöden som refererar till dem.

## <a name="integration-runtime-types"></a>Integration Runtime

Data Factory erbjuder tre typer av Integration Runtime (IR) och du bör välja den typ som passar bäst för de funktioner för data integrering och nätverks miljö som du letar efter.  Dessa tre typer är:

- Azure
- Egen värd
- Azure-SSIS

I följande tabell beskrivs funktioner och nätverksstöd för varje Integration Runtime-typ:

IR-typ | Offentligt nätverk | Privat nätverk
------- | -------------- | ---------------
Azure | Dataflöde<br/>Dataförflyttning<br/>Aktivitetssändning | Dataflöde<br/>Dataförflyttning<br/>Aktivitetssändning
Egen värd | Dataförflyttning<br/>Aktivitetssändning | Dataförflyttning<br/>Aktivitetssändning
Azure-SSIS | Körning av SSIS-paket | Körning av SSIS-paket


## <a name="azure-integration-runtime"></a>Azure Integration Runtime

En Azure integration runtime kan:

- Köra data flöden i Azure 
- Kör kopierings aktivitet mellan moln data lager
- Skicka följande transformerings aktiviteter i det offentliga nätverket: Databricks Notebook/jar/python Activity, HDInsight Hive-aktivitet, HDInsight gris-aktivitet, HDInsight MapReduce-aktivitet, HDInsight Spark-aktivitet, HDInsight streaming-aktivitet, Azure Machine Learning Studio (klassisk) batch execution Activity, Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteter, aktivitet för lagrade procedurer, Data Lake Analytics U-SQL-aktivitet, .NET anpassad aktivitet, webb aktivitet, söknings

### <a name="azure-ir-network-environment"></a>Azure IR-nätverksmiljö

Azure Integration Runtime stöder anslutning till data lager och beräknings tjänster med offentliga tillgängliga slut punkter. Genom att aktivera hanterade Virtual Network kan Azure Integration Runtime ansluta till data lager med hjälp av tjänsten privata länkar i privat nätverks miljö.

### <a name="azure-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure IR
Med Azure Integration Runtime får du en helt hanterad, serverlös beräkning i Azure.  Du behöver inte bekymra dig om infrastruktur etablering, program varu installation, uppdatering eller kapacitets skalning.  Dessutom betalar du bara för den faktiska användningen.

Med Azure Integration Runtime får du interna beräkningsfunktioner för att flytta data mellan molndatalager på ett säkert, pålitligt sätt med höga prestanda.  Du kan ange hur många dataintegreringsenheter som ska användas för kopieringsaktiviteten, och beräkningsstorleken för Azure IR skalas elastiskt upp efter detta utan att du behöver justera storleken på Azure Integration Runtime. 

Aktivitets sändning är en förenklad åtgärd för att dirigera aktiviteten till mål beräknings tjänsten, så det är inte nödvändigt att skala upp beräknings storleken för det här scenariot.

Information om hur du skapar och konfigurerar en Azure IR finns i [så här skapar och konfigurerar du Azure integration runtime](create-azure-integration-runtime.md). 

> [!NOTE] 
> Azure integration Runtime har egenskaper som är relaterade till data Flow runtime, som definierar den underliggande beräknings infrastrukturen som ska användas för att köra data flöden. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime med egen värd

En IR med egen värd kan:

- Köra kopieringsaktivitet mellan molndatalager och ett datalager i privat nätverk.
- Skickar följande Transform-aktiviteter mot beräknings resurser i lokala eller Azure-Virtual Network: HDInsight Hive-aktivitet (BYOC-ta med ditt eget kluster), HDInsight gris-aktivitet (BYOC), HDInsight MapReduce Activity (BYOC), HDInsight Spark-aktivitet (BYOC), HDInsight streaming Activity (BYOC), Azure Machine Learning Studio (klassisk) batch execution Activity, Azure Machine Learning Studio (klassisk) uppdatera resurs aktiviteter, lagrad procedur aktivitet, Data Lake Analytics U-SQL-aktivitet , Anpassad aktivitet (körs på Azure Batch), lookup-aktivitet och hämta metadata-aktivitet.

> [!NOTE] 
> Använd integration runtime med egen värd för att få stöd för data lager som kräver en egen driv rutin som SAP HANA, MySQL osv.  Mer information finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> Java Runtime Environment (JRE) är ett beroende av egen värd-IR. Kontrol lera att du har JRE installerat på samma värd.

### <a name="self-hosted-ir-network-environment"></a>IR-nätverksmiljö med egen värd

Om du vill utföra data integrering på ett säkert sätt i en privat nätverks miljö, som inte har direkt insikter från den offentliga moln miljön, kan du installera en lokal IR på en lokal miljö bakom din företags brand vägg eller i ett virtuellt privat nätverk.  IR med egen värd utför bara utgående HTTP-baserade anslutningar till öppet internet.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i IR med egen värd

Installera IR med egen värd på en lokal dator eller en virtuell dator i ett privat nätverk. För närvarande stöder vi bara att IR med egen värd körs på ett Windows-operativsystem.  

För hög tillgänglighet och skalbarhet kan du skala ut IR med egen värd genom att associera den logiska instansen med flera lokala datorer i aktiv/aktiv-läge.  Mer information finns i så här [skapar och konfigurerar du en IR-artikel med egen värd](create-self-hosted-integration-runtime.md) under instruktioner för mer information.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime

Om du vill lyfta och skifta befintlig SSIS-arbetsbelastning kan du skapa en Azure-SSIS IR för att köra SSIS-paket internt.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR-nätverksmiljö

Azure-SSIS IR kan etableras i offentliga eller privata nätverk.  Åtkomst till lokala data stöds genom att koppla Azure-SSIS IR till ett virtuellt nätverk som är anslutet till det lokala nätverket.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Beräkningsresurs och skalning i Azure-SSIS IR

Azure-SSIS IR är ett helt hanterat kluster av virtuella Azure-datorer särskilt avsedda att köras SSIS-paketen. Du kan ta med din egen Azure SQL Database-eller SQL-hanterade instans för katalogen med SSIS-projekt/-paket (SSISDB). Du kan skala upp kraften i beräkningen genom att ange nodstorlek och skala ut den genom att ange antalet noder i klustret. Du kan hantera kostnaden för att köra Azure-SSIS Integration Runtime genom att stoppa och starta den efter önskemål.

Om du vill ha mer information läser du artikeln om hur du skapar och konfigurerar Azure-SSIS IR under ”så här gör du”-guiderna.  När du har skapat den kan du distribuera och hantera dina befintliga SSIS-paket med få eller inga ändringar med välbekanta verktyg, som SQL Server Data Tools (SSDT) och SQL Server Management Studio (SSMS), precis som när du använder SSIS lokalt.

Mer information om Azure-SSIS runtime finns i följande artiklar: 

- [Självstudie: distribuera SSIS-paket till Azure](./tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL Database som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln expanderar i självstudien och innehåller instruktioner om hur du använder SQL-hanterad instans och ansluter till IR till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 

## <a name="integration-runtime-location"></a>Integration Runtime-plats

### <a name="relationship-between-factory-location-and-ir-location"></a>Förhållandet mellan fabriks platsen och IR-platsen

När kunden skapar en Data Factory-instans måste de ange platsen för data fabriken. Data Factory-platsen är där metadata för datafabriken lagras och där utlösningen av pipeline initieras. Metadata för fabriken lagras endast i den region där kunden väljer och kommer inte att lagras i andra regioner.

Dock kan en datafabrik ha åtkomst till datalager och beräkna tjänster i andra Azure-regioner för att flytta data mellan datalager eller bearbeta data med hjälp av beräkningstjänster. Det här beteende realiseras via [globalt tillgängligt IR](https://azure.microsoft.com/global-infrastructure/services/) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för nätverksegress.

IR-platsen definierar platsen för backend-beräkningen och i stort sett platsen där dataflytt, aktivitetssändning och SSIS-paketkörning utförs. IR-platsen kan skilja sig från platsen som datafabriken tillhör. 

### <a name="azure-ir-location"></a>Azure IR-plats

Du kan ange en viss plats för en Azure IR, i vilket fall aktiviteten körs eller sändningen sker i den specifika regionen.

Om du väljer att använda den automatiska matchningen Azure IR i offentligt nätverk, vilket är standard,

- För kopierings aktiviteten gör ADF det bästa arbetet för att automatiskt identifiera ditt mottagares data lagers plats. Använd sedan IR i antingen samma region om det är tillgängligt eller var som helst i samma geografi. om mottagar data lagrets region inte kan identifieras, används IR i Data Factory-regionen som ett alternativ.

  Du har till exempel din fabrik som skapats i östra USA, 
  
  - När du kopierar data till Azure blob i västra USA, om ADF har identifierat att blobben är i västra USA, körs kopierings aktiviteten på IR i västra USA. om region identifieringen Miss lyckas körs kopierings aktiviteten på IR i USA, östra.
  - När du kopierar data till Salesforce som regionen inte kan identifieras på, körs kopierings aktiviteten på IR i USA, östra.

  >[!TIP] 
  >Om du måste efterleva strikta datakrav och måste säkerställa att data inte lämnar ett visst område kan du uttryckligen skapa en Azure IR i den regionen och hänvisa den länkade tjänsten till denna IR med egenskapen ConnectVia. Om du till exempel vill kopiera data från BLOB i Storbritannien, södra till Azure Synapse Analytics i Storbritannien, södra och vill säkerställa att data inte lämnar Storbritannien, skapar du en Azure IR i Storbritannien, södra och länkar båda länkade tjänster till denna IR.

- För sökning/GetMetadata/ta bort aktivitet (kallas även för pipeline-aktiviteter), sändning av omvandlings aktivitet (även kallat externa aktiviteter) och redigerings åtgärder (test anslutning, bläddra i Mapplista och tabell lista, för hands data), används IR i Data Factory-regionen.

- För data flöden använder ADF IR i Data Factory-regionen. 

  > [!TIP] 
  > En bra idé är att se till att data flödet körs i samma region som dina motsvarande data lager (om möjligt). Du kan antingen åstadkomma detta genom att matcha Azure IR automatiskt (om data lager platsen är samma som Data Factory plats), eller genom att skapa en ny Azure IR instans i samma region som dina data lager och sedan köra data flödet på den. 

Om du aktiverar hanterade Virtual Network för automatisk lösning Azure IR, använder ADF IR i Data Factory-regionen. 

Du kan övervaka vilken IR plats som börjar gälla under körning av aktiviteten i övervakningsvyn för pipeline-aktivitet i gränssnittet eller en aktivitetsövervakningsnyttolast.

### <a name="self-hosted-ir-location"></a>Namn på IR med egen värd

IR med egen värd registreras logiskt hos Data Factory och du får beräkningen som används för att stödja dess funktioner. Det finns därför ingen uttrycklig platsegenskapen för IR med egen värd. 

När IR med egen värd används för att utföra dataflyttning extraherar den data från källan och skriver dem till målet.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR-plats

Att välja rätt plats för Azure-SSIS IR är viktigt för att uppnå höga prestanda i dina arbetsflöden för extrahering, transformering och laddning (ETL).

- Platsen för din Azure-SSIS IR behöver inte vara samma som platsen för din data fabrik, men den bör vara samma som platsen för din egen Azure SQL Database eller SQL-hanterad instans där SSISDB. På så sätt kan din Azure-SSIS Integration Runtime enkelt få åtkomst till SSISDB utan att det medför överdriven trafik mellan olika platser.
- Om du inte har en befintlig SQL Database-eller SQL-hanterad instans, men du har lokala data källor/mål, bör du skapa en ny Azure SQL Database eller SQL-hanterad instans på samma plats som ett virtuellt nätverk som är anslutet till ditt lokala nätverk.  På så sätt kan du skapa Azure-SSIS IR med hjälp av den nya Azure SQL Database eller SQL-hanterade instansen och ansluta till det virtuella nätverket, allt på samma plats, vilket effektivt minimerar data förflyttningar mellan olika platser.
- Om platsen för din befintliga Azure SQL Database-eller SQL-hanterade instans inte är samma som platsen för ett virtuellt nätverk som är anslutet till ditt lokala nätverk, måste du först skapa Azure-SSIS IR med en befintlig Azure SQL Database eller SQL-hanterad instans och ansluta till ett annat virtuellt nätverk på samma plats, och sedan konfigurera ett virtuellt nätverk till en virtuell nätverks anslutning mellan olika platser.

I följande diagram visas platsinställningar för Data Factory och dess Integration Runtime-instanser:

![Integration Runtime-plats](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>Bestämma vilken IR som ska användas

### <a name="copy-activity"></a>Kopieringsaktivitet

För kopieringsaktiviteten kräver den länkade tjänster-källa och länkade tjänster-mottagare för att definiera dataflödets riktning. Följande logik används till att bestämma vilken Integration Runtime-instans som används för att utföra kopieringen: 

- **Kopiera mellan två moln data källor**: när de länkade tjänsterna källa och mottagare använder Azure IR, använder ADF den regionala Azure IR om du har angett, eller automatiskt bestämmer en plats för Azure IR om du väljer automatcha IR (standard) enligt beskrivningen i avsnittet [integration runtime location](#integration-runtime-location) .
- **Kopiera mellan molndatalager och datakälla i privat nätverk**: om den länkade källtjänsten eller den länkade mottagartjänsten pekar på en IR med egen värd körs kopieringsaktiviteten på den Integration Runtime med egen värd.
- **Kopiera mellan två data källor i privat nätverk**: både den länkade källan och mottagar tjänsten måste peka på samma instans av integration Runtime och att integration runtime används för att köra kopierings aktiviteten.

### <a name="lookup-and-getmetadata-activity"></a>Lookup och GetMetadata-aktivitet

Aktiviteterna Lookup och GetMetadata har körts på integreringskörningsmiljön som är associerad med den länkade datalagringstjänsten.

### <a name="external-transformation-activity"></a>Extern omvandlings aktivitet

Varje extern omvandlings aktivitet som använder en extern beräknings motor har en länkad mål beräknings tjänst, som pekar på en integration Runtime. Den här integration runtime-instansen avgör platsen där den externa handkodade omvandlings aktiviteten skickas från.

### <a name="data-flow-activity"></a>Data flödes aktivitet

Data flödes aktiviteter körs på Azure integration runtime som är kopplad till den. Spark-beräkningen som används av data flöden bestäms av data flödes egenskaperna i dina Azure Integration Runtime och hanteras fullständigt av ADF.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Skapa Azure integration runtime](create-azure-integration-runtime.md)
- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln expanderar i självstudien och innehåller instruktioner om hur du använder SQL-hanterad instans och ansluter till IR till ett virtuellt nätverk.