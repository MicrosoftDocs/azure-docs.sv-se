---
title: Plattformar och verktyg för data vetenskaps projekt – team data science process
description: Specificerar och diskuterar de data-och analys resurser som är tillgängliga för företag som standardiserar processen för team data vetenskap.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96453851"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Plattformar och verktyg för data vetenskaps projekt

Microsoft tillhandahåller ett fullständigt spektrum av analys resurser för både molnbaserade eller lokala plattformar. De kan distribueras för att göra körningen av dina data vetenskaps projekt effektiv och skalbar. Vägledning för team som implementerar data vetenskaps projekt i en spårnings bara, versions kontroll och samarbets väg tillhandahålls av TDSP ( [team data science process](overview.md) ).  En översikt över personal rollerna och deras associerade uppgifter som hanteras av ett data vetenskaps team som är standardiserade för den här processen finns i [process roller och uppgifter för team data vetenskap](roles-tasks.md).

De analys resurser som är tillgängliga för data vetenskaps team som använder TDSP omfattar:

- Data vetenskaps Virtual Machines (både Windows-och Linux-CentOS)
- HDInsight Spark-kluster
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight Hive-kluster
- Azure File Storage
- SQL Server 2019 R-och python-tjänster
- Azure Databricks

I det här dokumentet beskriver vi kortfattat resurserna och tillhandahåller länkar till självstudierna och genom gångar som TDSP-teamen har publicerat. De kan hjälpa dig att lära dig hur du använder dem steg för steg och börja använda dem för att skapa intelligenta program. Mer information om dessa resurser finns på deras produkt sidor. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

Den virtuella datorn för data vetenskap som erbjuds både Windows och Linux av Microsoft innehåller populära verktyg för data vetenskaps-och utvecklings aktiviteter. Den innehåller verktyg som:

- Microsoft R Server Developer Edition 
- Anaconda Python-distribution
- Jupyter-anteckningsböcker för python och R 
- Visual Studio Community Edition med python och R-verktyg för Windows/Sol förmörkelse på Linux
- Power BI Desktop för Windows
- SQL Server 2016 Developer Edition på Windows/postgres på Linux

Den innehåller också **ml-och AI-verktyg** som xgboost, Mxnet och Vowpal Wabbit.

För närvarande finns DSVM i operativ systemen **Windows** och **Linux CentOS** . Välj storlek på din DSVM (antal processor kärnor och mängden minne) baserat på behoven hos de data vetenskaps projekt som du planerar att köra på den. 

Mer information om Windows-versionen av DSVM finns i [Microsoft data science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) på Azure Marketplace. Linux-versionen av DSVM finns i [linux data science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Information om hur du kör några vanliga data vetenskaps uppgifter på DSVM effektivt finns i [10 saker du kan göra på den virtuella datorn för data vetenskap](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark kluster

Apache Spark är ett ramverk för parallellbearbetning med öppen källkod som stöder intern bearbetning för att höja prestandan hos program för stordataanalys. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory Compute-funktioner gör det ett bra val för iterativa algoritmer i maskin inlärning och för diagram beräkningar. Spark är också kompatibelt med Azure Blob Storage (WASB) så att dina befintliga data som lagras i Azure enkelt kan bearbetas med Spark.

När du skapar ett Spark-kluster i HDInsight skapas Azure-beräkningsresurser med Spark installerat och konfigurerat. Det tar cirka 10 minuter att skapa ett Spark-kluster i HDInsight. Lagra data som ska bearbetas i Azure Blob Storage. Information om hur du använder Azure Blob Storage med ett kluster finns i [använda HDFS-kompatibel Azure Blob Storage med Hadoop i HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP-teamet från Microsoft har publicerat två fullständiga genom gångar om hur du använder Azure HDInsight Spark-kluster för att bygga data vetenskaps lösningar, en med python och den andra Scala. Mer information om Azure HDInsight **Spark-kluster** finns i [Översikt: Apache Spark på HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Information om hur du skapar en data vetenskaps lösning med **python** i ett Azure HDInsight Spark kluster finns i [Översikt över data vetenskap med Spark på Azure HDInsight](spark-overview.md). Information om hur du skapar en data vetenskaps lösning med **Scala** på ett Azure HDInsight Spark-kluster finns i [data vetenskap med Scala och Spark på Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Med Azure Synapse Analytics kan du skala beräknings resurser enkelt och i sekunder, utan att behöva betala eller betala. Det ger också det unika alternativet att pausa användningen av beräknings resurser, vilket ger dig friheten att hantera dina moln kostnader bättre. Möjligheten att distribuera skalbara beräknings resurser gör det möjligt att hämta alla dina data till Azure Synapse Analytics. Lagrings kostnaderna är minimala och du kan bara köra beräkningarna på de delar av data uppsättningar som du vill analysera. 

Mer information om Azure Synapse Analytics finns på webbplatsen för [Azure Synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse) . Information om hur du skapar avancerade analys lösningar från slut punkt till slut punkt med Azure Synapse Analytics finns [i processen team data science i praktiken: använda Azure Synapse Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake är en företagsomfattande lagrings plats för alla typer av data som samlas in på en och samma plats, före eventuella formella krav eller schema som införs. Den här flexibiliteten gör det möjligt för alla typer av data att behållas i data Lake, oavsett storlek eller struktur eller hur snabbt den matas in. Organisationer kan sedan använda Hadoop eller avancerad analys för att hitta mönster i dessa data sjöar. Data sjöar kan också fungera som en lagrings plats för förberedelse av data innan du kan granska data och flytta dem till ett informations lager.

Mer information om Azure Data Lake finns i [Introduktion av Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Information om hur du skapar en skalbar data vetenskaps lösning från slut punkt till slut punkt med Azure Data Lake finns i [scalable data science i Azure Data Lake: en slut punkt till slut punkts genom gång](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive-kluster (Hadoop)

Apache Hive är ett informations lager system för Hadoop, som möjliggör data sammanfattning, frågor och analys av data med hjälp av HiveQL, ett frågespråk som liknar SQL. Hive kan användas för att utforska dina data interaktivt eller för att skapa återanvändbara batchbearbetnings jobb.

Med Hive kan du projicera strukturen på mycket ostrukturerade data. När du har definierat strukturen kan du använda Hive för att fråga efter data i ett Hadoop-kluster utan att behöva använda, eller till och med känna till Java eller MapReduce. HiveQL (Hive-frågespråket) gör att du kan skriva frågor med instruktioner som liknar T-SQL.

För data forskare kan Hive köra python User-Defined functions (UDF: er) i Hive-frågor för att bearbeta poster. Den här funktionen utökar funktionaliteten för Hive-frågor i data analys avsevärt. Mer specifikt gör det möjligt för data experter att utföra skalbara funktioner i språk som de är mest bekanta med: SQL-liknande HiveQL och python. 

Mer information om Azure HDInsight Hive-kluster finns i [använda Hive och HiveQL med Hadoop i HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Information om hur du skapar en skalbar lösning för data vetenskap från slut punkt till slut punkt med Azure HDInsight Hive-kluster finns [i processen team data science i praktiken: använda HDInsight Hadoop-kluster](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure File Storage 

Azure File Storage är en tjänst som erbjuder fil resurser i molnet med hjälp av SMB-protokollet (Server Message Block). Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Program som körs på virtuella Azure-datorer eller molntjänster eller från lokala klienter kan montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Särskilt användbart för data vetenskaps projekt är möjligheten att skapa en Azure-fillagring som plats för att dela projekt data med dina projekt grupp medlemmar. Var och en av dem har sedan åtkomst till samma kopia av data i Azure File Storage. De kan också använda fil lagringen för att dela funktions uppsättningar som genereras under projekt körningen. Om projektet är ett klient engagemang kan dina klienter skapa en Azure File Storage under sin egen Azure-prenumeration för att dela projekt data och funktioner med dig. På så sätt har klienten fullständig kontroll över projekt data till gångarna. Mer information om Azure File Storage finns i [komma igång med Azure File Storage i Windows](../../storage/files/storage-dotnet-how-to-use-files.md) och [hur du använder Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R-och python-tjänster

R Services (in-Database) tillhandahåller en plattform för utveckling och distribution av intelligenta program som kan få nya insikter. Du kan använda det omfattande och kraftfulla R-språket, inklusive de många paket som tillhandahålls av R-communityn, för att skapa modeller och generera förutsägelser från dina SQL Server data. Eftersom R-tjänster (i databasen) integrerar R-språket med SQL Server, kommer Analytics att stå nära data, vilket eliminerar de kostnader och säkerhets risker som är kopplade till att flytta data.

R Services (in-Database) stöder R-språket med öppen källkod med en omfattande uppsättning SQL Server verktyg och tekniker. De erbjuder överlägsen prestanda, säkerhet, tillförlitlighet och hanterbarhet. Du kan distribuera R-lösningar med hjälp av praktiska och välbekanta verktyg. Dina produktions program kan anropa R-körningsmiljön och hämta förutsägelser och visuella objekt med hjälp av Transact-SQL. Du kan också använda skalnings biblioteken för att förbättra skalningen och prestandan för dina R-lösningar. Mer information finns i [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services).

TDSP-teamet från Microsoft har publicerat två slut för ande genom gångar som visar hur du skapar data vetenskaps lösningar i SQL Server 2016 R-tjänster: en för R-programmerare och en för SQL-utvecklare. För **R-programmerare**, se [genom gång av data vetenskap från slut punkt till slut punkt](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). För **SQL-utvecklare**, se [Avancerad analys i databasen för SQL-utvecklare (självstudie)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Bilaga: verktyg för att konfigurera data vetenskaps projekt

### <a name="install-git-credential-manager-on-windows"></a>Installera git Credential Manager i Windows

Om du följer TDSP i **Windows** måste du installera **git CREDENTIAL Manager (GCM)** för att kommunicera med git-databaserna. Om du vill installera GCM måste du först installera **Chocolaty**. Om du vill installera Chocolaty och GCM kör du följande kommandon i Windows PowerShell som **administratör**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Installera git på Linux-datorer (CentOS)

Kör följande bash-kommando för att installera git på Linux-datorer (CentOS):

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generera CentOS-datorer (Public SSH Key på Linux)

Om du använder Linux-datorer (CentOS) för att köra git-kommandon måste du lägga till den offentliga SSH-nyckeln för datorn i Azure DevOps-tjänsterna, så att den här datorn kan identifieras av Azure DevOps-tjänsterna. Först måste du generera en offentlig SSH-nyckel och lägga till nyckeln i offentliga SSH-nycklar på sidan säkerhets inställning för Azure DevOps Services. 

1. Skapa SSH-nyckeln genom att köra följande två kommandon: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Kommandon för att generera SSH-nyckeln](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Kopiera hela SSH-nyckeln inklusive *SSH-RSA*. 
1. Logga in på Azure DevOps-tjänsterna. 
1. Klicka på **<ditt \> namn** i det övre högra hörnet på sidan och klicka på **säkerhet**. 
    
   ![Klicka på ditt namn och sedan på säkerhet](./media/platforms-and-tools/resources-2-user-setting.png)

1. Klicka på **offentliga SSH-nycklar** och klicka på **+ Lägg till**. 

   ![Klicka på offentliga SSH-nycklar och klicka sedan på + Lägg till](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Klistra in SSH-nyckeln som kopierats i text rutan och spara.


## <a name="next-steps"></a>Nästa steg

Fullständiga genom gångar från slut punkt till slut punkt som visar alla steg i processen för **särskilda scenarier** tillhandahålls också. De visas och länkas med miniatyr beskrivningar i [exempel avsnittet genom gångar](walkthroughs.md) . De illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel som visar hur du utför stegen i team data science-processen genom att använda Azure Machine Learning Studio (klassisk) finns i [Azure ml](./index.yml) Learning-sökvägen.