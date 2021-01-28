---
title: 'Självstudie: skapa ett Apache Kafka REST proxy-aktiverat kluster i HDInsight med Azure CLI'
description: Lär dig hur du utför Apache Kafka åtgärder med en Kafka REST-proxy på Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff11b8461b483f5a66df19bb1b108a1fe1168fb9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944020"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Självstudie: skapa ett Apache Kafka REST proxy-aktiverat kluster i HDInsight med Azure CLI

I den här självstudien får du lära dig hur du skapar ett Apache Kafka [rest proxy-aktiverat](./rest-proxy.md) kluster i Azure HDInsight med hjälp av kommando rads gränssnittet i Azure (CLI). Azure HDInsight är en hanterad analystjänst med fullständigt spektrum med öppen källkod för företag. Apache Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. Med Kafka REST proxy kan du interagera med ditt Kafka-kluster via en [REST API](/rest/api/hdinsight-kafka-rest-proxy/) över http. Azure CLI är Microsofts plattformsoberoende kommandoradsmiljö för att hantera Azure-resurser.

Apache Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. Du kan komma åt klustret direkt med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Apache Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket. Mer information finns i [ansluta till Apache Kafka med hjälp av ett virtuellt nätverk](./apache-kafka-connect-vpn-gateway.md).

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Krav för Kafka REST proxy
> * Skapa ett Apache Kafka-kluster med Azure CLI

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett program som är registrerat i Azure AD. Klient programmen som du skriver för att interagera med Kafka REST-proxyn använder programmets ID och hemlighet för att autentisera till Azure. Mer information finns i [Registrera ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).

* En Azure AD-säkerhetsgrupp med ditt registrerade program som medlem. Den här säkerhets gruppen används för att kontrol lera vilka program som får interagera med REST-proxyn. Mer information om hur du skapar Azure AD-grupper finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Se till att du har minst version 2.0.79. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

1. Logga in på din Azure-prenumeration.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Ange miljövariabler. Användningen av variabler i den här självstudien baseras på bash. Små variationer kommer att krävas för andra miljöer.

    |Variabel | Beskrivning |
    |---|---|
    |resourceGroupName|Ersätt RESOURCEGROUPNAME med namnet på den nya resurs gruppen.|
    |location|Ersätt plats med en region där klustret ska skapas. Om du vill ha en lista över giltiga platser använder du `az account list-locations` kommandot|
    |clusterName|Ersätt kluster namn med ett globalt unikt namn för det nya klustret.|
    |storageAccount|Ersätt STORAGEACCOUNTNAME med ett namn för ditt nya lagrings konto.|
    |httpPassword|Ersätt lösen ordet med ett lösen ord för kluster inloggningen, **admin**.|
    |sshPassword|Ersätt PASSWORD med ett lösen ord för Secure Shell-användarnamnet, **sshuser**.|
    |securityGroupName|Ersätt SECURITYGROUPNAME med klientens AAD-säkerhetsgrupp namn för Kafka rest proxy. Variabeln skickas till- `--kafka-client-group-name` parametern för `az-hdinsight-create` .|
    |securityGroupID|Ersätt SECURITYGROUPID med klientens AAD-säkerhetsgrupp-ID för Kafka rest proxy. Variabeln skickas till- `--kafka-client-group-id` parametern för `az-hdinsight-create` .|
    |storageContainer|Lagrings behållare som klustret ska använda, lämna det som är i den här självstudien. Den här variabeln kommer att anges med namnet på klustret.|
    |workernodeCount|Antalet arbetsnoder i klustret, lämna det som är i den här självstudien. För att garantera hög tillgänglighet kräver Kafka minst tre arbetsnoder|
    |clusterType|Typ av HDInsight-kluster, lämna det som är i den här självstudien.|
    |clusterVersion|HDInsight-kluster version, lämna det som är i den här självstudien. Kafka rest-proxyn kräver en lägsta kluster version på 4,0.|
    |componentVersion|Kafka-version, lämna det som är för den här självstudien. Kafka rest-proxyn kräver en lägsta komponent version på 2,1.|

    Uppdatera variablerna med önskade värden. Ange sedan CLI-kommandona för att ställa in miljövariablerna.

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

1. [Skapa resurs gruppen](/cli/azure/group#az-group-create) genom att ange kommandot nedan:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Skapa ett Azure Storage konto](/cli/azure/storage/account#az-storage-account-create) genom att ange kommandot nedan:

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

1. [Extrahera primär nyckeln](/cli/azure/storage/account/keys#az-storage-account-keys-list) från Azure Storage-kontot och lagra den i en variabel genom att ange kommandot nedan:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Skapa en Azure Storage-behållare](/cli/azure/storage/container#az-storage-container-create) genom att ange kommandot nedan:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Skapa HDInsight-klustret](/cli/azure/hdinsight#az-hdinsight-create). Observera följande parametrar innan du anger kommandot:

    1. Obligatoriska parametrar för Kafka-kluster:

        |Parameter | Beskrivning|
        |---|---|
        |--typ|Värdet måste vara **Kafka**.|
        |--workernode-data-disks-per nod|Det antal data diskar som ska användas per arbets nod. HDInsight-Kafka stöds bara med data diskar. I den här självstudien används värdet **2**.|

    1. Obligatoriska parametrar för Kafka REST proxy:

        |Parameter | Beskrivning|
        |---|---|
        |--Kafka-Management-Node-size|Nodens storlek. I den här självstudien används värdet **Standard_D4_v2**.|
        |--Kafka-client-Group-ID|Klientens AAD säkerhets grupp-ID för Kafka rest proxy. Värdet skickas från variabeln **$securityGroupID**.|
        |--Kafka-client-Group-name|Klientens AAD säkerhets grupp namn för Kafka rest proxy. Värdet skickas från variabeln **$securityGroupName**.|
        |--version|HDInsight-klustrets version måste vara minst 4,0. Värdet skickas från variabeln **$clusterVersion**.|
        |--komponent-version|Kafka-versionen måste vara minst 2,1. Värdet skickas från variabeln **$componentVersion**.|
    
        Om du vill skapa klustret utan REST proxy, eliminera `--kafka-management-node-size` , `--kafka-client-group-id` och `--kafka-client-group-name` från `az hdinsight create` kommandot.

    1. Om du har ett befintligt virtuellt nätverk lägger du till parametrarna `--vnet-name` och `--subnet` , och deras värden.

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

    Det kan ta flera minuter innan kluster skapande processen har slutförts. Vanligt vis runt 15.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med artikeln kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

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

Nu när du har skapat ett Apache Kafka REST proxy-aktiverat kluster i Azure HDInsight med Azure CLI använder du python-kod för att interagera med REST-proxyn:

> [!div class="nextstepaction"]
> [Skapa exempel program](./rest-proxy.md#client-application-sample)