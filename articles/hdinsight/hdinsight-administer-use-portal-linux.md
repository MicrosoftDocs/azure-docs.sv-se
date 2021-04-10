---
title: Hantera Apache Hadoop kluster i HDInsight med Azure Portal
description: Lär dig hur du skapar och hanterar Azure HDInsight-kluster med hjälp av Azure Portal.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/24/2020
ms.openlocfilehash: 33171527f03b0cc386f683f55f6cf9a70913abdb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871222"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Hantera Apache Hadoop kluster i HDInsight med hjälp av Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Med hjälp av [Azure Portal](https://portal.azure.com)kan du hantera [Apache Hadoop](https://hadoop.apache.org/) kluster i Azure HDInsight. Använd tabbväljaren ovan för information om hur du hanterar Hadoop-kluster i HDInsight med andra verktyg.

## <a name="prerequisites"></a>Förutsättningar

Ett befintligt Apache Hadoop-kluster i HDInsight.  Se [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Komma igång

Logga in på [https://portal.azure.com](https://portal.azure.com).

## <a name="list-and-show-clusters"></a><a name="showClusters"></a> Visa och Visa kluster

Sidan **HDInsight-kluster** visar dina befintliga kluster.  Från portalen:
1. Välj **alla tjänster** på den vänstra menyn.
2. Välj **HDInsight-kluster** under **Analytics**.

## <a name="cluster-home-page"></a><a name="homePage"></a> Kluster start sida

Välj ditt kluster namn på sidan [**HDInsight-kluster**](#showClusters) .  Vyn **Översikt** öppnas, som ser ut ungefär som på följande bild:

:::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png" alt-text="Azure Portal HDInsight-kluster Essentials":::

**Översta menyn:**  

| Objekt| Beskrivning |
|---|---|
|Flytta|Flyttar klustret till en annan resurs grupp eller till en annan prenumeration.|
|Ta bort|Tar bort klustret. |
|Uppdatera|Uppdaterar vyn.|

**Vänster meny:**  

  - **Övre vänstra menyn**

    | Objekt| Beskrivning |
    |---|---|
    |Översikt|Tillhandahåller allmän information om klustret.|
    |Aktivitetslogg|Visa och fråga aktivitets loggar.|
    |Åtkomstkontroll (IAM)|Använd roll tilldelningar.  Se [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../role-based-access-control/role-assignments-portal.md).|
    |Taggar|Gör att du kan ange nyckel/värde-par för att definiera en anpassad taxonomi för moln tjänsterna. Du kan till exempel skapa en nyckel med namnet **Project** och sedan använda ett vanligt värde för alla tjänster som är associerade med ett särskilt projekt.|
    |Diagnostisera och lösa problem|Visa felsöknings information.|
    |Snabbstart|Visar information som hjälper dig att komma igång med HDInsight.|
    |Verktyg|Hjälp information för HDInsight-relaterade verktyg.|

  - **Menyn Inställningar**  

    | Objekt| Beskrivning |
    |---|---|
    |Kluster storlek|Kontrol lera, öka och minska antalet kluster arbets noder. Se [skala kluster](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kvot gränser|Visa de använda och tillgängliga kärnorna för din prenumeration.|
    |SSH + kluster inloggning|Visar anvisningarna för att ansluta till klustret med hjälp av SSH-anslutning (Secure Shell). Mer information finns i [använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Data Lake Storage Gen1|Konfigurera åtkomst Data Lake Storage Gen1.  Se [snabb start: Konfigurera kluster i HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).|
    |Lagringskonton|Visa lagrings kontona och nycklarna. Lagrings kontona konfigureras när klustret skapas.|
    |Program|Lägg till/ta bort HDInsight-program.  Se [installera anpassade HDInsight-program](hdinsight-apps-install-custom-applications.md).|
    |Skript åtgärder|Kör bash-skript i klustret. Se [Anpassa Linux-baserade HDInsight-kluster med skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).|
    |Extern metastores|Visa [Apache Hive](https://hive.apache.org/) -och [Apache Oozie](https://oozie.apache.org/) -metastores. Metastores kan bara konfigureras när klustret skapas.|
    |HDInsight-partner|Lägg till/ta bort aktuell HDInsight-partner.|
    |Egenskaper|Visa [kluster egenskaperna](#properties).|
    |Lås|Lägg till ett lås för att förhindra att klustret ändras eller tas bort.|
    |Exportera mall|Visa och exportera Azure Resource Manager mall för klustret. För närvarande kan du bara exportera det beroende Azure Storage-kontot. Se [skapa Linux-baserade Apache Hadoop kluster i HDInsight med Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Menyn övervakning**

    | Objekt| Beskrivning |
    |---|---|
    |Aviseringar|Hantera aviseringar och åtgärder.|
    |Mått|Övervaka kluster måtten i Azure Monitor loggar.|
    |Diagnos inställningar|Inställningar för var du vill lagra diagnos måtten.|
    |Azure Monitor|Övervaka klustret i Azure Monitor.|

  - **Support och fel söknings menyn**

    | Objekt| Beskrivning |
    |---|---|
    |Resurshälsa|Se [Översikt över Azure Resource Health](../service-health/resource-health-overview.md).|
    |Ny supportbegäran|Gör att du kan skapa ett support ärende med Microsoft support.|

## <a name="cluster-properties"></a><a name="properties"></a> Kluster egenskaper

På [Start sidan för klustret](#homePage)under **Inställningar** väljer du **Egenskaper**.

|Objekt | Beskrivning |
|---|---|
|VÄRDNAMN|Kluster namn.|
|KLUSTER-URL|URL: en för Ambari-webbgränssnittet.|
|Privat slutpunkt|Klustrets privata slut punkt.|
|Secure Shell (SSH)|Användar namn och värdnamn som ska användas vid åtkomst till klustret via SSH.|
|STATUS|En av: avbruten, accepterad, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, drift, körning, fel, borttagning, borttagning, stängningsåtgärd, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued eller ClusterCustomization.|
|REGION|Azure-plats. En lista över Azure-platser som stöds finns i list rutan **region** på [HDInsight-prissättning](https://azure.microsoft.com/pricing/details/hdinsight/).|
|SKAPAD DEN|Det datum då klustret distribuerades.|
|OPERATIV SYSTEM|Antingen **Windows** eller **Linux**.|
|TYP|Hadoop, HBase, Storm, Spark.|
|Version|Se [HDInsight-versioner](hdinsight-component-versioning.md).|
|Lägsta TLS-version|TLS-versionen.|
|PRENUMERATION|Prenumerations namn.|
|STANDARD DATA KÄLLA|Standard kluster fil systemet.|
|Storlekar på arbetsnoder|Vald VM-storlek för arbetsnoderna.|
|Huvudnodens storlek|Den valda virtuella dator storleken för huvudnoderna.|
|Virtuellt nätverk|Namnet på Virtual Network som klustret har distribuerats till, om ett sådant valdes vid distributions tiden.|

## <a name="move-clusters"></a>Flytta kluster

Du kan flytta ett HDInsight-kluster till en annan Azure-resurs grupp eller en annan prenumeration.

Från [klustrets start sida](#homePage):

1. Välj **Flytta** på den översta menyn.
2. Välj **Flytta till en annan resurs grupp** eller **Flytta till en annan prenumeration**.
3. Följ anvisningarna på den nya sidan.

## <a name="delete-clusters"></a>Ta bort kluster

Om du tar bort ett kluster raderas inte standard lagrings kontot eller länkade lagrings konton. Du kan återskapa klustret med samma lagrings konton och samma metastores. Vi rekommenderar att du använder en ny standard-BLOB-behållare när du skapar klustret igen.

Från [klustrets start sida](#homePage):

1. Välj **ta bort** på den översta menyn.
2. Följ anvisningarna på den nya sidan.

Se även [pausa/Stäng ner kluster](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Du kan lägga till ytterligare Azure Storage konton och Azure Data Lake Storage konton när ett kluster har skapats. Mer information finns i [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="scale-clusters"></a>Skala kluster

Med funktionen för kluster skalning kan du ändra antalet arbetsnoder som används av ett Azure HDInsight-kluster utan att behöva skapa klustret på nytt.

Se [skala HDInsight-kluster](./hdinsight-scaling-best-practices.md) för fullständig information.

## <a name="pauseshut-down-clusters"></a>Pausa/Stäng ner kluster

De flesta Hadoop-jobb är batch-jobb som bara körs ibland. För de flesta Hadoop-kluster finns det stora tids perioder då klustret inte används för bearbetning. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används.
Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

Processen kan programmeras på många sätt:

- Användar Azure Data Factory. Se [skapa Linux-baserade Apache Hadoop-kluster på begäran i HDInsight med hjälp av Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) för att skapa HDInsight-länkade tjänster på begäran.
- Använd Azure PowerShell.  Se [analysera flyg fördröjnings data](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Använd Azures CLI. Se [Hantera Azure HDInsight-kluster med Azure CLI](hdinsight-administer-use-command-line.md).
- Använd HDInsight .NET SDK. Se [skicka Apache Hadoop jobb](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Pris information finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Information om hur du tar bort ett kluster från portalen finns i [ta bort kluster](#delete-clusters)

## <a name="upgrade-clusters"></a>Uppgradera kluster

Se [Uppgradera HDInsight-kluster till en nyare version](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Öppna Apache Ambari Web UI

Ambari tillhandahåller ett intuitivt, lättanvänt Hadoop Management-webbgränssnitt som backas upp av dess RESTful-API: er. Ambari gör det möjligt för system administratörer att hantera och övervaka Hadoop-kluster.

Från [klustrets start sida](#homePage):

1. Välj **kluster instrument paneler**.

    :::image type="content" source="./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png" alt-text="HDInsight Apache Hadoop kluster meny":::

1. Välj **Ambari start** från den nya sidan.
1. Ange användar namn och lösen ord för klustret.  Standard namnet för kluster är _admin_.

Mer information finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Ändra lösen ord

An-HDInsight kluster kan ha två användar konton. HDInsight-klustrets användar konto (HTTP-användarkonto) och SSH-användarkontot skapas när du skapar processen. Du kan använda portalen för att ändra lösen ordet för klustrets användar konto och skript åtgärder för att ändra SSH-användarkontot.

### <a name="change-the-cluster-user-password"></a>Ändra lösen ordet för kluster användaren

> [!NOTE]  
> Att ändra lösen ordet för kluster användare (admin) kan orsaka att skript åtgärder körs mot klustret för att Miss lyckas. Om du har några sparade skript åtgärder som riktar sig mot arbetsnoder kan dessa skript Miss lyckas när du lägger till noder i klustret genom att ändra storlek. Mer information om skript åtgärder finns i [Anpassa HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Från [klustrets start sida](#homePage):
1. Välj **SSH + kluster inloggning** under **Inställningar**.
2. Välj **Återställ autentiseringsuppgifter**.
3. Ange och bekräfta det nya lösen ordet i text rutorna.
4. Välj **OK**.

Lösen ordet har ändrats på alla noder i klustret.

### <a name="change-the-ssh-user-password-or-public-key"></a>Ändra lösen ord för SSH-användare eller offentlig nyckel

1. Använd en text redigerare och spara följande text som en fil med namnet **changecredentials.sh**.

    > [!IMPORTANT]  
    > Du måste använda ett redigerings program som använder LF som linje slut. Om redigeraren använder CRLF fungerar inte skriptet.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Överför filen till en lagrings plats som kan nås från HDInsight med hjälp av en HTTP-eller HTTPS-adress. Till exempel ett offentligt fil arkiv som OneDrive eller Azure Blob Storage. Spara URI (HTTP eller HTTPS-adress) till filen, eftersom denna URI behövs i nästa steg.
3. Från [klustrets start sida](#homePage)väljer du **skript åtgärder** under **Inställningar**.
4. På sidan **skript åtgärder** väljer du **Skicka ny**.
5. På sidan **Skicka skript åtgärd** anger du följande information:

> [!NOTE]
> SSH-lösenord får inte innehålla följande tecken:
> ```
> " ' ` / \ < % ~ | $ & ! 
> ```

   | Fält | Värde |
   | --- | --- |
   | Skript typ | Välj **anpassad** i list rutan.|
   | Name |"Ändra SSH-autentiseringsuppgifter" |
   | Bash-skript-URI |URI: n till changecredentials.sh-filen |
   | Nodtyp (er): (Head, Worker, Nimbus, chef eller Zookeeper.) |✓ för alla nodtyper i listan |
   | Parametrar |Ange SSH-användarnamnet och sedan det nya lösen ordet. Det måste finnas ett blank steg mellan användar namnet och lösen ordet. |
   | Spara den här skript åtgärden... |Lämna fältet omarkerat. |

6. Välj **skapa** för att tillämpa skriptet. När skriptet är klart kan du ansluta till klustret med hjälp av SSH med de nya autentiseringsuppgifterna.

## <a name="find-the-subscription-id"></a>Hitta prenumerations-ID: t

Varje kluster är knutet till en Azure-prenumeration.  ID för Azure-prenumerationen visas på [Start sidan för klustret](#homePage).

## <a name="find-the-resource-group"></a>Hitta resurs gruppen

I Azure Resource Managers läge skapas varje HDInsight-kluster med en Azure Resource Manager-grupp. Resource Manager-gruppen är synlig från [klustrets start sida](#homePage).

## <a name="find-the-storage-accounts"></a>Hitta lagrings kontona

HDInsight-kluster använder antingen ett Azure Storage konto eller Azure Data Lake Storage för att lagra data. Varje HDInsight-kluster kan ha ett standard lagrings konto och ett antal länkade lagrings konton. Om du vill visa lagrings kontona går du till [klustrets start sida](#homePage) under **Inställningar** och väljer **lagrings konton**.

## <a name="monitor-jobs"></a>Övervaka jobb

Se [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Kluster storlek

På panelen **kluster storlek** från [klustrets start sida](#homePage) visas antalet kärnor som allokerats till det här klustret och hur de allokeras för noderna i klustret.

> [!IMPORTANT]  
> Om du vill övervaka de tjänster som tillhandahålls av HDInsight-klustret måste du använda Ambari Web eller Ambari REST API. Mer information om hur du använder Ambari finns i [Hantera HDInsight-kluster med Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Anslut till ett kluster

- [Använda Apache Hive med HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig några grundläggande administrativa funktioner. Mer information finns i följande artiklar:

- [Administrera HDInsight med Azure PowerShell](hdinsight-administer-use-powershell.md)
- [Administrera HDInsight med Azure CLI](hdinsight-administer-use-command-line.md)
- [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md)
- [Information om hur du använder Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Använda Apache Hive i HDInsight](hadoop/hdinsight-use-hive.md)
- [Använda Apache Sqoop i HDInsight](hadoop/hdinsight-use-sqoop.md)
- [Använda python-användardefinierade funktioner (UDF) med Apache Hive och Apache-gris i HDInsight](hadoop/python-udf-hdinsight.md)
- [Vilken version av Apache Hadoop finns i Azure HDInsight?](hdinsight-component-versioning.md)