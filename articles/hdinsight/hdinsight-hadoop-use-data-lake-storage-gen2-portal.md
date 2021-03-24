---
title: Skapa Azure HDInsight – Azure Data Lake Storage Gen2-Portal
description: Lär dig hur du använder Azure Data Lake Storage Gen2 med Azure HDInsight-kluster med hjälp av portalen.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866377"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Skapa ett kluster med Data Lake Storage Gen2 med hjälp av Azure Portal

Azure Portal är ett webbaserat hanterings verktyg för tjänster och resurser som finns i Microsoft Azure molnet. I den här artikeln får du lära dig hur du skapar Linux-baserade Azure HDInsight-kluster med hjälp av portalen. Ytterligare information finns i [skapa HDInsight-kluster](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill skapa ett HDInsight-kluster som använder Data Lake Storage Gen2 för lagring, följer du dessa steg för att konfigurera ett lagrings konto som har ett hierarkiskt namn område.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Skapa en användardefinierad hanterad identitet om du inte redan har en.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **skapa en resurs** längst upp till vänster.
1. I rutan Sök skriver du **tilldelade användare** och klickar på **användare tilldelad hanterad identitet**.
1. Klicka på **Skapa**.
1. Ange ett namn för din hanterade identitet, Välj rätt prenumeration, resurs grupp och plats.
1. Klicka på **Skapa**.

Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Skapa en användartilldelad hanterad identitet":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Skapa ett lagrings konto som ska användas med Data Lake Storage Gen2

Skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **skapa en resurs** längst upp till vänster.
1. Skriv **Storage** i sökrutan och klicka på **lagrings konto**.
1. Klicka på **Skapa**.
1. På skärmen **skapa lagrings konto** :
    1. Välj rätt prenumeration och resurs grupp.
    1. Ange ett namn för ditt lagrings konto med Data Lake Storage Gen2.
    1. Klicka på fliken **Avancerat** .
    1. Klicka på **aktive rad** bredvid **hierarkiskt namn område** under **data Lake Storage Gen2**.
    1. Klicka på **Granska + skapa**.
    1. Klicka på **Skapa**

Mer information om andra alternativ när du skapar lagrings konton finns i [snabb start: skapa ett lagrings konto för Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="Skärm bild som visar hur du skapar lagrings konton i Azure Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Konfigurera behörigheter för den hanterade identiteten på Data Lake Storage Gen2

Tilldela den hanterade identiteten till rollen **Storage BLOB data-ägare** på lagrings kontot.

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.
1. Välj ditt lagrings konto och välj sedan **åtkomst kontroll (IAM)** om du vill visa inställningarna för åtkomst kontroll för kontot. Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="Skärm bild som visar inställningar för åtkomst kontroll för lagring":::

1. Lägg till en ny roll genom att klicka på knappen **+ Lägg till roll tilldelning** .
1. I fönstret **Lägg till roll tilldelning** väljer du rollen **Storage BLOB data-ägare** . Välj sedan den prenumeration som har den hanterade identitets-och lagrings kontot. Sedan söker du efter den användarspecifika hanterade identitet som du skapade tidigare. Välj slutligen den hanterade identiteten så visas den under **valda medlemmar**.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Skärm bild som visar hur du tilldelar en Azure-roll":::

1. Välj **Spara**. Den användardefinierade identitet som du har valt visas nu under den valda rollen.
1. När den här inledande installationen är klar kan du skapa ett kluster via portalen. Klustret måste finnas i samma Azure-region som lagrings kontot. På fliken **lagring** på menyn skapa kluster väljer du följande alternativ:

    * För **primär lagrings typ** väljer du **Azure Data Lake Storage Gen2**.
    * Under **primärt lagrings konto** söker du efter och väljer det nyligen skapade lagrings kontot med data Lake Storage Gen2 lagring.

    * Under **identitet** väljer du den nyligen skapade hanterade identiteten som skapats av användaren.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="Lagrings inställningar för att använda Data Lake Storage Gen2 med Azure HDInsight":::

    > [!NOTE]
    > * Om du vill lägga till ett sekundärt lagrings konto med Data Lake Storage Gen2, på lagrings konto nivå, tilldelar du bara den hanterade identitet som skapades tidigare till den nya Data Lake Storage Gen2 som du vill lägga till. Du bör uppmanas att lägga till ett sekundärt lagrings konto med Data Lake Storage Gen2 via bladet "ytterligare lagrings konton" i HDInsight stöds inte.
    > * Du kan aktivera RA-GRS eller RA-ZRS på det Azure Blob Storage-konto som HDInsight använder. Att skapa ett kluster mot den sekundära slut punkten RA-GRS eller RA-ZRS stöds dock inte.

## <a name="delete-the-cluster"></a>Ta bort klustret

Se [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Du har skapat ett HDInsight-kluster. Lär dig nu hur du arbetar med klustret.

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)
* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
