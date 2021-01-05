---
title: Snabb start – Azure Key Vault hemligt klient bibliotek för Java
description: Innehåller en snabb start för klient biblioteket Azure Key Vault Secret för Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 35133b32360f65d70aa1931b31fac6886fd00b02
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732962"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Snabb start: Azure Key Vault hemligt klient bibliotek för Java
Kom igång med Azure Key Vault hemliga klient biblioteket för Java. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter.

Ytterligare resurser:

* [Källkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [Referensdokumentation för API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktdokumentation](index.yml)
* [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare
- [Apache maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli) och [Apache maven](https://maven.apache.org) i ett Linux-terminalfönster.

## <a name="setting-up"></a>Konfigurera
Den här snabb starten använder Azure Identity-biblioteket med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. mer information finns i [autentisera klienten med klient biblioteket för Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logga in på Azure
1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

   Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

   Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="create-a-new-java-console-app"></a>Skapa en ny Java-konsol-app
I ett konsol fönster använder du `mvn` kommandot för att skapa en ny Java-konsol-app med namnet `akv-secrets-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Resultatet från att generera projektet ser ut ungefär så här:

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

Ändra katalogen till den nya `akv-secrets-java/` mappen.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Installera paketet
Öppna *pom.xml* -filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

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

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv
Skapa en åtkomst princip för nyckel valvet som ger hemliga behörigheter till ditt användar konto.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>Ange miljövariabler
Det här programmet använder nyckel valvets namn som en miljö variabel som kallas `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS eller Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objekt modell
Med det Azure Key Vault hemliga klient biblioteket för Java kan du hantera hemligheter. I avsnittet [kod exempel](#code-examples) visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Hela konsol programmet är [nedan](#sample-code).

## <a name="code-examples"></a>Kodexempel
### <a name="add-directives"></a>Lägg till direktiv
Lägg till följande direktiv överst i koden:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient
I den här snabb starten används en inloggad användare för att autentisera till Key Vault, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska en hanterad identitet tilldelas till en App Service eller virtuell dator. Mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I exemplet nedan expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". I det här exemplet används klassen ["DefaultAzureCredential ()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) , som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information finns i [Azure Credential Authentication](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Spara en hemlighet
Nu när ditt program är autentiserat kan du ange en hemlighet i ditt nyckel valv med hjälp av `secretClient.setSecret` metoden. Detta kräver ett namn för hemligheten – vi har tilldelat värdet "hemligt" till `secretName` variabeln i det här exemplet.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Du kan kontrol lera att hemligheten har angetts med kommandot [AZ-valvets hemliga show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet
Nu kan du hämta den tidigare angivna hemligheten med- `secretClient.getSecret` metoden.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Du kan nu komma åt värdet för den hämtade hemligheten med `retrievedSecret.getValue()` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet
Slutligen tar vi bort hemligheten från nyckel valvet med- `secretClient.beginDeleteSecret` metoden.

Hemlig borttagning är en tids krävande åtgärd, som du kan använda för att avsöka förloppet eller vänta tills den är klar.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

Du kan kontrol lera att hemligheten har tagits bort med kommandot [AZ-valv för hemligt show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckel valvet och motsvarande resurs grupp.

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
I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet, hämtat det och tog sedan bort det. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Så här [säkrar du åtkomst till ett nyckel valv](../general/secure-your-key-vault.md)
