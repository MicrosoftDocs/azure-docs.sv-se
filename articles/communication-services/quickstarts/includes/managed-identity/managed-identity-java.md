---
ms.openlocfilehash: b51f52e24ca843abd94a8511e86b3193a797edd5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511092"
---
## <a name="additional-prerequisites-for-java"></a>Ytterligare förutsättningar för Java
För Java behöver du också:
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

Öppna terminalen eller kommandofönstret. Gå till katalogen där du vill skapa Java-programmet. Kör kommandot nedan för att generera Java-projektet från mallen maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Du ser att aktiviteten "generate" skapade en katalog med samma namn som `artifactId` . Under den här katalogen innehåller katalogen src/main/java projektkällkoden, innehåller testkällan och filen är projektets `src/test/java directory` Project Object Model eller `pom.xml` POM.

### <a name="install-the-package"></a>Installera paketet

Öppna **pom.xml** i textredigeraren. Lägg till följande beroendeelement i gruppen med beroenden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande `import` -direktiv i koden för att använda AZURE Identity- och Azure Communication-ID:erna.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>Skapa en DefaultAzureCredential

Vi använder [DefaultAzureCredential för den](/java/api/com.azure.identity.defaultazurecredential) här snabbstarten. Dessa autentiseringsuppgifter är lämpliga för produktions- och utvecklingsmiljöer. Eftersom det behövs för varje åtgärd ska vi skapa den i `App.java` klassen . Lägg till följande överst i `App.java` klassen.

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-managed-identities"></a>Utfärda en token med hanterade identiteter

Nu ska vi lägga till kod som använder de autentiseringsuppgifter som skapats för att utfärda en VoIP-åtkomsttoken. Vi anropar den här koden senare.

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-managed-identities"></a>Skicka ett SMS med hanterade identiteter

Som ett annat exempel på hur du använder hanterade identiteter lägger vi till den här koden som använder samma autentiseringsuppgifter för att skicka ett SMS:

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>Skriva Main-metoden

Din bör redan ha en Main-metod. Nu ska vi lägga till kod som anropar vår tidigare skapade kod för att demonstrera användningen `App.java` av hanterade identiteter:
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

Den slutliga `App.java` bör se ut så här:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
}
```

## <a name="run-the-code"></a>Kör koden

Navigera till katalogen som innehåller *pom.xml* och kompilera projektet med hjälp av följande `mvn` kommando.

```console
mvn compile
```

Skapa sedan paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De slutliga utdata bör likna följande:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey..A
Sending SMS using Managed Identities
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```

