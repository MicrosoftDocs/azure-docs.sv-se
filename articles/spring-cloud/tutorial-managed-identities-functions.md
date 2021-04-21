---
title: 'Självstudie: Hanterad identitet för att anropa Azure Functions'
description: Använda en hanterad identitet för anropa Azure Functions från en Azure Spring Cloud-app
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: b737ea751d3b3d2132691e04a1a2cd853748db65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792515"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Självstudie: Använda en hanterad identitet för att anropa Azure Functions från en Azure Spring Cloud app

Den här artikeln visar hur du skapar en hanterad identitet för en Azure Spring Cloud-app och använder den för att anropa Http-utlösta funktioner.

Både Azure Functions och App Services har inbyggt stöd för Azure Active Directory -autentisering (Azure AD). Genom att utnyttja den här inbyggda autentiseringsfunktionerna tillsammans med hanterade identiteter för Azure Spring Cloud kan vi anropa RESTful-tjänster med modern OAuth-semantik. Den här metoden kräver inte lagring av hemligheter i kod och ger mer detaljerade kontroller för att kontrollera åtkomsten till externa resurser. 


## <a name="prerequisites"></a>Förutsättningar

* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* [Installera Azure CLI version 2.0.67 eller senare](/cli/azure/install-azure-cli)
* [Installera Maven 3.0 eller högre](https://maven.apache.org/download.cgi)
* [Installera Azure Functions Core Tools version 3.0.2009 eller senare](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp som ska innehålla både funktionsappen och Spring Cloud med kommandot [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Skapa en funktionsapp
Om du vill skapa en funktionsapp måste du först skapa ett lagringskonto med hjälp av kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create):

> [!Important]
> Varje funktionsapp och lagringskonto måste ha ett unikt namn. Ersätt <ditt-functionapp-name> med namnet på din funktionsapp och <ditt-lagringskontonamn> med namnet på ditt lagringskonto i följande exempel.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

När lagringskontot har skapats kan du skapa funktionsappen.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Anteckna det returnerade **hostNames,** som har formatet "https://<your-functionapp-name>.azurewebsites.net". Det kommer att användas i följande steg.


## <a name="enable-azure-active-directory-authentication"></a>Aktivera Azure Active Directory autentisering

Öppna den nyligen skapade funktionsappen från [Azure Portal](https://portal.azure.com) och välj "Autentisering/auktorisering" på inställningsmenyn. Aktivera App Service autentisering och ange "Åtgärd att vidta när begäran inte har autentiserats" till "Logga in med Azure Active Directory". Den här inställningen ser till att alla oauticerade begäranden nekas (401-svar).

![Autentiseringsinställningar som Azure Active Directory som standardprovider](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Under Autentiseringsproviders väljer Azure Active Directory konfigurera programregistreringen. Om du väljer Express Management Mode skapas automatiskt en programregistrering i din Azure AD-klientorganisation med rätt konfiguration.

![Azure Active Directory-providern inställd på Express Management Mode](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

När du sparar inställningarna startas funktionsappen om och alla efterföljande begäranden uppmanas att logga in via Azure AD. Du kan testa att oauthenticerade begäranden nu avvisas genom att gå till funktionsappens rot-URL (som returneras i **hostNames-utdata** i steget ovan). Du bör omdirigeras till din organisations Azure AD-inloggningsskärm.


## <a name="create-an-http-triggered-function"></a>Skapa en HTTP-utlöst funktion

Skapa en ny funktionsapp i en tom lokal katalog och lägg till en Http-utlöst funktion.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Som standard använder Functions nyckelbaserad autentisering för att skydda HTTP-slutpunkter. Eftersom vi kommer att aktivera Azure AD-autentisering för att skydda åtkomsten till Functions vill vi ställa in funktionens [autentiseringsnivå på anonym](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Appen kan nu publiceras till den [funktionsappinstans](#create-a-function-app) som skapades i föregående steg.

```console
func azure functionapp publish <your-functionapp-name>
```

Utdata från publiceringskommandot bör lista URL:en till den nya funktionen.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Skapa Azure Spring Cloud tjänst och app
När du har installerat spring-cloud-tillägget skapar du en Azure Spring Cloud instans med Azure CLI-kommandot `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

I följande exempel skapas en app med `msiapp` namnet med en system tilldelad hanterad identitet, på begäran av `--assign-identity` parametern .

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Skapa exempel Spring Boot app för att anropa funktionen

Det här exemplet anropar den Http-utlösta funktionen genom att först begära en åtkomsttoken från [MSI-slutpunkten](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) och använda denna token för att autentisera http-begäran för funktionen.

1. Klona exempelprojektet. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Ange funktions-URI och utlösarnamnet i appegenskaperna. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet Azure Spring Cloud appar lägger du till egenskaper med följande innehåll i *src/main/resources/application.properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Paketera din exempelapp. 

    ```console
    mvn clean package
    ```

4. Distribuera nu appen till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Öppna den offentliga slutpunkten eller testslutpunkten för att testa din app. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Följande meddelande returneras i svarstexten.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Du kan skicka olika värden till funktionen genom att ändra sökvägsparametern.

## <a name="next-steps"></a>Nästa steg

* [Så här aktiverar du system tilldelad hanterad identitet för Azure Spring Cloud program](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Läs mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Konfigurera klientappar för att få åtkomst till App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
