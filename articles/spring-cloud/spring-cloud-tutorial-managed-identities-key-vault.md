---
title: Självstudie – hanterad identitet för att ansluta Key Vault till Azure våren Cloud-appen
description: Konfigurera hanterad identitet för att ansluta Key Vault till en Azure våren Cloud-App
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c091241a4928b3f498be7944559aa5b91c6fecf0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705066"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Självstudie: använda en hanterad identitet för att ansluta Key Vault till en Azure våren Cloud-App

**Den här artikeln gäller för:** ✔️ Java

Den här artikeln visar hur du skapar en hanterad identitet för en Azure våren Cloud-App och använder den för att få åtkomst till Azure Key Vault.

Azure Key Vault kan användas för säker lagring och strikt kontroll av åtkomst till token, lösen ord, certifikat, API-nycklar och andra hemligheter för din app. Du kan skapa en hanterad identitet i Azure Active Directory (AAD) och autentisera till en tjänst som stöder AAD-autentisering, inklusive Key Vault, utan att behöva Visa autentiseringsuppgifter i din kod.

## <a name="prerequisites"></a>Förutsättningar

* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
* [Installera Azure CLI-versionen 2.0.67 eller högre](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Installera maven 3,0 eller senare](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resurs grupp som innehåller både Key Vault och våren Cloud med kommandot [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Konfigurera din Key Vault
Om du vill skapa en Key Vault använder du kommandot [AZ-valv skapa](/cli/azure/keyvault?view=azure-cli-latest&preserve-view=true#az-keyvault-create):

> [!Important]
> Varje Key Vault måste ha ett unikt namn. Ersätt <ditt-nyckel valv> med namnet på din Key Vault i följande exempel.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Anteckna den returnerade `vaultUri` , som kommer att ha formatet "https://<Your-Vault.Azure.net"-namn>. ". Den kommer att användas i följande steg.

Du kan nu placera en hemlighet i Key Vault med kommandot AZ för [nyckel valv](/cli/azure/keyvault/secret?view=azure-cli-latest&preserve-view=true#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Skapa moln tjänst och app i Azure våren
När du har installerat motsvarande tillägg skapar du en Azure våren Cloud-instans med Azure CLI-kommandot `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
I följande exempel skapas en app `springapp` med namnet med en systemtilldelad hanterad identitet, enligt begäran från `--assign-identity` parametern.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Anteckna den returnerade `url` , som kommer att visas i formatet `https://<your-app-name>.azuremicroservices.io` . Den kommer att användas i följande steg.


## <a name="grant-your-app-access-to-key-vault"></a>Ge appen åtkomst till Key Vault
Används `az keyvault set-policy` för att ge rätt åtkomst i Key Vault för din app.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Använd `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` för att ta bort åtkomsten för appen när systemtilldelad hanterad identitet har inaktiverats.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Bygg en exempel källa Boot-app med våren Boot starter
Den här appen kommer att ha åtkomst till att hämta hemligheter från Azure Key Vault. Använd Start programmet: [Azure Key Vault hemligheter fjäder start starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault läggs till som en instans av våren **PropertySource**.  Hemligheter som lagras i Azure Key Vault kan användas för att komma åt och användas som en eventuell extern konfigurations egenskap, till exempel egenskaper i filer. 

1. Generera ett exempel projekt från start.spring.io med Azure Key Vault våren starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Ange Key Vault i din app. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet för Azure våren Cloud Apps lägger du till egenskaper med följande innehåll i src/main/Resources/Application. Properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Måste lägga till Key Vault-URL: en i `application.properties` som ovan. Annars får inte Key Vault-webbadressen fångas under körning.

3. Lägg till kod exemplet i src/main/Java/com/exempel/demo/DemoApplication. java. Den hämtar anslutnings strängen från Key Vault. 

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

    Om du öppnar pom.xml visas beroendet av `azure-keyvault-secrets-spring-boot-starter` . Lägg till det här beroendet i ditt projekt i pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Paketera din exempel App. 

    ```azurecli
    mvn clean package
    ```

5. Nu kan du distribuera din app till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Testa din app genom att komma åt den offentliga slut punkten eller test slut punkten.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Meddelandet "har fått värdet hemlig connectionString från Key Vault https://<ditt-nyckel valv>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; databas = databas;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Bygg exempel på våren Boot-appen med Java SDK

Det här exemplet kan ställa in och hämta hemligheter från Azure Key Vault. [Klient biblioteket för Azure Key Vault Secret för Java](/java/api/overview/azure/security-keyvault-secrets-readme?preserve-view=true&view=azure-java-stablelibrary) tillhandahåller stöd för Azure Active Directory token-autentisering i Azure SDK. Den innehåller en uppsättning **TokenCredential** -implementeringar som kan användas för att skapa Azure SDK-klienter för att stödja AAD-token-autentisering.

Med Azure Key Vault Secrets klient bibliotek kan du lagra och kontrol lera åtkomsten till token, lösen ord, API-nycklar och andra hemligheter på ett säkert sätt. I biblioteket finns åtgärder för att skapa, Hämta, uppdatera, ta bort, rensa, säkerhetskopiera, återställa och visa en lista över hemligheter och dess versioner.

1. Klona ett exempel projekt. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Ange Key Vault i din app. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Om du vill använda hanterad identitet för Azure våren Cloud Apps lägger du till egenskaper med följande innehåll i *src/main/Resources/Application. Properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Ta med [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder?preserve-view=true&view=azure-java-stable) för att hämta token från Azure Active Directory och [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?preserve-view=true&view=azure-java-stable) för att ange eller hämta hemligheter från Key Vault i din kod.

    Hämta exemplet från [MainController. java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) för det klonade exempelprojektet.

    Ta även med `azure-identity` och `azure-security-keyvault-secrets` som beroende av pom.xml. Hämta exemplet från [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) av ett klonat exempel projekt. 

4. Paketera din exempel App. 

    ```azurecli
    mvn clean package
    ```

5. Distribuera nu appen till Azure med Azure CLI-kommandot  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Få åtkomst till den offentliga slut punkten eller test slut punkten för att testa appen. 

    Börja med att hämta värdet för den hemlighet som du angav i Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Meddelandet "har fått värdet hemlig connectionString från Key Vault https://<ditt-nyckel valv>. vault.azure.net/: JDBC: SQLServer://SERVER.database.windows.net: 1433; databas = databas;".

    Skapa en hemlighet och hämta den sedan med Java SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Meddelandet "har fått värdet hemligt test från Key Vault https://<ditt-nyckel valv>. vault.azure.net: lyckades". 

## <a name="next-steps"></a>Nästa steg

* [Så här kommer du åt Storage BLOB med hanterad identitet i Azure våren Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Lär dig mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autentisera Azure våren Cloud med Key Vault i GitHub-åtgärder](./spring-cloud-github-actions-key-vault.md)
