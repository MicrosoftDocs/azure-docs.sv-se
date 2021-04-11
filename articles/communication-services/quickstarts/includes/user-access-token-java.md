---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a0f3e3547c38df63bdab77cf378525072d1e9ad4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125707"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).
- En distribuerad kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

Öppna terminalen eller kommando fönstret. Navigera till den katalog där du vill skapa ditt Java-program. Kör kommandot nedan för att skapa Java-projektet från maven-archetype-snabb starts mal len.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Du ser att aktiviteten "generera" skapade en katalog med samma namn som `artifactId` . Under den här katalogen innehåller src/main/Java-katalogen projekt käll koden, `src/test/java directory` innehåller test källan och `pom.xml` filen är projektets projekt objekt modell eller POM.

### <a name="install-the-package"></a>Installera paketet

Öppna **pom.xml** -filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Navigera till katalogen */src/main/Java/com/Communication/QuickStart*
1. Öppna filen *app. java* i redigeraren
1. Ersätt `System.out.println("Hello world!");` instruktionen
1. Lägg till `import` direktiv

Använd följande kod för att börja:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `CommunicationIdentityClient` med resursens åtkomst nyckel och slut punkt. Lär dig hur du [hanterar din resurs anslutnings sträng](../create-communication-resource.md#store-your-connection-string). Dessutom kan du initiera klienten med valfri anpassad HTTP-klient som implementerar `com.azure.core.http.HttpClient` gränssnittet.

Lägg till följande kod i `main`-metoden:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .buildClient();
```

Du kan också ange hela anslutnings strängen med hjälp av `connectionString()` funktionen i stället för att tillhandahålla slut punkten och åtkomst nyckeln.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

Om du har konfigurerat identiteter, se [Använd hanterade](../managed-identity.md)identiteter, kan du även autentisera med hanterad identitet.
```java
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
TokenCredential credential = new DefaultAzureCredentialBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(credential)
        .buildClient();
```

## <a name="create-an-identity"></a>Skapa en identitet

Azure Communication Services upprätthåller en Lightweight Identity-katalog. Använd `createUser` metoden för att skapa en ny post i katalogen med en unik `Id` . Lagra mottagen identitet med mappning till programmets användare. Till exempel genom att lagra dem i program serverns databas. Identiteten krävs senare för att utfärda åtkomsttoken.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Utfärda åtkomsttoken

Använd `getToken` metoden för att utfärda en åtkomsttoken för redan befintlig kommunikations tjänst identitet. Parameter `scopes` definierar uppsättning primitiver som auktoriserar denna åtkomsttoken. Se [listan över åtgärder som stöds](../../concepts/authentication.md). En ny instans av parametern `user` kan konstrueras baserat på en sträng representation av Azure Communication Service-identiteten.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Skapa en token för identitet och utfärdare i ett anrop

Du kan också använda metoden ' createUserAndToken ' för att skapa en ny post i katalogen med en unik `Id` och utfärda en åtkomsttoken.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Åtkomsttoken är korta autentiseringsuppgifter som måste återutfärdas. Om du inte gör det kan det orsaka störningar i programmets användar upplevelse. `expiresAt`Egenskapen anger livs längden för åtkomsttoken.

## <a name="refresh-access-tokens"></a>Uppdatera åtkomsttoken

Om du vill uppdatera en åtkomsttoken använder du `CommunicationUserIdentifier` objektet för att utfärda följande:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Återkalla åtkomsttoken

I vissa fall kan du uttryckligen återkalla åtkomsttoken. Till exempel när ett programs användare ändrar lösen ordet som de använder för att autentisera till din tjänst. Metoden `revokeTokens` ogiltig förklarade alla aktiva åtkomsttoken som utfärdats till identiteten.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Ta bort en identitet

Om du tar bort en identitet återkallar du alla aktiva åtkomsttoken och förhindrar att du utfärdar åtkomsttoken för identiteten. Det tar också bort allt beständigt innehåll som är associerat med identiteten.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Kör koden

Navigera till den katalog som innehåller *pom.xml* -filen och kompilera projektet med hjälp av följande `mvn` kommando.

```console
mvn compile
```

Sedan skapar du paketet.

```console
mvn package
```

Kör följande `mvn` kommando för att köra appen.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
