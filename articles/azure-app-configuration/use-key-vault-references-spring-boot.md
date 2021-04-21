---
title: Självstudie om att använda Azure App Configuration Key Vault referenser i en Java Spring Boot-app | Microsoft Docs
description: I den här självstudien får du lära dig hur Azure App Configuration använder Key Vault från en Java Spring Boot-app
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7c5534ab836968bc4e72a54db1ddb9667d366558
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768857"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Självstudie: Använda Key Vault referenser i en Java Spring-app

I den här självstudien lär du dig att använda Azure App Configuration-tjänsten tillsammans med Azure Key Vault. App Configuration och Key Vault kompletterande tjänster som används sida vid sida i de flesta programdistributioner.

App Configuration hjälper dig att använda tjänsterna tillsammans genom att skapa nycklar som refererar till värden som lagras i Key Vault. När App Configuration skapar sådana nycklar lagras URI:er för Key Vault snarare än själva värdena.

Programmet använder klientprovidern App Configuration för att hämta Key Vault referenser, precis som för andra nycklar som lagras i App Configuration. I det här fallet är värdena som lagras App Configuration URI:er som refererar till värdena i Key Vault. De är inte Key Vault värden eller autentiseringsuppgifter. Eftersom klientprovidern identifierar nycklarna som Key Vault referenser använder den Key Vault för att hämta deras värden.

Ditt program ansvarar för att autentisera korrekt till både App Configuration och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault referenser i koden. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter slutför du [Skapa en Java Spring-app med App Configuration](./quickstart-java-spring-app.md) först.

Du kan använda valfri kodredigerare för att göra stegen i den här självstudien. Till exempel [Visual Studio Code](https://code.visualstudio.com/) är en plattformsoberoende kodredigerare som är tillgänglig för Windows-, macOS- och Linux-operativsystemen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en App Configuration nyckel som refererar till ett värde som lagras Key Vault.
> * Få åtkomst till värdet för den här nyckeln från ett Java Spring-program.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
* Ett [Java Development Kit (JDK) med](/java/azure/jdk) version 8 som stöds.
* [Apache Maven](https://maven.apache.org/download.cgi) version 3.0 eller senare.

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **Skapa en** resurs i det övre vänstra hörnet i Azure Portal:

    ![Skärmbild som visar alternativet Skapa en resurs i Azure Portal.](./media/quickstarts/search-services.png)
1. Ange **Key Vault** i sökrutan.
1. I resultatlistan väljer du **Nyckelvalv** till vänster.
1. I **Nyckelvalv väljer** du Lägg **till**.
1. Ange följande information **till höger i** Skapa nyckelvalv:
    * Välj **Prenumeration** för att välja en prenumeration.
    * I **Resursgrupp** väljer du **Skapa ny** och anger ett resursgruppsnamn.
    * I **Nyckelvalvsnamn** krävs ett unikt namn. I den här självstudien anger **du Contoso-vault2**.
    * Välj **en** plats i listrutan Region.
1. Lämna **standardvärdena för de andra** alternativen för Att skapa nyckelvalv.
1. Välj **Skapa**.

Nu är ditt Azure-konto det enda som har behörighet att komma åt det nya valvet.

![Skärmbild som visar nyckelvalvet.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet behöver du bara vidta några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att Key Vault hämtning. Meddelandet kallas Meddelande **och** du lagrar värdet "Hello from Key Vault" i det.

1. Från sidan Key Vault egenskaper väljer du **Hemligheter.**
1. Välj **Generera/importera**.
1. I fönstret **Skapa en** hemlighet anger du följande värden:
    * **Uppladdningsalternativ:** Ange **Manuell**.
    * **Namn:** Ange **Meddelande**.
    * **Värde:** Ange **Hello från Key Vault**.
1. Lämna **standardvärdena för de andra egenskaperna** Skapa en hemlighet.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägga till en Key Vault-referens för App Configuration

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Alla resurser** och välj sedan den App Configuration Store-instans som du skapade i snabbstarten.

1. Välj **Configuration Explorer.**

1. Välj **+ Skapa**  >  **nyckelvalvsreferens** och ange sedan följande värden:
    * **Nyckel:** Välj **/application/config.keyvaultmessage**
    * **Etikett:** Lämna det här värdet tomt.
    * **Prenumeration,** **Resursgrupp** och **Nyckelvalv:** Ange de värden som motsvarar värdena i nyckelvalvet som du skapade i föregående avsnitt.
    * **Hemlighet:** Välj hemligheten med **namnet Meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Ansluta till Key Vault

1. I den här självstudien använder du ett huvudnamn för tjänsten för autentisering för att Key Vault. Om du vill skapa tjänstens huvudnamn använder du kommandot Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie nyckel/värde-par:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Kör följande kommando för att ge tjänstens huvudnamn åtkomst till nyckelvalvet:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Kör följande kommando för att hämta ditt objekt-id och lägg sedan till det i App Configuration.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Skapa miljövariablerna **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** och **AZURE_TENANT_ID**. Använd värdena för tjänstens huvudnamn som visades i föregående steg. Kör följande kommandon på kommandoraden och starta om kommandotolken så att ändringen börjar gälla:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> De Key Vault autentiseringsuppgifterna används bara i ditt program.  Ditt program autentiseras direkt med Key Vault med dessa autentiseringsuppgifter utan att App Configuration tjänsten.  Den Key Vault autentisering för både ditt program och din App Configuration utan att dela eller exponera nycklar.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera koden så att den använder Key Vault referens

1. Skapa en miljövariabel med **namnet APP_CONFIGURATION_ENDPOINT**. Ange dess värde till slutpunkten för App Configuration store. Du hittar slutpunkten på **bladet Åtkomstnycklar** i Azure Portal. Starta om kommandotolken så att ändringen börjar gälla. 


1. Öppna *bootstrap.properties* i *resursmappen.* Uppdatera den här filen så att den **APP_CONFIGURATION_ENDPOINT värdet.** Ta bort alla referenser till en anslutningssträng i den här filen. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Öppna *MessageProperties.java*. Lägg till en ny variabel med *namnet keyVaultMessage:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Öppna *HelloController.java*. Uppdatera metoden *getMessage* så att den inkluderar meddelandet som hämtats från Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Skapa en ny fil med *namnet AzureCredentials.java* och lägg till koden nedan.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Skapa en ny fil med *namnet AppConfiguration.java.* Lägg till koden nedan.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Skapa en ny fil i dina resursers META-INF-katalog med namnet *spring.factories* och lägg till koden nedan.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Skapa ditt Spring Boot med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. När programmet körs använder du *curl för* att testa programmet, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Du ser meddelandet som du angav i App Configuration store. Du ser också meddelandet som du angav i Key Vault.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat App Configuration nyckel som refererar till ett värde som lagras i Key Vault. Fortsätt till nästa självstudie om du vill lära dig hur du använder funktionsflaggor i ditt Java Spring-program.

> [!div class="nextstepaction"]
> [Integrering av hanterade identiteter](./quickstart-feature-flag-spring-boot.md)
