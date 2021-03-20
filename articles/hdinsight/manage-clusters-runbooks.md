---
title: 'Självstudie: använda Azure Automation runbooks för att skapa kluster – Azure HDInsight'
description: Lär dig hur du skapar och tar bort Azure HDInsight-kluster med skript som körs i molnet med hjälp av Azure Automation runbooks.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: 255542d820d135d1a88e193a8ef13ae590ce4016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944039"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>Självstudie: Skapa Azure HDInsight-kluster med Azure Automation

Med Azure Automation kan du skapa skript som körs i molnet och hantera Azure-resurser på begäran eller enligt ett schema. Den här artikeln beskriver hur du skapar och tar bort Azure HDInsight-kluster med PowerShell-Runbooks.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Installera moduler som krävs för att interagera med HDInsight.
> * Skapa och lagra autentiseringsuppgifter som krävs när klustret skapas.
> * Skapa en ny Azure Automation Runbook för att skapa ett HDInsight-kluster.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett befintligt [Azure Automation-konto](../automation/automation-quickstart-create-account.md).
* Ett befintligt [Azure Storage-konto](../storage/common/storage-account-create.md)som kommer att användas som kluster lagring.

## <a name="install-hdinsight-modules"></a>Installera HDInsight-moduler

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj dina Azure Automation-konton.
1. Välj **moduler-Galleri** under **delade resurser**.
1. Skriv **AzureRM. Profile** i rutan och tryck på RETUR för att söka. Välj det tillgängliga Sök resultatet.
1. På skärmen **AzureRM. Profile** väljer du **Importera**. Markera kryss rutan för att uppdatera Azure-moduler och välj sedan **OK**.

    ![Importera AzureRM. Profile-modul](./media/manage-clusters-runbooks/import-azurermprofile-module.png)

1. Gå tillbaka till modulens Galleri genom att välja **Galleri** under **delade resurser**.
1. Skriv **HDInsight**. Välj **AzureRM. HDInsight**.

    ![Bläddra bland HDInsight-moduler](./media/manage-clusters-runbooks/browse-modules-hdinsight.png)

1. På panelen **AzureRM. HDInsight** väljer du **Importera** och **OK**.

    ![Importera AzureRM. HDInsight-modul](./media/manage-clusters-runbooks/import-azurermhdinsight-module.png)

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

1. Under **delade resurser** väljer du **autentiseringsuppgifter**.
1. Välj **Lägg till en autentiseringsuppgift**.
1. Ange den information som krävs på panelen **ny autentiseringsuppgift** . Den här autentiseringsuppgiften är att lagra kluster lösen ordet, vilket gör att du kan logga in på Ambari.

    | Egenskap | Värde |
    | --- | --- |
    | Namn | `cluster-password` |
    | Användarnamn | `admin` |
    | Lösenord | `SECURE_PASSWORD` |
    | Bekräfta lösenordet | `SECURE_PASSWORD` |

1. Välj **Skapa**.
1. Upprepa samma process för en ny autentiseringsuppgift `ssh-password` med användar namn `sshuser` och lösen ord som du själv väljer. Välj **Skapa**. Den här autentiseringsuppgiften är att lagra SSH-lösenordet för klustret.

    ![skapa autentiseringsuppgift](./media/manage-clusters-runbooks/create-credentials.png)

## <a name="create-a-runbook-to-create-a-cluster"></a>Skapa en Runbook för att skapa ett kluster

1. Välj **Runbooks** under **process automatisering**.
1. Välj **skapa en Runbook**.
1. På panelen **skapa en Runbook** anger du ett namn för runbooken, till exempel `hdinsight-cluster-create` . Välj **PowerShell** i list rutan **Runbook-typ** .
1. Välj **Skapa**.

    ![Skapa Runbook](./media/manage-clusters-runbooks/create-runbook.png)

1. Ange följande kod på skärmen **Redigera PowerShell-Runbook** och välj **publicera**:

    ![publicera Runbook](./media/manage-clusters-runbooks/publish-runbook.png)

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>Skapa en Runbook för att ta bort ett kluster

1. Välj **Runbooks** under **process automatisering**.
1. Välj **skapa en Runbook**.
1. På panelen **skapa en Runbook** anger du ett namn för runbooken, till exempel `hdinsight-cluster-delete` . Välj **PowerShell** i list rutan **Runbook-typ** .
1. Välj **Skapa**.
1. Ange följande kod på skärmen **Redigera PowerShell-Runbook** och välj **publicera**:

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Köra Runbooks

### <a name="create-a-cluster"></a>Skapa ett kluster

1. Visa listan över Runbooks för ditt Automation-konto genom att välja **Runbooks** under **process automatisering**.
1. Välj `hdinsight-cluster-create` eller det namn som du använde när du skapade en Runbook för att skapa klustret.
1. Välj **Starta** för att köra runbooken omedelbart. Du kan också schemalägga att Runbooks ska köras regelbundet. Se [schemaläggning av en Runbook i Azure Automation](../automation/shared-resources/schedules.md)
1. Ange de parametrar som krävs för skriptet och välj **OK**. Då skapas ett nytt HDInsight-kluster med det namn som du angav i parametern **kluster** namn.

    ![Kör skapa kluster-Runbook](./media/manage-clusters-runbooks/execute-create-runbook.png)

### <a name="delete-a-cluster"></a>Ta bort ett kluster

Ta bort klustret genom att välja den `hdinsight-cluster-delete` Runbook som du skapade. Välj **Start**, ange parametern **kluster** namn och sselect **OK**.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort det Azure Automation konto som skapades för att undvika oönskade avgifter. Om du vill göra det navigerar du till Azure Portal, väljer den resurs grupp där du skapade Azure Automation-kontot, väljer Automation-kontot och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera Apache Hadoop kluster i HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md)