---
title: C#, Apache Hive & Apache gris på Apache Hadoop-Azure HDInsight
description: Lär dig hur du använder C#-användardefinierade funktioner (UDF) med Apache Hive och Apache gris streaming i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 97b23a2b9e3b95a5ea0efcd27d0ec185b1c456f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946549"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Använd C#-användardefinierade funktioner med Apache Hive och Apache gris på Apache Hadoop i HDInsight

Lär dig hur du använder C#-användardefinierade funktioner (UDF) med [Apache Hive](https://hive.apache.org) och [Apache gris](https://pig.apache.org) på HDInsight.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar med Linux-baserade HDInsight-kluster. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [versions hantering av HDInsight-komponenter](../hdinsight-component-versioning.md).

Både Hive och gris kan skicka data till externa program för bearbetning. Den här processen kallas för _strömning_. När du använder ett .NET-program skickas data till programmet på STDIN och programmet returnerar resultatet i STDOUT. Om du vill läsa och skriva från STDIN och STDOUT kan du använda `Console.ReadLine()` och `Console.WriteLine()` från ett konsol program.

## <a name="prerequisites"></a>Förutsättningar

* En välbekanthet med att skriva och skapa C#-kod som är riktad mot .NET Framework 4,5.

    Använd vilken IDE du vill. Vi rekommenderar [Visual Studio](https://www.visualstudio.com/vs) eller [Visual Studio Code](https://code.visualstudio.com/). Stegen i det här dokumentet använder Visual Studio 2019.

* Ett sätt att ladda upp exe-filer till klustret och köra gris-och Hive-jobb. Vi rekommenderar [data Lake verktyg för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure POWERSHELL](/powershell/azure)och [Azure CLI](/cli/azure/install-azure-cli). Stegen i det här dokumentet använder Data Lake verktyg för Visual Studio för att ladda upp filerna och köra exempel filen Hive-fråga.

    Information om andra sätt att köra Hive-frågor finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).

* Ett Hadoop på HDInsight-kluster. Mer information om hur du skapar ett kluster finns i [skapa HDInsight-kluster](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET på HDInsight

*Linux-baserade HDInsight-* kluster använder [mono ( https://mono-project.com)](https://mono-project.com) för att köra .NET-program. Mono version 4.2.1 ingår i HDInsight version 3,6.

Mer information om mono-kompatibilitet med .NET Framework-versioner finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/).

Mer information om den version av .NET Framework och mono som ingår i HDInsight-versioner finns i versions [versioner för HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Skapa C- \# projekt

I följande avsnitt beskrivs hur du skapar ett C#-projekt i Visual Studio för en Apache Hive UDF och en Apache-gris UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Så här skapar du ett C#-projekt för en Apache Hive UDF:

1. Starta Visual Studio.

2. Välj **skapa ett nytt projekt**.

3. I fönstret **skapa ett nytt projekt** väljer du mallen **konsol program (.NET Framework)** (C#-versionen). Välj sedan **Nästa**.

4. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn** för *HiveCSharp* och navigerar till eller skapar en **plats där** du vill spara det nya projektet i. Välj sedan **Skapa**.

5. I Visual Studio IDE ersätter du innehållet i *program. cs* med följande kod:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Välj **bygge**  >  **build-lösning** i meny raden för att bygga projektet.

7. Stäng lösningen.

### <a name="apache-pig-udf"></a>Apache gris UDF

Så här skapar du ett C#-projekt för en Apache Hive UDF:

1. Öppna Visual Studio.

2. I fönstret **Starta** väljer du **skapa ett nytt projekt**.

3. I fönstret **skapa ett nytt projekt** väljer du mallen **konsol program (.NET Framework)** (C#-versionen). Välj sedan **Nästa**.

4. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn** för *PigUDF* och går till eller skapar en **plats där** du sparar det nya projektet i. Välj sedan **Skapa**.

5. I Visual Studio IDE ersätter du innehållet i *program. cs* med följande kod:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Den här koden analyserar de rader som skickas från gris och formaterar om rader som börjar med `java.lang.Exception` .

6. Välj **bygge**  >  **build-lösning** i meny raden för att bygga projektet.

7. Lämna lösningen öppen.

## <a name="upload-to-storage"></a>Ladda upp till lagring

Sedan laddar du upp Hive-och gris-UDF-programmen till lagringen i ett HDInsight-kluster.

1. Gå till **Visa**  >  **Server Explorer** i Visual Studio.

1. Från **Server Explorer** högerklickar du på **Azure**, väljer **Anslut till Microsoft Azure prenumeration** och slutför inloggnings processen.

1. Expandera det HDInsight-kluster som du vill distribuera programmet till. En post med texten **(standard lagrings kontot)** visas.

    ![Standard lagrings konto, HDInsight-kluster, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Om du kan expandera den här posten använder du ett **Azure Storage konto** som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret expanderar du posten och dubbelklickar sedan på **(standard container)**.

    * Om den här posten inte kan utökas använder du **Azure Data Lake Storage** som standard lagring för klustret. Om du vill visa filerna på standard lagrings utrymmet för klustret dubbelklickar du på posten **(standard lagrings konto)** .

1. Använd någon av följande metoder för att ladda upp exe-filerna:

    * Om du använder ett **Azure Storage konto** väljer du ikonen **Ladda upp BLOB** .

        ![HDInsight upload-ikon för nytt projekt](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Välj **Bläddra** under **fil namn** i dialog rutan **Ladda upp ny fil** . I dialog rutan **Ladda upp BLOB** går du till mappen *bin\debug* för *HiveCSharp* -projektet och väljer sedan filen *HiveCSharp.exe* . Klicka slutligen på **Öppna** och sedan på **OK** för att slutföra överföringen.

    * Om du använder **Azure Data Lake Storage** högerklickar du på ett tomt utrymme i fil listan och väljer sedan **Ladda upp**. Välj slutligen *HiveCSharp.exe* -filen och välj **Öppna**.

    När *HiveCSharp.exe* uppladdning har avslut ATS upprepar du överförings processen för *PigUDF.exes* filen.

## <a name="run-an-apache-hive-query"></a>Köra en Apache Hive fråga

Nu kan du köra en Hive-fråga som använder ditt Hive UDF-program.

1. Gå till **Visa**  >  **Server Explorer** i Visual Studio.

2. Expandera **Azure** och expandera därefter **HDInsight**.

3. Högerklicka på det kluster som du har distribuerat *HiveCSharp* -programmet till och välj sedan **Skriv en Hive-fråga**.

4. Använd följande text för Hive-frågan:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Kommentera den `add file` instruktion som matchar den typ av standard lagring som används för klustret.

    Den här frågan väljer `clientid` `devicemake` fälten, och `devicemodel` från `hivesampletable` , och skickar sedan fälten till *HiveCSharp.exe* -programmet. Frågan förväntar sig att programmet ska returnera tre fält, som lagras som `clientid` , `phoneLabel` och `phoneHash` . Frågan förväntar sig också att hitta *HiveCSharp.exe* i roten för standard lagrings behållaren.

5. Ändra standardinställningen för **interaktiv** till **batch** och välj sedan **Skicka** för att skicka jobbet till HDInsight-klustret. Fönstret **Sammanfattning av Hive-jobb** öppnas.

6. Välj **Uppdatera** för att uppdatera sammanfattningen tills **jobbets status** ändras till **slutförd**. Om du vill visa jobbets utdata väljer du **jobbets utdata**.

## <a name="run-an-apache-pig-job"></a>Köra ett Apache gris-jobb

Du kan också köra ett jobb i gris som använder ditt UDF-program för svinhållning.

1. Använd SSH för att ansluta till ditt HDInsight-kluster. (Du kan till exempel köra kommandot `ssh sshuser@<clustername>-ssh.azurehdinsight.net` .) Mer information finns i [använda SSH-withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommando för att starta kommando raden för gris:

    ```shell
    pig
    ```

    En `grunt>` prompt visas.

3. Ange följande för att köra ett gris-jobb som använder .NET Framework-programmet:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE`Instruktionen skapar ett alias för `streamer` för *PigUDF.exe* programmet och `CACHE` läser in det från standard lagrings utrymmet för klustret. Senare `streamer` används med `STREAM` operatorn för att bearbeta de enskilda raderna i `LOG` och returnera data som en serie kolumner.

    > [!NOTE]
    > Det program namn som används för strömning måste omges av \` tecknet (baktick) vid alias och med tecknet "(enkelt citat tecken) när det används med `SHIP` .

4. När du har angett den sista raden ska jobbet starta. Den returnerar utdata som liknar följande text:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Används `exit` för att avsluta gris.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder ett .NET Framework program från Hive och gris i HDInsight. Om du vill lära dig hur du använder python med Hive och gris, se [Använd python med Apache Hive och Apache gris i HDInsight](python-udf-hdinsight.md).

Om du vill lära dig mer om hur du använder Hive och lär dig mer om hur du använder MapReduce kan du läsa följande artiklar:

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
* [Grunderna i gris](https://pig.apache.org/docs/latest/basic.html)
