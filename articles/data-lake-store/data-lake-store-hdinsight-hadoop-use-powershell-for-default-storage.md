---
title: PowerShell – HDInsight-kluster med Data Lake Storage Gen1 – Azure
description: Använd Azure PowerShell för att skapa och använda Azure HDInsight-kluster med Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 5e899f28cf5b3c11ae5f935d7bc273c566214225
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606784"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 som standard lagring med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Använda Azure-portalen](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använd PowerShell (för standard lagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använd PowerShell (för ytterligare lagrings utrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Lär dig hur du använder Azure PowerShell för att konfigurera Azure HDInsight-kluster med Azure Data Lake Storage Gen1 som standard lagring. Anvisningar om hur du skapar ett HDInsight-kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme finns i [skapa ett HDInsight-kluster med data Lake Storage gen1 som ytterligare lagrings utrymme](data-lake-store-hdinsight-hadoop-use-powershell.md).

Här är några viktiga överväganden när du använder HDInsight med Data Lake Storage Gen1:

* Alternativet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standard lagrings utrymme är tillgängligt för HDInsight version 3,5 och 3,6.

* Alternativet att skapa HDInsight-kluster med åtkomst till Data Lake Storage Gen1 som standard lagring är *inte tillgängligt* för HDInsight Premium-kluster.

Följ anvisningarna i följande fem avsnitt om du vill konfigurera HDInsight att arbeta med Data Lake Storage Gen1 med hjälp av PowerShell.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Innan du börjar den här självstudien kontrollerar du att du uppfyller följande krav:

* **En Azure-prenumeration**: gå till [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1,0 eller mer**: se [så här installerar och konfigurerar du PowerShell](/powershell/azure/).
* **Windows Software Development Kit (SDK)**: om du vill installera Windows SDK går du till [hämtningar och verktyg för Windows 10](https://dev.windows.com/downloads). SDK används för att skapa ett säkerhetscertifikat.
* **Azure Active Directory tjänstens huvud namn**: den här självstudien beskriver hur du skapar ett huvud namn för tjänsten i Azure Active Directory (Azure AD). Om du vill skapa ett huvud namn för tjänsten måste du dock vara en Azure AD-administratör. Om du är administratör kan du hoppa över den här förutsättningen och fortsätta med självstudien.

    >[!NOTE]
    >Du kan bara skapa ett huvud namn för tjänsten om du är en Azure AD-administratör. Din Azure AD-administratör måste skapa ett huvud namn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Tjänstens huvud namn måste skapas med ett certifikat, enligt beskrivningen i [skapa ett tjänst huvud namn med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Skapa ett Azure Data Lake Storage Gen1 konto

Om du vill skapa ett Data Lake Storage Gen1 konto gör du följande:

1. Öppna ett PowerShell-fönster från Skriv bordet och ange sedan kodfragmenten nedan. När du uppmanas att logga in loggar du in som en av prenumerationens administratörer eller ägare. 

    ```azurepowershell
    # Sign in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

    > [!NOTE]
    > Om du registrerar Data Lake Storage Gen1 Resource Provider och får ett fel som liknar `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` , kanske din prenumeration inte godkänns för data Lake Storage gen1. Om du vill aktivera Azure-prenumerationen för Data Lake Storage Gen1 följer du anvisningarna i [Kom igång med Azure Data Lake Storage gen1 genom att använda Azure Portal](data-lake-store-get-started-portal.md).
    >

2. Ett Data Lake Storage Gen1 konto är associerat med en Azure-resurs grupp. Börja med att skapa en resurs grupp.

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

3. Skapa ett Data Lake Storage Gen1-konto. Konto namnet som du anger får bara innehålla gemena bokstäver och siffror.

    ```azurepowershell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. Om du använder Data Lake Storage Gen1 som standard lagrings utrymme måste du ange en rot Sök väg som de filer som ska lagras i klustret ska kopieras till när klustret skapas. Om du vill skapa en rot Sök väg, som är **/Clusters/hdiadlcluster** i kodfragmentet, använder du följande cmdlets:

    ```azurepowershell
    $myrootdir = "/"
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster
    ````

## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Konfigurera autentisering för rollbaserad åtkomst till Data Lake Storage Gen1
Varje Azure-prenumeration är associerad med en Azure AD-entitet. Användare och tjänster som har åtkomst till prenumerations resurser med hjälp av Azure Portal eller Azure Resource Manager-API måste först autentiseras med Azure AD. Åtkomst beviljas till Azure-prenumerationer och-tjänster genom att tilldela dem lämplig roll på en Azure-resurs. För tjänster identifierar tjänstens huvud namn tjänsten i Azure AD.

I det här avsnittet beskrivs hur du beviljar en program tjänst, till exempel HDInsight, åtkomst till en Azure-resurs (Data Lake Storage Gen1 konto som du skapade tidigare). Du gör detta genom att skapa ett huvud namn för tjänsten för programmet och tilldela roller till det via PowerShell.

Om du vill konfigurera Active Directory autentisering för Data Lake Storage Gen1 utför du uppgifterna i följande två avsnitt.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Kontrol lera att du har [Windows SDK](https://dev.windows.com/en-us/downloads) installerat innan du fortsätter med stegen i det här avsnittet. Du måste också ha skapat en katalog, till exempel *C:\mycertdir*, där du skapar certifikatet.

1. Från PowerShell-fönstret går du till den plats där du installerade Windows SDK (vanligt vis *C:\Program Files (x86) \Windows Kits\10\bin\x86*) och använder verktyget [MakeCert][makecert] för att skapa ett självsignerat certifikat och en privat nyckel. Använd följande kommandon:

    ```azurepowershell
    $certificateFileDir = "<my certificate directory>"
    cd $certificateFileDir

    makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048
    ```

    Du uppmanas att ange lösen ordet för den privata nyckeln. När kommandot har körts, bör du se filen **certifikat fil. cer** och **MyKey. PVK** i den certifikat katalog som du har angett.
2. Använd verktyget [Pvk2Pfx][pvk2pfx] för att konvertera. PVK-och. cer-filerna som MakeCert skapade till en. pfx-fil. Kör följande kommando:

    ```azurepowershell
    pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>
    ```

    När du uppmanas till det anger du lösen ordet för den privata nyckeln du angav tidigare. Värdet som du anger för parametern **-Po** är det lösen ord som är kopplat till. pfx-filen. När kommandot har slutförts bör du även se en **certifikat fil. pfx** i den certifikat katalog som du har angett.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Skapa en Azure AD och ett huvud namn för tjänsten
I det här avsnittet skapar du ett huvud namn för tjänsten för ett Azure AD-program, tilldelar en roll till tjänstens huvud namn och autentiserar som tjänstens huvud namn genom att ange ett certifikat. Kör följande kommandon för att skapa ett program i Azure AD:

1. Klistra in följande cmdlets i PowerShell-konsolens fönster. Kontrol lera att värdet som du anger för egenskapen **-DisplayName** är unikt. Värdena för **-Start-** och **-IdentiferUris** är platshållarfält och kontrol leras inte.

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

3. Ge tjänstens huvud namn åtkomst till Data Lake Storage Gen1 roten och alla mappar i rot Sök vägen som du angav tidigare. Använd följande cmdletar:

    ```azurepowershell
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
    Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All
    ```

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>Skapa ett HDInsight Linux-kluster med Data Lake Storage Gen1 som standard lagring

I det här avsnittet skapar du ett HDInsight Hadoop Linux-kluster med Data Lake Storage Gen1 som standard lagring. I den här versionen måste HDInsight-klustret och Data Lake Storage Gen1 finnas på samma plats.

1. Hämta klient-ID: t för prenumerationen och lagra det för att använda senare.

    ```azurepowershell
    $tenantID = (Get-AzContext).Tenant.TenantId
    ```

2. Skapa HDInsight-klustret med hjälp av följande cmdlets:

    ```azurepowershell
    # Set these variables

    $location = "East US 2"
    $storageAccountName = $dataLakeStorageGen1Name    # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>"     # e.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
    $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
    $httpCredentials = Get-Credential
    $sshCredentials = Get-Credential

    New-AzHDInsightCluster `
           -ClusterType Hadoop `
           -OSType Linux `
           -ClusterSizeInNodes $clusterNodes `
           -ResourceGroupName $resourceGroupName `
           -ClusterName $clusterName `
           -HttpCredential $httpCredentials `
           -Location $location `
           -DefaultStorageAccountType AzureDataLakeStore `
           -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
           -DefaultStorageRootPath $storageRootPath `
           -Version "3.6" `
           -SshCredential $sshCredentials `
           -AadTenantId $tenantId `
           -ObjectId $objectId `
           -CertificateFilePath $certificateFilePath `
           -CertificatePassword $password
    ```

    När cmdleten har slutförts bör du se utdata som visar kluster informationen.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Kör test jobb på HDInsight-klustret om du vill använda Data Lake Storage Gen1
När du har konfigurerat ett HDInsight-kluster kan du köra test jobb på det för att säkerställa att det kan komma åt Data Lake Storage Gen1. Det gör du genom att köra ett Hive-exempel jobb för att skapa en tabell som använder exempel data som redan finns i Data Lake Storage Gen1 på *\<cluster root> /example/data/Sample.log*.

I det här avsnittet ska du skapa en SSH-anslutning (Secure Shell) till det HDInsight Linux-kluster som du skapade och sedan köra en exempel-Hive-fråga.

* Om du använder en Windows-klient för att skapa en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att skapa en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. När du har skapat anslutningen startar du Hive-kommando rads gränssnittet (CLI) med hjälp av följande kommando:

    ```powershell
    hive
    ```

2. Använd CLI för att ange följande instruktioner för att skapa en ny tabell med namnet **fordon** genom att använda exempel data i data Lake Storage gen1:

    ```azurepowershell
    DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

Du bör se frågans utdata i SSH-konsolen.

>[!NOTE]
>Sökvägen till exempel data i föregående CREATE TABLE-kommando är `adl:///example/data/` , där `adl:///` är kluster roten. Efter exemplet på kluster roten som anges i den här självstudien är kommandot `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster` . Du kan antingen använda det kortare alternativet eller ange den fullständiga sökvägen till kluster roten.
>

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Åtkomst Data Lake Storage Gen1 med hjälp av HDFS-kommandon
När du har konfigurerat HDInsight-klustret att använda Data Lake Storage Gen1 kan du använda kommandona Hadoop Distributed File System (HDFS) för att få åtkomst till butiken.

I det här avsnittet ska du skapa en SSH-anslutning till det HDInsight Linux-kluster som du skapade och sedan köra HDFS-kommandona.

* Om du använder en Windows-klient för att skapa en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Om du använder en Linux-klient för att skapa en SSH-anslutning till klustret, se [använda SSH med Linux-baserat Hadoop på HDInsight från Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

När du har skapat anslutningen kan du lista filerna i Data Lake Storage Gen1 med hjälp av följande HDFS-fil system kommando.

```azurepowershell
hdfs dfs -ls adl:///
```

Du kan också använda `hdfs dfs -put` kommandot för att ladda upp filer till data Lake Storage gen1 och sedan använda `hdfs dfs -ls` för att kontrol lera om filerna har laddats upp.

## <a name="see-also"></a>Se även
* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [Azure Portal: skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx