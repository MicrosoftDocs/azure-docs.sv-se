---
title: Köra anpassade MapReduce-program – Azure HDInsight
description: När och hur du kör anpassade Apache MapReduce-program i Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0f7a3676db14dbf61d3976690c8f9c71a660f889
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943237"
---
# <a name="run-custom-mapreduce-programs"></a>Köra anpassade MapReduce-program

Apache Hadoop-baserade Big data system som HDInsight möjliggör data bearbetning med en mängd olika verktyg och tekniker. I följande tabell beskrivs de största fördelarna och övervägandena för var och en.

| Frågans mekanism | Fördelar | Överväganden |
| --- | --- | --- |
| **Apache Hive med HiveQL** | <ul><li>En utmärkt lösning för batchbearbetning och analys av stora mängder oföränderliga data, för data Sammanfattning och för frågor på begäran. Den använder en välbekant SQL-liknande syntax.</li><li>Den kan användas för att skapa permanenta data tabeller som enkelt kan partitioneras och indexeras.</li><li>Flera externa tabeller och vyer kan skapas över samma data.</li><li>Den har stöd för en enkel data lager implementering som ger enorma skalbara och fel tolerans funktioner för data lagring och bearbetning.</li></ul> | <ul><li>Det kräver att källdata har minst viss identifierbar struktur.</li><li>Det är inte lämpligt för real tids frågor och uppdateringar på radnivå. Det är bäst att använda batch-jobb över stora mängder data.</li><li>Det kanske inte går att utföra vissa typer av komplexa bearbetnings uppgifter.</li></ul> |
| **Apache gris som använder gris Latin** | <ul><li>En utmärkt lösning för att manipulera data som uppsättningar, sammanslagning och filtrering av data uppsättningar, tillämpa funktioner på poster eller grupper med poster och för att omstrukturera data genom att definiera kolumner, gruppera värden eller konvertera kolumner till rader.</li><li>Den kan använda en arbets flödes-baserad metod som en sekvens med åtgärder för data.</li></ul> | <ul><li>SQL-användare kan hitta gris Latin är mindre välbekant och svårare att använda än HiveQL.</li><li>Standardutdata är vanligt vis en textfil och kan vara svårare att använda med visualiserings verktyg som Excel. Normalt skiktar du en Hive-tabell över utdata.</li></ul> |
| **Anpassad karta/minska** | <ul><li>Den ger fullständig kontroll över kartan och minskar faser och körning.</li><li>Den tillåter att frågor optimeras för att uppnå högsta prestanda från klustret, eller för att minimera belastningen på servrarna och nätverket.</li><li>Komponenterna kan skrivas i ett intervall med välkända språk.</li></ul> | <ul><li>Det är svårare än att använda gris eller Hive eftersom du måste skapa en egen karta och minska komponenterna.</li><li>Processer som kräver koppling av data uppsättningar är svårare att implementera.</li><li>Även om det finns tillgängliga test ramverk är fel söknings kod mer komplex än ett normalt program eftersom koden körs som ett batch-jobb för kontrollen av Hadoop-jobbschemaläggaren.</li></ul> |
| **Apache HCatalog** | <ul><li>Den sammanfattar Sök vägs informationen för lagringen, underlättar administrationen och tar bort behovet av användare för att veta var data lagras.</li><li>Den aktiverar meddelanden om händelser som data tillgänglighet, vilket gör att andra verktyg som Oozie kan upptäcka när åtgärder har inträffat.</li><li>Den visar en relationell vy över data, inklusive partitionering efter nyckel och gör det lätt att komma åt data.</li></ul> | <ul><li>Det stöder RCFile-, CSV-text, JSON-text, SequenceFile och ORC fil format som standard, men du kan behöva skriva en anpassad SerDe för andra format.</li><li>HCatalog är inte tråd säker.</li><li>Det finns vissa begränsningar av data typerna för kolumner när du använder HCatalog-inläsaren i gris-skript. Mer information finns i [HCatLoader data types](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) in the Apache HCatalog-dokumentationen.</li></ul> |

Normalt använder du den enklaste av dessa metoder som kan ge de resultat du behöver. Till exempel kanske du kan få sådana resultat genom att använda bara Hive, men för mer komplexa scenarier kan du behöva använda gris eller till och med skriva din egen karta och minska komponenter. Du kan också bestämma att när du experimenterar med Hive eller gris, kan den anpassade kartan och minska komponenter ge bättre prestanda genom att göra det möjligt att finjustera och optimera bearbetningen.

## <a name="custom-mapreduce-components"></a>Anpassad karta/minska komponenter

Kart/minska kod består av två separata funktioner som implementeras som en **karta** och **minskar** komponenter. **Kart** komponenten körs parallellt på flera klusternoder, varje nod som använder mappningen till nodens egna delmängd av data. Den **reducerande** komponenten sorterar och sammanfattar resultaten från alla kart funktioner. Mer information om dessa två komponenter finns i [använda MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

I de flesta HDInsight-bearbetnings scenarier är det enklare och mer effektivt att använda en abstraktion på högre nivå, till exempel gris eller Hive. Du kan också skapa en anpassad mappning och minska komponenter för användning i Hive-skript för att utföra mer avancerad bearbetning.

Anpassad karta/reducera komponenter skrivs vanligt vis i Java. Hadoop tillhandahåller ett strömmande gränssnitt som även tillåter att komponenter används som har utvecklats på andra språk, till exempel C#, F #, Visual Basic, python och Java Script.

* En genom gång av hur du utvecklar anpassade Java MapReduce-program finns i [utveckla Java MapReduce-program för Hadoop på HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Överväg att skapa en egen karta och minska komponenterna för följande villkor:

* Du måste bearbeta data som är helt ostrukturerade genom att parsa data och använda anpassad logik för att få strukturerad information från den.
* Du vill utföra komplexa uppgifter som är svåra (eller omöjliga) att uttrycka i gris eller Hive utan att behöva skapa en UDF. Du kan till exempel behöva använda en extern kod tjänst för att konvertera latitud-och longitud-koordinater eller IP-adresser i käll data till geografiska plats namn.
* Du vill återanvända din befintliga .NET-, python-eller JavaScript-kod i mappa/minska komponenter med hjälp av Hadoop streaming Interface.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Ladda upp och köra ditt anpassade MapReduce-program

De vanligaste MapReduce-programmen är skrivna i Java och kompileras till en jar-fil.

1. När du har utvecklat, kompilerat och testat ditt MapReduce-program använder du `scp` kommandot för att ladda upp jar-filen till huvudnoden.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Ersätt kluster namn med kluster namnet. Om du har använt ett lösen ord för att skydda SSH-kontot uppmanas du att ange lösen ordet. Om du använde ett certifikat kan du behöva använda- `-i` parametern för att ange den privata nyckel filen.

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör ditt MapReduce-program via garn från SSH-sessionen.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Det här kommandot skickar MapReduce-jobbet till garn. Indatafilen är `/example/data/sample.log` och utdata-katalogen är `/example/data/logoutput` . Indatafilen och eventuella utdatafiler lagras i standard lagrings utrymmet för klustret.

## <a name="next-steps"></a>Nästa steg

* [Använda C# med MapReduce streaming på Apache Hadoop i HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Utveckla Java MapReduce-program för Apache Hadoop i HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Använd Azure Toolkit for Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster](../spark/apache-spark-eclipse-tool-plugin.md)
* [Använda python-användardefinierade funktioner (UDF) med Apache Hive och Apache-gris i HDInsight](python-udf-hdinsight.md)
