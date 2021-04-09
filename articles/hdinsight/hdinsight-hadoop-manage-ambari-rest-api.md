---
title: Övervaka och hantera Hadoop med Ambari REST API – Azure HDInsight
description: Lär dig hur du använder Ambari för att övervaka och hantera Hadoop-kluster i Azure HDInsight. I det här dokumentet får du lära dig hur du använder Ambari-REST API som ingår i HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1d4e6f0d6a0242cda919364965a61e4314927d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945582"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Hantera HDInsight-kluster med hjälp av Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Lär dig hur du använder Apache Ambari-REST API för att hantera och övervaka Apache Hadoop kluster i Azure HDInsight.

## <a name="what-is-apache-ambari"></a>Vad är Apache Ambari

Apache Ambari fören klar hanteringen och övervakningen av Hadoop-kluster genom att tillhandahålla ett lättanvänt webb gränssnitt som stöds av dess [REST-API: er](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari tillhandahålls som standard med Linux-baserade HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash på Ubuntu i Windows 10.  I exemplen i den här artikeln används bash-gränssnittet i Windows 10. Installations [Guide för Windows-undersystem för Linux finns i Windows 10](/windows/wsl/install-win10) för installations steg.  Andra [UNIX-gränssnitt](https://www.gnu.org/software/bash/) fungerar också.  Exempel, med vissa små ändringar, kan fungera i en kommando tolk i Windows.  Du kan också använda Windows PowerShell.

* JQ, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Windows PowerShell.  Eller så kan du använda bash.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Bas Uniform Resource Identifier för REST-API för Ambari

 Bas Uniform Resource Identifier (URI) för Ambari REST API på HDInsight är `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , där `CLUSTERNAME` är namnet på klustret.  Kluster namn i URI: er är Skift läges **känsliga**.  Kluster namnet i det fullständigt kvalificerade domän namnet (FQDN) i URI: n () är Skift läges okänsligt `CLUSTERNAME.azurehdinsight.net` , men andra förekomster i URI: n är Skift läges känsliga.

## <a name="authentication"></a>Autentisering

För att ansluta till Ambari i HDInsight krävs HTTPS. Använd administratörs konto namnet (standard är **administratör**) och lösen ord som du angav när du skapade klustret.

För Enterprise Security Package kluster, i stället för `admin` , använder du ett fullständigt kvalificerat användar namn som `username@domain.onmicrosoft.com` .

## <a name="examples"></a>Exempel

### <a name="setup-preserve-credentials"></a>Installations program (bevara autentiseringsuppgifter)

Behåll dina autentiseringsuppgifter för att undvika att behöva ange dem igen för varje exempel.  Kluster namnet kommer att bevaras i ett separat steg.

**A. bash**  
Redigera skriptet nedan genom att ersätta `PASSWORD` med det faktiska lösen ordet.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt bokstäver-kluster namn

Det faktiska Skift läget i kluster namnet kan skilja sig från förväntat.  Stegen här visar det faktiska Skift läget och lagrar det sedan i en variabel för alla senare exempel.

Redigera skripten nedan och Ersätt `CLUSTERNAME` med ditt kluster namn. Ange sedan kommandot. (Kluster namnet för FQDN är inte Skift läges känsligt.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Parsar JSON-data

I följande exempel används [JQ](https://stedolan.github.io/jq/) eller [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) för att parsa JSON Response-dokumentet och bara visa `health_report` informationen från resultaten.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>Hämta FQDN för klusternoder

Du kan behöva känna till det fullständigt kvalificerade domän namnet (FQDN) för en klusternod. Du kan enkelt hämta FQDN för de olika noderna i klustret med hjälp av följande exempel:

**Alla noder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Huvudnoder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Arbetsnoder**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper-noder**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Hämta den interna IP-adressen för klusternoder

De IP-adresser som returneras av exemplen i det här avsnittet är inte direkt tillgängliga via Internet. De är bara tillgängliga i Azure-Virtual Network som innehåller HDInsight-klustret.

Mer information om hur du arbetar med HDInsight och virtuella nätverk finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

Du måste känna till det interna fullständigt kvalificerade domän namnet (FQDN) för klusternoderna för att hitta IP-adressen. När du har FQDN kan du hämta IP-adressen för värden. I följande exempel frågar Ambari efter FQDN för alla noder på värden. Frågar sedan Ambari efter IP-adressen för varje värd.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Hämta standard lagringen

HDInsight-kluster måste använda ett Azure Storage konto eller Data Lake Storage som standard lagring. Du kan använda Ambari för att hämta den här informationen när klustret har skapats. Om du till exempel vill läsa/skriva data till behållaren utanför HDInsight.

I följande exempel hämtas standard lagrings konfigurationen från klustret:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Dessa exempel returnerar den första konfigurationen som tillämpas på servern ( `service_config_version=1` ) som innehåller den här informationen. Om du hämtar ett värde som har ändrats efter att klustret har skapats kan du behöva lista konfigurations versionerna och hämta det senaste.

Returvärdet liknar något av följande exempel:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Det här värdet anger att klustret använder ett Azure Storage konto för standard lagring. `ACCOUNTNAME`Värdet är namnet på lagrings kontot. `CONTAINER`Delen är namnet på BLOB-behållaren i lagrings kontot. Behållaren är roten till den HDFS-kompatibla lagringen för klustret.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Det här värdet anger att klustret använder Azure Data Lake Storage Gen2 för standard lagring. `ACCOUNTNAME`Värdena och `CONTAINER` har samma betydelser som för Azure Storage tidigare nämnts.

* `adl://home` – Det här värdet anger att klustret använder Azure Data Lake Storage Gen1 för standard lagring.

    Använd följande exempel för att hitta Data Lake Storage konto namn:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Det returnerade värdet liknar `ACCOUNTNAME.azuredatalakestore.net` , där `ACCOUNTNAME` är namnet på det data Lake Storage kontot.

    Använd följande exempel för att hitta katalogen i Data Lake Storage som innehåller lagringen för klustret:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Returvärdet liknar `/clusters/CLUSTERNAME/` . Det här värdet är en sökväg i Data Lake Storage-kontot. Den här sökvägen är roten i det HDFS-kompatibla fil systemet för klustret.  

> [!NOTE]  
> Cmdleten [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) som tillhandahålls av [Azure PowerShell](/powershell/azure/) även returnera lagrings informationen för klustret.

### <a name="get-all-configurations"></a>Hämta alla konfigurationer

Hämta de konfigurationer som är tillgängliga för klustret.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen för installerade komponenter. Se *taggens* värde. Följande exempel är ett utdrag från data som returneras från en Spark-kluster typ.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Hämta konfiguration för en enskild komponent

Hämta konfigurationen för den komponent som du är intresse rad av. I följande exempel ersätter `INITIAL` du med det tagg värde som returnerades från föregående begäran.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Det här exemplet returnerar ett JSON-dokument som innehåller den aktuella konfigurationen för `livy2-conf` komponenten.

### <a name="update-configuration"></a>Uppdatera konfiguration

1. Skapa `newconfig.json` .  
   Ändra och ange sedan följande kommandon:

   * Ersätt `livy2-conf` med den nya komponenten.
   * Ersätt `INITIAL` med det faktiska värdet som hämtades för `tag` från [Hämta alla konfigurationer](#get-all-configurations).

     **A. bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShell-skriptet använder [JQ](https://stedolan.github.io/jq/).  Redigera `C:\HD\jq\jq-win64` nedan om du vill visa din faktiska sökväg och version av [JQ](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ används för att omvandla data som hämtats från HDInsight till en ny konfigurations mall. Mer specifikt gör de här exemplen följande åtgärder:

   * Skapar ett unikt värde som innehåller strängen "version" och datumet, som lagras i `newtag` .

   * Skapar ett rot dokument för den nya konfigurationen.

   * Hämtar innehållet i `.items[]` matrisen och lägger till det under **desired_config** -elementet.

   * Tar bort `href` `version` `Config` elementen, och, eftersom dessa element inte behövs för att skicka en ny konfiguration.

   * Lägger till ett- `tag` element med värdet `version#################` . Den numeriska delen baseras på det aktuella datumet. Varje konfiguration måste ha en unik tagg.

     Slutligen sparas data i `newconfig.json` dokumentet. Dokument strukturen bör se ut ungefär som i följande exempel:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Redigera `newconfig.json` .  
   Öppna `newconfig.json` dokumentet och ändra/Lägg till värden i `properties` objektet. I följande exempel ändras värdet `"livy.server.csrf_protection.enabled"` från `"true"` till `"false"` .

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Spara filen när du är klar med ändringarna.

3. Skicka `newconfig.json` .  
   Använd följande kommandon för att skicka den uppdaterade konfigurationen till Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    De här kommandona skickar innehållet i **newconfig.jspå** -filen till klustret som den nya konfigurationen. Begäran returnerar ett JSON-dokument. **VersionTag** -elementet i det här dokumentet måste matcha den version du skickade, och objektet **configs** innehåller de konfigurations ändringar som du har begärt.

### <a name="restart-a-service-component"></a>Starta om en tjänst komponent

I det här läget anger Ambari-webbgränssnittet Spark-tjänsten måste startas om innan den nya konfigurationen kan börja gälla. Använd följande steg för att starta om tjänsten.

1. Använd följande för att aktivera underhålls läget för Spark2-tjänsten:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Verifiera underhålls läge  

    De här kommandona skickar ett JSON-dokument till servern som aktiverar underhålls läge. Du kan kontrol lera att tjänsten nu är i underhålls läge med följande begäran:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    Returvärdet är `ON` .

3. Använd sedan följande för att stänga av Spark2-tjänsten:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Svaret liknar följande exempel:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > `href`Värdet som returneras av denna URI använder den interna IP-adressen för klusternoden. Om du vill använda den utanför klustret, ersätter du `10.0.0.18:8080` delen med FQDN för klustret.  

4. Verifiera begäran.  
    Redigera kommandot nedan genom att ersätta `29` med det faktiska värdet som `id` returnerades från föregående steg.  Följande kommandon hämtar status för begäran:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Ett svar på `COMPLETED` indikerar att begäran har avslut ATS.

5. När den tidigare begäran har slutförts använder du följande för att starta Spark2-tjänsten.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    Tjänsten använder nu den nya konfigurationen.

6. Använd slutligen följande för att inaktivera underhålls läget.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Nästa steg

En fullständig referens till REST API finns i [Apache AMBARI API Reference v1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Se även [auktorisera användare för Apache Ambari views](./hdinsight-authorize-users-to-ambari.md)