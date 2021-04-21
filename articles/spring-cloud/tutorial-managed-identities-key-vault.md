---
title: 'Självstudie: Hanterad identitet för att ansluta Key Vault'
description: Konfigurera hanterad identitet för att ansluta Key Vault till en Azure Spring Cloud app
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 870a04af244d18826e1041316895f746e27870eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786593"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Självstudie: Använda en hanterad identitet för att ansluta Key Vault till en Azure Spring Cloud app

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln visar hur du skapar en hanterad identitet för en Azure Spring Cloud-app och använder den för att komma Azure Key Vault.

Azure Key Vault kan användas för att lagra och kontrollera åtkomsten till token, lösenord, certifikat, API-nycklar och andra hemligheter för din app på ett säkert sätt. Du kan skapa en hanterad identitet i Azure Active Directory (AAD) och autentisera till alla tjänster som stöder AAD-autentisering, inklusive Key Vault, utan att behöva visa autentiseringsuppgifter i koden.

## <a name="prerequisites"></a>Förutsättningar

* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* [Installera Azure CLI version 2.0.67 eller senare](/cli/azure/install-azure-cli)
* [Installera Maven 3.0 eller högre](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp som ska innehålla både Key Vault och Spring Cloud med kommandot [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Konfigurera Key Vault
Skapa en Key Vault med kommandot [az keyvault create](/cli/azure/keyvault#az_keyvault_create):

> [!Important]
> Varje Key Vault måste ha ett unikt namn. Ersätt <ditt-nyckelvalvsnamn> namnet på ditt Key Vault i följande exempel.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Anteckna det returnerade `vaultUri` , som ska ha formatet "https://<your-keyvault-name>.vault.azure.net". Det kommer att användas i följande steg.

Nu kan du placera en hemlighet i Key Vault med kommandot [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Skapa Azure Spring Cloud tjänst och app
När du har installerat motsvarande tillägg skapar du Azure Spring Cloud instans med Azure CLI-kommandot `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
I följande exempel skapas en app med `springapp` namnet med en system tilldelad hanterad identitet, enligt `--assign-identity` parametern .

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Anteckna den returnerade `url` , som ska ha formatet `https://<your-app-name>.azuremicroservices.io` . Den kommer att användas i följande steg.


## <a name="grant-your-app-access-to-key-vault"></a>Ge appen åtkomst till Key Vault
Använd `az keyvault set-policy` för att bevilja rätt åtkomst i Key Vault för din app.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Använd `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` för att ta bort åtkomsten för din app när den system tilldelade hanterade identiteten har inaktiverats.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Skapa en exempelapp Spring Boot med Spring Boot starter
Den här appen har åtkomst till att hämta hemligheter från Azure Key Vault. Använd startappen: [Azure Key Vault Secrets Spring boot starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault läggs till som en instans av Spring **PropertySource**.  Hemligheter som lagras Azure Key Vault kan enkelt nås och användas precis som alla externaliserade konfigurationsegenskaper, till exempel egenskaper i filer. 

1. Generera ett exempelprojekt från start.spring.io med Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Ange Key Vault i din app. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet Azure Spring Cloud appar lägger du till egenskaper med innehållet nedan i src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Måste lägga till nyckelvalvs-URL:en `application.properties` i som ovan. Annars kan nyckelvalvs-URL:en inte avbildas under körning.

3. Lägg till kodexe exemplet i src/main/java/com/example/demo/DemoApplication.java. Den hämtar anslutningssträngen från Key Vault. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Om du pom.xml visas beroendet för `azure-keyvault-secrets-spring-boot-starter` . Lägg till det här beroendet i projektet i pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Paketera exempelappen. 

    ```azurecli
    mvn clean package
    ```

5. Nu kan du distribuera din app till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Testa appen genom att öppna den offentliga slutpunkten eller testslutpunkten.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Du ser meddelandet "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Skapa exempelapp Spring Boot med Java SDK

Det här exemplet kan ange och hämta hemligheter från Azure Key Vault. Det [Azure Key Vault Secret-klientbiblioteket för Java ger](/java/api/overview/azure/security-keyvault-secrets-readme) stöd Azure Active Directory för tokenautentisering i Hela Azure SDK. Den innehåller en uppsättning **TokenCredential-implementeringar** som kan användas för att konstruera Azure SDK-klienter för att stödja AAD-tokenautentisering.

Med Azure Key Vault Secret-klientbiblioteket kan du på ett säkert sätt lagra och kontrollera åtkomsten till token, lösenord, API-nycklar och andra hemligheter. Biblioteket erbjuder åtgärder för att skapa, hämta, uppdatera, ta bort, rensa, kvarvarande, återställa och lista hemligheterna och dess versioner.

1. Klona ett exempelprojekt. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Ange Key Vault i din app. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet Azure Spring Cloud appar lägger du till egenskaper med följande innehåll i *src/main/resources/application.properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Inkludera [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) för att hämta token från Azure Active Directory och [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) för att ange eller hämta hemligheter från Key Vault i koden.

    Hämta exemplet från [MainController.java för](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) det klonade exempelprojektet.

    Inkludera även `azure-identity` och som beroende i `azure-security-keyvault-secrets` pom.xml. Hämta exemplet från [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) av det klonade exempelprojektet. 

4. Paketera exempelappen. 

    ```azurecli
    mvn clean package
    ```

5. Distribuera nu appen till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Öppna den offentliga slutpunkten eller testslutpunkten för att testa din app. 

    Börja med att hämta värdet för din hemlighet som du anger i Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Du ser meddelandet "Successfully got the value of secret connectionString from Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

    Skapa nu en hemlighet och hämta den med hjälp av Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Du ser meddelandet "Successfully got the value of secret test from Key Vault https://<your-keyvault-name>.vault.azure.net: success". 

## <a name="next-steps"></a>Nästa steg

* [Så här kommer du åt Lagringsblob med hanterad identitet i Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Så här aktiverar du system tilldelad hanterad identitet för Azure Spring Cloud program](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Läs mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autentisera Azure Spring Cloud med Key Vault i GitHub Actions](./spring-cloud-github-actions-key-vault.md)
