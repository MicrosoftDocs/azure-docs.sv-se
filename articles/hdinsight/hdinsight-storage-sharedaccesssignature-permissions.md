---
title: Begränsa åtkomst med signaturer för delad åtkomst – Azure HDInsight
description: Lär dig hur du använder signaturer för delad åtkomst för att begränsa åtkomsten till HDInsight till data som lagras i Azure Blob Storage.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/28/2020
ms.openlocfilehash: 141db7feee987b7fffc578e19c60bd94ad56d239
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511645"
---
# <a name="use-azure-blob-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Använd signaturer för delad åtkomst för Azure Blob Storage för att begränsa åtkomsten till data i HDInsight

HDInsight har fullständig åtkomst till data i Azure Blob Storage-kontona som är kopplade till klustret. Du kan använda signaturer för delad åtkomst på BLOB-behållaren för att begränsa åtkomsten till data. Signaturer för delad åtkomst (SAS) är en funktion i Azure Blob Storage-konton som gör att du kan begränsa åtkomsten till data. Till exempel tillhandahåller skrivskyddad åtkomst till data.

> [!IMPORTANT]  
> Om du använder en lösning med Apache Ranger bör du överväga att använda domänanslutna HDInsight. Mer information finns i Konfigurera ett [domänanslutet HDInsight-](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) dokument.

> [!WARNING]  
> HDInsight måste ha fullständig åtkomst till standard lagrings utrymmet för klustret.

## <a name="prerequisites"></a>Krav

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* En befintlig [lagrings behållare](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/).

* Om du vill använda Azure CLI och du ännu inte har installerat det kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

* Om du använder [python](https://www.python.org/downloads/), version 2,7 eller senare.

* Om du använder C# måste Visual Studio vara version 2013 eller högre.

* URI-schemat för ditt lagrings konto. Det här schemat är `wasb://` för Azure Blob Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Blob Storage blir URI: n `wasbs://` .

* Ett befintligt HDInsight-kluster för att lägga till en signatur för delad åtkomst till. Annars kan du använda Azure PowerShell för att skapa ett kluster och lägga till en signatur för delad åtkomst när klustret skapas.

* Exempelfilerna från [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) . Den här lagrings platsen innehåller följande objekt:

  * Ett Visual Studio-projekt som kan skapa en lagrings behållare, lagrad princip och SAS för användning med HDInsight
  * Ett Python-skript som kan skapa en lagrings behållare, lagrad princip och SAS för användning med HDInsight
  * Ett PowerShell-skript som kan skapa ett HDInsight-kluster och konfigurera det att använda SAS. En uppdaterad version används nedan.
  * En exempel fil: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Det finns två former av signaturer för delad åtkomst:

* `Ad hoc`: Start tid, förfallo tid och behörigheter för SAS anges i SAS-URI: n.

* `Stored access policy`: En lagrad åtkomst princip definieras på en resurs behållare, till exempel en BLOB-behållare. En princip kan användas för att hantera begränsningar för en eller flera signaturer för delad åtkomst. När du associerar en SAS med en lagrad åtkomst princip ärver SAS begränsningarna-start tiden, förfallo tiden och de behörigheter som definierats för den lagrade åtkomst principen.

Skillnaden mellan de två formulären är viktig för ett nyckel scenario: återkallning. En SAS är en URL, så vem som helst som får SAS kan använda den. Det spelar ingen roll vem som begärde att börja med. Om en SAS publiceras offentligt kan den användas av vem som helst i världen. En SAS som är distribuerad är giltig tills något av fyra saker sker:

1. Den förfallo tid som angetts för SAS har uppnåtts.

2. Utgångs tiden som angetts för den lagrade åtkomst principen som refereras av SAS har nåtts. Följande scenarier orsakar förfallo tiden som nåtts:

    * Tidsintervallet har förflutit.
    * Den lagrade åtkomst principen ändras till att ha en förfallo tid tidigare. Att ändra förfallo tiden är ett sätt att återkalla SAS.

3. Den lagrade åtkomst principen som refereras av SAS tas bort, vilket är ett annat sätt att återkalla SAS. Om du återskapar den lagrade åtkomst principen med samma namn är alla SAS-token för den tidigare principen giltiga (om förfallo tiden på SAS: t inte har passerat). Om du tänker återkalla SAS måste du använda ett annat namn om du återskapar åtkomst principen med en förfallo tid i framtiden.

4. Den konto nyckel som användes för att skapa SAS återskapas. Om du återskapar nyckeln kan alla program som använder den föregående nyckeln inte autentiseras. Uppdatera alla komponenter till den nya nyckeln.

> [!IMPORTANT]  
> En signatur-URI för delad åtkomst är kopplad till den konto nyckel som används för att skapa signaturen och den associerade lagrade åtkomst principen (om sådan finns). Om ingen lagrad åtkomst princip anges, är det enda sättet att återkalla en signatur för delad åtkomst att ändra konto nyckeln.

Vi rekommenderar att du alltid använder lagrade åtkomst principer. När du använder lagrade principer kan du antingen återkalla signaturer eller förlänga förfallo datumet vid behov. Stegen i det här dokumentet använder lagrade åtkomst principer för att generera SAS.

Mer information om signaturer för delad åtkomst finns i [förstå SAS-modellen](../storage/common/storage-sas-overview.md).

## <a name="create-a-stored-policy-and-sas"></a>Skapa en lagrad princip och SAS

Spara SAS-token som skapas i slutet av varje metod. Token ser ut ungefär som följande utdata:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Använda PowerShell

Ersätt `RESOURCEGROUP` , `STORAGEACCOUNT` och `STORAGECONTAINER` med lämpliga värden för din befintliga lagrings behållare. Ändra katalogen till `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` eller ändra `-File` parametern så att den innehåller den absoluta sökvägen för `Set-AzStorageblobcontent` . Ange följande PowerShell-kommando:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Använda Azure CLI

Användningen av variabler i det här avsnittet baseras på en Windows-miljö. Små variationer kommer att krävas för bash eller andra miljöer.

1. Ersätt `STORAGEACCOUNT` och `STORAGECONTAINER` med lämpliga värden för din befintliga lagrings behållare.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Ange den hämtade primär nyckeln till en variabel för senare användning. Ersätt `PRIMARYKEY` med det hämtade värdet i föregående steg och ange sedan kommandot nedan:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Ändra katalogen till `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` eller ändra `--file` parametern så att den innehåller den absoluta sökvägen för `az storage blob upload` . Kör återstående kommandon:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name %AZURE_STORAGE_CONTAINER% --policy-name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Använda Python

Öppna `SASToken.py` filen och Ersätt `storage_account_name` , `storage_account_key` och `storage_container_name` med lämpliga värden för din befintliga lagrings behållare, och kör sedan skriptet.

Du kan behöva köra `pip install --upgrade azure-storage` om du får fel meddelandet `ImportError: No module named azure.storage` .

### <a name="using-c"></a>Använda C\#

1. Öppna lösningen i Visual Studio.

2. I Solution Explorer högerklickar du på projektet **SASExample** och väljer **Egenskaper**.

3. Välj **Inställningar** och Lägg till värden för följande poster:

    |Objekt |Beskrivning |
    |---|---|
    |StorageConnectionString|Anslutnings strängen för det lagrings konto som du vill skapa en lagrad princip och SAS för. Formatet bör vara `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` där `myaccount` är namnet på ditt lagrings konto och `mykey` är nyckeln för lagrings kontot.|
    |ContainerName|Den behållare i lagrings kontot som du vill begränsa åtkomsten till.|
    |SASPolicyName|Namnet som ska användas för den lagrade principen som ska skapas.|
    |FileToUpload|Sökvägen till en fil som överförs till behållaren.|

4. Kör projektet. Spara SAS-principens token, lagrings kontots namn och behållar namnet. Dessa värden används när du kopplar lagrings kontot till ditt HDInsight-kluster.

## <a name="use-the-sas-with-hdinsight"></a>Använda SAS med HDInsight

När du skapar ett HDInsight-kluster måste du ange ett primärt lagrings konto. Du kan också ange ytterligare lagrings konton. Båda dessa metoder för att lägga till lagring kräver fullständig åtkomst till de lagrings konton och behållare som används.

Använd en signatur för delad åtkomst för att begränsa åtkomsten till behållaren. Lägg till en anpassad post i **kärn plats** konfigurationen för klustret. Du kan lägga till posten när klustret skapas med hjälp av PowerShell eller när klustret har skapats med Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Skapa ett kluster som använder SAS

Ersätt,,,, `CLUSTERNAME` `RESOURCEGROUP` `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER` `STORAGEACCOUNT` och `TOKEN` med lämpliga värden. Ange PowerShell-kommandon:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> När du uppmanas att ange ett användar namn och lösen ord för HTTP/s eller SSH måste du ange ett lösen ord som uppfyller följande kriterier:
>
> * Måste vara minst 10 tecken långt.
> * Måste innehålla minst en siffra.
> * Måste innehålla minst ett icke-alfanumeriskt tecken.
> * Måste innehålla minst en versal eller gemen bokstav.

Det tar en stund innan skriptet slutförs, vanligt vis cirka 15 minuter. När skriptet har slutförts utan fel har klustret skapats.

### <a name="use-the-sas-with-an-existing-cluster"></a>Använda SAS med ett befintligt kluster

Om du har ett befintligt kluster kan du lägga till SAS i **Core-site-** konfigurationen med hjälp av följande steg:

1. Öppna Ambari-webbgränssnittet för klustret. Adressen till den här sidan är `https://YOURCLUSTERNAME.azurehdinsight.net` . När du uppmanas till detta ska du autentisera till klustret med administratörs namnet (admin) och lösen ordet som du använde när du skapade klustret.

1. Navigera till **HDFS**  >  **configs**  >  **Advanced**  >  **anpassad Core-site**.

1. Expandera avsnittet **anpassad Core-site** , bläddra till slutet och välj sedan **Lägg till egenskap...**. Använd följande värden för **nyckel** och **värde**:

    * **Nyckel**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Värde**: den SAS som returnerades av en av metoderna som kördes tidigare.

    Ersätt `CONTAINERNAME` med namnet på den behållare som du använde med C#-eller SAS-programmet. Ersätt `STORAGEACCOUNTNAME` med det lagrings konto namn som du använde.

    Välj **Lägg till** för att spara den här nyckeln och värdet

1. Välj knappen **Spara** för att spara konfigurations ändringarna. När du uppmanas till det lägger du till en beskrivning av ändringen ("lägga till SAS-åtkomstkontroll" till exempel) och väljer sedan **Spara**.

    Välj **OK** när ändringarna har slutförts.

   > [!IMPORTANT]  
   > Du måste starta om flera tjänster innan ändringen börjar gälla.

1. List rutan **starta om** visas. Välj **starta om alla som påverkas** från List rutan och bekräfta sedan __starta om alla__.

    Upprepa den här processen för **MapReduce2** och **garn**.

1. När tjänsterna har startats om väljer du var och en och inaktiverar underhålls läget från List rutan **service åtgärder** .

## <a name="test-restricted-access"></a>Testa begränsad åtkomst

Använd följande steg för att kontrol lera att du bara kan läsa och lista objekt på SAS-lagrings kontot.

1. Anslut till klustret. Ersätt `CLUSTERNAME` med namnet på klustret och ange följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Om du vill visa innehållet i behållaren använder du följande kommando från prompten:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Ersätt `SASCONTAINER` med namnet på den behållare som skapats för SAS-lagrings kontot. Ersätt `SASACCOUNTNAME` med namnet på det lagrings konto som används för SAS.

    Listan innehåller den fil som laddats upp när behållaren och SAS skapades.

3. Använd följande kommando för att kontrol lera att du kan läsa innehållet i filen. Ersätt `SASCONTAINER` och `SASACCOUNTNAME` som i föregående steg. Ersätt `sample.log` med namnet på filen som visas i föregående kommando:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Det här kommandot visar filens innehåll.

4. Använd följande kommando för att ladda ned filen till det lokala fil systemet:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Det här kommandot laddar ned filen till en lokal fil med namnet **testfile.txt**.

5. Använd följande kommando för att överföra den lokala filen till en ny fil med namnet **testupload.txt** på SAS-lagringen:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Du får ett meddelande som liknar följande text:

    ```output
    put: java.io.IOException
    ```

    Felet beror på att lagrings platsen är Read + List. Använd följande kommando för att lagra data på standard lagrings utrymmet för klustret, vilket är skrivbart:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Den här gången bör åtgärden slutföras.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du lägger till lagring med begränsad åtkomst till ditt HDInsight-kluster kan du lära dig andra sätt att arbeta med data i klustret:

* [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Auktorisera användare för Apache Ambari Views](hdinsight-authorize-users-to-ambari.md)
