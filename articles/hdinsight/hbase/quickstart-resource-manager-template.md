---
title: 'Snabb start: skapa Apache HBase-kluster med hjälp av mall – Azure HDInsight'
description: Den här snabb starten visar hur du använder Resource Manager-mall för att skapa ett Apache HBase-kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 0d56fa4e1c93cec514076593d229e58b25af142a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865153"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-arm-template"></a>Snabb start: skapa Apache HBase-kluster i Azure HDInsight med ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa ett [Apache HBase](./apache-hbase-overview.md) -kluster i Azure HDInsight. HBase är en NoSQL-databas med öppen källkod som bygger på Apache Hadoop och modelleras efter [Google BigTable](https://cloud.google.com/bigtable/).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft. HDInsight/kluster](/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Klicka på knappen **distribuera till Azure** nedan för att logga in på Azure och öppna arm-mallen.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

1. Ange eller välj följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|I list rutan väljer du den Azure-prenumeration som används för klustret.|
    |Resursgrupp|Välj din befintliga resurs grupp i list rutan eller Välj **Skapa ny**.|
    |Location|Värdet fylls i automatiskt med den plats som används för resurs gruppen.|
    |Klusternamn|Ange ett globalt unikt namn. Använd bara gemena bokstäver och siffror för den här mallen.|
    |Användarnamn för klusterinloggning|Ange användar namnet, standard är **administratör**.|
    |Lösenord för klusterinloggning|Ange ett lösen ord. Lösen ordet måste bestå av minst 10 tecken och måste innehålla minst en siffra, en versal och en gemen bokstav, ett icke-alfanumeriskt tecken (förutom tecknen "" "). |
    |Användar namn för SSH|Ange användar namnet, standard är sshuser|
    |SSH-lösenord|Ange lösen ordet.|

    :::image type="content" source="./media/quickstart-resource-manager-template/resource-manager-template-hbase.png" alt-text="Distribuera Resource Manager-mall HBase" border="true":::

1. Granska de **allmänna** villkoren. Välj sedan **Jag accepterar villkoren som anges ovan** och **köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.


## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett meddelande om att **distributionen har slutförts** med en **gå till resurs** länken. På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) -konto, ett [Azure Data Lake Storage gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)eller ett  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) beroende. Det kallas för standard lagrings kontot. HDInsight-klustret och dess standard lagrings konto måste samplaceras i samma Azure-region. Om du tar bort kluster tas inte lagrings kontot bort.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

Från Azure Portal, navigera till klustret och välj **ta bort**.

[Ta bort Resource Manager-mall HBase](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resurs gruppen tar du bort både HDInsight-klustret och standard lagrings kontot.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Apache HBase-kluster i HDInsight med en ARM-mall. I nästa artikel får du lära dig hur du frågar HBase i HDInsight med HBase Shell.

> [!div class="nextstepaction"]
> [Fråga Apache HBase i Azure HDInsight med HBase Shell](./query-hbase-with-hbase-shell.md)
