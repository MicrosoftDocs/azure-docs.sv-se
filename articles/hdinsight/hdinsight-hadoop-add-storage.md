---
title: Lägg till ytterligare Azure Storage konton i HDInsight
description: Lär dig hur du lägger till ytterligare Azure Storage konton i ett befintligt HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 32757fef131c5e443350f032c0ac987d7f491396
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864320"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Lägg till ytterligare lagrings konton i HDInsight

Lär dig hur du använder skript åtgärder för att lägga till ytterligare Azure Storage *konton* i HDInsight. Stegen i det här dokumentet lägger till ett lagrings *konto* i ett befintligt HDInsight-kluster. Den här artikeln gäller lagrings *konton* (inte standard klustrets lagrings konto) och inte ytterligare lagrings utrymme, till exempel [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) och [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) .

> [!IMPORTANT]  
> Informationen i det här dokumentet är att lägga till ytterligare lagrings konton i ett kluster när det har skapats. Information om hur du lägger till lagrings konton när du skapar kluster finns i [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Lagrings kontots namn och nyckel. Se [Hantera åtkomst nycklar för lagrings konton](../storage/common/storage-account-keys-manage.md).
* Om du använder PowerShell behöver du AZ-modulen.  Se [Översikt över Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Så här fungerar det

Under bearbetningen utför skriptet följande åtgärder:

* Om lagrings kontot redan finns i core-site.xml konfigurationen för klustret, avslutas skriptet och inga ytterligare åtgärder utförs.

* Kontrollerar att lagrings kontot finns och kan nås med hjälp av nyckeln.

* Krypterar nyckeln med hjälp av kluster autentiseringsuppgiften.

* Lägger till lagrings kontot i core-site.xmls filen.

* Stoppar och startar om Apache Oozie, Apache Hadoop garn, Apache Hadoop MapReduce2 och Apache Hadoop HDFS-tjänster. Genom att stoppa och starta dessa tjänster kan de använda det nya lagrings kontot.

> [!WARNING]  
> Det finns inte stöd för att använda ett lagrings konto på en annan plats än HDInsight-klustret.

## <a name="add-storage-account"></a>Lägg till lagringskonto

Använd [skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) för att tillämpa ändringarna med följande överväganden:

|Egenskap | Värde |
|---|---|
|Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Node-typ (er)|Head|
|Parametrar|`ACCOUNTNAME``ACCOUNTKEY` `-p` (valfritt)|

* `ACCOUNTNAME` är namnet på det lagrings konto som ska läggas till i HDInsight-klustret.
* `ACCOUNTKEY` är åtkomst nyckeln för `ACCOUNTNAME` .
* `-p` är valfritt. Om det här alternativet har angetts krypteras nyckeln inte och lagras i core-site.xml-filen som oformaterad text.

## <a name="verification"></a>Verifiering

När du visar HDInsight-klustret i Azure Portal, visas inte lagrings konton som lagts till genom den här skript åtgärden om du väljer posten __lagrings konton__ under __Egenskaper__ . Azure PowerShell och Azure CLI visar inte det ytterligare lagrings kontot antingen. Lagrings informationen visas inte eftersom skriptet bara ändrar `core-site.xml` konfigurationen för klustret. Den här informationen används inte när du hämtar kluster informationen med hjälp av API: er för Azure Management.

Om du vill kontrol lera ytterligare lagrings utrymme använder du en av de metoder som visas nedan:

### <a name="powershell"></a>PowerShell

Skriptet kommer att returnera de lagrings konto namn som är associerade med det aktuella klustret. Ersätt `CLUSTERNAME` med det faktiska kluster namnet och kör skriptet.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Navigera till **HDFS**  >  **configs**  >  **Advanced**  >  **anpassad Core-site**.

1. Observera de nycklar som börjar med `fs.azure.account.key` . Konto namnet kommer att ingå i den nyckel som visas i den här exempel bilden:

   :::image type="content" source="./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png" alt-text="verifiering med Apache Ambari":::

## <a name="remove-storage-account"></a>Ta bort lagrings konto

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net` , där `CLUSTERNAME` är namnet på klustret.

1. Navigera till **HDFS**  >  **configs**  >  **Advanced**  >  **anpassad Core-site**.

1. Ta bort följande nycklar:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

När du har tagit bort nycklarna och sparat konfigurationen måste du starta om Oozie, garn, MapReduce2, HDFS och Hive en i taget.

## <a name="known-issues"></a>Kända problem

### <a name="storage-firewall"></a>Lagrings brand vägg

Om du väljer att skydda ditt lagrings konto med **brand väggar och begränsningar för virtuella nätverk** på **valda nätverk** måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt ditt lagrings konto`.`

### <a name="unable-to-access-storage-after-changing-key"></a>Det gick inte att komma åt lagring efter ändring av nyckel

Om du ändrar nyckeln för ett lagrings konto kan HDInsight inte längre komma åt lagrings kontot. HDInsight använder en cachelagrad kopia av nyckeln i core-site.xml för klustret. Den cachelagrade kopian måste uppdateras för att matcha den nya nyckeln.

Om du kör skript åtgärden igen uppdateras **inte** nyckeln, eftersom skriptet kontrollerar om det redan finns en post för lagrings kontot. Om det redan finns en post görs inga ändringar.

Så här löser du det här problemet:  
1. Ta bort lagrings kontot.
1. Lägg till lagrings kontot.

> [!IMPORTANT]  
> Det går inte att rotera lagrings nyckeln för det primära lagrings kontot som är kopplat till ett kluster.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du lägger till ytterligare lagrings konton i ett befintligt HDInsight-kluster. Mer information om skript åtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md)