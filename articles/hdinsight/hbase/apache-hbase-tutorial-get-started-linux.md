---
title: Självstudie – Använd Apache HBase i Azure HDInsight
description: Följ den här Apache HBase-självstudien för att börja använda Hadoop på HDInsight. Skapa tabeller från HBase-gränssnittet och ställ frågor för dem med Hive.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 50216b83475e92cbabb5ed78c3836264ceff4600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866615"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Självstudie: använda Apache HBase i Azure HDInsight

Den här självstudien visar hur du skapar ett Apache HBase-kluster i Azure HDInsight, skapar HBase-tabeller och frågetabeller med hjälp av Apache Hive.  Allmän HBase-information finns i [HDInsight HBase-översikt](./apache-hbase-overview.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa Apache HBase-kluster
> * Skapa HBase-tabeller och infoga data
> * Använda Apache Hive för att fråga Apache HBase
> * Använd HBase REST API:er med Curl
> * Kontrollera klusterstatus

## <a name="prerequisites"></a>Förutsättningar

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. I exemplen i den här artikeln används bash-gränssnittet i Windows 10 för Vändnings kommandona. Installations [Guide för Windows-undersystem för Linux finns i Windows 10](/windows/wsl/install-win10) för installations steg.  Andra [UNIX-gränssnitt](https://www.gnu.org/software/bash/) fungerar också.  Exempel på vändning, med vissa små ändringar, kan fungera i en kommando tolk i Windows.  Du kan också använda Windows PowerShell-cmdleten [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Skapa Apache HBase-kluster

I följande procedur används en Azure Resource Manager-mall för att skapa ett HBase-kluster. Mallen skapar även det beroende standard Azure Storage-kontot. Mer information om de parametrar som används i proceduren och andra metoder för att skapa kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Välj följande bild för att öppna mallen i Azure Portal. Mallen finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. I dialog rutan **Anpassad distribution** anger du följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj din Azure-prenumeration som används för att skapa klustret.|
    |Resursgrupp|Skapa en Azure-resurs hanterings grupp eller Använd en befintlig.|
    |Location|Ange platsen för resurs gruppen. |
    |ClusterName|Ange ett namn för HBase-klustret.|
    |Inloggningsnamn och lösenord för klustret|Standard inloggnings namnet är **admin**.|
    |SSH-användarnamn och lösenord|Standardanvändarnamnet är **sshuser**.|

    Andra parametrar är valfria.  

    Varje kluster är beroende av ett Azure Storage-konto. När du har tagit bort ett kluster finns data kvar i lagrings kontot. Klustrets lagringskonto av standardtyp har det klusternamn som omfattar tillägget ”store”. Det är hårdkodad i avsnittet mallens variabler.

3. Välj **Jag godkänner villkoren som anges ovan** och välj sedan **Köp**. Det tar cirka 20 minuter att skapa ett kluster.

När ett HBase-kluster har tagits bort kan du skapa ett annat HBase-kluster med hjälp av samma standard-blob-container. Det nya klustret hämtar de HBase-tabeller som du skapade i det ursprungliga klustret. Om du vill undvika inkonsekvenser rekommenderar vi att du inaktiverar HBase-tabellerna innan du tar bort klustret.

## <a name="create-tables-and-insert-data"></a>Skapa tabeller och infoga data

Du kan använda SSH för att ansluta till HBase-kluster och sedan använda [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) för att skapa HBase-tabeller, infoga data och köra frågor mot data.

För de flesta visas data i tabellformat:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png" alt-text="HDInsight Apache HBase tabell data" border="false":::

I HBase (en implementering av [Cloud BigTable](https://cloud.google.com/bigtable/)) ser samma data ut så här:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png" alt-text="HDInsight Apache HBase BigTable-data" border="false":::

**Använda HBase-gränssnittet**

1. Använd `ssh` kommandot för att ansluta till ditt HBase-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Använd `hbase shell` kommandot för att starta det interaktiva HBase-gränssnittet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

1. Använd `create` kommandot för att skapa en HBase-tabell med två kolumn serier. Tabell-och kolumn namn är Skift läges känsliga. Ange följande kommando:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Använd `list` kommandot för att visa en lista över alla tabeller i HBase. Ange följande kommando:

    ```hbase
    list
    ```

1. Använd `put` kommandot för att infoga värden i en angiven kolumn i en angiven rad i en viss tabell. Ange följande kommandon:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Använd `scan` kommandot för att genomsöka och returnera `Contacts` tabell data. Ange följande kommando:

    ```hbase
    scan 'Contacts'
    ```

    :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png" alt-text="HDInsight Apache Hadoop HBase-gränssnitt" border="false":::

1. Använd `get` kommandot för att hämta innehållet i en rad. Ange följande kommando:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Du ser liknande resultat som när du använder `scan` kommandot eftersom det bara finns en rad.

    Mer information om HBase Table-schemat finns i [Introduktion till Apache HBase schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Fler HBase-kommandon finns i [referensguiden för Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Använd `exit` kommandot för att stoppa det interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbaseshell
    exit
    ```

**Masskopiera data till HBase-tabellen för kontakter**

HBase innehåller flera metoder för att läsa in data i tabeller.  Mer information finns i [Massinläsning](https://hbase.apache.org/book.html#arch.bulk.load).

En exempeldatafil finns i en offentlig blob-container, `wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt`.  Innehållet i datafilen är:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Du kan även skapa en textfil och överföra filen till ditt eget lagringskonto. Anvisningar finns i [överföra data för Apache Hadoop jobb i HDInsight](../hdinsight-upload-data.md).

I den här proceduren används `Contacts` HBase-tabellen som du skapade i den senaste proceduren.

1. Kör följande kommando från den öppna ssh-anslutningen för att transformera data filen till StoreFiles och lagra på en relativ sökväg som anges av `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Kör följande kommando för att överföra data från `/example/data/storeDataFileOutput` till tabellen HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Du kan öppna HBase-gränssnittet och använda `scan` kommandot för att Visa tabell innehållet.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Använda Apache Hive för att fråga Apache HBase

Du kan fråga efter data i HBase-tabeller med hjälp av [Apache Hive](https://hive.apache.org/). I det här avsnittet skapar du en Hive-tabell som mappar till HBase-tabellen och använder den för att fråga efter data i din HBase-tabell.

1. Använd följande kommando för att starta Beeline från den öppna ssh-anslutningen:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Mer information om Beeline finns i [Use Hive with Hadoop in HDInsight with Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) (Använda Hive med Hadoop i HDInsight med Beeline).

1. Kör följande [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) -skript för att skapa en Hive-tabell som mappar till HBase-tabellen. Se till att du har skapat exempel tabellen som refereras tidigare i den här artikeln med hjälp av HBase-gränssnittet innan du kör den här instruktionen.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Kör följande HiveQL-skript för att fråga data i HBase-tabellen:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Om du vill avsluta Beeline använder du `!exit` .

1. Använd om du vill avsluta ssh-anslutningen `exit` .

### <a name="separate-hive-and-hbase-clusters"></a>Separata Hive-och HBase-kluster

Hive-frågan för att komma åt HBase-data behöver inte köras från HBase-klustret. Alla kluster som ingår i Hive (inklusive Spark, Hadoop, HBase eller interaktiv fråga) kan användas för att fråga HBase-data, förutsatt att följande steg har slutförts:

1. Båda klustren måste kopplas till samma Virtual Network och undernät
2. Kopiera `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` från HBase-huvudnoderna till Hive-klustret huvudnoderna

### <a name="secure-clusters"></a>Säkra kluster

HBase-data kan också frågas från Hive med hjälp av ESP-aktiverade HBase: 

1. När du följer ett mönster för flera kluster måste båda klustren vara ESP-aktiverade. 
2. Om du vill tillåta Hive att fråga HBase data, se till att `hive` användaren har behörighet att komma åt HBase-data via HBase Apache Ranger-plugin-programmet
3. När du använder separata, ESP-aktiverade kluster måste innehållet i `/etc/hosts` från HBase-huvudnoderna läggas till i `/etc/hosts` Hive-klustrets huvudnoderna. 
> [!NOTE]
> När du har skalat kluster `/etc/hosts` måste du lägga till det igen

## <a name="use-hbase-rest-apis-using-curl"></a>Använd HBase REST API:er med Curl

REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begäranden genom att använda säker HTTP (HTTPS) för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

1. Om du vill aktivera HBase REST API: er i HDInsight-klustret lägger du till följande anpassade Start skript i avsnittet **skript åtgärd** . Du kan lägga till Start skriptet när du skapar klustret eller när klustret har skapats. För **nodtyp** väljer du **region servrar** för att säkerställa att skriptet bara körs i HBase regions servrar.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Ställ in miljö variabel för enkel användning. Redigera kommandona nedan genom `MYPASSWORD` att ersätta med lösen ordet för kluster inloggning. Ersätt `MYCLUSTERNAME` med namnet på ditt HBase-kluster. Ange sedan kommandona.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Använd följande kommando för att lista de befintliga HBase-tabellerna:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Använd följande kommando för att skapa en ny HBase-tabell med två kolumnserier:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schemat tillhandahålls i JSon-format.
1. Använd följande kommando för att infoga vissa data:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64 koda värdena som anges i växeln-d. I exemplet:

   * MTAwMA ==: 1000
   * UGVyc29uYWw6TmFtZQ = =: personligt: namn
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) gör att du kan infoga flera (gruppbaserade) värden.

1. Använd följande kommando för att få en rad:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Mer information om HBase Rest finns i [Referensguiden för Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift stöds inte av HBase i HDInsight.
>
> När du använder Curl eller annan REST-kommunikation med WebHCat, måste du autentisera begärandena genom att ange användarnamn och lösenord för HDInsight-klustrets administratör. Du måste också använda klustrets namn som en del av den URI (Uniform Resource Identifier) som används för att skicka begäranden till servern.
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Du bör få ett svar som liknar följande svar:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Kontrollera klusterstatus

HBase i HDInsight levereras med ett webbgränssnitt för övervakning av kluster. Du kan använda webbgränssnittet för att begära statistik eller information om regioner.

**Så här får du åtkomst till HBase Master UI**

1. Logga in på Ambari-webbgränssnittet på `https://CLUSTERNAME.azurehdinsight.net` där `CLUSTERNAME` är namnet på ditt HBase-kluster.

1. Välj **HBase** på menyn till vänster.

1. Välj **snabb länkar** överst på sidan, peka på länken aktiva Zookeeper och välj sedan **HBase Master användar gränssnitt**.  Gränssnittet har öppnats i en annan webbläsarflik:

   :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png" alt-text="HDInsight Apache HBase HMaster UI" border="false":::

   HBase Master UI innehåller följande avsnitt:

   - regionservrar
   - huvudservrar för säkerhetskopiering
   - tabeller
   - uppgifter
   - attribut för programvara

## <a name="cluster-recreation"></a>Kluster fritid

När ett HBase-kluster har tagits bort kan du skapa ett annat HBase-kluster med hjälp av samma standard-blob-container. Det nya klustret hämtar de HBase-tabeller som du skapade i det ursprungliga klustret. För att undvika inkonsekvenser rekommenderar vi dock att du inaktiverar HBase-tabellerna innan du tar bort klustret. 

Du kan använda kommandot HBase `disable 'Contacts'` . 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet tar du bort det HBase-kluster som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. I rutan **Sök** längst upp skriver du **HDInsight**.
1. Välj **HDInsight-kluster** under **Tjänster**.
1. I listan över HDInsight-kluster som visas klickar du på **...** intill det kluster som du skapade för den här självstudien.
1. Klicka på **Ta bort**. Klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar ett Apache HBase-kluster. Och hur du skapar tabeller och visar data i dessa tabeller från HBase-gränssnittet. Du har också lärt dig hur du använder en Hive-fråga på data i HBase-tabeller. Och hur du använder HBase C# REST-API: er för att skapa en HBase-tabell och hämta data från tabellen. Mer information finns i:

> [!div class="nextstepaction"]
> [HDInsight HBase-översikt](./apache-hbase-overview.md)
