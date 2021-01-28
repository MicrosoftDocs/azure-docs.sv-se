---
title: Utveckla skript åtgärder för att anpassa Azure HDInsight-kluster
description: Lär dig hur du använder Bash-skript för att anpassa HDInsight-kluster. Med skript åtgärder kan du köra skript under eller efter att klustret har skapats för att ändra kluster konfigurations inställningar eller installera ytterligare program vara.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945474"
---
# <a name="script-action-development-with-hdinsight"></a>Utveckling av skript åtgärder med HDInsight

Lär dig hur du anpassar ditt HDInsight-kluster med bash-skript. Skript åtgärder är ett sätt att anpassa HDInsight under eller efter att klustret har skapats.

## <a name="what-are-script-actions"></a>Vad är skript åtgärder

Skript åtgärder är Bash-skript som Azure kör på klusternoderna för att göra konfigurations ändringar eller installera program vara. En skript åtgärd körs som rot och ger fullständig åtkomst behörighet till klusternoderna.

Skript åtgärder kan tillämpas på följande sätt:

| Använd den här metoden om du vill använda ett skript... | När klustret skapas... | På ett kluster som körs... |
| --- |:---:|:---:|
| Azure-portalen |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klassisk Azure CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-mall |✓ |&nbsp; |

Mer information om hur du använder dessa metoder för att använda skript åtgärder finns i [Anpassa HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Metod tips för skript utveckling

När du utvecklar ett anpassat skript för ett HDInsight-kluster finns det flera metod tips att tänka på:

* [Rikta den Apache Hadoop versionen](#bPS1)
* [Rikta in OS-versionen](#bps10)
* [Tillhandahålla stabila länkar till skript resurser](#bPS2)
* [Använd förkompilerade resurser](#bPS4)
* [Kontrol lera att skriptet för kluster anpassning är idempotenta](#bPS3)
* [Säkerställ hög tillgänglighet för kluster arkitekturen](#bPS5)
* [Konfigurera anpassade komponenter för användning av Azure Blob Storage](#bPS6)
* [Skriv information till STDOUT och STDERR](#bPS7)
* [Spara filer som ASCII med LF-linje slut](#bps8)
* [Använd logik för återförsök för att återställa efter tillfälliga fel](#bps9)

> [!IMPORTANT]  
> Skript åtgärder måste slutföras inom 60 minuter, annars Miss lyckas processen. Vid nodens etablering körs skriptet samtidigt med andra installations-och konfigurations processer. Konkurrens för resurser som CPU-tid eller nätverks bandbredd kan orsaka att skriptet tar längre tid att slutföra än i utvecklings miljön.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Rikta den Apache Hadoop versionen

Olika versioner av HDInsight har olika versioner av Hadoop-tjänster och-komponenter installerade. Om skriptet förväntar sig en speciell version av en tjänst eller komponent bör du bara använda skriptet med den version av HDInsight som innehåller de nödvändiga komponenterna. Du hittar information om komponent versioner som ingår i HDInsight med hjälp av [versions](hdinsight-component-versioning.md) dokumentet för HDInsight-komponenten.

### <a name="checking-the-operating-system-version"></a>Kontrollerar operativ systemets version

Olika versioner av HDInsight förlitar sig på vissa versioner av Ubuntu. Det kan finnas skillnader mellan OS-versioner som du måste söka efter i skriptet. Du kan till exempel behöva installera en binärfil som är kopplad till versionen av Ubuntu.

Använd om du vill kontrol lera operativ system versionen `lsb_release` . Följande skript visar till exempel hur du refererar till en speciell tar-fil beroende på operativ system version:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> Rikta in dig på operativ systemets version

HDInsight baseras på Ubuntu Linux distribution. Olika versioner av HDInsight förlitar sig på olika versioner av Ubuntu, vilket kan ändra hur skriptet beter sig. HDInsight 3,4 och tidigare baseras till exempel på Ubuntu-versioner som använder start. Version 3,5 och senare baseras på Ubuntu 16,04, som använder system. System-och uppstart förlitar sig på olika kommandon, så skriptet bör skrivas för att fungera med båda.

En annan viktig skillnad mellan HDInsight 3,4 och 3,5 är att `JAVA_HOME` nu pekar på Java 8. Följande kod visar hur du avgör om skriptet körs på Ubuntu 14 eller 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Du kan hitta det fullständiga skriptet som innehåller de här kodfragmenten på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

Den version av Ubuntu som används av HDInsight finns i [versions](hdinsight-component-versioning.md) dokumentet för HDInsight-komponenten.

Information om skillnaderna mellan system-och uppstarter finns i [system för att starta användare](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Tillhandahålla stabila länkar till skript resurser

Skriptet och de associerade resurserna måste vara tillgängliga under hela klustrets livstid. De här resurserna krävs om nya noder läggs till i klustret under skalnings åtgärder.

Det bästa sättet är att ladda ned och arkivera allt i ett Azure Storage-konto i din prenumeration.

> [!IMPORTANT]  
> Det lagrings konto som används måste vara ett standard lagrings konto för klustret eller en offentlig, skrivskyddad behållare på ett annat lagrings konto.

Exempel: de exempel som tillhandahålls av Microsoft lagras i [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) lagrings kontot. Den här platsen är en offentlig, skrivskyddad behållare som hanteras av HDInsight-teamet.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Använd förkompilerade resurser

För att minska den tid det tar att köra skriptet, Undvik åtgärder som kompilerar resurser från käll koden. Till exempel förkompilera resurser och lagra dem i en Azure Storage-konto-BLOB i samma data Center som HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Kontrol lera att skriptet för kluster anpassning är idempotenta

Skripten måste vara idempotenta. Om skriptet körs flera gånger bör det returnera klustret till samma tillstånd varje gång.

Ett skript som till exempel ändrar konfigurationsfiler får inte lägga till dubblettposter om det körts flera gånger.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Säkerställ hög tillgänglighet för kluster arkitekturen

Linux-baserade HDInsight-kluster tillhandahåller två huvudnoder som är aktiva i klustret och skript åtgärder körs på båda noderna. Om de komponenter som du installerar förväntar sig endast en head-nod ska du inte installera komponenterna på båda huvudnoderna.

> [!IMPORTANT]  
> Tjänster som ingår i HDInsight är utformade för att växla mellan de två huvudnoderna vid behov. Den här funktionen är inte utökad för anpassade komponenter som installeras via skript åtgärder. Om du behöver hög tillgänglighet för anpassade komponenter måste du implementera din egen redundans.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurera anpassade komponenter för användning av Azure Blob Storage

Komponenter som du installerar i klustret kan ha en standard konfiguration som använder Apache Hadoop Distributed File System-lagring (HDFS). HDInsight använder antingen Azure Storage eller Data Lake Storage som standard lagring. Båda anger ett HDFS-kompatibelt fil system som sparar data även om klustret tas bort. Du kan behöva konfigurera komponenter som du installerar för att använda WASB eller ADL i stället för HDFS.

För de flesta åtgärder behöver du inte ange fil systemet. Följande kopierar till exempel filen Hadoop-common. jar från det lokala fil systemet till kluster lagringen:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

I det här exemplet `hdfs` använder kommandot Parent standard kluster lagringen. För vissa åtgärder kan du behöva ange URI: n. Till exempel för `adl:///example/jars` Azure Data Lake Storage gen1 för `abfs:///example/jars` data Lake Storage Gen2 eller `wasb:///example/jars` för Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Skriv information till STDOUT och STDERR

HDInsight loggar skript utdata som skrivs till STDOUT och STDERR. Du kan visa den här informationen med Ambari-webbgränssnittet.

> [!NOTE]  
> Apache Ambari är endast tillgängligt om klustret har skapats. Om du använder en skript åtgärd när klustret skapas och det inte går att skapa, se [Felsöka skript åtgärder](./troubleshoot-script-action.md) för andra sätt att komma åt den loggade informationen.

De flesta verktyg och installations paket har redan skrivit information till STDOUT och STDERR, men du kanske vill lägga till ytterligare loggning. Använd om du vill skicka text till STDOUT `echo` . Exempel:

```bash
echo "Getting ready to install Foo"
```

Som standard `echo` skickar strängen till stdout. För att dirigera den till STDERR, Lägg till `>&2` före `echo` . Exempel:

```bash
>&2 echo "An error occurred installing Foo"
```

Detta omdirigerar information som skrivs till STDOUT till STDERR (2) i stället. Mer information om IO-omdirigering finns i [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

Mer information om hur du visar information som loggas av skript åtgärder finns i [Felsöka skript åtgärder](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Spara filer som ASCII med LF-linje slut

Bash-skript ska lagras som ASCII-format, med rader som avslut ATS av LF. Filer som lagras som UTF-8 eller som använder CRLF eftersom rad slutet kan Miss lyckas med följande fel:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Använd logik för återförsök för att återställa efter tillfälliga fel

När du laddar ned filer, installerar paket med apt-get eller andra åtgärder som skickar data via Internet, kan åtgärden Miss lyckas på grund av tillfälliga nätverks fel. Den fjärranslutna resurs som du kommunicerar med kan till exempel vara i processen att redundansväxla till en säkerhets kopierings nod.

Om du vill göra ditt skript flexibelt för tillfälliga fel kan du implementera logik för omprövning. Följande funktion visar hur du implementerar omprövnings logik. Åtgärden försöker igen tre gånger innan det går.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Följande exempel visar hur du använder den här funktionen.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Hjälp metoder för anpassade skript

Skript åtgärdens hjälp metoder är verktyg som du kan använda när du skriver anpassade skript. Dessa metoder finns i [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skriptet. Använd följande för att ladda ned och använda dem som en del av skriptet:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Följande hjälpprogram som är tillgängliga för användning i skriptet:

| Hjälp användning | Description |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Laddar ned en fil från käll-URI: n till angiven fil Sök väg. Som standard skriver den inte över en befintlig fil. |
| `untar_file TARFILE DESTDIR` |Extraherar en tar-fil (med `-xf` ) till mål katalogen. |
| `test_is_headnode` |Om skriptet kördes på en klusternod, returnerar 1; Annars, 0. |
| `test_is_datanode` |Om den aktuella noden är en data-nod (Worker), returnerar du 1; Annars, 0. |
| `test_is_first_datanode` |Om den aktuella noden är den första data (Work)-noden (med namnet workernode0) returnerar 1; Annars, 0. |
| `get_headnodes` |Returnera det fullständigt kvalificerade domän namnet för huvudnoderna i klustret. Namnen är kommaavgränsade. En tom sträng returnerades vid fel. |
| `get_primary_headnode` |Hämtar det fullständigt kvalificerade domän namnet för den primära huvudnoden. En tom sträng returnerades vid fel. |
| `get_secondary_headnode` |Hämtar det fullständigt kvalificerade domän namnet för den sekundära huvudnoden. En tom sträng returnerades vid fel. |
| `get_primary_headnode_number` |Hämtar det numeriska suffixet för den primära huvudnoden. En tom sträng returnerades vid fel. |
| `get_secondary_headnode_number` |Hämtar det numeriska suffixet för den sekundära huvudnoden. En tom sträng returnerades vid fel. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Vanliga användnings mönster

Det här avsnittet innehåller vägledning om hur du implementerar några vanliga användnings mönster som du kan köra i när du skriver ditt eget anpassade skript.

### <a name="passing-parameters-to-a-script"></a>Skicka parametrar till ett skript

I vissa fall kan skriptet kräva parametrar. Du kan till exempel behöva administratörs lösen ordet för klustret när du använder Ambari-REST API.

Parametrar som skickas till skriptet kallas för *positions parametrar* och tilldelas `$1` för den första parametern, `$2` för den andra, och så vidare. `$0` innehåller namnet på själva skriptet.

Värden som skickas till skriptet som parametrar ska omges av enkla citat tecken ('). Detta säkerställer att det skickade värdet behandlas som en literal.

### <a name="setting-environment-variables"></a>Ställa in miljövariabler

Att ställa in en miljö variabel utförs av följande uttryck:

```bash
VARIABLENAME=value
```

I föregående exempel `VARIABLENAME` är namnet på variabeln. Använd för att få åtkomst till variabeln `$VARIABLENAME` . Om du till exempel vill tilldela ett värde som tillhandahålls av en positions parameter som en miljö variabel med namnet PASSWORD, använder du följande instruktion:

```bash
PASSWORD=$1
```

Efterföljande åtkomst till informationen kan sedan användas `$PASSWORD` .

Miljövariabler som anges i skriptet finns bara inom skriptets omfattning. I vissa fall kan du behöva lägga till systemomfattande miljövariabler som behålls när skriptet har avslut ATS. Lägg till variabeln i om du vill lägga till miljövariabler i hela systemet `/etc/environment` . Följande uttryck lägger till exempel till `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där de anpassade skripten lagras

Skript som används för att anpassa ett kluster måste lagras på någon av följande platser:

* Ett __Azure Storage konto__ som är associerat med klustret.

* Ett __ytterligare lagrings konto__ som är kopplat till klustret.

* En __offentligt LÄSBAR URI__. Till exempel en URL till data som lagras i OneDrive, Dropbox eller annan fil värd tjänst.

* Ett __Azure Data Lake Storage konto__ som är associerat med HDInsight-klustret. Mer information om hur du använder Azure Data Lake Storage med HDInsight finns i [snabb start: Konfigurera kluster i HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Det tjänst huvud namn som används för att komma åt Data Lake Storage måste ha Läs behörighet till skriptet.

Resurser som används av skriptet måste också vara offentligt tillgängliga.

Att lagra filerna i ett Azure Storage konto eller Azure Data Lake Storage ger snabb åtkomst, precis som i Azure-nätverket.

> [!NOTE]  
> URI-formatet som används för att referera till skriptet varierar beroende på vilken tjänst som används. För lagrings konton som är associerade med HDInsight-klustret använder du `wasb://` eller `wasbs://` . För offentligt läsbara URI: er, använder `http://` eller `https://` . För Data Lake Storage använder du `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Check lista för att distribuera en skript åtgärd

Här följer stegen när du förbereder distributionen av ett skript:

* Placera filerna som innehåller de anpassade skripten på en plats som är tillgängliga för klusternoderna under distributionen. Till exempel standard lagring för klustret. Filer kan också lagras på offentligt läsbara värd tjänster.
* Kontrol lera att skriptet är idempotenta. Detta gör att skriptet kan köras flera gånger på samma nod.
* Använd en temporär fil katalog katalogen/tmp för att behålla de hämtade filerna som används av skripten och rensa dem sedan när skripten har körts.
* Om inställningarna för operativ system nivå eller Hadoop-tjänstens konfigurationsfiler ändras kanske du vill starta om HDInsight-tjänsterna.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Så här kör du en skript åtgärd

Du kan använda skript åtgärder för att anpassa HDInsight-kluster med hjälp av följande metoder:

* Azure-portalen
* Azure PowerShell
* Azure Resource Manager-mallar
* HDInsight .NET SDK.

Mer information om hur du använder varje metod finns i [så här använder du skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Anpassade skript exempel

Microsoft tillhandahåller exempel skript för att installera komponenter i ett HDInsight-kluster. Se [Installera och använda nyanser i HDInsight-kluster](hdinsight-hadoop-hue-linux.md) som ett exempel på en skript åtgärd.

## <a name="troubleshooting"></a>Felsökning

Följande fel kan uppstå i samband med att du använder skript som du har utvecklat:

**Fel**: `$'\r': command not found` . Ibland följt av `syntax error: unexpected end of file` .

*Orsak*: det här felet orsakas när raderna i ett skript slutar med CRLF. UNIX-system förväntar sig bara LF när linjen slutar.

Det här problemet uppstår oftast när skriptet har skapats i en Windows-miljö, eftersom CRLF är en gemensam rad som slutar för många text redigerare i Windows.

*Lösning*: om det är ett alternativ i text redigeraren väljer du UNIX-format eller LF för rad slutet. Du kan också använda följande kommandon i ett UNIX-system för att ändra CRLF till en LF:

> [!NOTE]  
> Följande kommandon är ungefär likvärdiga i att de ska ändra CRLF-linjen till LF. Välj en baserad på de verktyg som är tillgängliga i systemet.

| Kommando | Kommentarer |
| --- | --- |
| `unix2dos -b INFILE` |Den ursprungliga filen säkerhets kopie ras med en. BAK-tillägg |
| `tr -d '\r' < INFILE > OUTFILE` |Utdatafilen innehåller en version med endast LF-ändelser |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Ändrar filen direkt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |Utdatafilen innehåller en version med endast LF-ändelser. |

**Fel**: `line 1: #!/usr/bin/env: No such file or directory` .

*Orsak*: det här felet uppstår när skriptet sparades som UTF-8 med en byte ordnings markering (BOM).

*Lösning*: Spara filen antingen som ASCII eller som UTF-8 utan en struktur. Du kan också använda följande kommando i ett Linux-eller UNIX-system för att skapa en fil utan struktur listan:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

Ersätt `INFILE` med den fil som innehåller struktur listan. `OUTFILE` ska vara ett nytt fil namn som innehåller skriptet utan struktur.

## <a name="next-steps"></a><a name="seeAlso"></a>Nästa steg

* Lär dig hur du [anpassar HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)
* Använd [HDInsight .NET SDK-referensen](/dotnet/api/overview/azure/hdinsight) för att lära dig mer om att skapa .NET-program som hanterar HDInsight
* Använd [HDInsight-REST API](/rest/api/hdinsight/) för att lära dig hur du använder rest för att utföra hanterings åtgärder på HDInsight-kluster.