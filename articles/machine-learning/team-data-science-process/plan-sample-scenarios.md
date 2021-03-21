---
title: Identifiera scenarier för Azure Machine Learning – team data science process
description: Välj lämpliga scenarier för avancerad förutsägelse analys med processen för team data vetenskap.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319300"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scenarier för avancerade analyser i Azure Machine Learning
I den här artikeln beskrivs olika exempel data källor och mål scenarier som kan hanteras av [TDSP (Team data science process)](overview.md). TDSP innehåller en systematisk metod för team att samar beta med att skapa intelligenta program. De scenarier som visas här visar de alternativ som är tillgängliga i arbets flödet för data bearbetning som är beroende av data egenskaper, käll platser och mål databaser i Azure.

**Besluts trädet** för att välja de exempel scenarier som passar dina data och målet visas i det sista avsnittet.

I följande avsnitt presenteras ett exempel scenario. För varje scenario visas ett möjligt data vetenskaps-eller avancerad analys flöde och stöd för Azure-resurser.

> [!NOTE]
> **För alla följande scenarier måste du:**
> <br/>
> 
> * [Skapa ett lagringskonto](../../storage/common/storage-account-create.md)
>   <br/>
> * [Skapa en Azure Machine Learning-arbetsyta](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scenario \# 1: liten till medels Tor tabell data uppsättning i lokala filer
![Små till medel stora lokala filer][1]

#### <a name="additional-azure-resources-none"></a>Ytterligare Azure-resurser: ingen
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Ladda upp en data uppsättning.
1. Bygg ett Azure Machine Learning experiment flöde med Uppladdad data uppsättning (ar).

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scenario \# 2: liten till medelhög data uppsättning för lokala filer som kräver bearbetning
![Små till medel stora lokala filer med bearbetning][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (IPython Notebook-Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. Förbearbeta och rensa data i IPython-anteckningsboken för att komma åt data från Azure Storage behållare.
1. Transformera data till ett rensat tabell format.
1. Spara transformerade data i Azure-blobar.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data][import-data] .
1. Bygg ett Azure Machine Learning experiment flöde som börjar med inmatad data uppsättning (ar).

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scenario \# 3: stor data uppsättning lokala filer, riktade till Azure-blobbar
![Stora lokala filer][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (IPython Notebook-Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. Förbearbeta och rensa data i IPython-anteckningsboken för att komma åt data från Azure-blobar.
1. Transformera data till ett rensat tabell format vid behov.
1. Utforska data och skapa funktioner efter behov.
1. Extrahera ett litet till medel stort data exempel.
1. Spara exempel data i Azure-blobar.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data från Azure-blobbar med modulen [Importera data][import-data] .
1. Bygg Azure Machine Learning experiment flöde som börjar med inmatad data uppsättning (ar).

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scenario \# 4: små till medel stora data uppsättningar för lokala filer, riktade SQL Server på en virtuell Azure-dator
![Små till medel stora lokala filer till SQL DB i Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (SQL Server/IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server + IPython Notebook.
1. Ladda upp data till en Azure Storage-behållare.
1. I förväg bearbeta och rensa data i Azure Storage container med hjälp av IPython Notebook.
1. Transformera data till ett rensat tabell format vid behov.
1. Spara data till VM-lokala filer (IPython Notebook körs på den virtuella datorn, lokala enheter avser VM-enheter).
1. Läs in data till SQL Server databas som körs på en virtuell Azure-dator.
   
   Alternativ \# 1: använda SQL Server Management Studio.
   
   * Logga in på SQL Server VM
   * Kör SQL Server Management Studio.
   * Skapa databas-och mål tabeller.
   * Använd någon av metoderna för Mass import för att läsa in data från VM-lokala filer.
   
   Alternativ \# 2: använda IPython Notebook – inte lämpligt för medel stora och större data uppsättningar
   
   <!-- -->    
   * Använd ODBC-anslutningssträng för att få åtkomst till SQL Server på den virtuella datorn.
   * Skapa databas-och mål tabeller.
   * Använd någon av metoderna för Mass import för att läsa in data från VM-lokala filer.
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databas tabellerna. Anteckna bara den nödvändiga frågan för att skapa dem.
1. Välj en data urvals storlek, om det behövs och/eller önska.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Bygg Azure Machine Learning experiment flöde som börjar med inmatad data uppsättning (ar).

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scenario \# 5: stor data uppsättning i lokala filer, mål SQL Server i virtuell Azure-dator
![Stora lokala filer till SQL DB i Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (SQL Server/IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server-och IPython Notebook-Server.
1. Ladda upp data till en Azure Storage-behållare.
1. Valfritt Förbearbeta och rensa data.
   
    a.  Förbearbeta och rensa data i IPython-anteckningsboken för att komma åt data från Azure-blobar.
   
    b.  Transformera data till ett rensat tabell format vid behov.
   
    c.  Spara data till VM-lokala filer (IPython Notebook körs på den virtuella datorn, lokala enheter avser VM-enheter).
1. Läs in data till SQL Server databas som körs på en virtuell Azure-dator.
   
    a.  Logga in på SQL Server VM.
   
    b.  Om data inte redan sparats laddar du ned datafiler från Azure Storage-behållaren till en lokal VM-mapp.
   
    c.  Kör SQL Server Management Studio.
   
    d.  Skapa databas-och mål tabeller.
   
    e.  Använd en av de Mass import metoder som används för att läsa in data.
   
    f.  Om tabell koppling krävs skapar du index för att påskynda kopplingarna.
   
   > [!NOTE]
   > För snabbare inläsning av stora data storlekar rekommenderar vi att du skapar partitionerade tabeller och Mass import av data parallellt. Mer information finns i [Parallel Data import to SQL partitioned tables](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databas tabellerna. Anteckna bara den nödvändiga frågan för att skapa dem.
1. Välj en data urvals storlek, om det behövs och/eller önska.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt Azure Machine Learning experiment flöde som börjar med överförd data uppsättning

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scenario \# 6: stor data uppsättning i en SQL Server databas lokalt, mål SQL Server på en virtuell Azure-dator
![Stora SQL DB-lokal till SQL DB i Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (SQL Server/IPython Notebook Server)
1. Skapa en virtuell Azure-dator som kör SQL Server-och IPython Notebook-Server.
1. Använd en av data export metoderna för att exportera data från SQL Server till dumpfiler.
   
   > [!NOTE]
   > Om du vill flytta alla data från den lokala databasen kan du flytta den fullständiga databasen till SQL Server-instansen i Azure med en alternativ metod (snabbare). Hoppa över stegen för att exportera data, skapa databas och läsa in/importera data till mål databasen och följ den alternativa metoden.
   > 
   > 
1. Ladda upp dumpfiler till Azure Storage container.
1. Läs in data till en SQL Server databas som körs på en virtuell Azure-dator.
   
   a.  Logga in på SQL Server VM.
   
   b.  Hämta datafiler från en Azure Storage-behållare till mappen Local-VM.
   
   c.  Kör SQL Server Management Studio.
   
   d.  Skapa databas-och mål tabeller.
   
   e.  Använd en av de Mass import metoder som används för att läsa in data.
   
   f.  Om tabell koppling krävs skapar du index för att påskynda kopplingarna.
   
   > [!NOTE]
   > För snabbare inläsning av stora data storlekar skapar du partitionerade tabeller och Mass importeringen av data parallellt. Mer information finns i [Parallel Data import to SQL partitioned tables](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Utforska data, skapa funktioner efter behov. Funktionerna behöver inte materialiseras i databas tabellerna. Anteckna bara den nödvändiga frågan för att skapa dem.
1. Välj en data urvals storlek, om det behövs och/eller önska.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från SQL Server med modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt Azure Machine Learning experiment flöde som börjar med överförd data uppsättning.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativ metod för att kopiera en fullständig databas från en lokal SQL Server till Azure SQL Database
![Koppla från lokal databas och Anslut till SQL DB i Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ytterligare Azure-resurser: virtuell Azure-dator (SQL Server/IPython Notebook Server)
Om du vill replikera hela SQL Server databasen i SQL Server VM ska du kopiera en databas från en plats/Server till en annan, förutsatt att databasen kan tas offline tillfälligt. Du kan använda SQL Server Management Studio Object Explorer eller med motsvarande Transact-SQL-kommandon.

1. Koppla från databasen på käll platsen. Mer information finns i [Koppla från en databas](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. I Utforskaren eller Windows kommando tolks fönster kopierar du den frånkopplade databas filen eller filerna och logg filen eller loggfilerna till mål platsen på SQL Server VM i Azure.
1. Bifoga de kopierade filerna till mål SQL Servers instansen. Mer information finns i [bifoga en databas](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Flytta en databas med hjälp av koppla från och Anslut (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scenario \# 7: Big data i lokala filer, målets Hive-databas i Azure HDInsight Hadoop kluster
![Big data i den lokala mål registrerings data filen][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ytterligare Azure-resurser: Azure HDInsight Hadoop kluster och virtuell Azure-dator (IPython Notebook-Server)
1. Skapa en virtuell Azure-dator som kör IPython Notebook Server.
1. Skapa ett Azure HDInsight Hadoop-kluster.
1. Valfritt Förbearbeta och rensa data.
   
   a.  Förbearbeta och rensa data i IPython-anteckningsboken för att komma åt data från Azure-blobar.
   
   b.  Transformera data till ett rensat tabell format vid behov.
   
   c.  Spara data till VM-lokala filer (IPython Notebook körs på den virtuella datorn, lokala enheter avser VM-enheter).
1. Ladda upp data till standard behållaren för det Hadoop-kluster som valts i steg 2.
1. Läs in data till Hive-databasen i Azure HDInsight Hadoop kluster.
   
   a.  Logga in på noden Head i Hadoop-klustret
   
   b.  Öppna kommando raden för Hadoop.
   
   c.  Ange rot katalogen för Hive via kommando `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Kör Hive-frågorna för att skapa databas och tabeller och Läs in data från Blob Storage till Hive-tabeller.
   
   > [!NOTE]
   > Om data är stora kan användarna skapa Hive-tabellen med partitioner. Sedan kan användare använda en `for` loop i Hadoop-kommandoraden på Head-noden för att läsa in data i Hive-tabellen partitionerade efter partition.
   > 
   > 
1. Utforska data och skapa funktioner efter behov i Hadoop-kommandoraden. Funktionerna behöver inte materialiseras i databas tabellerna. Anteckna bara den nödvändiga frågan för att skapa dem.
   
   a.  Logga in på noden Head i Hadoop-klustret
   
   b.  Öppna kommando raden för Hadoop.
   
   c.  Ange rot katalogen för Hive via kommando `cd %hive_home%\bin` i Hadoop-kommandoraden.
   
   d.  Kör Hive-frågorna i Hadoop-kommandoraden på noden Head i Hadoop-klustret för att utforska data och skapa funktioner efter behov.
1. Om det behövs och/eller önskas kan du testa data som passar i Azure Machine Learning Studio.
1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Läs data direkt från `Hive Queries` med hjälp av modulen [Importera data][import-data] . Klistra in nödvändig fråga som extraherar fält, skapar funktioner och exempel data om det behövs direkt i frågan [Importera data][import-data] .
1. Enkelt Azure Machine Learning experiment flöde som börjar med överförd data uppsättning.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Besluts träd för val av scenario
---
I följande diagram sammanfattas de scenarier som beskrivs ovan och de avancerade analys process-och teknik val som tar dig till var och en av de specificerade scenarierna. Data bearbetning, utforskning, funktions teknik och sampling kan äga rum i en eller flera metoder/miljöer – i käll-, mellanliggande och/eller mål miljöer – och kan fortsätta iterativt efter behov. Diagrammet fungerar bara som en illustration av några möjliga flöden och ger inte en uttömmande uppräkning.

![Exempel scenarier för DS-process][8]

### <a name="advanced-analytics-in-action-examples"></a>Avancerad analys i åtgärds exempel
För Azure Machine Learning från slut punkt till slut punkt som använder avancerad analys process och teknik som använder offentliga data uppsättningar, se:

* [Team data science process i praktiken: använda SQL Server](sql-walkthrough.md).
* [Team data science process i praktiken: använda HDInsight Hadoop-kluster](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data