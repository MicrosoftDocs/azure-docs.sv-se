---
title: Snabbstart – Azure Key Vault hemligt klientbibliotek för Java
description: Innehåller en snabbstart för Azure Key Vault Secret-klientbiblioteket för Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: b45bb353ae32f1037fde7dc5d518472089edf12f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766343"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Snabbstart: Azure Key Vault Hemligt klientbibliotek för Java
Kom igång med Azure Key Vault Secret-klientbiblioteket för Java. Följ stegen nedan för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Ytterligare resurser:

* [Källkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [Referensdokumentation för API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktdokumentation](index.yml)
* [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabbstarten förutsätter att du [kör Azure CLI](/cli/azure/install-azure-cli) och Apache [Maven i](https://maven.apache.org) ett Linux-terminalfönster.

## <a name="setting-up"></a>Inrätta
Den här snabbstarten använder Azure Identity-biblioteket med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logga in på Azure
1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

   Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

   Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="create-a-new-java-console-app"></a>Skapa en ny Java-konsolapp
I ett konsolfönster använder du kommandot `mvn` för att skapa en ny Java-konsolapp med namnet `akv-secrets-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Utdata från att generera projektet ser ut ungefär så här:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Ändra katalogen till den nyligen skapade `akv-secrets-java/` mappen.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Installera paketet
Öppna *pom.xml* i textredigeraren. Lägg till följande beroendeelement i gruppen med beroenden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv
Skapa en åtkomstprincip för ditt nyckelvalv som beviljar hemliga behörigheter till ditt användarkonto.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>Ange miljövariabler
Det här programmet använder nyckelvalvsnamnet som en miljövariabel med namnet `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektmodell
Med Azure Key Vault Secret-klientbiblioteket för Java kan du hantera hemligheter. Avsnittet [Kodexempel](#code-examples) visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsolappen finns [under](#sample-code).

## <a name="code-examples"></a>Kodexempel
### <a name="add-directives"></a>Lägga till direktiv
Lägg till följande -direktiv överst i koden:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient
I den här snabbstarten används en inloggad användare för att autentisera till Key Vault, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska en hanterad identitet tilldelas till en App Service eller virtuell dator. Mer information finns i Översikt [över hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI:t i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder klassen ["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identiteter. Mer information finns i [Standardautentisering med Azure-autentiseringsuppgifter.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Spara en hemlighet
Nu när programmet har autentiserats kan du placera en hemlighet i nyckelvalvet med hjälp av `secretClient.setSecret` metoden . Detta kräver ett namn för hemligheten – vi har tilldelat värdet "mySecret" till variabeln `secretName` i det här exemplet.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Du kan kontrollera att hemligheten har angetts med [kommandot az keyvault secret](/cli/azure/keyvault/secret?#az_keyvault_secret_show) show:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet
Nu kan du hämta den tidigare konfigurerade hemligheten med `secretClient.getSecret` metoden .

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Nu kan du komma åt värdet för den hämtade hemligheten med `retrievedSecret.getValue()` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet
Slutligen tar vi bort hemligheten från nyckelvalvet med `secretClient.beginDeleteSecret` metoden .

Borttagning av hemligheter är en långvarig åtgärd som du kan avse förloppet för eller vänta tills den har slutförts.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

Du kan kontrollera att hemligheten har tagits bort med kommandot [az keyvault secret](/cli/azure/keyvault/secret?#az_keyvault_secret_show) show:

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckelvalvet och motsvarande resursgrupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exempelkod
```java
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du ett nyckelvalv, lagrade en hemlighet, hämtade det och tog sedan bort det. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault utvecklarhandbok](../general/developers-guide.md)
- Skydda [åtkomst till ett nyckelvalv](../general/security-overview.md)
