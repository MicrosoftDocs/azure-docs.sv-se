---
title: Själv studie kurs om att använda Azure App konfiguration Key Vault referenser i en Java våren Boot-app | Microsoft Docs
description: I den här självstudien får du lära dig att använda Azure App konfigurations Key Vault referenser från en Java våren Boot-app
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
ms.openlocfilehash: 04d9c7a343570349851a206fd69fdda822f790a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981484"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Självstudie: använda Key Vault referenser i en Java våren-app

I den här självstudien får du lära dig hur du använder Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Konfiguration av appar och Key Vault är kompletterande tjänster som används sida vid sida i de flesta program distributioner.

Med konfiguration av appar kan du använda tjänsterna tillsammans genom att skapa nycklar som refererar till värden som lagras i Key Vault. När app-konfigurationen skapar sådana nycklar lagrar den URI: er för Key Vault värden i stället för själva värdena.

Programmet använder klient leverantören för konfiguration av appar för att hämta Key Vault referenser, precis som för alla andra nycklar som lagras i app-konfigurationen. I det här fallet är de värden som lagras i app-konfigurationen URI: er som refererar till värdena i Key Vault. De är inte Key Vault värden eller autentiseringsuppgifter. Eftersom klient leverantören känner igen nycklarna som Key Vault referenser, använder den Key Vault för att hämta sina värden.

Ditt program ansvarar för att autentisera korrekt för både app-konfigurationen och Key Vault. De två tjänsterna kommunicerar inte direkt.

Den här självstudien visar hur du implementerar Key Vault referenser i din kod. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter måste du först [skapa en Java våren-app med app-konfigurationen](./quickstart-java-spring-app.md) .

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. Till exempel är [Visual Studio Code](https://code.visualstudio.com/) en plattforms oberoende kod redigerare som är tillgänglig för operativ systemen Windows, MacOS och Linux.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault.
> * Få åtkomst till värdet för den här nyckeln från ett Java våren-program.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* En [Java Development Kit (JDK)](/java/azure/jdk) som stöds med version 8.
* [Apache maven](https://maven.apache.org/download.cgi) version 3,0 eller senare.

## <a name="create-a-vault"></a>Skapa ett valv

1. Välj alternativet **skapa en resurs** i det övre vänstra hörnet i Azure Portal:

    ![Skärm bild som visar alternativet Skapa en resurs i Azure Portal.](./media/quickstarts/search-services.png)
1. Ange **Key Vault** i sökrutan.
1. Välj **nyckel valv** till vänster i listan resultat.
1. I **nyckel valv** väljer du **Lägg till**.
1. Ange följande information till höger i **skapa nyckel valv**:
    * Välj en **prenumeration för att välja en** prenumeration.
    * I **resurs grupp** väljer du **Skapa ny** och anger ett resurs grupp namn.
    * I **Key Vault-namnet** krävs ett unikt namn. I den här självstudien anger du **contoso-vault2**.
    * Välj en plats i list rutan **region** .
1. Lämna de andra alternativen för att **skapa nyckel valv** med standardvärdena.
1. Välj **Skapa**.

I det här läget är ditt Azure-konto det enda som har behörighet att komma åt det här nya valvet.

![Skärm bilden visar ditt nyckel valv.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault

Om du vill lägga till en hemlighet i valvet behöver du bara utföra några ytterligare steg. I det här fallet lägger du till ett meddelande som du kan använda för att testa Key Vault hämtning. Meddelandet kallas **meddelande** och du lagrar värdet "hej från Key Vault" i det.

1. Välj **hemligheter** på sidan Key Vault egenskaper.
1. Välj **generera/importera**.
1. Ange följande värden i fönstret **skapa en hemlighet** :
    * **Överförings alternativ**: ange **manuell**.
    * **Namn**: Ange ett **meddelande**.
    * **Värde**: ange **Hej från Key Vault**.
1. Lämna den andra **skapa en hemlig** egenskap med standardvärdena.
1. Välj **Skapa**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Lägg till en Key Vault referens till app-konfigurationen

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj sedan den instans av app konfigurations arkiv som du skapade i snabb starten.

1. Välj **konfigurations Utforskaren**.

1. Välj **+ skapa**  >  **Key Vault-referens** och ange sedan följande värden:
    * **Nyckel**: Välj **/Application/config.keyvaultmessage**
    * **Etikett**: lämna värdet tomt.
    * **Prenumeration**, **resurs grupp** och **nyckel valv**: ange värdena som motsvarar värdena i nyckel valvet som du skapade i föregående avsnitt.
    * **Hemlighet**: Välj det hemliga namnet **meddelande** som du skapade i föregående avsnitt.

## <a name="connect-to-key-vault"></a>Anslut till Key Vault

1. I den här självstudien använder du ett huvud namn för tjänsten för autentisering till Key Vault. Om du vill skapa tjänstens huvud namn använder du kommandot Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Den här åtgärden returnerar en serie med nyckel/värde-par:

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

1. Kör följande kommando för att ge tjänstens huvud namn åtkomst till ditt nyckel valv:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Kör följande kommando för att hämta ditt objekt-ID och Lägg sedan till det i appens konfiguration.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Skapa miljövariabler **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** och **AZURE_TENANT_ID**. Använd värdena för tjänstens huvud namn som visades i föregående steg. Kör följande kommandon på kommando raden och starta om kommando tolken för att ändringarna ska börja gälla:

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
> Dessa Key Vault autentiseringsuppgifter används bara i ditt program.  Ditt program autentiserar direkt med Key Vault med hjälp av dessa autentiseringsuppgifter utan att involvera app Configuration service.  Key Vault tillhandahåller autentisering för både ditt program och din app Configuration-tjänst utan att dela eller exponera nycklar.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uppdatera din kod för att använda en Key Vault referens

1. Skapa en miljö variabel som heter **APP_CONFIGURATION_ENDPOINT**. Ange dess värde till slut punkten för appens konfigurations lager. Du kan hitta slut punkten på bladet **åtkomst nycklar** i Azure Portal. Starta om kommando tolken så att ändringen börjar gälla. 


1. Öppna *bootstrap. Properties* i mappen *resurser* . Uppdatera den här filen så att den använder **APP_CONFIGURATION_ENDPOINT** -värdet. Ta bort alla referenser till en anslutnings sträng i den här filen. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Öppna *MessageProperties. java*. Lägg till en ny variabel med namnet *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Öppna *HelloController. java*. Uppdatera *GetMessage* -metoden för att inkludera det meddelande som hämtats från Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Skapa en ny fil med namnet *AzureCredentials. java* och Lägg till koden nedan.

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

1. Skapa en ny fil med namnet *AppConfiguration. java*. Och Lägg till koden nedan.

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

1. Skapa en ny fil i din resurs-META-INF-katalog med namnet *våren. factors* och Lägg till koden nedan.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Skapa ditt våren Boot-program med Maven och kör det, till exempel:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. När programmet har körts använder du *sväng* för att testa programmet, till exempel:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Du ser meddelandet som du angav i appens konfigurations arkiv. Du ser också meddelandet som du angav i Key Vault.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en konfigurations nyckel för appen som refererar till ett värde som lagras i Key Vault. Fortsätt till nästa självstudie om du vill lära dig hur du använder funktions flaggor i ditt Java våren-program.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./quickstart-feature-flag-spring-boot.md)
