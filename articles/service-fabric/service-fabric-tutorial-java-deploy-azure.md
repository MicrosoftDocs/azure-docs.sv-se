---
title: Distribuera en Java-app till ett Service Fabric kluster i Azure
description: I den här självstudiekursen får du lära dig hur du distribuerar en Java Service Fabric-tillämpning till ett Service Fabric-kluster i Azure.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: c2e2b2883bfa01d3a36de5d58425449f6f973010
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702165"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Självstudie: Distribuera en Java-tillämpning till ett Service Fabric-kluster i Azure

Den här självstudien är del tre i en serie. Här får du se hur du distribuerar en Service Fabric-tillämpning till ett kluster i Azure.

I den tredje delen i serien får du lära dig att:

> [!div class="checklist"]
> * Skapa ett säkert Linux-kluster i Azure
> * Distribuera ett program till klustret

I den här självstudieserien får du lära du dig att:

> [!div class="checklist"]
> * [Skapa ett Java Service Fabric Reliable Services-program](service-fabric-tutorial-create-java-app.md)
> * [Distribuera och felsöka ditt program på ett lokalt kluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * Distribuera programmet till ett Azure-kluster
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-java-elk.md)
> * [Konfigurera CI/CD](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Installera Service Fabric SDK för [Mac](service-fabric-get-started-mac.md) eller [Linux](service-fabric-get-started-linux.md)
* [Installera Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Skapa ett Service Fabric-kluster i Azure

Med följande steg skapar du de resurser som krävs för att distribuera tillämpningen till ett Service Fabric-kluster. Du skapar också de resurser som krävs för att övervaka hälsotillståndet hos din lösning med hjälp av ELK (Elasticsearch, Logstash, Kibana). Mer specifikt används [Event Hubs](https://azure.microsoft.com/services/event-hubs/) som en kanalmottagare för loggar från Service Fabric. Den är konfigurerad för att skicka loggarna från Service Fabric-klustret till din Logstash-instans.

1. Öppna en terminal och hämta följande paket, som innehåller nödvändiga hjälpskript och mallarna för att skapa resurser i Azure

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Logga in på ditt Azure-konto

    ```azurecli
    az login
    ```

3. Ange vilken Azure-prenumeration som du vill använda för att skapa resurserna

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Från mappen *service-fabric-java-quickstart/AzureCluster* kör du sedan följande kommando för att skapa ett klustercertifikat i Key Vault. Det här certifikatet används för att skydda ditt Service Fabric-kluster. Ange region (måste vara samma som för ditt Service Fabric-kluster), namnet på Key Vault-resursgruppen, Key Vault-namn, lösenord för certifikatet och klustrets DNS-namn.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Föregående kommando returnerar följande information, som bör antecknas för användning senare.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Skapa en resursgrupp för lagringskontot där dina loggar sparas

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Skapa ett lagringskonto som ska användas för att spara de loggar som skapas

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Öppna [Azure-portalen](https://portal.azure.com) och gå till fliken **Signatur för delad åtkomst** för ditt lagringskonto. Generera din SAS-token på följande sätt.

    ![Generera en signatur för delad åtkomst (SAS) för lagring](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Kopiera kontots SAS-URL och ha den tillgänglig när du skapar ditt Service Fabric-kluster. Den liknar följande URL:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Skapa en resursgrupp som innehåller Event Hub-resurserna. Event Hubs används för att skicka meddelanden från Service Fabric till den server som kör ELK-resurserna.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Skapa en resurs för Event Hubs med hjälp av följande kommando. Följ anvisningarna för att fylla i information för namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule och receiveAuthorizationRule.

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Kopiera innehållet i fältet **utdata** i JSON-utdata från föregående kommando. Information om avsändaren används när Service Fabric-klustret skapas. Mottagarens namn och nyckel ska sparas för användning i nästa självstudie, där Logstash-tjänsten konfigureras för att ta emot meddelanden från Event Hub. Följande blob är ett exempel på JSON-utdata:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Kör skriptet *eventhubssastoken.py* för att generera SAS-URL:en för EventHubs-resursen du skapade. SAS-URL:en används av Service Fabric-klustret för att skicka loggar till Event Hubs. Som ett resultat av det används **avsändarens** princip för att generera URL:en. Skriptet returnerar SAS-URL:en för den Event Hubs-resurs som används i följande steg:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Kopiera värdet från fältet **sr** i den JSON som returneras. Fältvärdet **sr** används som SAS-token för EventHubs. Följande URL är ett exempel på fältet **sr**:

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    Din SAS-URL för EventHubs följer strukturen: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` . Till exempel `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Öppna filen *sfdeploy.parameters.json* och ersätt följande innehåll från föregående steg. [SAS-URL-STORAGE-ACCOUNT] beskrevs i steg 8. [SAS-URL-EVENT-HUBS] beskrevs i steg 11.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Öppnar **sfdeploy.parameters.json**. Ändra följande parametrar och spara därefter filen.
    - **clusterName**. Använd bara gemena bokstäver och siffror.
    - **adminUserName** (till ett värde som inte är tomt)
    - **adminPassword** (till ett värde som inte är tomt)

14. Kör följande kommando för att skapa Service Fabric-klustret

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Distribuera din tillämpning till klustret

1. Innan du distribuerar din tillämpning måste du lägga till följande kodfragment i filen *Voting/VotingApplication/ApplicationManifest.xml*. Fältet **X509FindValue** är det tumavtryck som returnerades från Steg 4 i avsnittet **Skapa ett Service Fabric-kluster i Azure**. Kodfragmentet är kapslat under fältet **ApplicationManifest** (rotfältet).

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. För att kunna distribuera din tillämpning till klustret måste du etablera en anslutning till klustret med hjälp av SFCTL. SFCTL kräver en PEM-fil med både offentlig och privat nyckel för att bevilja anslutning till klustret. Kör följande kommando om du vill skapa en PEM-fil med både offentlig och privat nyckel. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Kör följande kommando för att ansluta till klustret.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Om du vill distribuera programmet går du till mappen *Voting/Scripts* (Röstning/Skript) och kör skriptet **install.sh**.

    ```bash
    ./install.sh
    ```

5. Du kommer åt Service Fabric Explorer genom at öppna din vanliga webbläsare och skriva `https://testlinuxcluster.westus.cloudapp.azure.com:19080`. Välj certifikatet från det certifikatarkiv som du vill använda för att ansluta till slutpunkten. Om du använder en Linux-dator måste de certifikat som genererats av skriptet *new-service-fabric-cluster-certificate.sh* importeras till Chrome för att du ska kunna visa Service Fabric Explorer. Om du använder en Mac-dator måste du installera PFX-filen i din nyckelring. Du kan nu se att din tillämpning har installerats i klustret.

    ![SFX Java i Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Du kommer åt din tillämpning genom att skriva `https://testlinuxcluster.westus.cloudapp.azure.com:8080`

    ![Röstningsapp i Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Om du vill avinstallera din tillämpning från klustret kör du skriptet *uninstall.sh* i **Skript**-mappen

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett säkert Linux-kluster i Azure
> * Skapa nödvändiga resurser för övervakning med ELK

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Konfigurera övervakning + diagnostik](service-fabric-tutorial-java-elk.md)
