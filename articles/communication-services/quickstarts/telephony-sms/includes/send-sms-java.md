---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 313832c29eb3f3f175fb20a60ba712937fdede0a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488363"
---
Kom igång med Azure Communication Services med hjälp av kommunikations tjänsterna Java SMS-klient bibliotek för att skicka SMS-meddelanden.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare.
- [Apache Maven](https://maven.apache.org/download.cgi).
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett SMS-aktiverat telefonnummer. [Hämta ett telefonnummer](../get-phone-number.md).

### <a name="prerequisite-check"></a>Krav kontroll

- I en terminal-eller kommando fönster kör `mvn -v` du för att kontrol lera att maven har installerats.
- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

Öppna terminalen eller kommando fönstret och navigera till den katalog där du vill skapa ditt Java-program. Kör kommandot nedan för att skapa Java-projektet från maven-archetype-snabb starts mal len.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Målet "generate" kommer att skapa en katalog med samma namn som artifactId. Under den här katalogen innehåller **src/main/Java-** katalogen projekt käll koden, **src/test/java-katalogen** innehåller test källan och **pom.xml** -filen är projektets projekt objekts modell eller POM.

### <a name="install-the-package"></a>Installera paketet

Öppna **pom.xml** -filen i text redigeraren. Lägg till följande beroende element i gruppen med beroenden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Lägg till `azure-core-http-netty` beroendet i **pom.xml** -filen.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Öppna **/src/main/Java/com/Communication/QuickStart/app.java** i en text redigerare, Lägg till import direktiv och ta bort `System.out.println("Hello world!");` instruktionen:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS-klient biblioteket för Java.

| Namn                                                             | Beskrivning                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Den här klassen skapar SmsClient. Du anger den med slut punkt, autentiseringsuppgift och en http-klient. |
| SmsClient                    | Den här klassen krävs för alla SMS-funktioner. Du använder den för att skicka SMS-meddelanden.                |
| SendSmsResponse               | Den här klassen innehåller svaret från SMS-tjänsten.                                          |
| PhoneNumber                   | Den här klassen innehåller information om telefonnummer

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `SmsClient` med anslutnings strängen. I koden nedan hämtas slut punkt och autentiseringsuppgifter för resursen från miljövariabler med namnet `COMMUNICATION_SERVICES_ENDPOINT_STRING` och `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (autentiseringsuppgiften är `Key` från Azure Portal. Lär dig hur [du hanterar anslutnings strängen](../../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till följande kod i `main`-metoden:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Du kan initiera klienten med valfri anpassad HTTP-klient som implementerar `com.azure.core.http.HttpClient` gränssnittet. Ovanstående kod visar användningen av [Azure Core nett-HTTP-klienten](/java/api/overview/azure/core-http-netty-readme) som tillhandahålls av `azure-core` .

Du kan också ange hela anslutnings strängen med hjälp av funktionen connectionString () i stället för att tillhandahålla slut punkten och åtkomst nyckeln. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande

Skicka ett SMS-meddelande genom att anropa sendMessage-metoden. Lägg till den här koden i slutet av `main` metoden:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Ersätt `<leased-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number>` med telefonnumret som du vill skicka ett meddelande till.

`enableDeliveryReport`Parametern är en valfri parameter som du kan använda för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Kör koden

Navigera till den katalog som innehåller **pom.xml** -filen och kompilera projektet med hjälp av `mvn` kommandot.

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