---
title: Felsöka ett långsamt eller misslyckat jobb i Azure HDInsight-kluster
description: Diagnostisera och Felsök ett jobb som är långsamt eller inte fungerar på ett Azure HDInsight-kluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: eae5b5e1430f4e9bf1db62a4413e3b7abe3744cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699325"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Felsöka ett jobb som är långsamt eller som inte fungerar i ett HDInsight-kluster

Om ett program som bearbetar data i ett HDInsight-kluster antingen körs långsamt eller om en felkod uppstår, har du flera fel söknings alternativ. Om jobben tar längre tid att köra än förväntat, eller om du ser långsamma svars tider i allmänhet, kan det hända att det inte går att strömma från klustret, till exempel de tjänster som klustret kör. Den vanligaste orsaken till den här minskningen är dock inte tillräcklig för skalning. När du skapar ett nytt HDInsight-kluster väljer du lämpliga [storlekar för virtuella datorer](hdinsight-supported-node-configuration.md).

Om du vill diagnostisera ett långsamt eller misslyckat kluster samlar du in information om alla aspekter av miljön, till exempel associerade Azure-tjänster, kluster konfiguration och information om jobb körning. En användbar diagnostik är att försöka återskapa fel tillstånd i ett annat kluster.

* Steg 1: samla in information om problemet.
* Steg 2: verifiera HDInsight-kluster miljön.
* Steg 3: Visa klustrets hälsa.
* Steg 4: granska miljö stacken och-versionerna.
* Steg 5: granska loggfilerna för klustret.
* Steg 6: kontrol lera konfigurations inställningarna.
* Steg 7: återskapa felet på ett annat kluster.

## <a name="step-1-gather-data-about-the-issue"></a>Steg 1: samla in information om problemet

HDInsight innehåller många verktyg som du kan använda för att identifiera och felsöka problem med kluster. Följande steg vägleder dig genom de här verktygen och ger förslag på att hitta problemet.

### <a name="identify-the-problem"></a>Identifiera problemet

Du kan identifiera problemet genom att tänka på följande frågor:

* Vad förväntades mig? Vad hände i stället?
* Hur lång tid tar det att köra processen? Hur lång tid tar det att köra?
* Ska mina aktiviteter alltid köras långsamt i det här klustret? Kördes de snabbare på ett annat kluster?
* När gjorde detta problem först? Hur ofta har det hänt sedan?
* Har allt ändrats i min kluster konfiguration?

### <a name="cluster-details"></a>Kluster information

Viktig kluster information innehåller:

* Kluster namn.
* Kluster region – Sök efter [regions avbrott](https://azure.microsoft.com/status/).
* HDInsight-kluster typ och-version.
* Typ av och antal HDInsight-instanser som angetts för Head-och Worker-noderna.

Azure Portal kan ange den här informationen:

![HDInsight Azure Portal information](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Du kan också använda [Azure CLI](/cli/azure/):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Ett annat alternativ är att använda PowerShell. Mer information finns i  [hantera Apache Hadoop kluster i HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Steg 2: verifiera HDInsight-klustrets miljö

Varje HDInsight-kluster förlitar sig på olika Azure-tjänster och på program med öppen källkod som Apache HBase och Apache Spark. HDInsight-kluster kan också anropa andra Azure-tjänster, till exempel virtuella Azure-nätverk.  Ett kluster fel kan orsakas av alla tjänster som körs i klustret eller av en extern tjänst.  En konfigurations ändring i kluster tjänsten kan även orsaka att klustret slutar fungera.

### <a name="service-details"></a>Tjänst information

* Kontrol lera biblioteks versionerna med öppen källkod.
* Kontrol lera om det finns [avbrott i Azure-tjänsten](https://azure.microsoft.com/status/).  
* Sök efter begränsningar för Azure-tjänstens användning. 
* Kontrol lera Azure Virtual Network Subnet-konfigurationen.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Visa kluster konfigurations inställningar med Ambari-ANVÄNDARGRÄNSSNITTET

Apache Ambari tillhandahåller hantering och övervakning av ett HDInsight-kluster med ett webb gränssnitt och en REST API. Ambari ingår i Linux-baserade HDInsight-kluster. Välj fönstret **kluster instrument panel** på sidan Azure Portal HDInsight.  Välj **panelen för kluster instrument panelen i HDInsight** för att öppna AMBARI-användargränssnittet och ange autentiseringsuppgifter för inloggning för klustret.  

![Översikt över Apache Ambari Dashboard](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Öppna en lista över tjänstevyer genom att välja **Ambari-vyer** på sidan Azure Portal.  Den här listan beror på vilka bibliotek som är installerade. Du kan till exempel se garn Queue Manager, Hive och Tez vy.  Välj en tjänst länk om du vill visa information om konfigurationen och tjänsten.

#### <a name="check-for-azure-service-outages"></a>Sök efter avbrott i Azure-tjänsten

HDInsight förlitar sig på flera Azure-tjänster. Den kör virtuella servrar på Azure HDInsight, lagrar data och skript på Azure Blob Storage eller Azure Data Lake Storage och indexerar loggfiler i Azure Table Storage. Avbrott i dessa tjänster, även om det är sällsynt, kan orsaka problem i HDInsight. Om du har oväntade avbrott eller fel i klustret kan du kontrol lera [instrument panelen för Azure-status](https://azure.microsoft.com/status/). Status för varje tjänst visas efter region. Kontrol lera klustrets region och även regioner för relaterade tjänster.

#### <a name="check-azure-service-usage-limits"></a>Kontrol lera användnings gränser för Azure-tjänsten

Om du startar ett stort kluster eller har lanserat flera kluster samtidigt, kan ett kluster inte köras om du har överskridit en Azure-tjänstegräns. Tjänst gränserna varierar beroende på din Azure-prenumeration. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).
Du kan begära att Microsoft ökar antalet tillgängliga HDInsight-resurser (till exempel virtuella dator kärnor och VM-instanser) med en [bas kvot för Resource Manager-begäranden](../azure-portal/supportability/resource-manager-core-quotas-request.md).

#### <a name="check-the-release-version"></a>Kontrol lera slut versionen

Jämför kluster versionen med den senaste versionen av HDInsight. Varje HDInsight-utgåva innehåller förbättringar som nya program, funktioner, korrigeringar och fel korrigeringar. Problemet som påverkar klustret kan ha åtgärd ATS i den senaste versionen. Om möjligt kan du köra klustret igen med den senaste versionen av HDInsight och associerade bibliotek som Apache HBase, Apache Spark och andra.

#### <a name="restart-your-cluster-services"></a>Starta om dina kluster tjänster

Om du upplever långsammare i klustret kan du överväga att starta om tjänsterna via Ambari-ANVÄNDARGRÄNSSNITTET eller den klassiska Azure-CLI. Klustret kan drabbas av tillfälliga fel och det snabbaste sättet att starta om miljön är att öka prestandan.

## <a name="step-3-view-your-clusters-health"></a>Steg 3: Visa klustrets hälsa

HDInsight-kluster består av olika typer av noder som körs på virtuella dator instanser. Varje nod kan övervakas för resurs effekter, problem med nätverks anslutningen och andra problem som kan sakta ned klustret. Varje kluster innehåller två huvudnoder och de flesta kluster typerna innehåller en kombination av arbetar-och Edge-noder. 

En beskrivning av de olika noderna som varje kluster typ använder finns i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

I följande avsnitt beskrivs hur du kontrollerar hälsan för varje nod och det övergripande klustret.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Få en ögonblicks bild av kluster hälsan med hjälp av instrument panelen för Ambari-gränssnittet

[Instrument panelen för Ambari-gränssnittet](#view-cluster-configuration-settings-with-the-ambari-ui) ( `https://<clustername>.azurehdinsight.net` ) innehåller en översikt över kluster hälsa, till exempel drift tid, minne, nätverk och CPU-användning, HDFS disk användning och så vidare. Använd avsnittet värdar i Ambari om du vill visa resurser på en värd nivå. Du kan även stoppa och starta om tjänster.

### <a name="check-your-webhcat-service"></a>Kontrol lera din WebHCat-tjänst

Ett vanligt scenario för Apache Hive-, Apache gris-eller Apache Sqoop-jobb Miss lyckas är ett problem med [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) -tjänsten (eller *Templeton*). WebHCat är ett REST-gränssnitt för fjärrkörning av jobb, till exempel Hive, gris, bilden och MapReduce. WebHCat översätter jobb sändnings begär anden till Apache Hadoop garn program, och returnerar en status som härletts från form av garn program.  I följande avsnitt beskrivs vanliga status koder för WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (502 status kod)

Den här koden är ett allmänt meddelande från Gateway-noderna och är de vanligaste fel status koderna. En möjlig orsak till detta är att WebHCat-tjänsten är nere på den aktiva Head-noden. Använd följande spiral kommando för att kontrol lera den här möjligheten:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari visar en avisering som visar värdarna där WebHCat-tjänsten är nere. Du kan försöka att ta WebHCat-tjänsten tillbaka genom att starta om tjänsten på värddatorn.

![Apache Ambari restart WebHCat Server](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Om en WebHCat-Server fortfarande inte är igång kan du kontrol lera i åtgärds loggen om felen. Mer detaljerad information finns i och de `stderr` `stdout` filer som refereras till på noden.

#### <a name="webhcat-times-out"></a>WebHCat tids gräns

An-HDInsight gateways tids gräns för svar som tar längre tid än två minuter att returnera `502 BadGateway` . WebHCat skickar frågor till garn tjänster för jobb status, och om garn tar längre tid än två minuter att svara kan denna begäran ta lång tid.

I det här fallet granskar du följande loggar i `/var/log/webhcat` katalogen:

* **webhcat. log** är den log4j-logg som servern skriver loggar till
* **webhcat-Console. log** är STDOUT för servern när den startas
* **webhcat-Console-error. log** är stderr för Server processen

> [!NOTE]  
> Varje `webhcat.log` överförs varje dag och genererar filer med namnet `webhcat.log.YYYY-MM-DD` . Välj lämplig fil för tidsintervallet som du undersöker.

I följande avsnitt beskrivs några möjliga orsaker till WebHCat-timeout.

##### <a name="webhcat-level-timeout"></a>Timeout för WebHCat-nivå

När WebHCat är under belastning, med fler än 10 öppna Sockets, tar det längre tid att upprätta nya socketanslutningar, vilket kan resultera i en tids gräns. Om du vill visa nätverks anslutningarna till och från WebHCat använder `netstat`  du på den aktuella aktiva huvudnoden:

```bash
netstat | grep 30111
```

30111 är porten WebHCat lyssnar på. Antalet öppna Sockets måste vara mindre än 10.

Om det inte finns några öppna Sockets skapas inget resultat i föregående kommando. Om du vill kontrol lera om Templeton är igång och lyssnar på port 30111 använder du:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Timeout för garn nivå

Templeton anropar garn för att köra jobb, och kommunikationen mellan Templeton och garn kan orsaka en tids gräns.

På garn nivån finns det två typer av tids gränser:

1. Att skicka ett garn jobb kan ta tillräckligt lång tid för att orsaka en tids gräns.

    Om du öppnar `/var/log/webhcat/webhcat.log` logg filen och söker efter "köade jobb" kan du se flera poster där körnings tiden är alltför lång (>2000 MS), med poster som visar ökande vänte tider.

    Tiden för de köade jobben fortsätter att öka eftersom den hastighet med vilken nya jobb skickas är högre än den hastighet med vilken de gamla jobben har slutförts. När garn minnet har 100% använt kan joblauncher- *kön* inte längre låna kapacitet från *standard kön*. Därför kan inga fler nya jobb accepteras i joblauncher-kön. Det här beteendet kan orsaka att vänte tiden blir längre och längre, vilket orsakar ett tids gräns fel som vanligt vis följs av många andra.

    Följande bild visar joblauncher-kön vid 714,4% överanvändning. Detta är acceptabelt så länge det fortfarande finns ledig kapacitet i standard kön till låna från. Men när klustret används fullt ut och garn minnet har en kapacitet på 100%, måste nya jobb vänta, vilket kan orsaka timeout.

    ![Vyn queue i HDInsight-jobb](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Det finns två sätt att lösa det här problemet: du kan antingen minska hastigheten på nya jobb som skickas eller öka förbruknings hastigheten för gamla jobb genom att skala upp klustret.

2. GARN bearbetning kan ta lång tid, vilket kan orsaka timeout.

    * Lista alla jobb: det här är ett tids krävande anrop. Det här anropet räknar upp programmen från garn hanteraren och hämtar status från garn JobHistoryServer för varje slutfört program. Det här anropet kan ta lång tid med ett högre antal jobb.

    * Visa en lista över jobb som är äldre än sju dagar: HDInsight-GARNets JobHistoryServer har kon figurer ATS för att behålla slutförd jobb information under sju dagar ( `mapreduce.jobhistory.max-age-ms` värde) Att försöka räkna upp rensade jobb resulterar i en tids gräns.

Så här diagnostiserar du problemen:

1. Fastställa UTC-tidsintervallet för fel sökning
2. Välj lämplig `webhcat.log` (a) fil (er)
3. Sök efter VARNINGs-och fel meddelanden under den tiden

#### <a name="other-webhcat-failures"></a>Andra WebHCat-problem

1. HTTP-statuskod 500

    I de flesta fall där WebHCat returnerar 500 innehåller fel meddelandet information om felet. Annars kan du söka `webhcat.log` efter varnings-och fel meddelanden.

2. Jobb haverier

    Det kan finnas fall där interaktioner med WebHCat lyckas, men jobben Miss lyckas.

    Templeton samlar in jobb konsolens utdata som `stderr` i `statusdir` , vilket ofta är användbart vid fel sökning. `stderr` innehåller den faktiska frågans garn program identifierare.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Steg 4: granska miljö stacken och versionerna

Ambari-GRÄNSSNITTets **stack-och version** -sida innehåller information om kluster tjänstens konfiguration och tjänst versions historik.  Felaktiga Hadoop service Library-versioner kan vara orsaken till kluster fel.  I Ambari-ANVÄNDARGRÄNSSNITTET väljer du **Administratörs** menyn och sedan  **stackar och versioner**.  Välj fliken **versioner** på sidan för att se information om tjänst version:

![Apache Ambari stack och versioner](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Steg 5: granska loggfilerna

Det finns många typer av loggar som genereras från de många tjänster och komponenter som utgör ett HDInsight-kluster. [WebHCat loggfiler](#check-your-webhcat-service) beskrivs tidigare. Det finns flera andra användbara loggfiler som du kan undersöka för att begränsa problem med klustret, enligt beskrivningen i följande avsnitt.

* HDInsight-kluster består av flera noder, varav de flesta är aktiviteter för att köra skickade jobb. Jobb körs samtidigt, men loggfiler kan bara visa resultat linjärt. HDInsight kör nya uppgifter och avslutar andra som inte kan slutföras först. All den här aktiviteten loggas till- `stderr` och- `syslog` filerna.

* I logg filen för skript åtgärd visas fel eller oväntade konfigurations ändringar under skapande processen för klustret.

* Hadoop-stegen loggar identifierar Hadoop-jobb som lanserats som en del av ett steg som innehåller fel.

### <a name="check-the-script-action-logs"></a>Kontrol lera skript åtgärds loggarna

HDInsight- [skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md) kör skript i klustret manuellt eller när det angetts. Skript åtgärder kan till exempel användas för att installera ytterligare program vara i klustret eller ändra konfigurations inställningarna från standardvärdena. Att kontrol lera skript åtgärds loggarna kan ge insikter om fel som uppstått under kluster konfigurationen och konfigurationen.  Du kan visa statusen för en skript åtgärd genom att välja **Ops** -knappen i AMBARI-användargränssnittet eller genom att komma åt loggarna från standard lagrings kontot.

Skript åtgärden loggar finns i `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` katalogen.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Visa HDInsight-loggar med Ambari snabb länkar

HDInsight Ambari-ANVÄNDARGRÄNSSNITTET innehåller ett antal **snabb länkar** avsnitt.  Om du vill komma åt logg Länkar för en viss tjänst i HDInsight-klustret öppnar du Ambari-ANVÄNDARGRÄNSSNITTET för klustret och väljer sedan tjänst länken i listan till vänster. Välj List rutan **snabb länkar** , sedan HDInsight-noden av intresse och välj sedan länken för den associerade loggen.

Till exempel för HDFS-loggar:

![Ambari snabb länkar till loggfiler](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Visa Hadoop-genererade loggfiler

An-HDInsight Cluster genererar loggar som skrivs till Azure-tabeller och Azure Blob Storage. GARN skapar egna körnings loggar. Mer information finns i [hantera loggar för ett HDInsight-kluster](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Granska heap-dumpar

Heap-dumpar innehåller en ögonblicks bild av programmets minne, inklusive Variablernas värden vid tidpunkten, som är användbara för att diagnostisera problem som inträffar vid körning. Mer information finns i [Aktivera heap Dumps för Apache Hadoop Services på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Steg 6: kontrol lera konfigurations inställningarna

HDInsight-kluster är förkonfigurerade med standardinställningar för relaterade tjänster, till exempel Hadoop, Hive, HBase och så vidare. Beroende på typ av kluster, dess maskin varu konfiguration, antalet noder, vilka typer av jobb du kör och vilka data du arbetar med (och hur dessa data bearbetas) kan du behöva optimera konfigurationen.

Detaljerade anvisningar om hur du optimerar prestanda konfigurationen för de flesta scenarier finns i [optimera klusterkonfigurationer med Apache Ambari](hdinsight-changing-configs-via-ambari.md). När du använder Spark, se [optimera Apache Spark jobb för prestanda](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Steg 7: återskapa felet på ett annat kluster

För att diagnostisera källan till ett kluster fel, starta ett nytt kluster med samma konfiguration och skicka sedan om de misslyckade jobb stegen ett i taget. Kontrol lera resultaten för varje steg innan du bearbetar nästa. Den här metoden ger dig möjlighet att korrigera och köra ett enda misslyckat steg. Den här metoden har också fördelen att bara läsa in dina indata en gång.

1. Skapa ett nytt test kluster med samma konfiguration som det felande klustret.
2. Skicka det första jobb steget till test klustret.
3. När steget har slutfört bearbetningen söker du efter fel i stegen i-logg filen. Anslut till test klustrets huvud nod och Visa loggfilerna där. Steg loggfilerna visas bara när steget körs under en viss tid, har slutförts eller misslyckats.
4. Kör nästa steg om det första steget lyckades. Om det finns fel kan du undersöka felet i loggfilerna. Om det var ett fel i din kod, gör du korrigeringen och kör sedan steget igen.
5. Fortsätt tills alla steg körs utan fel.
6. När du är färdig med fel sökningen av test klustret tar du bort det.

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
* [Analysera HDInsight-loggar](./hdinsight-troubleshoot-guide.md)
* [Åtkomst Apache Hadoop garn program logga in Linux-baserade HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Aktivera heap-dum par för Apache Hadoop tjänster på Linux-baserade HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Kända problem för Apache Spark kluster i HDInsight](./spark/apache-spark-known-issues.md)