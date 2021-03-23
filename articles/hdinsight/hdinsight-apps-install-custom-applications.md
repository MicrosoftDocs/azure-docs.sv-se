---
title: Installera dina anpassade Apache Hadoop-program i Azure HDInsight
description: Lär dig hur du installerar HDInsight-program för Apache Hadoop kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 19dd5bf94b524ff3eb6eb601c77b503a0040bd75
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867652"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Installera anpassade Apache Hadoop-program i Azure HDInsight

I den här artikeln får du lära dig hur du installerar ett [Apache Hadoop](https://hadoop.apache.org/) -program på Azure HDInsight, som inte har publicerats till Azure Portal. Det program som du installerar i den här artikeln är [nyans](https://gethue.com/).

An-HDInsight program är ett program som användare kan installera i ett HDInsight-kluster.  Dessa program kan utvecklas av Microsoft, oberoende programvaruleverantörer och av dig själv.  

## <a name="prerequisites"></a>Förutsättningar

Om du vill installera HDInsight-program i ett befintligt HDInsight-kluster måste du ha ett HDInsight-kluster. Om du vill skapa ett läser du [Skapa kluster](hadoop/apache-hadoop-linux-tutorial-get-started.md). Du kan även installera HDInsight-program när du skapar ett HDInsight-kluster.

## <a name="install-hdinsight-applications"></a>Installera HDInsight-program

HDInsight-program kan installeras när du skapar ett kluster eller i ett befintligt HDInsight-kluster. Information om hur du definierar Azure Resource Manager-mallar finns i [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application).

Filer som behövs för att distribuera programmet (Hue):

* [azuredeploy.JSON](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): Resource Manager-mallen för att installera programmet HDInsight. Information om hur du utvecklar en egen Resource Manager-mall finns i [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application).
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Den skriptåtgärd som anropas av Resource Manager-mallen för konfiguration av kantnoden.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hue-binärfilen som anropas från hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hue-binärfilen som anropas från hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): En exempelwebbapp (Tomcat) som anropas från hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Installera Hue i ett befintligt HDInsight-kluster

1. Välj följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager-mallen finns på [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) .  Information om hur du skriver Resource Manager-mallen finns i [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application).

1. Välj den befintliga **resurs grupp** som innehåller ditt kluster i list rutan. Du måste använda samma resurs grupp som klustret.

1. Ange namnet på det kluster där du vill installera programmet. Det här klustret måste vara ett befintligt kluster.

1. Markera kryss rutan för **Jag accepterar de villkor som anges ovan**.

1. Välj **Köp**.

Du kan se installationsstatus via ikonen som är fäst på portalens instrumentpanel och i portalmeddelandet (klicka på klockikonen överst i portalen).  Det tar cirka 10 minuter att installera programmet.

### <a name="to-install-hue-while-creating-a-cluster"></a>Installera Hue när du skapar ett kluster

1. Välj följande bild för att logga in på Azure och öppna Resource Manager-mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager-mallen finns på [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) .  Information om hur du skriver Resource Manager-mallen finns i [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application).

2. Följ anvisningarna för att skapa kluster och installera Hue. Mer information om hur du skapar HDInsight-kluster finns i [Skapa Linux-baserade Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Andra installationsmetoder

Utöver Azure Portal kan du också använda [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) och [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) för att anropa Resource Manager-mallar.

## <a name="validate-the-installation"></a>Verifiera installationen

Du kan validera programinstallationen genom att kontrollera statusen för programmet på Azure Portal. Dessutom kan du också verifiera att alla HTTP-slutpunkter visas som förväntat och på webb sidan om det finns en sådan.

För **nyans** kan du använda följande steg:

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj det kluster där du installerade programmet.
1. Välj **program** på menyn **Inställningar** .
1. Välj **nyans** i listan för att visa egenskaperna.  
1. Välj webb sidans länk för att verifiera webbplatsen.

### <a name="azure-cli"></a>Azure CLI

Ersätt `CLUSTERNAME` och `RESOURCEGROUP` med relevanta värden och ange sedan följande kommandon:

* Visar alla program för HDInsight-klustret.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* För att hämta egenskaperna för det angivna programmet.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Felsöka installationen

Du kan kontrollera programmets installationsstatus i portalmeddelandet (klicka på klockikonen överst i portalen).

Om en programinstallation Miss lyckas kan du se fel meddelandena och felsöknings information från tre platser:

* HDInsight-program: allmän felinformation.

    Öppna klustret från portalen och välj program från inställningar:

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png" alt-text="hdinsight-program programinstallationsfel":::

* HDInsight-skriptåtgärder: Om HDInsight-programmets felmeddelande anger att en skriptåtgärd inte kunde utföras hittar du mer information om skriptfelet i skriptåtgärdsfönstret.

    Välj skript åtgärd från inställningar. I historiken för skriptåtgärder visas felmeddelanden

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png" alt-text="hdinsight-program skriptåtgärdsfel":::

* Apache Ambari Web UI: om installations skriptet är orsaken till felet använder du Ambari Web UI för att kontrol lera fullständiga loggar om installations skripten.

    Mer information finns i [Felsöka skript åtgärder](./troubleshoot-script-action.md).

## <a name="remove-hdinsight-applications"></a>Ta bort HDInsight-program

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj det kluster där du installerade programmet.
1. Välj **program** på menyn **Inställningar** .
1. Högerklicka på det program som du vill ta bort och välj sedan **ta bort**.
1. Bekräfta genom att välja **Ja**.

### <a name="azure-cli"></a>Azure CLI

Ersätt `NAME` , `CLUSTERNAME` och `RESOURCEGROUP` med relevanta värden, och ange sedan kommandot nedan:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Nästa steg

* [MSDN: Installera ett HDInsight-program](/rest/api/hdinsight/hdinsight-application): Läs mer om hur du utvecklar Resource Manager-mallar för distribution av HDInsight-program.
* [Installera HDInsight-program](hdinsight-apps-install-applications.md): Läs mer om hur du installerar ett HDInsight-program till dina kluster.
* [Publicera HDInsight-program](hdinsight-apps-publish-applications.md): information om hur du publicerar anpassade HDInsight-program på Azure Marketplace.
* [Anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md): information om hur du använder skriptåtgärd till att installera fler program.
* [Skapa Linux-baserade Apache Hadoop kluster i HDInsight med Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lär dig hur du anropar Resource Manager-mallar för att skapa HDInsight-kluster.
* [Använda tomma edge-noder i HDInsight](hdinsight-apps-use-edge-node.md): Information om hur du använder en tom edge-nod för att få åtkomst till HDInsight-kluster, testa HDInsight-program och vara värd för HDInsight-program.
