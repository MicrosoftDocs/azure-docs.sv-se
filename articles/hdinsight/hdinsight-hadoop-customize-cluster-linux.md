---
title: Anpassa Azure HDInsight med hjälp av skriptåtgärder
description: Lägg till anpassade komponenter i HDInsight-kluster med hjälp av skriptåtgärder. Skriptåtgärder är Bash-skript som kan användas för att anpassa klusterkonfigurationen. Eller lägg till ytterligare tjänster och verktyg som Hue, Solr eller R.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: d5500c04b4299c215eba843530dc84932fa10894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775051"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Anpassa Azure HDInsight med hjälp av skriptåtgärder

Azure HDInsight en konfigurationsmetod som **kallas skriptåtgärder** som anropar anpassade skript för att anpassa klustret. Dessa skript används för att installera ytterligare komponenter och ändra konfigurationsinställningarna. Skriptåtgärder kan användas under eller efter att klustret har skapats.

Skriptåtgärder kan också publiceras till Azure Marketplace ett HDInsight-program. Mer information om HDInsight-program finns i [Publicera ett HDInsight-program i Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="understand-script-actions"></a>Förstå skriptåtgärder

En skriptåtgärd är Ett Bash-skript som körs på noderna i ett HDInsight-kluster. Egenskaper och funktioner för skriptåtgärder är följande:

- Bash-skriptets URI (platsen för att komma åt filen) måste vara tillgänglig från HDInsight-resursprovidern och klustret.
- Följande är möjliga lagringsplatser:

   - För vanliga (icke-ESP)-kluster:
     - En blob i ett Azure Storage som antingen är det primära eller ytterligare lagringskontot för HDInsight-klustret. HDInsight beviljas åtkomst till båda dessa typer av lagringskonton när klustret skapas.
    
       > [!IMPORTANT]  
       > Rotera inte lagringsnyckeln på det här Azure Storage konto eftersom det leder till att efterföljande skriptåtgärder med skript som lagras där misslyckas.

     - Data Lake Storage Gen1: HDInsight-tjänstens huvudnamn använder för Data Lake Storage måste ha läsbehörighet till skriptet. URI-formatet för Bash-skript är `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` . 

     - Data Lake Storage Gen2 rekommenderas inte att använda för skriptåtgärder. `abfs://` stöds inte för Bash-skriptets URI. `https://` URI:er är möjliga, men de fungerar för containrar som har offentlig åtkomst och brandväggen är öppen för HDInsight-resursprovidern och rekommenderas därför inte.

     - En offentlig fildelningstjänst som är tillgänglig via `https://` sökvägar. Exempel är Azure Blob, GitHub eller OneDrive. Exempel på URI:er finns [i Exempelskript för åtgärdsskript.](#example-script-action-scripts)

  - För kluster med ESP stöds `wasb://` URI:erna `wasbs://` `http[s]://` eller .

- Skriptåtgärderna kan begränsas till att endast köras på vissa nodtyper. Exempel är huvudnoder eller arbetsnoder.
- Skriptåtgärderna kan bevaras eller *ad hoc.*

  - Ständiga skriptåtgärder måste ha ett unikt namn. Bestående skript används för att anpassa nya arbetsnoder som läggs till i klustret via skalningsåtgärder. Ett bestående skript kan också tillämpa ändringar på en annan nodtyp när skalningsåtgärder utförs. Ett exempel är en huvudnod.
  - *Ad hoc-skript* bevaras inte. Skriptåtgärder som används när klustret skapas sparas automatiskt. De tillämpas inte på arbetsnoder som läggs till i klustret när skriptet har körts. Sedan kan du höja upp *ett ad hoc-skript* till ett bestående skript eller degradera ett bestående skript till ett *ad hoc-skript.* Skript som misslyckas bevaras inte, även om du specifikt anger att de ska vara det.

- Skriptåtgärder kan acceptera parametrar som används av skriptet under körningen.
- Skriptåtgärder körs med rotnivåbehörigheter på klusternoderna.
- Skriptåtgärder kan användas via Azure Portal, Azure PowerShell, Azure CLI eller HDInsight .NET SDK.
- Skriptåtgärder som tar bort eller ändrar tjänstfiler på den virtuella datorn kan påverka tjänstens hälsa och tillgänglighet.

Klustret sparar en historik över alla skript som har körts. Historiken hjälper dig när du behöver hitta ID:t för ett skript för uppflyttnings- eller degraderingsåtgärder.

> [!IMPORTANT]  
> Det finns inget automatiskt sätt att ångra ändringar som gjorts av en skriptåtgärd. Antingen ångra ändringarna manuellt eller ange ett skript som gör dem omvända.

## <a name="permissions"></a>Behörigheter

För ett domän-ansluten HDInsight-kluster finns det två Apache Ambari-behörigheter som krävs när du använder skriptåtgärder med klustret:

* **AMBARI. KÖR \_ DET ANPASSADE \_ KOMMANDOT**. Rollen Ambari-administratör har den här behörigheten som standard.
* **KLUSTER. KÖR \_ DET ANPASSADE \_ KOMMANDOT**. Både HDInsight-klusteradministratören och Ambari-administratören har den här behörigheten som standard.

Mer information om hur du arbetar med behörigheter med domän-ansluten HDInsight finns [i Hantera HDInsight-kluster med Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Åtkomstkontroll

Om du inte är administratör eller ägare till din Azure-prenumeration måste ditt konto minst ha åtkomst till resursgruppen som `Contributor` innehåller HDInsight-klustret.

Någon med minst deltagaråtkomst till Azure-prenumerationen måste ha registrerat providern tidigare. Providerregistrering sker när en användare med deltagaråtkomst till prenumerationen skapar en resurs. För utan att skapa en resurs, se [registrera en provider med hjälp av REST](/rest/api/resources/providers#Providers_Register).

Få mer information om hur du arbetar med åtkomsthantering:

- [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
- [Tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>Metoder för att använda skriptåtgärder

Du har möjlighet att konfigurera en skriptåtgärd som ska köras när klustret först skapas, eller att köra den på ett befintligt kluster.

### <a name="script-action-in-the-cluster-creation-process"></a>Skriptåtgärd i processen för att skapa kluster

Skriptåtgärder som används vid skapande av kluster skiljer sig något från skriptåtgärder som körs på ett befintligt kluster:

- Skriptet sparas automatiskt.
- Ett fel i skriptet kan orsaka att processen för att skapa klustret misslyckas.

Följande diagram illustrerar när skriptåtgärden körs under skapandeprocessen:


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="Faser när klustret skapas" border="false":::

Skriptet körs medan HDInsight konfigureras. Skriptet körs parallellt på alla angivna noder i klustret. Den körs med rotprivilegier på noderna.

Du kan göra åtgärder som att stoppa och starta tjänster, inklusive Apache Hadoop-relaterade tjänster. Om du stoppar tjänster ser du till att Ambari och andra Hadoop-relaterade tjänster körs innan skriptet slutförs. Dessa nödvändiga tjänster avgör hälsotillståndet och tillståndet för klustret när det skapas.

När klustret skapas kan du använda många skriptåtgärder samtidigt. Skripten anropas i den ordning som de har angetts.

> [!IMPORTANT]  
> Skriptåtgärder måste slutföras inom 60 minuter, annars tar det för lång tid. Under klusteretablering körs skriptet samtidigt med andra konfigurations- och konfigurationsprocesser. Konkurrens om resurser som CPU-tid eller nätverksbandbredd kan göra att skriptet tar längre tid att slutföra än i utvecklingsmiljön.
>
> Undvik uppgifter som att ladda ned och kompilera program från källan för att minimera den tid det tar att köra skriptet. Förkompilera program och lagra binärfilen i Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Skriptåtgärd i ett kluster som körs

Ett skriptfel på ett kluster som redan körs leder inte automatiskt till att klustret ändras till ett misslyckat tillstånd. När ett skript har körts bör klustret återgå till ett körningstillstånd. Även om klustret har ett körningstillstånd kan det misslyckade skriptet ha brutna saker. Ett skript kan till exempel ta bort filer som krävs av klustret.

Skriptåtgärder körs med rotprivilegier. Se till att du förstår vad ett skript gör innan du tillämpar det på klustret.

När du tillämpar ett skript på ett kluster ändras klustertillståndet från **Körs** till **Godkänt.** Sedan ändras den till **HDInsight-konfigurationen** och slutligen tillbaka till **Körs** för lyckade skript. Skriptstatusen loggas i skriptåtgärdshistoriken. Den här informationen anger om skriptet lyckades eller misslyckades. `Get-AzHDInsightScriptActionHistory`PowerShell-cmdleten visar till exempel status för ett skript. Den returnerar information som liknar följande text:

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> Om du ändrar klusteranvändaren, administratören, lösenordet när klustret har skapats kan skriptåtgärder som körs mot det här klustret misslyckas. Om du har några bestående skriptåtgärder som är mål för arbetsnoder kan skripten misslyckas när du skalar klustret.

## <a name="example-script-action-scripts"></a>Exempelskript för skriptåtgärder

Skriptåtgärdsskript kan användas via följande verktyg:

* Azure Portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight tillhandahåller skript för att installera följande komponenter på HDInsight-kluster:

| Name | Skript |
| --- | --- |
| Lägga till ett Azure Storage konto |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Se [Lägga till ytterligare lagringskonton i HDInsight](hdinsight-hadoop-add-storage.md). |
| Installera Hue |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Se [Installera och använda Hue på HDInsight Hadoop-kluster.](hdinsight-hadoop-hue-linux.md) |
| Förinstallera Hive-bibliotek |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Se [Lägga till Apache Hive bibliotek när du skapar ditt HDInsight-kluster.](hdinsight-hadoop-add-hive-libraries.md) |

## <a name="script-action-during-cluster-creation"></a>Skriptåtgärd när klustret skapas

I det här avsnittet beskrivs olika sätt att använda skriptåtgärder när du skapar ett HDInsight-kluster.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Använd en skriptåtgärd när klustret skapas från Azure Portal

1. Börja skapa ett kluster enligt beskrivningen i [Skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). På fliken **Konfiguration + prissättning** väljer du + Lägg till **skriptåtgärd**.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure Portal klusterskriptåtgärd":::

1. Använd posten __Välj ett skript__ för att välja ett fördefinierat skript. Om du vill använda ett anpassat skript väljer du __Anpassad__. Ange sedan __namn- och__ __Bash-skript-URI__ för skriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Lägga till ett skript i det valda skriptformuläret":::

   I följande tabell beskrivs elementen i formuläret:

   | Egenskap | Värde |
   | --- | --- |
   | Välj ett skript | Om du vill använda ditt eget skript väljer du __Anpassad__. Annars väljer du något av de angivna skripten. |
   | Name |Ange ett namn för skriptåtgärden. |
   | URI för Bash-skript |Ange skriptets URI. |
   | Head/Worker/ZooKeeper |Ange de noder där skriptet körs: **Huvud,** **Arbets eller** **ZooKeeper.** |
   | Parametrar |Ange parametrarna, om det krävs av skriptet. |

   Använd posten __Spara den här skriptåtgärden__ för att se till att skriptet tillämpas under skalningsåtgärder.

1. Spara __skriptet genom__ att välja Skapa. Sedan kan du använda __+ Skicka ny för att__ lägga till ytterligare ett skript.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="HdInsight-åtgärder med flera skript":::

   När du är klar med att lägga till skript återgår du till **fliken Konfiguration +** prissättning.

1. Slutför de återstående stegen för att skapa kluster som vanligt.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Använda en skriptåtgärd från Azure Resource Manager mallar

Skriptåtgärder kan användas med Azure Resource Manager mallar. Ett exempel finns i Skapa [HDInsight Linux-kluster och köra en skriptåtgärd](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

I det här exemplet läggs skriptåtgärden till med hjälp av följande kod:

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

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Använd en skriptåtgärd när klustret skapas från Azure PowerShell

I det här avsnittet använder du cmdleten [Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) för att anropa skript för att anpassa ett kluster. Innan du börjar måste du installera och konfigurera Azure PowerShell. Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](/powershell/azure/).

Följande skript visar hur du tillämpar en skriptåtgärd när du skapar ett kluster med hjälp av PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Det kan ta flera minuter innan klustret skapas.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Använda en skriptåtgärd när klustret skapas från HDInsight .NET SDK

HDInsight .NET SDK tillhandahåller klientbibliotek som gör det enklare att arbeta med HDInsight från ett .NET-program. Ett kodexempel finns i [Skriptåtgärder](/dotnet/api/overview/azure/hdinsight#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Skriptåtgärd till ett kluster som körs

I det här avsnittet beskrivs hur du tillämpar skriptåtgärder på ett kluster som körs.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Tillämpa en skriptåtgärd på ett kluster som körs från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och leta upp klustret.

1. I standardvyn går du till **Inställningar** och väljer **Skriptåtgärder**.

1. Längst upp på sidan **Skriptåtgärder** väljer du **+ Skicka ny.**

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="Lägga till ett skript i ett kluster som körs":::

1. Använd posten __Välj ett skript__ för att välja ett fördefinierat skript. Om du vill använda ett anpassat skript väljer du __Anpassad__. Ange sedan __namn- och__ __Bash-skript-URI__ för skriptet.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="Lägga till ett skript i det valda skriptformuläret":::

   I följande tabell beskrivs elementen i formuläret:

   | Egenskap | Värde |
   | --- | --- |
   | Välj ett skript | Om du vill använda ditt eget skript väljer du __anpassat__. Annars väljer du ett skript. |
   | Name |Ange ett namn för skriptåtgärden. |
   | URI för Bash-skript |Ange skriptets URI. |
   | Head/Worker/Zookeeper |Ange de noder där skriptet körs: **Huvud,** **Arbets eller** **ZooKeeper.** |
   | Parametrar |Ange parametrarna, om det krävs av skriptet. |

   Använd posten __Spara den här skriptåtgärden__ för att kontrollera att skriptet tillämpas under skalningsåtgärder.

1. Välj slutligen knappen **Skapa** för att tillämpa skriptet på klustret.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Tillämpa en skriptåtgärd på ett kluster som körs från Azure PowerShell

Om du vill använda dessa PowerShell-kommandon behöver du [AZ-modulen](/powershell/azure/). I följande exempel visas hur du tillämpar en skriptåtgärd på ett kluster som körs:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

När åtgärden har avslutats visas information som liknar följande text:

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Tillämpa en skriptåtgärd på ett kluster som körs från Azure CLI

Innan du börjar måste du installera och konfigurera Azure CLI. Kontrollera att du har den senaste versionen. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

1. Autentisera till din Azure-prenumeration:

   ```azurecli
   az login
   ```

1. Tillämpa en skriptåtgärd på ett kluster som körs:

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   Giltiga roller är `headnode` , `workernode` , , `zookeepernode` `edgenode` . Om skriptet ska tillämpas på flera nodtyper separerar du rollerna med ett blanksteg. Till exempel `--roles headnode workernode`.

   Om du vill spara skriptet lägger du till `--persist-on-success` . Du kan också spara skriptet senare med hjälp av `az hdinsight script-action promote` .

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Tillämpa en skriptåtgärd på ett kluster som körs med hjälp av REST API

Se [Kluster REST API i Azure HDInsight](/rest/api/hdinsight/hdinsight-cluster).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Tillämpa en skriptåtgärd på ett kluster som körs från HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att tillämpa skript på ett kluster finns i Tillämpa en skriptåtgärd mot ett [Linux-baserat HDInsight-kluster som körs.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

## <a name="view-history-and-promote-and-demote-script-actions"></a>Visa historik och höja upp och ned skriptåtgärder

### <a name="the-azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) och leta upp klustret.

1. I standardvyn går du till **Inställningar** och väljer **Skriptåtgärder**.

1. En historik över skript för det här klustret visas i avsnittet skriptåtgärder. Den här informationen innehåller en lista över bestående skript. Följande skärmbild visar att Solr-skriptet har körts på det här klustret. Skärmbilden visar inte några bestående skript.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="Skicka historik för portalskriptåtgärder":::

1. Välj ett skript i historiken för att visa **avsnittet Egenskaper** för det här skriptet. Längst upp på skärmen kan du köra skriptet igen eller höja upp det.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="Höja upp egenskaper för skriptåtgärder":::

1. Du kan också välja ellipsen **...** till höger om posterna i avsnittet skriptåtgärder för att utföra åtgärder.

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="Borttagning av ständiga skriptåtgärder":::

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Funktion |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Hämta information om bestående skriptåtgärder. Den här cmdleten ångrar inte åtgärder som utförts av ett skript, utan tar bara bort den beständiga flaggan.|
| `Get-AzHDInsightScriptActionHistory` |Hämta en historik över skriptåtgärder som tillämpas på klustret eller information för ett specifikt skript. |
| `Set-AzHDInsightPersistedScriptAction` |Befordra en `ad hoc` skriptåtgärd till en bestående skriptåtgärd. |
| `Remove-AzHDInsightPersistedScriptAction` |Degradera en bestående skriptåtgärd till en `ad hoc` åtgärd. |

Följande exempelskript visar hur du använder cmdletarna för att höja upp och sedan degradera ett skript.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Kommando | Beskrivning |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_delete) |Tar bort en angiven bestående skriptåtgärd i klustret. Det här kommandot ångrar inte de åtgärder som utförs av ett skript, utan tar bara bort den beständiga flaggan.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_execute)|Köra skriptåtgärder på det angivna HDInsight-klustret.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list) |Visar en lista över alla ständiga skriptåtgärder för det angivna klustret. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_list_execution_history)|Visar en lista över alla skripts körningshistorik för det angivna klustret.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_promote)|Befordrar den angivna ad hoc-skriptkörningen till ett bestående skript.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az_hdinsight_script_action_show_execution_details)|Hämtar information om skriptkörningen för det angivna skriptkörnings-ID:t.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Ett exempel på hur du använder .NET SDK för att hämta skripthistorik från ett kluster, höja upp eller degradera skript finns i Tillämpa en skriptåtgärd mot ett [Linux-baserat HDInsight-kluster som körs.](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)

> [!NOTE]  
> Det här exemplet visar också hur du installerar ett HDInsight-program med hjälp av .NET SDK.

## <a name="next-steps"></a>Nästa steg

* [Utveckla skript för åtgärdsskript för HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Lägga till ytterligare lagringsutrymme i ett HDInsight-kluster](hdinsight-hadoop-add-storage.md)
* [Felsöka skriptåtgärder](troubleshoot-script-action.md)
