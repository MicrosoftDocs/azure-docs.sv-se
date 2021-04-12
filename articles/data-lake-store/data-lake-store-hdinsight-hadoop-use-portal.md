---
title: Skapa Azure HDInsight-kluster med Data Lake Storage Gen1-Portal
description: Använd Azure Portal för att skapa och använda HDInsight-kluster med Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96340262"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Skapa HDInsight-kluster med Azure Data Lake Storage Gen1 med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Använd Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Använd PowerShell (för standard lagring)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Använd PowerShell (för ytterligare lagrings utrymme)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Använd Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Lär dig hur du använder Azure Portal för att skapa ett HDInsight-kluster med Azure Data Lake Storage Gen1 som standard lagring eller ytterligare lagrings utrymme. Även om ytterligare lagring är valfritt för ett HDInsight-kluster rekommenderar vi att du lagrar dina affärs data i de ytterligare lagrings kontona.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrol lera att du uppfyller följande krav:

* **En Azure-prenumeration**. Gå till [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Azure Data Lake Storage gen1 konto**. Följ anvisningarna från [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Du måste också skapa en rotmapp på kontot.  I den här artikeln används en rotmapp som heter __/Clusters__ .
* **Ett Azure Active Directory tjänstens huvud namn**. Den här instruktions guiden innehåller instruktioner om hur du skapar ett huvud namn för tjänsten i Azure Active Directory (Azure AD). Om du vill skapa ett huvud namn för tjänsten måste du dock vara en Azure AD-administratör. Om du är administratör kan du hoppa över den här förutsättningen och fortsätta.

>[!NOTE]
>Du kan bara skapa ett huvud namn för tjänsten om du är en Azure AD-administratör. Din Azure AD-administratör måste skapa ett huvud namn för tjänsten innan du kan skapa ett HDInsight-kluster med Data Lake Storage Gen1. Dessutom måste tjänstens huvud namn skapas med ett certifikat, enligt beskrivningen i [skapa ett huvud namn för tjänsten med certifikatet](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Skapa ett HDInsight-kluster

I det här avsnittet skapar du ett HDInsight-kluster med Data Lake Storage Gen1 som standard eller ytterligare lagrings utrymme. Den här artikeln fokuserar bara på en del av att konfigurera Data Lake Storage Gen1. Information och procedurer för allmän kluster skapande finns i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som standard lagring

Så här skapar du ett HDInsight-kluster med ett Data Lake Storage Gen1 som standard lagrings konto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Data Lake Storage gen1** under **primär lagrings typ** på bladet **lagring** och ange följande information:

    ![Inställningar för HDInsight-lagrings konto](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Välj Data Lake Store konto**: Välj ett befintligt data Lake Storage gen1-konto. Ett befintligt Data Lake Storage Gen1-konto krävs.  Se [krav](#prerequisites).
    * **Rot Sök väg**: Ange en sökväg där de klusterbaserade filerna ska lagras. På skärm bilden är det __/Clusters/myhdiadlcluster/__, där mappen __/Clusters__ måste finnas, och portalen skapar *myhdicluster* -mappen.  *Myhdicluster* är kluster namnet.
    * **Data Lake Store åtkomst**: konfigurera åtkomst mellan data Lake Storage gen1 konto och HDInsight-kluster. Instruktioner finns i [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).
    * **Ytterligare lagrings konton**: Lägg till Azure Storage-konton som ytterligare lagrings konton för klustret. Om du vill lägga till ytterligare Data Lake Storage Gen1 konton gör du det genom att ge kluster behörigheterna för data i fler Data Lake Storage Gen1-konton när du konfigurerar ett Data Lake Storage Gen1-konto som primär lagrings typ. Se [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).

4. Klicka på **Välj** på **data Lake Store åtkomst** och fortsätt sedan med att skapa kluster genom att följa anvisningarna i [skapa Hadoop-kluster i HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Skapa ett kluster med Data Lake Storage Gen1 som ytterligare lagrings utrymme

Följande instruktioner skapar ett HDInsight-kluster med ett Azure Blob Storage-konto som standard lagring och ett lagrings konto med Data Lake Storage Gen1 som ytterligare lagrings utrymme.

Så här skapar du ett HDInsight-kluster med Data Lake Storage Gen1 som ett ytterligare lagrings konto:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Följ [skapa kluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) för allmän information om hur du skapar HDInsight-kluster.
3. Välj **Azure Storage** under **primär lagrings typ** på bladet **lagring** och ange följande information:

    ![HDInsight-lagrings konto inställningar ytterligare lagring](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Urvals metod** – om du vill ange ett lagrings konto som ingår i din Azure-prenumeration väljer du **Mina prenumerationer** och väljer sedan lagrings kontot. Om du vill ange ett lagrings konto som ligger utanför din Azure-prenumeration väljer du **åtkomst nyckel** och anger sedan informationen för det externa lagrings kontot.

    * **Standard behållare** – Använd antingen standardvärdet eller ange ett eget namn.
    * **Ytterligare lagrings konton** – Lägg till fler Azure Storage-konton som ytterligare lagrings utrymme.
    * **Data Lake Store åtkomst** – konfigurera åtkomst mellan data Lake Storage gen1 konto och HDInsight-kluster. Anvisningar finns i [konfigurera data Lake Storage gen1 åtkomst](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurera Data Lake Storage Gen1 åtkomst

I det här avsnittet konfigurerar du Data Lake Storage Gen1 åtkomst från HDInsight-kluster med hjälp av ett Azure Active Directory tjänstens huvud namn.

### <a name="specify-a-service-principal"></a>Ange ett huvud namn för tjänsten

Från Azure Portal kan du antingen använda ett befintligt huvud namn för tjänsten eller skapa ett nytt.

Så här skapar du ett huvud namn för tjänsten från Azure Portal:
1. Se [skapa tjänstens huvud namn och certifikat](../active-directory/develop/howto-create-service-principal-portal.md) med hjälp av Azure Active Directory.

Så här använder du ett befintligt huvud namn för tjänsten från Azure Portal:

1. Tjänstens huvud namn ska ha ägar behörigheter för lagrings kontot. Se [Konfigurera behörigheter för tjänstens huvud namn som ägare på lagrings kontot](#configure-serviceprincipal-permissions).
1. Välj **data Lake Store åtkomst**.
1. På bladet **data Lake Storage gen1 åtkomst** väljer du **Använd befintlig**.
1. Välj **tjänstens huvud namn** och välj sedan ett huvud namn för tjänsten.
1. Ladda upp certifikatet (. pfx-fil) som är associerat med ditt valda huvud namn för tjänsten och ange sedan lösen ordet för certifikatet.

    ![Lägg till tjänstens huvud namn i HDInsight-kluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Välj **åtkomst** för att konfigurera åtkomst till mappen.  Se [Konfigurera fil behörigheter](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Ställ in behörigheter för tjänstens huvud namn som ägare på lagrings kontot
1. På bladet Access Control (IAM) i lagrings kontot klickar du på Lägg till en roll tilldelning. 
2. På bladet Lägg till en roll tilldelning väljer du roll som ägare och väljer SPN och klickar på Spara.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurera fil behörigheter

Konfigurationen skiljer sig åt beroende på om kontot används som standard lagring eller ytterligare ett lagrings konto:

* Används som standard lagring

  * behörighet på rot nivån för det Data Lake Storage Gen1 kontot
  * behörighet på rot nivån för kluster lagringen i HDInsight. Till exempel mappen __/Clusters__ som användes tidigare i självstudien.

* Använd som ytterligare lagrings utrymme

  * Behörighet för de mappar där du behöver fil åtkomst.

För att tilldela behörighet till lagrings kontot med Data Lake Storage Gen1 på rotnivå:

1. Välj **åtkomst** på bladet **data Lake Storage gen1 åtkomst** . Bladet **Välj fil behörigheter** öppnas. Den visar alla lagrings konton i din prenumeration.
1. Hovra (Klicka inte på) musen över namnet på kontot med Data Lake Storage Gen1 för att kryss rutan ska synas. Markera sedan kryss rutan.

    ![Välj fil behörigheter](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Som standard har __Läs__-, __Skriv__-och __körnings__ behörighet marker ATS.

1. Klicka på **Välj** längst ned på sidan.
1. Välj **Kör** för att tilldela behörighet.
1. Välj **Klar**.

Så här tilldelar du behörighet på HDInsight-klustrets rot nivå:

1. Välj **åtkomst** på bladet **data Lake Storage gen1 åtkomst** . Bladet **Välj fil behörigheter** öppnas. Den visar alla lagrings konton med Data Lake Storage Gen1 i din prenumeration.
1. Från bladet **Välj fil behörigheter** väljer du lagrings kontot med data Lake Storage gen1 namn för att visa dess innehåll.
1. Välj HDInsight-klustrets lagrings rot genom att markera kryss rutan till vänster i mappen. Enligt skärm bilden tidigare är kluster lagrings roten __/Clusters__ -mapp som du angav när du valde data Lake Storage gen1 som standard lagring.
1. Ange behörigheter för mappen.  Som standard har Läs-, skriv-och körnings behörighet marker ATS.
1. Klicka på **Välj** längst ned på sidan.
1. Välj **Kör**.
1. Välj **Klar**.

Om du använder Data Lake Storage Gen1 som ytterligare lagrings utrymme måste du tilldela behörighet för de mappar som du vill komma åt från HDInsight-klustret. I skärm bilden nedan ger du till exempel endast åtkomst till mappen **mynewfolder** i ett lagrings konto med data Lake Storage gen1.

![Tilldela tjänstens huvud namn behörigheter till HDInsight-klustret](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verifiera kluster konfigurationen

När kluster installationen är klar, går du till kluster bladet och kontrollerar resultatet genom att göra något av eller båda följande steg:

* För att kontrol lera att den associerade lagringen för klustret är kontot med Data Lake Storage Gen1 som du har angett väljer du **lagrings konton** i det vänstra fönstret.

    ![Verifiera tillhör ande lagring](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Kontrol lera att tjänstens huvud namn är korrekt associerat med HDInsight-klustret genom att välja **data Lake Storage gen1 åtkomst** i det vänstra fönstret.

    ![Verifiera tjänstens huvud namn](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Exempel

När du har konfigurerat klustret med Data Lake Storage Gen1 som lagrings plats, se följande exempel på hur du använder HDInsight-kluster för att analysera data som lagras i Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Köra en Hive-fråga mot data i en Data Lake Storage Gen1 (som primär lagring)

Om du vill köra en Hive-fråga använder du gränssnittet Hive views i Ambari-portalen. Instruktioner för hur du använder Ambari Hive-vyer finns i [använda Hive-vyn med Hadoop i HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

När du arbetar med data i en Data Lake Storage Gen1, finns det några strängar att ändra.

Om du exempelvis använder det kluster som du skapade med Data Lake Storage Gen1 som primär lagring, är sökvägen till data: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/File*. En Hive-fråga för att skapa en tabell från exempel data som lagras i Data Lake Storage Gen1 ser ut som i följande instruktion:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Förklaringar

* `adl://hdiadlsg1storage.azuredatalakestore.net/` är roten för kontot med Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` är roten för de kluster data som du angav när du skapade klustret.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` är platsen för den exempel fil som du använde i frågan.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Köra en Hive-fråga mot data i en Data Lake Storage Gen1 (som ytterligare lagrings utrymme)

Om klustret som du skapade använder Blob Storage som standard lagring, finns inte exempel data i lagrings kontot med Data Lake Storage Gen1 som används som ytterligare lagrings utrymme. I sådana fall måste du först överföra data från Blob Storage till lagrings kontot med Data Lake Storage Gen1 och sedan köra frågorna som visas i föregående exempel.

Information om hur du kopierar data från Blob Storage till ett lagrings konto med Data Lake Storage Gen1 finns i följande artiklar:

* [Använd Distcp för att kopiera data mellan Azure Blob Storage och Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Använd AdlCopy för att kopiera data från Azure Blob Storage till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Använda Data Lake Storage Gen1 med ett Spark-kluster

Du kan använda ett Spark-kluster för att köra Spark-jobb på data som lagras i en Data Lake Storage Gen1. Mer information finns i [använda HDInsight Spark-kluster för att analysera data i data Lake Storage gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Använda Data Lake Storage Gen1 i en Storm-topologi

Du kan använda lagrings kontot med Data Lake Storage Gen1 för att skriva data från en Storm-topologi. Instruktioner för hur du uppnår det här scenariot finns i [använda Azure Data Lake Storage gen1 med Apache Storm med HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Se även

* [Använda Data Lake Storage Gen1 med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx