---
title: 'Självstudie: kluster på begäran i Azure HDInsight med Data Factory'
description: Självstudie – lär dig hur du skapar Apache Hadoop-kluster på begäran i HDInsight med hjälp av Azure Data Factory.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: c61ad4d26c4a03889d9ac80332335543ec4140b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868989"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Självstudie: skapa Apache Hadoop kluster på begäran i HDInsight med Azure Data Factory

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här självstudien får du lära dig hur du skapar ett [Apache Hadoop](../hdinsight/hdinsight-overview.md#cluster-types-in-hdinsight) -kluster på begäran i Azure HDInsight med Azure Data Factory. Du använder sedan datapipelines i Azure Data Factory för att köra Hive-jobb och ta bort klustret. I slutet av den här självstudien får du lära dig hur du `operationalize` kör ett stort data jobb där kluster skapande, jobb körning och kluster borttagning görs enligt ett schema.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto
> * Förstå Azure Data Factory-aktivitet
> * Skapa en data fabrik med hjälp av Azure Portal
> * Skapa länkade tjänster
> * Skapa en pipeline
> * Utlösa en pipeline
> * Övervaka en pipeline
> * Verifiera utdata

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* PowerShell- [modulen för AZ](/powershell/azure/) är installerad.

* Ett Azure Active Directory tjänstens huvud namn. När du har skapat tjänstens huvud namn, se till att hämta **program-ID** och **autentiseringsnyckel** med hjälp av anvisningarna i den länkade artikeln. Du behöver dessa värden senare i den här självstudien. Kontrol lera också att tjänstens huvud namn är medlem i rollen *deltagare* i prenumerationen eller resurs gruppen där klustret skapas. Instruktioner för hur du hämtar de nödvändiga värdena och tilldelar rätt roller finns i [skapa ett Azure Active Directory tjänstens huvud namn](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-preliminary-azure-objects"></a>Skapa preliminära Azure-objekt

I det här avsnittet skapar du olika objekt som ska användas för HDInsight-klustret som du skapar på begäran. Det skapade lagrings kontot innehåller exempel HiveQL-skriptet, `partitionweblogs.hql` som du kan använda för att simulera ett exempel Apache Hive jobb som körs i klustret.

I det här avsnittet används ett Azure PowerShell-skript för att skapa lagrings kontot och kopiera över de filer som krävs inom lagrings kontot. Azure PowerShell exempel skriptet i det här avsnittet gör följande:

1. Loggar in på Azure.
2. Skapar en Azure-resursgrupp.
3. Konfigurerar ett Azure Storage-konto.
4. Skapar en BLOB-behållare i lagrings kontot
5. Kopierar exempel HiveQL-skriptet (**skriptfilen partitionweblogs. HQL**) till BLOB-behållaren. Skriptet är tillgängligt på [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql) . Exempel skriptet är redan tillgängligt i en annan offentlig BLOB-behållare. PowerShell-skriptet nedan gör en kopia av filerna i det Azure Storage konto som skapas.

### <a name="create-storage-account-and-copy-files"></a>Skapa lagrings konto och kopiera filer

> [!IMPORTANT]  
> Ange namn för Azure-resurs gruppen och Azure Storage-kontot som ska skapas av skriptet.
> Skriv ned **resurs grupp namn**, **lagrings konto namn** och **lagrings konto nyckel** som anges av skriptet. Du behöver dem i nästa avsnitt.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Verifiera lagrings konto

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster navigerar du till **alla tjänsters**  >  **allmänna**  >  **resurs grupper**.
1. Välj det resurs grupps namn som du skapade i PowerShell-skriptet. Använd filtret om du har för många resurs grupper i listan.
1. I vyn **Översikt** visas en resurs som listas om du inte delar resurs gruppen med andra projekt. Resursen är lagrings kontot med det namn du angav tidigare. Välj lagrings kontots namn.
1. Välj **behållarens** panel.
1. Välj behållaren **adfgetstarted** . Du ser en mapp med namnet **`hivescripts`** .
1. Öppna mappen och kontrol lera att den innehåller exempel skript filen, **skriptfilen partitionweblogs. HQL**.

## <a name="understand-the-azure-data-factory-activity"></a>Förstå Azure Data Factory aktivitet

[Azure Data Factory](../data-factory/introduction.md) dirigerar och automatiserar flytt och transformering av data. Azure Data Factory kan skapa ett HDInsight Hadoop-kluster just-in-Time för att bearbeta en indata-datasektor och ta bort klustret när bearbetningen är klar.

I Azure Data Factory kan en data fabrik ha en eller flera data pipelines. En datapipeline har en eller flera aktiviteter. Det finns två typer av aktiviteter:

* [Data förflyttnings aktiviteter](../data-factory/copy-activity-overview.md). Du använder data förflyttnings aktiviteter för att flytta data från ett käll data lager till ett mål data lager.
* [Data omvandlings aktiviteter](../data-factory/transform-data.md). Du använder data omvandlings aktiviteter för att transformera/bearbeta data. HDInsight Hive-aktiviteten är en av de omvandlings aktiviteter som stöds av Data Factory. Du använder Hive-Transformation-aktiviteten i den här självstudien.

I den här artikeln konfigurerar du Hive-aktiviteten för att skapa ett HDInsight Hadoop-kluster på begäran. Vad händer när aktiviteten körs för att bearbeta data:

1. An-HDInsight Hadoop-kluster skapas automatiskt åt dig just-in-Time för att bearbeta sektorn.

2. Indata bearbetas genom att köra ett HiveQL-skript i klustret. I den här självstudien gör HiveQL-skriptet som är associerat med Hive-aktiviteten följande åtgärder:

    * Använder den befintliga tabellen (*hivesampletable*) för att skapa en annan tabell **HiveSampleOut**.
    * Fyller i **HiveSampleOut** -tabellen med bara vissa kolumner från den ursprungliga *hivesampletable*.

3. HDInsight Hadoop-klustret tas bort när bearbetningen är klar och klustret är inaktivt under den konfigurerade mängden tid (timeToLive-inställning). Om nästa data sektor är tillgänglig för bearbetning med i den här timeToLive inaktiva tiden, används samma kluster för att bearbeta sektorn.  

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. På den vänstra menyn navigerar du till **`+ Create a resource`**  >  **Analytics**  >  **Data Factory**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png" alt-text="Azure Data Factory på portalen":::

3. Ange eller Välj följande värden för panelen **ny data fabrik** :

    |Egenskap  |Värde  |
    |---------|---------|
    |Namn | Ange ett namn för data fabriken. Det här namnet måste vara globalt unikt.|
    |Version | Lämna vid **v2**. |
    |Prenumeration | Välj din Azure-prenumeration. |
    |Resursgrupp | Välj den resurs grupp som du skapade med PowerShell-skriptet. |
    |Location | Platsen anges automatiskt till den plats som du angav när du skapade resurs gruppen tidigare. I den här självstudien anges platsen till **USA, östra**. |
    |Aktivera GIT|Avmarkera den här kryss rutan.|

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png" alt-text="Skapa Azure Data Factory med Azure Portal":::

4. Välj **Skapa**. Att skapa en data fabrik kan ta var som helst mellan 2 och 4 minuter.

5. När data fabriken har skapats får du ett meddelande om att **distributionen har slutförts** med knappen **gå till resurs** .  Öppna Data Factory standardvy genom att välja **gå till resurs** .

6. Välj **författar & övervakare** för att starta Azure Data Factory redigerings-och övervaknings Portal.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png" alt-text="Översikt över Azure Data Factory Portal":::

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet skapar du två länkade tjänster i din data fabrik.

* En **Azure Storage länkad tjänst** som länkar ett Azure Storage-konto till data fabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också det Hive-skript som körs i klustret.
* En **länkad HDInsight-tjänst på begäran**. Azure Data Factory skapar automatiskt ett HDInsight-kluster och kör Hive-skriptet. HDInsight-klustret tas bort när det har varit inaktivt under en förinställd tid.

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. Välj ikonen **författare** i den vänstra rutan på sidan **Kom igång** .

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png" alt-text="Skapa en Azure Data Factory länkad tjänst":::

2. Välj **anslutningar** i det nedre vänstra hörnet i fönstret och välj sedan **+ ny**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png" alt-text="Skapa anslutningar i Azure Data Factory":::

3. I dialog rutan **ny länkad tjänst** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png" alt-text="Skapa Azure Storage länkad tjänst för Data Factory":::

4. Ange följande värden för den länkade lagrings tjänsten:

    |Egenskap |Värde |
    |---|---|
    |Namn |Ange `HDIStorageLinkedService`.|
    |Azure-prenumeration |Välj din prenumeration i den nedrullningsbara listan.|
    |Lagringskontonamn |Välj det Azure Storage konto som du skapade som en del av PowerShell-skriptet.|

    Välj **test anslutning** och om det lyckas väljer du **skapa**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png" alt-text="Ange namn för Azure Storage länkad tjänst":::

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Välj knappen **+Ny** igen för att skapa ytterligare en länkad tjänst.

2. I fönstret **ny länkad tjänst** väljer du fliken **Compute** .

3. Välj **Azure HDInsight** och välj sedan **Fortsätt**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png" alt-text="Skapa en HDInsight-länkad tjänst för Azure Data Factory":::

4. I fönstret **ny länkad tjänst** anger du följande värden och lämnar resten som standard:

    | Egenskap | Värde |
    | --- | --- |
    | Namn | Ange `HDInsightLinkedService`.|
    | Typ | Välj **HDInsight på begäran**. |
    | Länkad Azure Storage-tjänst | Välj `HDIStorageLinkedService`. |
    | Kluster typ | Välj **Hadoop** |
    | Time to live | Ange hur länge du vill att HDInsight-klustret ska vara tillgängligt innan det tas bort automatiskt.|
    | Tjänstens huvud namns-ID | Ange program-ID: t för Azure Active Directory tjänstens huvud namn som du skapade som en del av förutsättningarna. |
    | Tjänstens huvud nyckel | Ange autentiseringsnyckel för Azure Active Directory tjänstens huvud namn. |
    | Prefix för kluster namn | Ange ett värde som ska användas för alla kluster typer som skapats av data fabriken. |
    |Prenumeration |Välj din prenumeration i den nedrullningsbara listan.|
    | Välj resursgrupp | Välj den resurs grupp som du skapade som en del av PowerShell-skriptet som du använde tidigare.|
    | OS-typ/kluster SSH-användarnamn | Ange ett SSH-användarnamn, vanligt vis `sshuser` . |
    | OS-typ/kluster SSH-lösenord | Ange ett lösen ord för SSH-användaren |
    | OS-typ/kluster användar namn | Ange ett användar namn för klustret, vanligt vis `admin` . |
    | OS-typ/kluster lösen ord | Ange ett lösen ord för kluster användaren. |

    Välj sedan **Skapa**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png" alt-text="Ange värden för den länkade HDInsight-tjänsten":::

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+** knappen (plus) och välj sedan **pipeline**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png" alt-text="Skapa en pipeline i Azure Data Factory":::

1. I verktygs lådan **aktiviteter** expanderar du **HDInsight** och drar **Hive** -aktiviteten till pipelinens designer-yta. På fliken **Allmänt** anger du ett namn för aktiviteten.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png" alt-text="Lägg till aktiviteter i Data Factory pipeline":::

1. Kontrol lera att du har valt Hive-aktiviteten och välj fliken **HDI-kluster** . Och i list rutan **HDInsight-länkad tjänst** väljer du den länkade tjänst som du skapade tidigare, **HDInsightLinkedService**, för HDInsight.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png" alt-text="Ange information om HDInsight-kluster för pipelinen":::

1. Välj fliken **skript** och utför följande steg:

    1. För **länkad skript tjänst** väljer du **HDIStorageLinkedService** i list rutan. Det här värdet är den länkade lagrings tjänsten som du skapade tidigare.

    1. För **fil Sök väg** väljer du **Bläddra i lagrings** plats och navigerar till den plats där exempel registrerings skriptet är tillgängligt. Om du körde PowerShell-skriptet tidigare bör platsen vara `adfgetstarted/hivescripts/partitionweblogs.hql` .

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png" alt-text="Ange Hive-skript information för pipelinen":::

    1. Under **avancerade**  >  **parametrar** väljer du **`Auto-fill from script`** . Det här alternativet söker efter parametrar i Hive-skriptet som kräver värden vid körning.

    1. I text rutan **värde** lägger du till den befintliga mappen i formatet `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/` . Sökvägen är skiftlägeskänslig. Den här sökvägen är den plats där utdata från skriptet kommer att lagras. `wasbs`Schemat är nödvändigt eftersom lagrings konton nu har säker överföring som krävs aktiverat som standard.

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png" alt-text="Ange parametrar för Hive-skriptet":::

1. Välj **validera** för att validera pipelinen. Klicka på **>>** knappen (högerpil) för att stänga verifierings fönstret.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png" alt-text="Verifiera Azure Data Factory pipelinen":::

1. Slutligen väljer du **publicera allt** för att publicera artefakterna till Azure Data Factory.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png" alt-text="Publicera Azure Data Factory pipelinen":::

## <a name="trigger-a-pipeline"></a>Utlösa en pipeline

1. I verktygsfältet på design ytan väljer du **Lägg till utlösare**  >  **nu**.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png" alt-text="Utlös Azure Data Factory pipelinen":::

2. Välj **OK** i popup-sido fältet.

## <a name="monitor-a-pipeline"></a>Övervaka en pipeline

1. Växla till fliken **Övervaka** till vänster. En pipelinekörning visas i listan **Pipeline Runs** (Pipelinekörningar). Observera status för körningen under kolumnen **status** .

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png" alt-text="Övervaka Azure Data Factory pipelinen":::

1. Välj **Uppdatera** för att uppdatera statusen.

1. Du kan också välja ikonen **Visa aktivitet som körs** för att se aktivitets körningen som är associerad med pipelinen. I skärm bilden nedan visas endast en aktivitets körning eftersom det bara finns en aktivitet i den pipeline som du skapade. Om du vill växla tillbaka till den föregående vyn väljer du **pipeliner** längst upp på sidan.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png" alt-text="Övervaka Azure Data Factory pipeline-aktivitet":::

## <a name="verify-the-output"></a>Verifiera utdata

1. För att verifiera utdata går du till Azure Portal gå till det lagrings konto som du använde för den här självstudien. Du bör se följande mappar eller behållare:

    * Du ser en **adfgerstarted/outputfolder** som innehåller utdata från Hive-skriptet som kördes som en del av pipelinen.

    * Du ser en **adfhdidatafactory- \<linked-service-name> - \<timestamp>** container. Den här behållaren är standard lagrings platsen för HDInsight-klustret som skapades som en del av pipeline-körningen.

    * Du ser en **adfjobs** -behållare som har Azure Data Factory jobb loggar.  

        :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png" alt-text="Verifiera Azure Data Factory pipeline-utdata":::

## <a name="clean-up-resources"></a>Rensa resurser

När du skapar HDInsight-kluster på begäran behöver du inte uttryckligen ta bort HDInsight-klustret. Klustret tas bort baserat på den konfiguration du angav när du skapade pipelinen. Även om klustret har tagits bort fortsätter de lagrings konton som är kopplade till klustret att existera. Det här beteendet är avsiktligt, så att du kan hålla dina data intakta. Men om du inte vill spara data kan du ta bort det lagrings konto som du har skapat.

Du kan också ta bort hela resurs gruppen som du skapade för den här självstudien. Den här processen tar bort lagrings kontot och Azure Data Factory som du har skapat.

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **resurs grupper** i det vänstra fönstret.
1. Välj det resurs grupps namn som du skapade i PowerShell-skriptet. Använd filtret om du har för många resurs grupper i listan. Den öppnar resurs gruppen.
1. På panelen **resurser** måste du ha standard lagrings kontot och data fabriken i listan, om du inte delar resurs gruppen med andra projekt.
1. Välj **Ta bort resursgrupp**. Detta tar bort lagrings kontot och de data som lagras i lagrings kontot.

    :::image type="content" source="./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png" alt-text="Azure Portal ta bort resurs grupp":::

1. Ange resurs gruppens namn för att bekräfta borttagningen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Azure Data Factory för att skapa HDInsight-kluster på begäran och köra Apache Hive-jobb. Gå vidare till nästa artikel om du vill lära dig hur du skapar HDInsight-kluster med anpassad konfiguration.

> [!div class="nextstepaction"]
> [Skapa Azure HDInsight-kluster med anpassad konfiguration](hdinsight-hadoop-provision-linux-clusters.md)