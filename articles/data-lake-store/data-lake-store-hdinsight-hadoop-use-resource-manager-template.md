---
title: Mall – HDInsight-kluster med Data Lake Storage Gen1
description: Använd Azure Resource Manager mallar för att skapa och använda Azure HDInsight-kluster med Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a7283ad4c4c61ecc293a55ffc4cb9626bb28d630
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92108736"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 med Azure Resource Manager-mall
> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standard lagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagrings utrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1, **som ytterligare lagrings utrymme**.

För kluster typer som stöds kan Data Lake Storage Gen1 användas som standard lagrings utrymme eller som ett ytterligare lagrings konto. När Data Lake Storage Gen1 används som ytterligare lagrings utrymme är standard lagrings kontot för klustren fortfarande Azure Blob Storage (WASB) och de klusterbaserade filerna (till exempel loggar osv.) skrivs fortfarande till standard lagringen, medan de data som du vill bearbeta kan lagras i ett Data Lake Storage Gen1 konto. Att använda Data Lake Storage Gen1 som ett ytterligare lagrings konto påverkar inte prestanda eller möjlighet att läsa och skriva till lagringen från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Använda Data Lake Storage Gen1 för kluster lagring för HDInsight

Här är några viktiga överväganden när du använder HDInsight med Data Lake Storage Gen1:

* Alternativet för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standard lagring är tillgängligt för HDInsight version 3,5 och 3,6.

* Alternativ för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som ytterligare lagrings utrymme är tillgängligt för HDInsight-versionerna 3,2, 3,4, 3,5 och 3,6.

I den här artikeln etablerar vi ett Hadoop-kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme. Instruktioner för hur du skapar ett Hadoop-kluster med Data Lake Storage Gen1 som standard lagring finns i [skapa ett HDInsight-kluster med data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).
* **Azure Active Directory tjänstens huvud namn**. Steg i den här självstudien innehåller instruktioner om hur du skapar ett tjänst objekt i Azure AD. Du måste dock vara en Azure AD-administratör för att kunna skapa ett huvud namn för tjänsten. Om du är en Azure AD-administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    **Om du inte är en Azure AD-administratör** kan du inte utföra de steg som krävs för att skapa ett huvud namn för tjänsten. I sådana fall måste Azure AD-administratören först skapa ett huvud namn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom måste tjänstens huvud namn skapas med ett certifikat, enligt beskrivningen i [skapa ett huvud namn för tjänsten med certifikatet](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Skapa ett HDInsight-kluster med Data Lake Storage Gen1
Resource Manager-mallen och förutsättningarna för att använda mallen är tillgängliga på GitHub vid [distribution av ett HDInsight Linux-kluster med nya data Lake Storage gen1](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Följ anvisningarna som visas på den här länken för att skapa ett HDInsight-kluster med Data Lake Storage Gen1 som det extra lagrings utrymmet.

Anvisningarna på länken ovan kräver PowerShell. Innan du börjar med dessa instruktioner ska du kontrol lera att du loggar in på ditt Azure-konto. Öppna ett nytt Azure PowerShell-fönster på Skriv bordet och ange följande kodfragment. När du uppmanas att logga in kontrollerar du att du loggar in som en av prenumerations administratörerna/ägaren:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Mallen distribuerar dessa resurs typer:

* [Microsoft. DataLakeStore/konton](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. HDInsight/kluster](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Ladda upp exempel data till Data Lake Storage Gen1
Resource Manager-mallen skapar ett nytt lagrings konto med Data Lake Storage Gen1 och associerar det med HDInsight-klustret. Nu måste du ladda upp exempel data till Data Lake Storage Gen1. Du behöver dessa data senare i självstudien för att köra jobb från ett HDInsight-kluster som har åtkomst till data i lagrings kontot med Data Lake Storage Gen1. Instruktioner för hur du överför data finns i [Ladda upp en fil till data Lake Storage gen1](data-lake-store-get-started-portal.md#uploaddata). Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Ange relevanta ACL: er för exempel data
För att se till att de exempel data som du överför kan nås från HDInsight-klustret måste du se till att Azure AD-programmet som används för att upprätta identiteten mellan HDInsight-klustret och Data Lake Storage Gen1 har åtkomst till filen/mappen som du försöker få åtkomst till. Det gör du genom att utföra följande steg.

1. Hitta namnet på det Azure AD-program som är associerat med HDInsight-kluster och lagrings kontot med Data Lake Storage Gen1. Ett sätt att söka efter namnet är att öppna bladet HDInsight-kluster som du skapade med Resource Manager-mallen, klicka på fliken **Azure AD-identitet** och leta efter värdet för **visnings namnet för tjänstens huvud namn**.
2. Ge till gång till det här Azure AD-programmet på filen/mappen som du vill komma åt från HDInsight-klustret. Om du vill ange rätt ACL: er för filen/mappen i Data Lake Storage Gen1, se [skydda data i data Lake Storage gen1](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör test jobb på HDInsight-klustret om du vill använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster kan du köra test jobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Storage Gen1. För att göra det, kommer vi att köra ett Hive-exempel jobb som skapar en tabell med hjälp av exempel data som du laddade upp tidigare till ditt lagrings konto med Data Lake Storage Gen1.

I det här avsnittet ska du använda SSH i ett HDInsight Linux-kluster och köra exempel registrerings frågan. Om du använder en Windows-klient rekommenderar vi att du använder **SparaTillFil**, som kan laddas ned från [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Mer information om hur du använder SparaTillFil finns i [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. När du är ansluten startar du Hive CLI med hjälp av följande kommando:

   ```
   hive
   ```
2. Använd CLI och ange följande instruktioner för att skapa en ny tabell med namnet **fordon** genom att använda exempel data i data Lake Storage gen1:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Du bör se utdata som liknar följande:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Åtkomst Data Lake Storage Gen1 med HDFS-kommandon
När du har konfigurerat HDInsight-klustret så att det använder Data Lake Storage Gen1 kan du använda HDFS-Shell-kommandona för att få åtkomst till butiken.

I det här avsnittet ska du använda SSH i ett HDInsight Linux-kluster och köra HDFS-kommandona. Om du använder en Windows-klient rekommenderar vi att du använder **SparaTillFil**, som kan laddas ned från [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

Mer information om hur du använder SparaTillFil finns i [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

När du är ansluten använder du följande HDFS filesystem-kommando för att visa en lista över filerna i lagrings kontot med Data Lake Storage Gen1.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Detta bör vara en lista över filen som du överförde tidigare till Data Lake Storage Gen1.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Du kan också använda `hdfs dfs -put` kommandot för att ladda upp filer till data Lake Storage gen1 och sedan använda `hdfs dfs -ls` för att kontrol lera om filerna har laddats upp.


## <a name="next-steps"></a>Nästa steg
* [Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)