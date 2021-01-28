---
title: Använda C# med MapReduce på Hadoop i HDInsight – Azure
description: Lär dig hur du använder C# för att skapa MapReduce-lösningar med Apache Hadoop i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 6948491091f558aa7f317d519b0f92c63cac4788
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946636"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Använda C# med MapReduce streaming på Apache Hadoop i HDInsight

Lär dig hur du använder C# för att skapa en MapReduce-lösning i HDInsight.

Med Apache Hadoop strömning kan du köra MapReduce-jobb med ett skript eller en körbar fil. Här används .NET för att implementera mapper och minskaren för en ord räknings lösning.

## <a name="net-on-hdinsight"></a>.NET på HDInsight

HDInsight-kluster använder [mono ( https://mono-project.com) ](https://mono-project.com) för att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3,6. Mer information om den version av mono som ingår i HDInsight finns i [Apache Hadoop-komponenter som är tillgängliga med olika HDInsight-versioner](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

Mer information om mono-kompatibilitet med .NET Framework-versioner finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Så här fungerar Hadoop streaming

Den grundläggande processen som används för strömning i det här dokumentet är följande:

1. Hadoop skickar data till Mapper (*mapper.exe* i det här exemplet) på STDIN.
2. Mapper bearbetar data och avgränsar TABB-avgränsade nyckel/värde-par till STDOUT.
3. Utdata läses av Hadoop och skickas sedan till minskningen (*reducer.exe* i det här exemplet) på STDIN.
4. Minsknings tjänsten läser in TABB-avgränsade nyckel/värde-par, bearbetar data och genererar sedan resultatet som TABB-avgränsade nyckel/värde-par i STDOUT.
5. Utdata läses av Hadoop och skrivs till utdata-katalogen.

Mer information om strömning finns i [Hadoop-direktuppspelning](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Förutsättningar

* Visual Studio.

* En välbekanthet med att skriva och skapa C#-kod som är riktad mot .NET Framework 4,5.

* Ett sätt att ladda upp exe-filer till klustret. Stegen i det här dokumentet använder Data Lake verktyg för Visual Studio för att överföra filerna till primär lagring för klustret.

* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/).

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* URI-schemat för klustrets primära lagring. Det här schemat är `wasb://` för Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage eller Data Lake Storage Gen2, skulle URI: n vara `wasbs://` respektive `abfss://` .

## <a name="create-the-mapper"></a>Skapa mapper

Skapa ett nytt .NET Framework konsol program med namnet *Mapper* i Visual Studio. Använd följande kod för programmet:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

När du har skapat programmet skapar du det för att skapa */bin/Debug/mapper.exe* -filen i projekt katalogen.

## <a name="create-the-reducer"></a>Skapa reduceraren

I Visual Studio skapar du ett nytt .NET Framework konsol program med namnet *reducere*. Använd följande kod för programmet:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

När du har skapat programmet skapar du det för att skapa */bin/Debug/reducer.exe* -filen i projekt katalogen.

## <a name="upload-to-storage"></a>Ladda upp till lagring

Därefter måste du ladda upp *mapparna* och *minska* programmen till HDInsight-lagringen.

1. I Visual Studio väljer du **Visa**  >  **Server Explorer**.

1. Högerklicka på **Azure**, Välj **Anslut till Microsoft Azure prenumeration...** och slutför inloggnings processen.

1. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten **(standard lagrings kontot)** visas.

   ![Lagrings konto, HDInsight-kluster, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Om posten **(standard lagrings konto)** kan expanderas använder du ett **Azure Storage konto** som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret, expanderar du posten och dubbelklickar sedan på **(standard behållare)**.

   * Om posten **(standard lagrings konto)** inte kan utökas använder du **Azure Data Lake Storage** som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret dubbelklickar du på posten **(standard lagrings konto)** .

1. Använd någon av följande metoder för att ladda upp exe-filerna:

    * Om du använder ett **Azure Storage konto** väljer du ikonen **Ladda upp BLOB** .

        ![HDInsight upload-ikon för Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Välj **Bläddra** under **fil namn** i dialog rutan **Ladda upp ny fil** . I dialog rutan **Ladda upp BLOB** går du till mappen *bin\debug* för *Mapper* -projektet och väljer sedan filen *mapper.exe* . Klicka slutligen på **Öppna** och sedan på **OK** för att slutföra överföringen.

    * För **Azure Data Lake Storage** högerklickar du på ett tomt utrymme i fil listan och väljer sedan **Ladda upp**. Välj slutligen *mapper.exe* -filen och välj sedan **Öppna**.

    När *mapper.exe* uppladdning har avslut ATS upprepar du överförings processen för *reducer.exes* filen.

## <a name="run-a-job-using-an-ssh-session"></a>Köra ett jobb: använda en SSH-session

Följande procedur beskriver hur du kör ett MapReduce-jobb med en SSH-session:

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd något av följande kommandon för att starta MapReduce-jobbet:

   * Om standard lagringen är **Azure Storage**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Om standard lagringen är **data Lake Storage gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Om standard lagringen är **data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   I följande lista beskrivs vad varje parameter och alternativ representerar:

   |Parameter | Beskrivning |
   |---|---|
   |Hadoop-streaming. jar|Anger jar-filen som innehåller de strömmande MapReduce-funktionerna.|
   |– filer|Anger *mapper.exe* och *reducer.exe* filer för det här jobbet. `wasbs:///`-, `adl:///` -Eller- `abfs:///` protokoll deklarationen före varje fil är sökvägen till roten för standard lagrings utrymmet för klustret.|
   |-mapper|Anger den fil som implementerar mapper.|
   |– minskning|Anger den fil som implementerar minskningen.|
   |-indatamängd|Anger indata.|
   |– utdata|Anger utdata-katalogen.|

1. När MapReduce-jobbet har slutförts använder du följande kommando för att visa resultaten:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   Följande text är ett exempel på de data som returneras av det här kommandot:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Köra ett jobb: använda PowerShell

Använd följande PowerShell-skript för att köra ett MapReduce-jobb och hämta resultaten.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

I det här skriptet uppmanas du att ange konto namnet och lösen ordet för klustrets inloggning, tillsammans med HDInsight-klustrets namn. När jobbet har slutförts hämtas utdata till en fil med namnet *output.txt*. Följande text är ett exempel på data i `output.txt` filen:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Nästa steg

* [Använd MapReduce i Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md).
* [Använd en C#-användardefinierad funktion med Apache Hive och Apache gris](apache-hadoop-hive-pig-udf-dotnet-csharp.md).
* [Utveckla Java MapReduce-program](apache-hadoop-develop-deploy-java-mapreduce-linux.md)