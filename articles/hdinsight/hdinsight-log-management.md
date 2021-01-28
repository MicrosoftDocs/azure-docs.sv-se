---
title: Hantera loggar för ett HDInsight-kluster – Azure HDInsight
description: Bestäm typ, storlek och bevarande principer för HDInsight-aktivitets logg filen.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 0a6e837284917129bb56c6230e68927b79e95dac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945277"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Hantera loggar för ett HDInsight-kluster

An-HDInsight klustret skapar en mängd olika loggfiler. Till exempel Apache Hadoop och relaterade tjänster, till exempel Apache Spark, skapa detaljerade jobb körnings loggar. Logg fils hantering är en del av att underhålla ett friskt HDInsight-kluster. Det kan också finnas regler för att arkivera loggen.  På grund av antalet loggfiler och storleken på loggfiler kan du optimera logg lagring och arkivering av tjänster för att hantera kostnader.

Hantering av HDInsight-kluster loggar innehåller information om alla aspekter i kluster miljön. Den här informationen omfattar alla associerade Azure Service-loggar, kluster konfiguration, jobb körnings information, eventuella fel tillstånd och andra data vid behov.

Vanliga steg i hantering av HDInsight-loggen är:

* Steg 1: Fastställ principer för logg bevarande
* Steg 2: hantera konfigurations loggar för kluster tjänst versioner
* Steg 3: hantera loggfiler för körning av kluster jobb
* Steg 4: beräkna logg volymens lagrings storlek och kostnader
* Steg 5: Bestäm principer och processer för logg Arkiv

## <a name="step-1-determine-log-retention-policies"></a>Steg 1: Fastställ principer för logg bevarande

Det första steget i att skapa en hanterings strategi för HDInsight-kluster är att samla in information om affärs scenarier och lagrings krav för jobb körnings historik.

### <a name="cluster-details"></a>Kluster information

Följande kluster information är användbar för att hjälpa till att samla in information i din strategi för logg hantering. Samla in den här informationen från alla HDInsight-kluster som du har skapat i ett visst Azure-konto.

* Klusternamn
* Kluster region och tillgänglighets zon för Azure
* Kluster tillstånd, inklusive information om den senaste tillstånds ändringen
* Ange och antal HDInsight-instanser som angetts för huvud-, kärn-och aktivitets noderna

Du kan få ut mesta möjliga av den här informationen på den översta nivån med hjälp av Azure Portal.  Du kan också använda [Azure CLI](/cli/azure/) för att få information om dina HDInsight-kluster:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Du kan också använda PowerShell för att visa den här informationen.  Mer information finns i [Apache Hantera Hadoop-kluster i HDInsight med hjälp av Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Förstå arbets belastningarna som körs i klustren

Det är viktigt att förstå de arbets belastnings typer som körs på dina HDInsight-kluster för att utforma lämpliga loggnings strategier för varje typ.

* Är arbets belastningarna experimentella (till exempel utveckling eller testning) eller produktions kvalitet?
* Hur ofta körs arbets belastningarna för produktions kvalitet normalt?
* Är någon av arbets belastningarna resurs intensiv och/eller lång drift?
* Använder någon av arbets belastningarna en komplex uppsättning Hadoop-tjänster där flera typer av loggar skapas?
* Har någon av arbets belastningarna kopplat härkomst krav för myndighets körning?

### <a name="example-log-retention-patterns-and-practices"></a>Exempel på logg kvarhållning mönster och metoder

* Överväg att underhålla data härkomst spårning genom att lägga till en identifierare till varje loggpost eller via andra tekniker. På så sätt kan du spåra den ursprungliga data källan och åtgärden och följa data via varje steg för att förstå dess konsekvens och giltighet.

* Överväg hur du kan samla in loggar från klustret eller från fler än ett kluster och sortera dem för exempelvis granskning, övervakning, planering och aviseringar. Du kan använda en anpassad lösning för att komma åt och hämta loggfilerna regelbundet och kombinera och analysera dem för att ange en instrument panels visning. Du kan också lägga till ytterligare funktioner för aviseringar om säkerhets-eller haveri identifiering. Du kan bygga dessa verktyg med PowerShell, HDInsight SDK: er eller kod som använder den klassiska Azure-distributions modellen.

* Överväg om en övervaknings lösning eller tjänst är en användbar förmån. Microsoft System Center tillhandahåller ett [hanterings paket för HDInsight](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). Du kan också använda verktyg från tredje part som Apache Chukwa och ganglia för att samla in och centralisera loggar. Många företag erbjuder tjänster för att övervaka Hadoop-baserade Big data-lösningar, till exempel: CenterY, CompuWare APM, Sematext SPM och Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Steg 2: Hantera kluster tjänst versioner och Visa loggar

Ett typiskt HDInsight-kluster använder flera tjänster och program varu paket med öppen källkod (till exempel Apache HBase, Apache Spark och så vidare). För vissa arbets belastningar, till exempel Bioinformatics, kan du behöva behålla tjänst konfigurations logg historiken förutom jobb körnings loggarna.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa kluster konfigurations inställningar med Ambari-ANVÄNDARGRÄNSSNITTET

Apache Ambari fören klar hanteringen, konfigurationen och övervakningen av ett HDInsight-kluster genom att tillhandahålla ett webb gränssnitt och en REST API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj rutan **kluster instrument panel** på sidan Azure Portal HDInsight för att öppna länk sidan **kluster instrument paneler** .  Välj sedan fönstret **instrument panel för HDInsight-kluster** för att öppna AMBARI-användargränssnittet.  Du uppmanas att ange dina autentiseringsuppgifter för ditt kluster inloggnings konto.

Öppna en lista över tjänstevyer genom att välja fönstret **Ambari vyer** på sidan Azure Portal för HDInsight.  Den här listan varierar beroende på vilka bibliotek som du har installerat.  Du kan till exempel se garn Queue Manager, Hive och Tez vy.  Välj en tjänst länk om du vill visa information om konfigurationen och tjänsten.  Sidan Ambari UI **stack och version** innehåller information om konfigurations historiken för kluster tjänster och tjänster. Om du vill navigera till det här avsnittet i Ambari-ANVÄNDARGRÄNSSNITTET väljer du **Administratörs** menyn och sedan **stackar och versioner**.  Välj fliken **versioner** för att se information om tjänst version.

![Apache Ambari admin stack och versioner](./media/hdinsight-log-management/ambari-stack-versions.png)

Med hjälp av användar gränssnittet för Ambari kan du ladda ned konfigurationen för alla (eller alla) tjänster som körs på en viss värd (eller nod) i klustret.  Välj menyn **värdar** och sedan länken för värden av intresse. På värddatorns sida väljer du knappen **värd åtgärder** och **laddar ned klient konfigurationerna**.

![Apache Ambari Ladda ned värd klient konfigurationerna](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Visa skript åtgärds loggar

HDInsight [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md) kör skript i ett kluster, antingen manuellt eller när det anges. Skript åtgärder kan till exempel användas för att installera ytterligare program vara i klustret eller ändra konfigurations inställningarna från standardvärdena. Skript åtgärds loggar kan ge insikter om fel som uppstått under installationen av klustret och även konfigurations inställningarnas ändringar som kan påverka kluster prestanda och tillgänglighet.  Om du vill se status för en skript åtgärd väljer du knappen **Ops** i AMBARI-användargränssnittet eller åtkomst till status loggarna på standard lagrings kontot. Lagrings loggarna är tillgängliga på `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Visa status loggar för Ambari-aviseringar

Apache Ambari skriver aviserings status ändringar till `ambari-alerts.log` . Den fullständiga sökvägen är `/var/log/ambari-server/ambari-alerts.log` . Om du vill aktivera fel sökning för loggen ändrar du en egenskap i `/etc/ambari-server/conf/log4j.properties.` ändra och sedan post under `# Log alert state changes` från:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Steg 3: hantera loggfilerna för körning av kluster jobb

Nästa steg är att granska loggfilerna för jobb körning för de olika tjänsterna.  Tjänsterna kan omfatta Apache HBase, Apache Spark och många andra. Ett Hadoop-kluster genererar ett stort antal utförliga loggar, så att du kan fastställa vilka loggar som är användbara (och vilka inte) kan vara tids krävande.  Att förstå loggnings systemet är viktigt för riktad hantering av loggfiler.  Följande bild är en exempel logg fil.

![Exempel på HDInsight-exempel på logg filens utdata](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Komma åt Hadoop-loggfilerna

HDInsight lagrar loggfilerna både i kluster fil systemet och i Azure Storage. Du kan granska loggfilerna i klustret genom att öppna en [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) -anslutning till klustret och bläddra i fil systemet, eller genom att använda status portalen för HADOOP-garn på servern för fjärrhead-noden. Du kan granska loggfilerna i Azure Storage med något av de verktyg som kan komma åt och hämta data från Azure Storage. Exempel är [AzCopy](../storage/common/storage-use-azcopy-v10.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)och Visual Studio-Server Explorer. Du kan också använda PowerShell och Azure Storage klient bibliotek eller Azure .NET-SDK: er för att komma åt data i Azure Blob Storage.

Hadoop kör jobbet för jobben som *aktivitets försök* på olika noder i klustret. HDInsight kan initiera spekulativa uppgifts försök och avsluta alla andra uppgifts försök som inte slutförs först. Detta genererar betydande aktivitet som är loggad till Controller-, stderr-och syslog-loggfilerna i farten. Dessutom körs flera aktivitets försök samtidigt, men en loggfil kan bara visa resultat linjärt.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight-loggar som skrivs till Azure Blob Storage

HDInsight-kluster konfigureras att skriva aktivitets loggar till ett Azure Blob Storage-konto för alla jobb som skickas med hjälp av Azure PowerShell-cmdlets eller .NET-jobbets sändnings-API: er.  Om du skickar jobb via SSH till klustret lagras körnings loggnings informationen i Azure-tabellerna enligt beskrivningen i föregående avsnitt.

Förutom de kärn loggfiler som genereras av HDInsight genererar installerade tjänster som t. ex. garn även loggfiler för jobb körning.  Antalet och typen av loggfiler beror på vilka tjänster som är installerade.  Vanliga tjänster är Apache HBase, Apache Spark och så vidare.  Undersök körnings filerna för jobb loggen för varje tjänst för att förstå de övergripande loggfilerna som finns i klustret.  Varje tjänst har sina egna unika metoder för loggning och platser för lagring av loggfiler.  Som exempel beskrivs information för att komma åt de vanligaste tjänsteloggfilerna (från garn) i följande avsnitt.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight-loggar som genereras av garn

GARN sammanställer loggar över alla behållare på en arbetsnoden och lagrar dessa loggar som en sammanslagen loggfil per arbets nod. Loggen lagras i standard fil systemet när ett program har slutförts. Ditt program kan använda hundratals eller tusentals behållare, men loggar för alla behållare som körs på en enda arbetsnoden sammanställs alltid i en enda fil. Det finns bara en logg per arbets nod som används av ditt program. Logg agg regering är aktiverat som standard i HDInsight-kluster version 3,0 och senare. Sammanställda loggar finns i standard lagrings utrymmet för klustret.

```
/app-logs/<user>/logs/<applicationId>
```

De sammanställda loggarna kan inte läsas direkt, eftersom de skrivs i ett TFile binärt format som indexeras av container. Använd garn-ResourceManager-loggarna eller CLI-verktygen för att visa dessa loggar som oformaterad text för program eller behållare av intresse.

#### <a name="yarn-cli-tools"></a>GARN CLI-verktyg

Om du vill använda garn CLI-verktygen måste du först ansluta till HDInsight-klustret med SSH. Ange `<applicationId>` `<user-who-started-the-application>` `<containerId>` information om,, och `<worker-node-address>` när du kör de här kommandona. Du kan visa loggarna som oformaterad text med något av följande kommandon:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>GARN-ResourceManager-gränssnitt

Användar gränssnittet för garn-ResourceManager körs på kluster huvud noden och nås via Ambari-webbgränssnittet. Använd följande steg för att Visa garn loggarna:

1. Gå till `https://CLUSTERNAME.azurehdinsight.net` i en webbläsare. Ersätt KLUSTERNAMN med namnet på ditt HDInsight-kluster.
2. I listan över tjänster till vänster väljer du garn.
3. I list rutan snabb länkar väljer du en av klustrets huvud-noder och väljer sedan **ResourceManager-loggar**. En lista med länkar till garn loggar visas.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Steg 4: beräkna logg volymens lagrings storlek och kostnader

När du har slutfört föregående steg har du en förståelse för de typer och volymer av loggfiler som dina HDInsight-kluster tillverkar.

Därefter analyserar du volymen av loggdata i nyckel logg lagrings platser under en viss tids period. Du kan till exempel analysera volym och tillväxt över 30-60-90-dagars perioder.  Registrera den här informationen i ett kalkyl blad eller Använd andra verktyg som Visual Studio, Azure Storage Explorer eller Power Query för Excel. ```

Nu har du tillräckligt med information för att skapa en logg hanterings strategi för nyckel loggarna.  Använd ditt kalkyl blad (eller valfritt verktyg) om du vill beräkna storleks ökningen och logg lagringen för Azure-tjänster i loggen.  Överväg även eventuella krav för logg kvarhållning för den uppsättning loggar som du undersöker.  Nu kan du omberäkna framtida logg lagrings kostnader efter att du har fastställt vilka loggfiler som kan tas bort (om det finns några) och vilka loggar som ska behållas och arkiveras för billigare Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Steg 5: Bestäm principer och processer för logg Arkiv

När du har fastställt vilka loggfiler som kan tas bort kan du justera loggnings parametrarna på många Hadoop-tjänster för att automatiskt ta bort loggfiler efter en angiven tids period.

För vissa loggfiler kan du använda en metod för att arkivera logg filen med lägre pris. För Azure Resource Manager aktivitets loggar kan du utforska den här metoden med hjälp av Azure Portal.  Konfigurera arkivering av Resource Manager-loggar genom att välja **aktivitets logg** länken i Azure Portal för din HDInsight-instans.  Överst på sidan för aktivitets loggs ökning väljer du meny alternativet **Exportera** för att öppna fönstret **Exportera aktivitets logg** .  Fyll i prenumerationen, regionen, om du vill exportera till ett lagrings konto och hur många dagar loggen ska sparas. I det här fönstret kan du också ange om du vill exportera till en Event Hub.

![Azure Portal för hands version av export av aktivitets logg](./media/hdinsight-log-management/hdi-export-log-files.png)

Alternativt kan du arkivera skript loggen med PowerShell.  Ett exempel på PowerShell-skript finns i [arkivera Azure Automation loggar till Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Åtkomst till Azure Storage mått

Azure Storage kan konfigureras för att logga lagrings åtgärder och åtkomst. Du kan använda dessa mycket detaljerade loggar för kapacitets övervakning och planering och för gransknings begär anden till lagring. Den loggade informationen innehåller svars information, så att du kan övervaka och finjustera lösningens prestanda.
Du kan använda .NET SDK för Hadoop för att undersöka de loggfiler som genereras för Azure Storage som innehåller data för ett HDInsight-kluster.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Kontrol lera storlek och antal säkerhets kopierings index för gamla loggfiler

Om du vill kontrol lera storleken på och antalet loggfiler, anger du följande egenskaper för `RollingFileAppender` :

* `maxFileSize` är filens kritiska storlek, ovanför vilken filen har registrerats. Standardvärdet är 10 MB.
* `maxBackupIndex` anger det antal säkerhets kopierings filer som ska skapas, standard 1.

### <a name="other-log-management-techniques"></a>Andra logg hanterings tekniker

För att undvika disk utrymme kan du använda vissa OS-verktyg som [logrotate](https://linux.die.net/man/8/logrotate) för att hantera hanteringen av loggfiler. Du kan konfigurera `logrotate` så att de körs dagligen, komprimera loggfiler och ta bort gamla. Ditt tillvägagångs sätt beror på dina krav, till exempel hur länge du vill behålla loggfilerna på lokala noder.  

Du kan också kontrol lera om fel SÖKNINGs loggning har Aktiver ATS för en eller flera tjänster, vilket avsevärt ökar logg storleken för utdata.  

Om du vill samla in loggar från alla noder till en central plats kan du skapa ett data flöde, till exempel mata in alla logg poster i Solr.

## <a name="next-steps"></a>Nästa steg

* [Övervaknings-och loggnings praxis för HDInsight](/previous-versions/msp-n-p/dn749790(v=pandp.10))
* [Åtkomst Apache Hadoop garn program loggar i Linux-baserade HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Så här kontrollerar du storleken på loggfiler för olika Apache Hadoop-komponenter](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
