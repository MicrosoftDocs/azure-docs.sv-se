---
title: 'Självstudie: Skapa ett Apache Kafka REST-proxyaktiverade kluster i HDInsight med Azure CLI'
description: Lär dig hur du utför Apache Kafka med hjälp av en Kafka REST-proxy på Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786647"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Självstudie: Skapa ett Apache Kafka REST-proxyaktiverade kluster i HDInsight med Azure CLI

I den här självstudien får du lära dig hur du skapar ett Apache Kafka [rest-proxyaktiverade](./rest-proxy.md) kluster i Azure HDInsight med hjälp av Azure-kommandoradsgränssnittet (CLI). Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. Med Kafka REST Proxy kan du interagera med ditt Kafka-kluster via en [REST API](/rest/api/hdinsight-kafka-rest-proxy/) över HTTP. Azure CLI är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Apache Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. Du kan komma åt klustret direkt med hjälp av SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Apache Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket. Mer information finns i Ansluta [till Apache Kafka ett virtuellt nätverk](./apache-kafka-connect-vpn-gateway.md).

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Krav för Kafka REST-proxy
> * Skapa ett Apache Kafka kluster med Azure CLI

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett program som registrerats med Azure AD. Klientprogrammen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure. Mer information finns i [Registrera ett program med Microsoft Identity Platform.](../../active-directory/develop/quickstart-register-app.md)

* En Azure AD-säkerhetsgrupp med ditt registrerade program som medlem. Den här säkerhetsgruppen används för att styra vilka program som får interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Kontrollera att du har minst version 2.0.79. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

1. Logga in på din Azure-prenumeration.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ange miljövariabler. Användningen av variabler i den här självstudien baseras på Bash. Små variationer krävs för andra miljöer.

    |Variabel | Beskrivning |
    |---|---|
    |resourceGroupName|Ersätt RESOURCEGROUPNAME med namnet på den nya resursgruppen.|
    |location|Ersätt LOCATION med en region där klustret ska skapas. Om du vill ha en lista över giltiga platser använder du `az account list-locations` kommandot|
    |clusterName|Ersätt CLUSTERNAME med ett globalt unikt namn för det nya klustret.|
    |storageAccount|Ersätt STORAGEACCOUNTNAME med ett namn för ditt nya lagringskonto.|
    |httpPassword|Ersätt PASSWORD med ett lösenord för klusterinloggningen, **admin**.|
    |sshPassword|Ersätt PASSWORD med ett lösenord för secure shell-användarnamnet, **sshuser**.|
    |securityGroupName|Ersätt SECURITYGROUPNAME med klientens AAD-säkerhetsgruppnamn för Kafka Rest Proxy. Variabeln skickas till `--kafka-client-group-name` parametern för `az-hdinsight-create` .|
    |securityGroupID|Ersätt SECURITYGROUPID med klientens AAD-säkerhetsgrupp-ID för Kafka Rest Proxy. Variabeln skickas till `--kafka-client-group-id` parametern för `az-hdinsight-create` .|
    |storageContainer|Lagringscontainer som klustret ska använda, lämna som den är för den här självstudien. Den här variabeln anges med namnet på klustret.|
    |workernodeCount|Antal arbetarnoder i klustret, lämna som det är för den här självstudien. För att garantera hög tillgänglighet kräver Kafka minst 3 arbetsnoder|
    |clusterType|Typ av HDInsight-kluster, lämna som det är för den här självstudien.|
    |clusterVersion|HDInsight-klusterversion, lämna som det är för den här självstudien. Kafka Rest Proxy kräver en lägsta klusterversion på 4.0.|
    |componentVersion|Kafka-version, lämna som den är för den här självstudien. Kafka Rest Proxy kräver en lägsta komponentversion på 2.1.|

    Uppdatera variablerna med önskade värden. Ange sedan CLI-kommandona för att ange miljövariablerna.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Skapa resursgruppen genom](/cli/azure/group#az_group_create) att ange kommandot nedan:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Skapa ett Azure Storage konto](/cli/azure/storage/account#az_storage_account_create) genom att ange kommandot nedan:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extrahera primärnyckeln från](/cli/azure/storage/account/keys#az_storage_account_keys_list) Azure Storage och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Skapa en Azure Storage container](/cli/azure/storage/container#az_storage_container_create) genom att ange kommandot nedan:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Skapa HDInsight-klustret](/cli/azure/hdinsight#az_hdinsight_create). Observera följande parametrar innan du anger kommandot:

    1. Obligatoriska parametrar för Kafka-kluster:

        |Parameter | Beskrivning|
        |---|---|
        |--type|Värdet måste vara **Kafka**.|
        |--workernode-data-disks-per-node|Antalet datadiskar som ska användas per arbetsnod. HDInsight Kafka stöds endast med datadiskar. I den här självstudien används värdet **2**.|

    1. Obligatoriska parametrar för Kafka REST-proxy:

        |Parameter | Beskrivning|
        |---|---|
        |--kafka-management-node-size|Nodens storlek. I den här självstudien används **värdet Standard_D4_v2**.|
        |--kafka-client-group-id|Klientens AAD-säkerhetsgrupp-ID för Kafka Rest Proxy. Värdet skickas från variabeln **$securityGroupID**.|
        |--kafka-client-group-name|Klientens AAD-säkerhetsgruppnamn för Kafka Rest Proxy. Värdet skickas från variabeln **$securityGroupName**.|
        |--version|HDInsight-klusterversionen måste vara minst 4.0. Värdet skickas från variabeln **$clusterVersion**.|
        |--component-version|Kafka-versionen måste vara minst 2.1. Värdet skickas från variabeln **$componentVersion**.|
    
        Om du vill skapa klustret utan REST-proxy tar du bort `--kafka-management-node-size` , och från kommandot `--kafka-client-group-id` `--kafka-client-group-name` `az hdinsight create` .

    1. Om du har ett befintligt virtuellt nätverk lägger du till `--vnet-name` parametrarna `--subnet` och och deras värden.

    Ange följande kommando för att skapa klustret:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Det kan ta flera minuter att skapa klustret. Vanligtvis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även om det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Ange alla eller några av följande kommandon för att ta bort resurser:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett Apache Kafka REST-proxyaktiverade kluster i Azure HDInsight Azure CLI kan du använda Python-kod för att interagera med REST-proxyn:

> [!div class="nextstepaction"]
> [Skapa exempelprogram](./rest-proxy.md#client-application-sample)