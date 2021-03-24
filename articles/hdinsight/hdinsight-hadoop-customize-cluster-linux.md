---
title: Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder
description: Lägg till anpassade komponenter i HDInsight-kluster med hjälp av skript åtgärder. Skript åtgärder är Bash-skript som kan användas för att anpassa kluster konfigurationen. Eller Lägg till ytterligare tjänster och verktyg som nyans, solr eller R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: 0b0fc1062f9e57ab716aa0fa88f90924f0485b08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864881"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder

Azure HDInsight tillhandahåller en konfigurations metod som kallas **skript åtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurations inställningar. Skript åtgärder kan användas under eller efter att klustret har skapats.

Skript åtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program. Mer information om HDInsight-program finns i [publicera ett HDInsight-program på Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Förstå skript åtgärder

En skript åtgärd är Bash-skript som körs på noderna i ett HDInsight-kluster. Egenskaper och funktioner för skript åtgärder är följande:

- Måste lagras på en URI som är tillgänglig från HDInsight-klustret. Följande är möjliga lagrings platser:

  - För vanliga (icke-ESP)-kluster:
    - Data Lake Storage Gen1/Gen2: HDInsight-tjänstens huvud namn använder för att få åtkomst till Data Lake Storage måste ha Läs behörighet till skriptet. URI-formatet för skript som lagras i Data Lake Storage Gen1 är `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` .
    - En BLOB i ett Azure Storage konto som antingen är det primära eller ytterligare lagrings kontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda typerna av lagrings konton när klustret skapas.

    > [!IMPORTANT]  
    > Rotera inte lagrings nyckeln på det här Azure Storage kontot eftersom det leder till efterföljande skript åtgärder med skript som lagras där.

    - En offentlig fildelnings tjänst som är tillgänglig via `http://` sökvägar. Exempel är Azure Blob, GitHub eller OneDrive. Till exempel URI: er, se skript för [skript åtgärder](#example-script-action-scripts).
  - För kluster med ESP `wasb://` `wasbs://` `http[s]://` stöds eller. URI: erna stöds.

- Kan begränsas till att endast köras på vissa nodtyper. Exempel är Head-noder eller arbetsnoder.
- Kan vara bestående eller *ad hoc*.

  - Beständiga skript åtgärder måste ha ett unikt namn. Bestående skript används för att anpassa nya arbetsnoder som läggs till i klustret genom skalnings åtgärder. Ett bestående skript kan också tillämpa ändringar av en annan nodtyp när skalnings åtgärder sker. Ett exempel är en head-nod.
  - *Ad hoc* -skript är inte beständiga. Skript åtgärder som används när klustret skapas sparas automatiskt. De används inte för arbetsnoder som läggs till i klustret när skriptet har körts. Sedan kan du befordra ett *ad hoc* -skript till ett beständigt skript eller nedgradera ett beständigt skript till ett *ad hoc* -skript. Skript som inte är beständiga, även om du specifikt anger att de ska vara.

- Kan acceptera parametrar som används av skriptet under körningen.
- Kör med behörigheter på rot nivå på klusternoderna.
- Kan användas via Azure Portal, Azure PowerShell, Azure CLI eller HDInsight .NET SDK.
- Skript åtgärder som tar bort eller ändrar tjänst filer på den virtuella datorn kan påverka tjänstens hälsa och tillgänglighet.

Klustret behåller en historik över alla skript som har körts. Historiken hjälper dig när du behöver hitta ID: t för ett skript för befordran eller degradering av åtgärder.

> [!IMPORTANT]  
> Det finns inget automatiskt sätt att ångra de ändringar som gjorts av en skript åtgärd. Ändra antingen manuellt eller ange ett skript som kastar om dem.

## <a name="permissions"></a>Behörigheter

För ett domänanslutet HDInsight-kluster finns det två Apache Ambari-behörigheter som krävs när du använder skript åtgärder med klustret:

* **AMBARI. KÖR \_ anpassat \_ kommando**. Administratörs rollen Ambari har den här behörigheten som standard.
* **Kluster. KÖR \_ anpassat \_ kommando**. Både HDInsight-kluster administratören och Ambari-administratören har den här behörigheten som standard.

Mer information om hur du arbetar med behörigheter med domänanslutna HDInsight finns i [Hantera HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör eller ägare av din Azure-prenumeration måste ditt konto ha minst `Contributor` åtkomst till resurs gruppen som innehåller HDInsight-klustret.

Någon med minst deltagar åtkomst till Azure-prenumerationen måste ha registrerat providern tidigare. Leverantörs registreringen sker när en användare med deltagar åtkomst till prenumerationen skapar en resurs. För utan att skapa en resurs, se [Registrera en Provider med hjälp av rest](/rest/api/resources/providers#Providers_Register).

Få mer information om hur du arbetar med åtkomst hantering:

- [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
- [Tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Metoder för att använda skript åtgärder

Du kan välja att konfigurera en skript åtgärd att köras när klustret skapas, eller köra det på ett befintligt kluster.

### <a name="script-action-in-the-cluster-creation-process"></a>Skript åtgärd i processen för att skapa kluster

Skript åtgärder som används när klustret skapas skiljer sig något från skript åtgärder som körs på ett befintligt kluster:

- Skriptet sparas automatiskt.
- Ett fel i skriptet kan orsaka att klustrets skapande process Miss lyckas.

Följande diagram illustrerar när skript åtgärder körs under skapande processen:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Steg när klustret skapas" border="false":::

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret. Den körs med rot behörigheter på noderna.

Du kan utföra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster kontrollerar du att Ambari och andra Hadoop-relaterade tjänster körs innan skriptet har slutförts. De här nödvändiga tjänsterna fastställer hälso tillståndet och tillståndet för klustret medan det skapas.

När klustret skapas kan du använda många skript åtgärder samtidigt. Dessa skript anropas i den ordning som de har angetts.

> [!IMPORTANT]  
> Skript åtgärder måste avslutas inom 60 minuter eller tids gränsen uppnåddes. Vid kluster etablering körs skriptet samtidigt med andra installations-och konfigurations processer. Konkurrens för resurser som CPU-tid eller nätverks bandbredd kan orsaka att skriptet tar längre tid att slutföra än i utvecklings miljön.
>
> Undvik hur lång tid det tar att köra skriptet genom att undvika åtgärder som att ladda ned och kompilera program från källan. Förkompilera program och lagra binärfilen i Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skript åtgärd i ett kluster som körs

Ett skript fel på ett kluster som redan körs medför inte automatiskt att klustret ändrar till ett felaktigt tillstånd. När ett skript har slutförts bör klustret återgå till ett kör tillstånd. Även om klustret har ett kör tillstånd kan det felaktiga skriptet ha brutna saker. Ett skript kan till exempel ta bort filer som krävs av klustret.

Skript åtgärder körs med rot privilegier. Se till att du förstår vad ett skript gör innan du tillämpar det på klustret.

När du tillämpar ett skript på ett kluster ändras kluster statusen från att **köras** till **godkänd**. Sedan ändras den till **HDInsight-konfigurationen** och slutligen tillbaka till att **köras** för lyckade skript. Skript status loggas i historiken för skript åtgärden. Den här informationen visar om skriptet lyckades eller misslyckades. `Get-AzHDInsightScriptActionHistory`PowerShell-cmdleten visar till exempel status för ett skript. Den returnerar information som liknar följande text:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Om du ändrar kluster användaren, administratören, lösen ordet efter att klustret har skapats kan skript åtgärder som körs mot det här klustret Miss förväntas. Om du har bestående skript åtgärder som riktar sig mot arbetsnoder kan dessa skript Miss Miss förväntat när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempel skript åtgärds skript

Skript åtgärds skript kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight innehåller skript för att installera följande komponenter i HDInsight-kluster:

| Name | Skript |
| --- | --- |
| Lägg till ett Azure Storage konto |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Se [lägga till ytterligare lagrings konton i HDInsight](hdinsight-hadoop-add-storage.md). |
| Installera nyans |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Se [Installera och använda nyans på HDInsight Hadoop-kluster](hdinsight-hadoop-hue-linux.md). |
| Förhandsladda Hive-bibliotek |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Se [Lägg till anpassade Apache Hive-bibliotek när du skapar ett HDInsight-kluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Skript åtgärd när klustret skapas

I det här avsnittet beskrivs de olika sätten att använda skript åtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använd en skript åtgärd när du skapar kluster från Azure Portal

1. Börja skapa ett kluster enligt beskrivningen i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). På fliken **konfiguration och priser** väljer du **+ Lägg till skript åtgärd**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Åtgärd för Azure Portal kluster skript":::

1. Använd posten __Välj en skript__ för att välja ett förtillverkade skript. Välj __anpassad__ om du vill använda ett anpassat skript. Ange sedan __namnet__ och __bash skript-URI__ för skriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Lägg till ett skript i formuläret Välj skript":::

   I följande tabell beskrivs elementen i formuläret:

   | Egenskap | Värde |
   | --- | --- |
   | Välj ett skript | Välj __anpassad__ om du vill använda ett eget skript. Annars väljer du något av de angivna skripten. |
   | Name |Ange ett namn för skript åtgärden. |
   | Bash-skript-URI |Ange URI: n för skriptet. |
   | Head/Worker/ZooKeeper |Ange noderna som skriptet körs på: **Head**, **Work** eller **ZooKeeper**. |
   | Parametrar |Ange parametrarna, om det krävs av skriptet. |

   Använd posten __Behåll den här skript åtgärden__ för att kontrol lera att skriptet används vid skalnings åtgärder.

1. Välj __skapa__ för att spara skriptet. Sedan kan du använda __+ Skicka ny__ för att lägga till ett annat skript.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="Åtgärder för flera skript i HDInsight":::

   När du är klar med att lägga till skript, återgår du till fliken **konfiguration + prissättning** .

1. Slutför de återstående stegen för att skapa kluster som vanligt.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skript åtgärd från Azure Resource Manager mallar

Skript åtgärder kan användas med Azure Resource Manager mallar. Ett exempel finns i [skapa HDInsight Linux-kluster och köra en skript åtgärd](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet läggs skript åtgärden till med hjälp av följande kod:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Få mer information om hur du distribuerar en mall:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använd en skript åtgärd när klustret skapas från Azure PowerShell

I det här avsnittet använder du cmdleten [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) för att anropa skript för att anpassa ett kluster. Innan du börjar ska du kontrol lera att du installerar och konfigurerar Azure PowerShell. Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](/powershell/azure/).

Följande skript visar hur du använder en skript åtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret skapas.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använd en skript åtgärd när du skapar kluster från HDInsight .NET SDK

HDInsight .NET SDK innehåller klient bibliotek som gör det enklare att arbeta med HDInsight från ett .NET-program. Ett kod exempel finns i [skript åtgärder](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Skript åtgärd för ett kluster som körs

I det här avsnittet beskrivs hur du tillämpar skript åtgärder på ett kluster som körs.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och leta upp klustret.

1. I standardvyn väljer du **skript åtgärder** under **Inställningar**.

1. Överst på sidan **skript åtgärder** väljer du **+ Skicka ny**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Lägga till ett skript i ett kluster som körs":::

1. Använd posten __Välj en skript__ för att välja ett förtillverkade skript. Välj __anpassad__ om du vill använda ett anpassat skript. Ange sedan __namnet__ och __bash skript-URI__ för skriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Lägg till ett skript i formuläret Välj skript":::

   I följande tabell beskrivs elementen i formuläret:

   | Egenskap | Värde |
   | --- | --- |
   | Välj ett skript | Välj __anpassad__ om du vill använda ett eget skript. Annars väljer du ett tillhandahållet skript. |
   | Name |Ange ett namn för skript åtgärden. |
   | Bash-skript-URI |Ange URI: n för skriptet. |
   | Head/Worker/Zookeeper |Ange noderna som skriptet körs på: **Head**, **Work** eller **ZooKeeper**. |
   | Parametrar |Ange parametrarna, om det krävs av skriptet. |

   Använd posten __Behåll den här skript åtgärden__ för att kontrol lera att skriptet används vid skalnings åtgärder.

1. Slutligen väljer du knappen **skapa** för att tillämpa skriptet på klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure PowerShell

Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](/powershell/azure/). I följande exempel visas hur du använder en skript åtgärd för ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har slutförts visas information som liknar följande text:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Tillämpa en skript åtgärd på ett kluster som körs från Azure CLI

Innan du börjar ska du kontrol lera att du installerar och konfigurerar Azure CLI. Se till att du har den senaste versionen. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. Autentisera till din Azure-prenumeration:

   ```azurecli
   az login
   ```

1. Tillämpa en skript åtgärd på ett kluster som körs:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Giltiga roller är `headnode` , `workernode` , `zookeepernode` , `edgenode` . Om skriptet ska tillämpas på flera nodtyper avgränsar du rollerna med ett blank steg. Till exempel `--roles headnode workernode`.

   Lägg till för att spara skriptet `--persist-on-success` . Du kan också Spara skriptet senare med hjälp av `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Tillämpa en skript åtgärd på ett kluster som körs med hjälp av REST API

Se [kluster REST API i Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Tillämpa en skript åtgärd på ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att tillämpa skript i ett kluster finns i [tillämpa en skript åtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visa historik och befordran och degradering av skript åtgärder

### <a name="the-azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och leta upp klustret.

1. I standardvyn väljer du **skript åtgärder** under **Inställningar**.

1. En historik över skript för det här klustret visas i avsnittet skript åtgärder. Den här informationen innehåller en lista över bestående skript. Följande skärm bild visar att solr-skriptet har körts på det här klustret. Skärm bilden visar inte några sparade skript.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Portal skript åtgärder skicka historik":::

1. Välj ett skript från historiken för att visa avsnittet **Egenskaper** för det här skriptet. Överst på skärmen kan du köra skriptet igen eller befordra det.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Egenskaper för skript åtgärder befordran":::

1. Du kan också välja ellipsen, **...**, till höger om poster i avsnittet skript åtgärder för att utföra åtgärder.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Borttagning av beständiga skript åtgärder":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Hämta information om beständiga skript åtgärder. Den här cmdleten återställer inte de åtgärder som utförs av ett skript, utan tar bara bort den bestående flaggan.|
| `Get-AzHDInsightScriptActionHistory` |Hämta en historik över skript åtgärder som tillämpas på klustret eller information för ett speciellt skript. |
| `Set-AzHDInsightPersistedScriptAction` |Höj en `ad hoc` skript åtgärd till en sparad skript åtgärd. |
| `Remove-AzHDInsightPersistedScriptAction` |Nedgradera en bestående skript åtgärd till en `ad hoc` åtgärd. |

Följande exempel skript visar hur du använder cmdlet: ar för att befordra och sedan nedgradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Kommando | Beskrivning |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |Tar bort en angiven Sparad skript åtgärd i klustret. Det här kommandot återställer inte de åtgärder som utförs av ett skript. det tar bara bort den bestående flaggan.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|Kör skript åtgärder på det angivna HDInsight-klustret.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |Visar alla beständiga skript åtgärder för det angivna klustret. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|Visar alla skript körnings historik för det angivna klustret.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|Höjer den angivna ad hoc-skript körningen till ett beständigt skript.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|Hämtar information om skript körning för angivet ID för skript körning.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skript historik från ett kluster, befordra eller nedgradera skript finns i [ tillämpa en skript åtgärd mot ett Linux-baserat HDInsight-kluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript åtgärds skript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Lägg till ytterligare lagrings utrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)
* [Felsöka skriptåtgärder](troubleshoot-script-action.md)
