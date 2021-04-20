---
title: Migrera Log Analytics-data för Azure HDInsight
description: Lär dig mer om ändringarna i Azure Monitor och metodtips för att använda de nya tabellerna.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741810"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Log Analytics-migreringsguide för Azure HDInsight kluster

 Azure HDInsight är en företagsklar, hanterad klustertjänst. Den här tjänsten kör analysramverk med öppen källkod som Apache Spark, Hadoop, HBase och Kafka på Azure. Azure HDInsight har integrerats med andra Azure-tjänster så att kunderna bättre kan hantera sina stordataanalysprogram.

Log Analytics innehåller ett verktyg i Azure Portal för att redigera och köra loggfrågor. Frågorna kommer från data som samlas in av Azure Monitor loggar och analyserar resultaten interaktivt. Kunder kan använda Log Analytics-frågor för att hämta poster som matchar specifika villkor. De kan också använda frågor för att identifiera trender, analysera mönster och ge insikter om sina data.

Azure HDInsight integrering med Log Analytics 2017. HDInsight-kunder har snabbt infört den här funktionen för att övervaka sina HDInsight-kluster och köra frågor mot loggarna i klustren. Även om införandet av den här funktionen har ökat har kunderna gett feedback om integrationen:

- Kunder kan inte bestämma vilka loggar som ska lagras och det kan bli dyrt att lagra alla loggar.
- Aktuella HDInsight-schemaloggar följer inte konsekventa namngivningskonventioner och vissa tabeller upprepas.
- Kunder vill ha en instrumentpanel för att enkelt övervaka KPI:et för sina HDInsight-kluster.
- Kunderna måste gå till Log Analytics för att köra enkla frågor.

## <a name="solution-overview"></a>Lösningsöversikt

Med tanke på kundfeedback Azure HDInsight teamet investerat i integrering med Azure Monitor. Den här integreringen möjliggör:

- En ny uppsättning tabeller på kundernas Log Analytics-arbetsyta. De nya tabellerna levereras via en ny Log Analytics-pipeline.
- Högre tillförlitlighet
- Snabbare loggleverans
- Resursbaserad tabellgruppering och standardfrågor

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Fördelar med den nya Azure Monitor integreringen

Det här dokumentet beskriver ändringarna i Azure Monitor integrering och innehåller metodtips för att använda de nya tabellerna.

**Omdesignade scheman:** Schemaformateringen för den nya Azure Monitor är bättre ordnad och lätt att förstå. Det finns två tredjedelar färre scheman för att ta bort så mycket tvetydighet i de äldre schemana som möjligt.

**Selektiv loggning (släpps snart):** Det finns loggar och mått tillgängliga via Log Analytics. För att hjälpa dig att spara på övervakningskostnader lanserar vi en ny funktion för selektiv loggning. Använd den här funktionen för att aktivera och inaktivera olika loggar och måttkällor. Med den här funktionen behöver du bara betala för det du använder.

**Integrering av loggklusterportalen:** **Fönstret** Loggar är nytt i HDInsight-klusterportalen. Alla med åtkomst till klustret kan gå till det här fönstret för att fråga vilken tabell som klusterresursen skickar poster till. Användare behöver inte längre åtkomst till Log Analytics-arbetsytan för att se posterna för en specifik klusterresurs.

**Integrering av insights-klusterportalen:** **Fönstret** Insikter är också nytt för HDInsight-klusterportalen. När du har aktiverar den nya Azure Monitor-integreringen kan du välja fönstret **Insikter** så fylls en instrumentpanel för inbyggda loggar och mått som är specifik för klustrets typ i automatiskt åt dig. Dessa instrumentpaneler har gjorts om från våra tidigare Azure-lösningar. De ger dig djupgående insikter om klustrets prestanda och hälsa.

**Insikter i stor skala:** Du  kan använda den nya arbetsboken Insikter i stor skala **i Azure Monitor portalen** för att övervaka dina klusters hälsa och prestanda i olika prenumerationer.

## <a name="customer-scenarios"></a>Kundscenarier

I följande avsnitt beskrivs hur kunder kan använda den nya Azure Monitor integrering i olika scenarier. Avsnittet [Aktivera en ny Azure Monitor integrering](#activate-a-new-azure-monitor-integration) beskriver hur du aktiverar och använder den nya Azure Monitor integreringen. Avsnittet Migrera från klassisk Azure Monitor till den [nya Azure Monitor-integreringen](#migrate-to-the-new-azure-monitor-integration) innehåller ytterligare information för användare som är beroende av den gamla Azure Monitor integreringen.

> [!NOTE]
> Endast kluster som skapats i slutet av september 2020 och senare är berättigade till den nya Azure Monitoring-integreringen.

## <a name="activate-a-new-azure-monitor-integration"></a>Aktivera en ny Azure Monitor integrering 

> [!NOTE]
> Du måste ha skapat en Log Analytics-arbetsyta i en prenumeration som du har åtkomst till innan du aktiverar den nya integreringen. Mer information om hur du skapar en Log Analytics-arbetsyta finns i [Skapa en Log Analytics-arbetsyta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Aktivera den nya integreringen genom att gå till klustrets portalsida och rulla nedåt i menyn till vänster tills du kommer till **avsnittet** Övervakning. I avsnittet **Övervakning** väljer du **Övervaka integrering.** Välj sedan Aktivera **så** kan du välja den Log Analytics-arbetsyta som du vill att loggarna ska skickas till. Välj **Spara** när du har valt din arbetsyta. 

### <a name="access-the-new-tables"></a>Få åtkomst till de nya tabellerna

Det finns två sätt att komma åt de nya tabellerna. 

### <a name="approach-1"></a>Metod 1:
Det första sättet att komma åt de nya tabellerna är via Log Analytics-arbetsytan. 

1. Gå till Log Analytics-arbetsytan som du valde när du aktiverade integreringen. 
2. Rulla nedåt i menyn till vänster på skärmen och välj **Loggar**. En loggfrågeredigerare visas med en lista över alla tabeller på arbetsytan. 
3. Om tabellerna är grupperade efter **Lösning** finns de nya HDI-tabellerna under **avsnittet Logghantering.** 
4. Om du grupperar tabellerna **efter Resurstyp** finns tabellerna under avsnittet **HDInsight-kluster** enligt bilden nedan. 

> [!NOTE]
> Den här processen beskriver hur loggarna koms åt i den gamla integreringen. Detta kräver att användaren har åtkomst till arbetsytan.

### <a name="approach-2"></a>Metod 2:

Det andra sättet att komma åt de nya tabellerna är via åtkomst till klusterportalen.
 
1. Gå till klusterportalsidan och rulla ned på menyn till vänster tills du ser **avsnittet** Övervakning. I det här avsnittet visas fönstret **Loggar.** 
2. Välj **Loggar** så visas frågeredigeraren Logs. Redigeraren innehåller alla loggar som är associerade med klusterresursen. Du skickade loggarna till Log Analytics-arbetsytan när du aktiverade integreringen. De här loggarna ger resursbaserad åtkomst (RBAC). Med RBAC kan användare som har åtkomst till klustret men inte till arbetsytan se loggarna som är associerade med klustret.

Som jämförelse visar följande skärmbilder vyn för den äldre integreringsarbetsytan och den nya vyn för integrationsarbetsytan:

**Vy för äldre integrationsarbetsyta**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Skärmbild som visar vyn för den äldre integreringsarbetsytan." border="false":::

**Vy för ny integrationsarbetsyta**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Skärmbild som visar vyn för den nya integreringsarbetsytan." border="false":::

### <a name="use-the-new-tables"></a>Använda de nya tabellerna

De här integreringarna kan hjälpa dig att använda de nya tabellerna:

#### <a name="default-queries-to-use-with-new-tables"></a>Standardfrågor som ska användas med nya tabeller

I frågeredigeraren för Loggar anger du **växlingsknappen till Frågor** ovanför tabelllistan. Kontrollera att du grupperar frågorna efter **Resurstyp** och att det inte finns något filter för en annan resurstyp än **HDInsight-kluster.** Följande bild visar hur resultatet ser ut när de grupperas efter **resurstyp** och filtreras för **HDInsight-kluster**. Välj bara en så visas den i frågeredigeraren För loggar. Se till att läsa kommentarerna som ingår i frågorna eftersom vissa kräver att du anger viss information, till exempel ditt klusternamn, för att frågan ska kunna köras.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Skärmbild som visar den grupperade resurstypen för standardfrågeresultat." border="true":::


#### <a name="create-your-own-queries"></a>Skapa egna frågor

Du kan ange egna frågor i frågeredigeraren för Loggar. Frågor som används på de gamla tabellerna är inte giltiga i de nya tabellerna eftersom många av de nya tabellerna har nya, förfinade scheman. Standardfrågorna är bra referenser för att forma frågor i de nya tabellerna.

#### <a name="insights"></a>Insikter

Insikter är klusterspecifika visualiseringsinstrumentpaneler som görs med [hjälp av Azure-arbetsböcker.](../azure-monitor/platform/workbooks-overview.md) Dessa instrumentpaneler ger dig detaljerade grafer och visualiseringar av hur klustret körs. Instrumentpanelerna har avsnitt för varje klustertyp, YARN, systemmått och komponentloggar. Du kan komma åt klustrets instrumentpanel genom att gå till klustrets  sida i portalen, rulla ned till avsnittet Övervakning och välja **fönstret** Insikter. Instrumentpanelen läses in automatiskt om du har aktiverat den nya integreringen. Tillåt några sekunder för graferna att läsas in när de frågar loggarna.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="Skärmbild som visar visualiseringsinstrumentpanelen.":::

#### <a name="custom-azure-workbooks"></a>Anpassade Azure-arbetsböcker

Du kan skapa egna Azure-arbetsböcker med anpassade grafer och visualiseringar. På **klustrets** portalsida rullar du ned till **avsnittet** Övervakning och väljer fönstret Arbetsböcker i menyn till vänster. Du kan antingen börja använda en tom mall eller använda någon av mallarna i avsnittet **HDInsight-kluster.** Det finns en mall för varje klustertyp. Mallar är användbara om du vill spara specifika anpassningar som HDInsight Insights inte tillhandahåller som standard. Skicka gärna in begäranden om nya funktioner i HDInsight Insights om du tycker att de saknar något.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Arbetsböcker i stor skala för Azure Monitor integreringar

Använd vår nya arbetsbok i stor skala för att få en övervakningsupplevelse med flera kluster för dina kluster. Vår arbetsbok i stor skala visar vilka av dina kluster som har övervakningspipelinen aktiverad. Arbetsboken ger dig också ett enkelt sätt att kontrollera hälsotillståndet för flera kluster samtidigt. Så här visar du den här arbetsboken:

1. Gå till **Azure Monitor** från Azure Portal startsidan
2. När du är **Azure Monitor** sidan väljer du **Insights Hub** under **avsnittet** Insikter.
3. Välj **HDInsight-kluster** under **avsnittet** Analys.

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Skärmbild som visar arbetsboken i stor skala." border="false":::

#### <a name="alerts"></a>Aviseringar

Du kan lägga till anpassade aviseringar i dina kluster och arbetsytor i Loggfrågeredigeraren. Gå till frågeredigeraren loggar genom att välja **fönstret Loggar** från antingen klustret eller arbetsytans portal. Kör en fråga och välj sedan **Ny aviseringsregel** enligt följande skärmbild. Mer information finns i konfigurera [aviseringar.](../azure-monitor/platform/alerts-log.md)

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Skärmbild som visar den nya regelaviseringen." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Migrera till den nya Azure Monitor-integreringen

Om du använder den klassiska Azure Monitor-integreringen måste du göra vissa justeringar i de nya tabellformaten när du har växlat till den Azure Monitor integreringen.

Om du vill aktivera Azure Monitor integrering följer du stegen i avsnittet [Aktivera en ny Azure Monitor integrering.](#activate-a-new-azure-monitor-integration)

### <a name="run-queries-in-log-analytics"></a>Köra frågor i Log Analytics

Eftersom det nya tabellformatet skiljer sig från det tidigare måste dina frågor omarbetas så att du kan använda våra nya tabeller. När du aktiverar den Azure Monitor integreringen kan du bläddra bland tabellerna och schemana för att identifiera de fält som används i dina gamla frågor.

Vi tillhandahåller en [mappningstabell](#appendix-table-mapping) mellan den gamla tabellen och den nya tabellen som hjälper dig att snabbt hitta de nya fält som du behöver använda för att migrera dina instrumentpaneler och frågor.

**Standardfrågor:** Vi har skapat standardfrågor som visar hur du använder de nya tabellerna för vanliga situationer. Standardfrågorna visar också vilken information som är tillgänglig i varje tabell. Du kan komma åt standardfrågorna genom att följa anvisningarna i [avsnittet Standardfrågor som ska användas med nya](#default-queries-to-use-with-new-tables) tabeller i den här artikeln.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Uppdatera instrumentpaneler för HDInsight-kluster

Om du har skapat flera instrumentpaneler för att övervaka dina HDInsight-kluster måste du justera frågan bakom tabellen när du aktiverar den nya Azure Monitor integreringen. Tabellnamnet eller fältnamnet kan ändras i den nya integreringen, men all information som du har i den gamla integreringen ingår.

Se [mappningstabellen](#appendix-table-mapping) mellan den gamla tabellen/schemat till den nya tabellen/schemat för att uppdatera frågan bakom instrumentpanelerna.

#### <a name="out-of-box-dashboards"></a>In-of-box-instrumentpaneler 

Vi har också förbättrat de in-box-instrumentpaneler som finns på klusternivå. Det finns en knapp längst upp till höger i varje graf som gör att du kan se den underliggande frågan som producerar informationen. Diagrammet är ett bra sätt att bekanta dig med hur de nya tabellerna kan efterfrågas effektivt. Du kan komma åt instrumentpanelerna genom att följa anvisningarna i [](#insights) avsnitten Insights and [At-scale workbooks](#at-scale-workbooks-for-new-azure-monitor-integrations) for new Azure Monitor integrations (Insikter och skalningsarbetsböcker för nya Azure Monitor integreringar).

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>Använda en instrumentpanel för HDInsight-övervakning i stor skala

Om du använder den inbyggda övervakningsinstrumentpanelen för HDInsight-kluster som HDInsight Spark-övervakning och interaktiv HDInsight-övervakning, arbetar vi för att ge dig samma funktioner på Azure Monitor-portalen.

Du ser att det finns ett alternativ för HDInsight-kluster i Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Skärmbild som visar HDInsight-alternativet i Azure Monitor." border="false":::

Med Azure Monitor-portalens Insights Hub kan du övervaka flera HDInsight-kluster på ett och samma ställe. Vi organiserar klustren baserat på arbetsbelastningstyp, så du ser typer som Spark, HBase och Hive. I stället för att gå till flera instrumentpaneler kan du nu övervaka alla dina HDInsight-kluster i den här vyn.

> [!NOTE]
> Mer information finns i avsnitten [Insights](#insights) and [At-scale workbooks for new Azure Monitor integrations](#at-scale-workbooks-for-new-azure-monitor-integrations) i den här artikeln.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Aktivera båda integreringarna för att påskynda migreringen

Du kan aktivera både klassiska och nya Azure Monitor-integreringar samtidigt på ett kluster som är berättigat till båda integreringarna för snabb migrering till den Azure Monitor integreringen. Den nya integreringen är tillgänglig för alla kluster som skapats efter mitten av september 2020.

På så sätt kan du enkelt göra en jämförelse sida vid sida för de frågor som du använder.

### <a name="enabling-the-classic-integration"></a>Aktivera klassisk integrering

Om du använder ett kluster som skapats efter mitten av september 2020 visas den nya portalupplevelsen i klustrets portal. Om du vill aktivera den nya pipelinen kan du följa stegen i avsnittet [Aktivera en Azure Monitor integrering.](#activate-a-new-azure-monitor-integration) Om du vill aktivera den klassiska integreringen i det här klustret går du till klustrets portalsida. Välj fönstret **Övervaka integrering** i **avsnittet** Övervakning på menyn till vänster på klusterportalsidan. Välj **Konfigurera Azure Monitor för HDInsight-klusterintegrering (klassisk).** En sidokontext visas med en växlingsknapp som du kan använda för att aktivera och inaktivera den klassiska Azure Monitoring-integreringen. 

> [!NOTE]
> Du ser inga loggar eller mått från den klassiska integreringen via klusterportalens sida med loggar och insikter. Endast de nya integreringsloggarna och måtten kommer att finnas på dessa platser.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Skärmbild som visar länken för att få åtkomst till den klassiska integreringen." border="false":::

Att skapa nya kluster med klassisk Azure Monitor är inte tillgängligt efter den 1 juli 2021.

## <a name="release-and-support-timeline"></a>Tidslinje för lansering och support

- Klassisk Azure Monitoring-integrering är inte tillgänglig efter den 15 oktober 2021. Du kan inte aktivera klassisk Azure Monitoring-integrering efter det datumet.
- Befintliga klassiska Azure-övervakningsintegreringar fortsätter att fungera. Det kommer att finnas begränsat stöd för den klassiska Azure Monitoring-integreringen. 
  - Problem undersöks när kunderna skickar in supportärenden.
  - Om lösningen kräver bildändring bör kunderna gå över till den nya integreringen.
  - Vi kommer inte att korrigera de klassiska Azure Monitoring-integreringsklusterna förutom kritiska säkerhetsproblem.

## <a name="appendix-table-mapping"></a>Bilaga: Tabellmappning

Följande diagram visar tabellmappningarna från den klassiska Azure Monitoring-integreringen till vår nya. Kolumnen **Arbetsbelastning** beskriver vilken arbetsbelastning varje tabell är associerad med. Raden **Ny tabell** visar namnet på den nya tabellen. På **raden** Beskrivning beskrivs vilken typ av loggar/mått som ska vara tillgängliga i den här tabellen. Raden **Gammal tabell** är en lista över alla tabeller från den klassiska Azure Monitor integrering vars data nu finns i tabellen som listas på raden **Ny** tabell.

> [!NOTE]
> Vissa tabeller är nya och baseras inte på gamla tabeller.

## <a name="general-workload-tables"></a>Allmänna arbetsbelastningstabeller

| Ny tabell | Information |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller systemmått som samlats in från Ambari. Måtten kommer nu från varje nod i klustret (förutom kantnoder) i stället för bara de två huvudnoderna. Varje mått är nu en kolumn och varje mått rapporteras en gång per post.</li><li>**Gammal tabell:** metrics \_ cpu nice \_ \_ cl, metrics \_ cpu system \_ \_ cl, metrics \_ cpu user \_ \_ cl, metrics \_ memory cache \_ \_ CL, metrics \_ memory swap \_ \_ CL, metrics \_ memory total \_ \_ CLmetrics \_ memory buffer \_ \_ CL, metrics \_ load \_ 1min \_ CL, metrics \_ load cpu \_ \_ CL, metrics \_ load nodes \_ \_ CL, metrics \_ load \_ procs \_ CL, metrics \_ network in \_ \_ CL, metrics \_ network out \_ \_ CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Beskrivning:** Den här tabellen innehåller Ambari-klusteraviseringar från varje nod i klustret (förutom kantnoder). Varje avisering är en post i den här tabellen.</li><li>**Gammal tabell:** \_ måttklusteraviseringar \_ \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller poster från Ambari Audit- och Auth-loggarna.</li><li>**Gammal tabell:** log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla poster från Ranger-granskningsloggen för ESP-kluster.</li><li>**Gammal tabell:** \_ Ranger-granskningsloggar \_ \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Beskrivning:** Den här tabellen innehåller granskningsinformation för gatewaynoder. Det är samma format som tabellen i kolumnen Gamla tabeller. **Den finns fortfarande i avsnittet Anpassade loggar.**</li><li>**Gammal tabell:** \_ logggateway granska \_ \_ CL</li></ul>|

## <a name="spark-workload"></a>Spark-arbetsbelastning

> [!NOTE]
> Spark-programrelaterade tabeller har ersatts med 11 nya Spark-tabeller (från och med HDInsightSpark*) som ger mer detaljerad information om dina Spark-arbetsbelastningar.


| Ny tabell | Information |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla loggar relaterade till Spark och dess relaterade komponent: Livy och Jupyter.</li><li>**Gammal tabell:** log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation för Spark-program, inklusive sändnings- och slutförandetid, app-ID och AppName. Det är användbart för att hålla reda på när program startades och slutfördes. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation relaterad till Sparks Block Manager. Den innehåller information som användning av körminne.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation relaterad till miljön som ett program körs i, inklusive Spark-distributionsläge, Huvud och information om utföraren.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation om Spark Executor-användningen för av ett program.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation som inte får plats i någon annan Spark-tabell. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller information om Spark-jobb, inklusive start- och sluttider, resultat och associerade faser.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation om Spark SQL-frågor, inklusive planinformation och beskrivning samt start- och sluttider.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation för Spark-faser, inklusive start- och slutförandetider, felstatus och detaljerad körningsinformation.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Beskrivning:** Den här tabellen innehåller prestandamått för faser och uppgifter.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation för Spark-uppgifter, inklusive start- och slutförandetid, associerade faser, körningsstatus och uppgiftstyp.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Beskrivning:** Den här tabellen innehåller händelseinformation för Jupyter Notebooks.</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN-arbetsbelastning

| Ny tabell | Information |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller JMX-mått från Hadoop- och YARN-ramverken. Den innehåller samma JMX-mått som de gamla tabellerna för anpassade loggar, plus fler mått som vi anser vara viktiga. Vi har lagt till mått för tidslinjeserver, Nodhanteraren och jobbhistorikservern. Den innehåller ett mått per post.</li><li>Gammal **tabell:** \_ metrics resourcemanager \_ clustermetrics \_ CL, metrics \_ resourcemanager \_ jvm \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ joblauncher \_ CL, metrics \_ resourcemanager \_ queue root default \_ \_ \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla loggar som genereras från Hadoop- och YARN-ramverken.</li><li>**Gammal tabell:** log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive/LLAP-arbetsbelastning 

| Ny tabell | Information |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller JMX-mått från Hive- och LLAP-ramverken. Den innehåller samma JMX-mått som de gamla anpassade loggarna. Den innehåller ett mått per post.</li><li>Gammal **tabell:** llap \_ metrics \_ hiveserver2 \_ CL, llap \_ metrics \_ hs2 \_ metrics \_ subsystemllap \_ metrics \_ jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info \_ CL, \_ \_ \_ llap metrics allocator \_ info \_ CL, llap \_ metrics \_ deamon \_ jvm \_ CL, \_ llap metrics \_ io \_ CL, llap \_ metrics \_ executor \_ metrics \_ CL, llap \_ metrics \_ metricssystem stats \_ \_ CL, llap \_ metrics cache \_ \_ CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller loggar som genereras från Hive, LLAP och deras relaterade komponenter: WebHCat och Zeppelin.</li><li>**Gammal tabell:** log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ zeppelin \_ zeppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Kafka-arbetsbelastning

| Ny tabell | Information |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller JMX-mått från Kafka. Den innehåller samma JMX-mått som de gamla anpassade loggarna, plus fler mått som vi anser vara viktiga. Den innehåller ett mått per post.</li><li>**Gammal tabell:** mått \_ kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla loggar som genereras från Kafka Brokers.</li><li>**Gammal tabell:** log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>HBase-arbetsbelastning

| Ny tabell | Information |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller JMX-mått från HBase. Den innehåller samma JMX-mått från tabellerna som anges i kolumnen Gammalt schema. Till skillnad från de gamla tabellerna innehåller varje rad ett mått.</li><li>**Gammal tabell:** mått \_ regionserver \_ CL, metrics \_ regionserver \_ wal \_ CL, metrics \_ regionserver \_ ipc \_ CL, metrics \_ regionserver \_ os \_ CL, metrics \_ regionserver replication \_ \_ CL, metrics \_ restserver \_ CL, metrics \_ restserver \_ jvm \_ CL, metrics \_ hmaster \_ assignmentmanager \_ CL, metrics \_ hmaster \_ ipc \_ CL, metrics \_ hmaser \_ os \_ CL, metrics \_ hmaster \_ balancer \_ CL, metrics \_ \_ hmaster jvm \_ CL, metrics \_ hmaster \_ CL,metrics \_ hmaster \_ fs \_ CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller loggar från HBase och dess relaterade komponenter: Phoenix och HDFS.</li><li>**Gammal tabell:** log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log \_ garbage collector \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Storm-arbetsbelastning

| Ny tabell | Information |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller samma JMX-mått som tabellerna i avsnittet Gamla tabeller. Dess rader innehåller ett mått per post.</li><li>**Gammal tabell:** mått \_ stormnimbus \_ CL, \_ mått stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Beskrivning:** Den här tabellen innehåller mått på topologinivå från Storm. Det är samma form som tabellen som anges i avsnittet Gamla tabeller.</li><li>**Gammal tabell:** mått \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla loggar som genereras från Storm.</li><li>**Gammal tabell:** \_ loggövervakare \_ CL, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Oozie-arbetsbelastning

| Ny tabell | Information |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Beskrivning:** Den här tabellen innehåller alla loggar som genereras från Oozie-ramverket.</li><li>**Gammal tabell:** Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Nästa steg
[Fråga Azure Monitor loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
