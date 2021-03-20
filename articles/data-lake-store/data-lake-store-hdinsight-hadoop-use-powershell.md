---
title: PowerShell – HDInsight med Data Lake Storage Gen1-tilläggs lagring – Azure
description: Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1 som ytterligare lagrings utrymme.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b7cb9d5c5c2ca850678d3f3194a9af8de526ada4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103398"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Använd Azure PowerShell för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 (som ytterligare lagrings utrymme)

> [!div class="op_single_selector"]
> * [Använda portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använda PowerShell (för standard lagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använda PowerShell (för ytterligare lagrings utrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använda Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure PowerShell för att konfigurera ett HDInsight-kluster med Azure Data Lake Storage Gen1, **som ytterligare lagrings utrymme**. Instruktioner för hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som standard lagring finns i [skapa ett HDInsight-kluster med data Lake Storage gen1 som standard lagring](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Om du ska använda Data Lake Storage Gen1 som ytterligare lagring för HDInsight-kluster rekommenderar vi starkt att du gör detta när du skapar klustret enligt beskrivningen i den här artikeln. Att lägga till Data Lake Storage Gen1 som ytterligare lagrings utrymme i ett befintligt HDInsight-kluster är en komplicerad process och är känslig för fel.
>

För kluster typer som stöds kan Data Lake Storage Gen1 användas som standard lagring eller ytterligare lagrings konto. När Data Lake Storage Gen1 används som ytterligare lagrings utrymme är standard lagrings kontot för klustren fortfarande Azure Blob Storage (WASB) och de klusterbaserade filerna (till exempel loggar osv.) skrivs fortfarande till standard lagringen, medan de data som du vill bearbeta kan lagras i en Data Lake Storage Gen1. Att använda Data Lake Storage Gen1 som ett ytterligare lagrings konto påverkar inte prestanda eller möjlighet att läsa och skriva till lagringen från klustret.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Använda Data Lake Storage Gen1 för kluster lagring för HDInsight

Här är några viktiga överväganden när du använder HDInsight med Data Lake Storage Gen1:

* Alternativ för att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som ytterligare lagrings utrymme är tillgängligt för HDInsight-versionerna 3,2, 3,4, 3,5 och 3,6.

Att konfigurera HDInsight för att fungera med Data Lake Storage Gen1 med hjälp av PowerShell omfattar följande steg:

* Skapa ett Data Lake Storage Gen1-konto 
* Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
* Skapa HDInsight-kluster med autentisering till Data Lake Storage Gen1
* Köra ett test jobb på klustret

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).
* **Windows SDK**. Du kan installera det [härifrån](https://dev.windows.com/en-us/downloads). Du kan använda detta för att skapa ett säkerhetscertifikat.
* **Azure Active Directory tjänstens huvud namn**. Steg i den här självstudien innehåller instruktioner om hur du skapar ett tjänst objekt i Azure AD. Du måste dock vara en Azure AD-administratör för att kunna skapa ett huvud namn för tjänsten. Om du är en Azure AD-administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    **Om du inte är en Azure AD-administratör** kan du inte utföra de steg som krävs för att skapa ett huvud namn för tjänsten. I sådana fall måste Azure AD-administratören först skapa ett huvud namn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom måste tjänstens huvud namn skapas med ett certifikat, enligt beskrivningen i [skapa ett huvud namn för tjänsten med certifikatet](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto 
Följ dessa steg om du vill skapa ett Data Lake Storage Gen1-konto.

1. Öppna ett nytt Azure PowerShell-fönster på Skriv bordet och ange följande kodfragment. När du uppmanas att logga in kontrollerar du att du loggar in som en av prenumerationens administratör/ägare:

    ```azurepowershell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

   > [!NOTE]
   > Om du får ett fel som liknar `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` när du registrerar data Lake Storage gen1 Resource Provider, är det möjligt att din prenumeration inte är godkänd för data Lake Storage gen1. Se till att aktivera Azure-prenumerationen för Data Lake Storage Gen1 genom att följa dessa [anvisningar](data-lake-store-get-started-portal.md).
   >
   >
2. Ett lagrings konto med Data Lake Storage Gen1 associeras med en Azure-resurs grupp. Börja med att skapa en Azure-resursgrupp.

    ```azurepowershell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    Utdata bör ser ut så här:

    ```output
    ResourceGroupName : hdiadlgrp
    Location          : eastus2
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp
    ```

3. Skapa ett lagrings konto med Data Lake Storage Gen1. Konto namnet som du anger får bara innehålla gemena bokstäver och siffror.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new storage account with Data Lake Storage Gen1 name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    Du bör se utdata som liknar följande:

    ```output
    ...
    ProvisioningState           : Succeeded
    State                       : Active
    CreationTime                : 5/5/2017 10:53:56 PM
    EncryptionState             : Enabled
    ...
    LastModifiedTime            : 5/5/2017 10:53:56 PM
    Endpoint                    : hdiadlstore.azuredatalakestore.net
    DefaultGroup                :
    Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
    Name                        : hdiadlstore
    Type                        : Microsoft.DataLakeStore/accounts
    Location                    : East US 2
    Tags                        : {}
    ```

5. Ladda upp exempel data till Data Lake Storage Gen1. Vi kommer att använda det senare i den här artikeln för att kontrol lera att data är tillgängliga från ett HDInsight-kluster. Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

    ```azurepowershell
    $myrootdir = "/"
    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv
    ```

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1

Varje Azure-prenumeration är associerad med en Azure Active Directory. Användare och tjänster som har åtkomst till resurser i prenumerationen med hjälp av Azure Portal-eller Azure Resource Manager-API måste först autentiseras med den Azure Active Directory. Åtkomst beviljas till Azure-prenumerationer och-tjänster genom att tilldela dem lämplig roll på en Azure-resurs.  För tjänster identifierar tjänstens huvud namn tjänsten i Azure Active Directory (Azure AD). I det här avsnittet beskrivs hur du beviljar en program tjänst, t. ex. HDInsight, åtkomst till en Azure-resurs (lagrings kontot med Data Lake Storage Gen1 du skapade tidigare) genom att skapa ett tjänst huvud namn för programmet och tilldela roller till det via Azure PowerShell.

Om du vill konfigurera Active Directory autentisering för Data Lake Storage Gen1 måste du utföra följande uppgifter.

* Skapa ett självsignerat certifikat
* Skapa ett program i Azure Active Directory och ett huvud namn för tjänsten

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Kontrol lera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerat innan du fortsätter med stegen i det här avsnittet. Du måste också ha skapat en katalog, till exempel **C:\mycertdir**, där certifikatet kommer att skapas.

1. Från PowerShell-fönstret navigerar du till den plats där du installerade Windows SDK (vanligt vis `C:\Program Files (x86)\Windows Kits\10\bin\x86` och använder verktyget [MakeCert][makecert] för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon.

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Du uppmanas att ange lösen ordet för den privata nyckeln. När kommandot har körts bör du se en certifikat fil. **CER** och **MyKey. PVK** i den certifikat katalog som du har angett.
2. Använd verktyget [Pvk2Pfx][pvk2pfx] för att konvertera. PVK-och. cer-filerna som MakeCert skapade till en. pfx-fil. Kör följande kommando.

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    När du uppmanas att ange lösen ordet för den privata nyckeln du angav tidigare. Värdet som du anger för parametern **-Po** är det lösen ord som är kopplat till. pfx-filen. När kommandot har slutförts bör du även se en certifikat fil. pfx i den certifikat katalog som du har angett.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Skapa en Azure Active Directory och ett huvud namn för tjänsten

I det här avsnittet ska du utföra stegen för att skapa ett huvud namn för tjänsten för ett Azure Active Directory program, tilldela en roll till tjänstens huvud namn och autentisera som tjänstens huvud namn genom att ange ett certifikat. Kör följande kommandon för att skapa ett program i Azure Active Directory.

1. Klistra in följande cmdlets i PowerShell-konsolens fönster. Kontrol lera att värdet som du anger för egenskapen **-DisplayName** är unikt. Dessutom är värdena för **-Start-** och **-IdentiferUris** värden för plats hållare och kontrol leras inte.

    ```azurepowershell
    $certificateFilePath = "$certificateFileDir\CertFile.pfx"

    $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

    $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

    $rawCertificateData = $certificatePFX.GetRawCertData()

    $credential = [System.Convert]::ToBase64String($rawCertificateData)

    $application = New-AzADApplication `
        -DisplayName "HDIADL" `
        -HomePage "https://contoso.com" `
        -IdentifierUris "https://mycontoso.com" `
        -CertValue $credential  `
        -StartDate $certificatePFX.NotBefore  `
        -EndDate $certificatePFX.NotAfter

    $applicationId = $application.ApplicationId
    ```

2. Skapa ett huvud namn för tjänsten med hjälp av program-ID: t.

    ```azurepowershell
    $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

     $objectId = $servicePrincipal.Id
    ```

3. Ge tjänstens huvud namn åtkomst till mappen Data Lake Storage Gen1 och filen som du kommer åt från HDInsight-klustret. Kodfragmentet nedan ger åtkomst till roten för lagrings kontot med Data Lake Storage Gen1 (där du kopierade exempel data filen) och själva filen.

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme

I det här avsnittet skapar vi ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme. I den här versionen måste HDInsight-klustret och lagrings kontot med Data Lake Storage Gen1 finnas på samma plats.

1. Börja med att hämta klient-ID: t för prenumerationen. Du kommer att behöva det senare.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. I den här versionen kan Data Lake Storage Gen1 endast användas som ytterligare lagrings utrymme för klustret för ett Hadoop-kluster. Standard lagringen är fortfarande Azure Blob Storage (WASB). Därför ska vi först skapa lagrings kontot och lagrings behållare som krävs för klustret.

    ```azurepowershell
    # Create an Azure storage account
    $location = "East US 2"
    $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

    New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzStorageContainer -Name $containerName -Context $destContext
    ```

3. Skapa HDInsight-klustret. Använd följande cmdletar.

    ```azurepowershell
    # Set these variables
    $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password
    ```

    När cmdleten har slutförts bör du se en lista med information om klustret.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1"></a>Kör test jobb på HDInsight-klustret för att använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster kan du köra test jobb på klustret för att testa att HDInsight-klustret har åtkomst till Data Lake Storage Gen1. För att göra det, kommer vi att köra ett Hive-exempel jobb som skapar en tabell med hjälp av exempel data som du laddade upp tidigare till ditt lagrings konto med Data Lake Storage Gen1.

I det här avsnittet ska du använda SSH i det HDInsight Linux-kluster som du skapade och köra en Hive-exempel fråga.

* Om du använder en Windows-klient för SSH i klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
* Om du använder en Linux-klient för SSH i klustret, se [Använd SSH med Linux-baserad Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. När du är ansluten startar du Hive CLI med hjälp av följande kommando:

    ```azurepowershell
    hive
    ```

2. Använd CLI och ange följande instruktioner för att skapa en ny tabell med namnet **fordon** genom att använda exempel data i data Lake Storage gen1:

    ```azurepowershell
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    Du bör se utdata som liknar följande:

    ```output
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

I det här avsnittet ska du använda SSH i HDInsight Linux-klustret som du har skapat och kört HDFS-kommandona.

* Om du använder en Windows-klient för SSH i klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
* Om du använder en Linux-klient för SSH i klustret, se [Använd SSH med Linux-baserad Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

När du är ansluten använder du följande HDFS filesystem-kommando för att visa en lista över filerna i lagrings kontot med Data Lake Storage Gen1.

```azurepowershell
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Detta bör vara en lista över filen som du överförde tidigare till Data Lake Storage Gen1.

```output
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Du kan också använda `hdfs dfs -put` kommandot för att ladda upp filer till data Lake Storage gen1 och sedan använda `hdfs dfs -ls` för att kontrol lera om filerna har laddats upp.

## <a name="see-also"></a>Se även
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Portal: skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx