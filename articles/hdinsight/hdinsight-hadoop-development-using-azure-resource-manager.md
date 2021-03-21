---
title: Migrera till Azure Resource Manager verktyg för HDInsight
description: Så här migrerar du till Azure Resource Manager utvecklingsverktyg för HDInsight-kluster
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.openlocfilehash: a8f808cd43f96f26db0de28e8059d02d9488320a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434669"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrera till Azure Resource Manager-baserade utvecklingsverktyg för HDInsight-kluster

HDInsight är inaktuellt Azure Service Manager-baserade (ASM) verktyg för HDInsight. Om du har använt Azure PowerShell, Azure klassisk CLI eller HDInsight .NET SDK för att arbeta med HDInsight-kluster, rekommenderar vi att du använder Azure Resource Manager versioner av PowerShell, CLI och .NET SDK. Den här artikeln innehåller länkar till hur du migrerar till den nya Resource Manager-baserade metoden. I tillämpliga fall visar det här dokumentet skillnaderna mellan ASM-och Resource Manager-metoderna för HDInsight.

> [!IMPORTANT]  
> Stödet för ASM-baserade PowerShell-, CLI-och .NET SDK upphör att fungera den **1 januari 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrera Azure klassisk CLI till Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI ger inte stöd för att arbeta med HDInsight-kluster. Du kan fortfarande använda den klassiska Azure-CLI: en med HDInsight, men Azures klassiska CLI är föråldrad.

Följande är de grundläggande kommandona för att arbeta med HDInsight via den klassiska Azure-CLI:

* `azure hdinsight cluster create` -skapar ett nytt HDInsight-kluster
* `azure hdinsight cluster delete` -tar bort ett befintligt HDInsight-kluster
* `azure hdinsight cluster show` – Visa information om ett befintligt kluster
* `azure hdinsight cluster list` – visar HDInsight-kluster för din Azure-prenumeration

Använd `-h` växeln för att kontrol lera de parametrar och växlar som är tillgängliga för varje kommando.

### <a name="new-commands"></a>Nya kommandon
Nya kommandon som är tillgängliga med Azure Resource Manager är:

* `azure hdinsight cluster resize` -ändrar antalet arbets noder i klustret dynamiskt
* `azure hdinsight cluster enable-http-access` -aktiverar HTTPs-åtkomst till klustret (aktiverat som standard)
* `azure hdinsight cluster disable-http-access` -inaktiverar HTTPs-åtkomst till klustret
* `azure hdinsight script-action` – innehåller kommandon för att skapa/hantera skript åtgärder i ett kluster
* `azure hdinsight config` – innehåller kommandon för att skapa en konfigurations fil som kan användas med `hdinsight cluster create` kommandot för att tillhandahålla konfigurations information.

### <a name="deprecated-commands"></a>Föråldrade kommandon
Om du använder `azure hdinsight job` kommandona för att skicka jobb till ditt HDInsight-kluster är de här kommandona inte tillgängliga via Resource Manager-kommandona. Om du behöver skicka jobb program mässigt till HDInsight från skript bör du i stället använda de REST-API: er som tillhandahålls av HDInsight. Mer information om hur du skickar jobb med hjälp av REST API: er finns i följande dokument.

* [Köra MapReduce-jobb med Hadoop på HDInsight med hjälp av sväng](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Kör Apache Hive frågor med Apache Hadoop på HDInsight med hjälp av sväng](hadoop/apache-hadoop-use-hive-curl.md)


Information om andra sätt att köra Apache Hadoop MapReduce, Apache Hive och Apache gris interaktivt finns i [använda MapReduce med Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md), [använda Apache Hive med Apache Hadoop på HDInsight](hadoop/hdinsight-use-hive.md)och [använda Apache gris med Apache Hadoop i HDInsight](./index.yml).

### <a name="examples"></a>Exempel
**Skapa ett kluster**

* Gammalt kommando (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nytt kommando- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Ta bort ett kluster**

* Gammalt kommando (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nytt kommando- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista kluster**

* Gammalt kommando (ASM)- `azure hdinsight cluster list`
* Nytt kommando- `azure hdinsight cluster list`

> [!NOTE]  
> För list kommandot visas `-g` bara kluster i den angivna resurs gruppen om du anger resurs gruppen med hjälp av.

**Visa kluster information**

* Gammalt kommando (ASM)- `azure hdinsight cluster show myhdicluster`
* Nytt kommando- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrerar Azure PowerShell till Azure Resource Manager
Allmän information om Azure PowerShell i Azure Resource Manager läge finns i [använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Azure PowerShell Resource Manager-cmdletar kan installeras sida vid sida med ASM-cmdletar. Cmdletarna från de två lägena kan särskiljas med deras namn.  Resource Manager-läget har *AzHDInsight* i cmdlet-namnen som jämförs med *AzureHDInsight* i det äldre Azure Service Management-läget.  Till exempel *New-AzHDInsightCluster* eller *New-AzureHDInsightCluster*. Parametrar och växlar kan ha namn på diskussions grupper och det finns många nya parametrar som är tillgängliga när du använder Resource Manager.  Till exempel kräver flera cmdlets en ny växel som heter *-ResourceGroupName*.

Innan du kan använda HDInsight-cmdlets måste du ansluta till ditt Azure-konto och skapa en ny resurs grupp:

* [Anslut – AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>Omdöpta cmdletar
Så här visar du HDInsight ASM-cmdletar i Windows PowerShell-konsolen:

```powershell
help *azurehdinsight*
```

I följande tabell visas ASM-cmdletar och deras namn i Resource Manager-läge:

| ASM-cmdletar | Resource Manager-cmdletar |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Använd-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Vänta-AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nya cmdletar
Följande är de nya cmdletar som endast är tillgängliga i Resource Manager-läge. 

**Skript åtgärd-relaterade cmdletar:**

* **Get-AzHDInsightPersistedScriptAction**: hämtar de bestående skript åtgärderna för ett kluster och listar dem i kronologisk ordning, eller hämtar information om en angiven beständiga skript åtgärd. 
* **Get-AzHDInsightScriptActionHistory**: hämtar skript åtgärds historiken för ett kluster och listar det i omvänd kronologisk ordning, eller hämtar information om en tidigare körd skript åtgärd. 
* **Remove-AzHDInsightPersistedScriptAction**: tar bort en bestående skript åtgärd från ett HDInsight-kluster.
* **Set-AzHDInsightPersistedScriptAction**: anger en tidigare utförd skript åtgärd som en bestående skript åtgärd.
* **Submit-AzHDInsightScriptAction**: skickar en ny skript åtgärd till ett Azure HDInsight-kluster. 

Mer information om användning finns i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

**Kluster identitet-relaterade cmdlet: ar:**

* **Add-AzHDInsightClusterIdentity**: lägger till en kluster identitet till ett kluster konfigurations objekt så att HDInsight-klustret kan komma åt Azure Data Lake Storage. Se [skapa ett HDInsight-kluster med data Lake Storage med hjälp av Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exempel
**Skapa kluster**

Gammalt kommando (ASM): 

```azurepowershell
New-AzureHDInsightCluster `
    -Name $clusterName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainerName $containerName `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -Credential $httpCredential `
    -SshCredential $sshCredential
```

Nytt kommando:

```azurepowershell
New-AzHDInsightCluster `
    -ClusterName $clusterName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $containerName  `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -HttpCredential $httpcredentials `
    -SshCredential $sshCredentials
```

**Ta bort klustret**

Gammalt kommando (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Nytt kommando:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Lista kluster**

Gammalt kommando (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Nytt kommando:

```azurepowershell
Get-AzHDInsightCluster
```

**Visa kluster**

Gammalt kommando (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Nytt kommando:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Andra exempel
* [Skapa HDInsight-kluster](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Skicka Apache Hive jobb](hadoop/apache-hadoop-use-hive-powershell.md)
* [Skicka Apache Sqoop-jobb](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrera till nya HDInsight .NET SDK
Azure Service Management-baserade [(ASM) HDInsight .NET SDK](/previous-versions/azure/reference/mt416619(v=azure.100)) är nu föråldrad. Du uppmanas att använda Azure Resource Management-baserade [Resource Manager-baserade HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight). Följande ASM-baserade HDInsight-paket är inaktuella.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Det här avsnittet innehåller pekare till mer information om hur du utför vissa uppgifter med hjälp av den Resource Manager-baserade SDK: n.

| Hur... använda Resource Manager-baserad HDInsight SDK | Länkar |
| --- | --- |
| Azure HDInsight SDK för .NET|Se [Azure HDInsight SDK för .net](/dotnet/api/overview/azure/hdinsight) |
| Autentisera program interaktivt med Azure Active Directory med .NET SDK |Se [kör Apache Hive frågor med .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Kodfragmentet i den här artikeln använder metoden för interaktiv autentisering. |
| Autentisera program som inte interaktivt använder Azure Active Directory med .NET SDK |Se [skapa icke-interaktiva program för HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Skicka ett Apache Hive jobb med .NET SDK |Se [skicka Apache Hive jobb](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Skicka ett Apache Sqoop-jobb med .NET SDK |Se [Skicka Apache Sqoop-jobb](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Visa en lista med HDInsight-kluster med .NET SDK |Se [lista HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Skala HDInsight-kluster med hjälp av .NET SDK |Se [skala HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Bevilja/återkalla åtkomst till HDInsight-kluster med hjälp av .NET SDK |Se [bevilja/återkalla åtkomst till HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Uppdatera HTTP-användarautentiseringsuppgifter för HDInsight-kluster med hjälp av .NET SDK |Se [Uppdatera HTTP-användarautentiseringsuppgifter för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Hitta standard lagrings kontot för HDInsight-kluster med hjälp av .NET SDK |Se [hitta standard lagrings kontot för HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Ta bort HDInsight-kluster med .NET SDK |Se [ta bort HDInsight-kluster](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exempel
Här följer några exempel på hur en åtgärd utförs med hjälp av ASM-baserade SDK och motsvarande kodfragment för Resource Manager-baserade SDK.

**Skapa en kluster CRUD-klient**

* Gammalt kommando (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Nytt kommando (tjänstens huvud tillstånd)

  ```azurecli
  //Service principal auth
  //This will log the application in as itself, rather than on behalf of a specific user.
  //For details, including how to set up the application, see:
  //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

* Nytt kommando (användarauktorisering)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Skapa ett kluster**

* Gammalt kommando (ASM)

  ```azurecli
  var clusterInfo = new ClusterCreateParameters
              {
                  Name = dnsName,
                  DefaultStorageAccountKey = key,
                  DefaultStorageContainer = defaultStorageContainer,
                  DefaultStorageAccountName = storageAccountDnsName,
                  ClusterSizeInNodes = 1,
                  ClusterType = type,
                  Location = "West US",
                  UserName = "admin",
                  Password = "*******",
                  Version = version,
                  HeadNodeSize = NodeVMSize.Large,
              };
  clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
  client.CreateCluster(clusterInfo);
  ```

* Nytt kommando

  ```azurecli
  var clusterCreateParameters = new ClusterCreateParameters
      {
          Location = "West US",
          ClusterType = "Hadoop",
          Version = "3.1",
          OSType = OSType.Linux,
          DefaultStorageAccountName = "mystorage.blob.core.windows.net",
          DefaultStorageAccountKey =
              "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
          UserName = "hadoopuser",
          Password = "*******",
          HeadNodeSize = "ExtraLarge",
          RdpUsername = "hdirp",
          RdpPassword = ""*******",
          RdpAccessExpiry = new DateTime(2025, 3, 1),
          ClusterSizeInNodes = 5
      };
  var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
  clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);
  ```

**Aktivera HTTP-åtkomst**

* Gammalt kommando (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Nytt kommando
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Ta bort ett kluster**

* Gammalt kommando (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Nytt kommando

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
