---
title: Självstudie – hanterad identitet för att anropa Azure Functions från en Azure våren Cloud-App
description: Använda en hanterad identitet för anropa Azure Functions från en Azure Spring Cloud-app
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 861463b13c6ce8b29911432ad96f98fed527745b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501064"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Självstudie: Använd en hanterad identitet för att anropa Azure Functions från en Azure våren Cloud-App

Den här artikeln visar hur du skapar en hanterad identitet för en Azure våren Cloud-App och använder den för att anropa http-utlösta funktioner.

Både Azure Functions och App Services har inbyggt stöd för Azure Active Directory (Azure AD)-autentisering. Genom att utnyttja den här inbyggda autentiseringen tillsammans med hanterade identiteter för Azure våren Cloud kan vi anropa RESTful Services med modern OAuth-semantik. Den här metoden kräver inte att du lagrar hemligheter i kod och ger mer detaljerade kontroller för att kontrol lera åtkomst till externa resurser. 


## <a name="prerequisites"></a>Förutsättningar

* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* [Installera Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli)
* [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)
* [Installera Azure Functions Core Tools version 3.0.2009 eller högre](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resurs grupp som ska innehålla både Function-appen och våren Cloud med kommandot [AZ Group Create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Skapa en funktionsapp
Om du vill skapa en Function-app måste du först skapa ett lagrings konto för återställning, använda kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> Varje Function-app och lagrings konto måste ha ett unikt namn. Ersätt <ditt-functionapp-> med namnet på din Function-app och <ditt storageaccount-namn> med namnet på ditt lagrings konto i följande exempel.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

När lagrings kontot har skapats kan du skapa Function-appen.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Anteckna de returnerade **värd** namnen, som kommer att ha formatet "https://<Your-functionapp-Name>. azurewebsites.net". Den kommer att användas i ett av följande steg.


## <a name="enable-azure-active-directory-authentication"></a>Aktivera Azure Active Directory autentisering

Öppna den nyligen skapade Function-appen från [Azure Portal](https://portal.azure.com) och välj "autentisering/auktorisering" på menyn Inställningar. Aktivera App Service autentisering och ange "åtgärd som ska vidtas när begäran inte autentiseras" till "logga in med Azure Active Directory". Med den här inställningen ser du till att alla oautentiserade begär Anden nekas (401-svar).

![Autentiseringsinställningar som visar Azure Active Directory som standardprovider](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Under autentiseringsproviders väljer du Azure Active Directory för att konfigurera program registreringen. Om du väljer Express hanterings läge skapas automatiskt en program registrering i din Azure AD-klient med rätt konfiguration.

![Azure Active Directory providern är inställd på Express Management mode](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

När du har sparat inställningarna startas Function-appen och alla efterföljande begär Anden kommer att uppmanas att logga in via Azure AD. Du kan testa att oautentiserade begär Anden nu avvisas genom att gå till rot-URL: en för Function-appar (returneras i **hostname** -utdata i steget ovan). Du bör omdirigera till din organisations inloggnings skärm för Azure AD.


## <a name="create-an-http-triggered-function"></a>Skapa en http-utlöst funktion

I en tom lokal katalog skapar du en ny function-app och lägger till en http-utlöst funktion.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Som standard används nyckelbaserad autentisering för att skydda http-slutpunkter. Eftersom vi aktiverar Azure AD-autentisering för att skydda åtkomsten till funktionerna, vill vi [ställa in funktions verifierings nivån på anonym](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

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

Appen kan nu publiceras till [Function](#create-a-function-app) -serverinstansen som skapades i föregående steg.

```console
func azure functionapp publish <your-functionapp-name>
```

Utdata från kommandot Publish ska visa URL: en för den nya funktionen.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Skapa moln tjänst och app i Azure våren
När du har installerat fjäder moln tillägget skapar du en Azure våren Cloud-instans med Azure CLI-kommandot `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

I följande exempel skapas en app `msiapp` med namnet med en systemtilldelad hanterad identitet, enligt begäran från `--assign-identity` parametern.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Bygg exempel på våren Boot-appen för att anropa funktionen

Det här exemplet anropar funktionen http-utlöst genom att först begära en åtkomsttoken från [MSI-slutpunkten](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) och använda denna token för att autentisera funktionen http-begäran.

1. Klona exempelprojektet. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Ange funktions-URI och Utlösarens namn i egenskaperna för appen. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet för Azure våren Cloud Apps lägger du till egenskaper med följande innehåll i *src/main/Resources/Application. Properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Paketera din exempel App. 

    ```console
    mvn clean package
    ```

4. Distribuera nu appen till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Få åtkomst till den offentliga slut punkten eller test slut punkten för att testa appen. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Du ser följande meddelande som returneras i svars texten.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Du kan prova att skicka olika värden till funktionen genom att ändra parametern Path.

## <a name="next-steps"></a>Nästa steg

* [Så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Lär dig mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Konfigurera klient program för att få åtkomst till din App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
