---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 0a59b18fa2c09cff943dbccd2203027cd72e146a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644314"
---
Kom igång med Azure Communication Services genom att använda kommunikations tjänsterna Java SMS SDK för att skicka SMS-meddelanden.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

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
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Lägg till `azure-core-http-netty` beroendet i **pom.xml** -filen.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Öppna **/src/main/Java/com/Communication/QuickStart/app.java** i en text redigerare, Lägg till import direktiv och ta bort `System.out.println("Hello world!");` instruktionen:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS SDK för Java.

| Name                                                             | Beskrivning                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Den här klassen skapar SmsClient. Du anger den med slut punkt, autentiseringsuppgift och en http-klient. |
| SmsClient                    | Den här klassen krävs för alla SMS-funktioner. Du använder den för att skicka SMS-meddelanden.                |
| SmsSendOptions               | Den här klassen innehåller alternativ för att lägga till anpassade taggar och konfigurera leverans rapportering. Om deliveryReportEnabled har angetts till True genereras en händelse när leveransen lyckades|                           |
| SmsSendResult                | Den här klassen innehåller resultatet från SMS-tjänsten.                                          |

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en `SmsClient` med anslutnings strängen. (Autentiseringsuppgiften är `Key` från Azure Portal. Lär dig hur du [hanterar din resurs anslutnings sträng](../../create-communication-resource.md#store-your-connection-string).

Lägg till följande kod i `main`-metoden:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Du kan initiera klienten med valfri anpassad HTTP-klient som implementerar `com.azure.core.http.HttpClient` gränssnittet. Ovanstående kod visar användningen av [Azure Core nett-HTTP-klienten](/java/api/overview/azure/core-http-netty-readme) som tillhandahålls av `azure-core` .

Du kan också ange hela anslutnings strängen med hjälp av funktionen connectionString () i stället för att tillhandahålla slut punkten och åtkomst nyckeln. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Skicka ett 1:1 SMS-meddelande

Om du vill skicka ett SMS-meddelande till en enda mottagare anropar du `send` metoden från SmsClient med ett enda mottagares telefonnummer. Du kan också skicka valfria parametrar för att ange om leverans rapporten ska vara aktive rad och för att ange anpassade taggar.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

Ersätt `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number>` med ett telefonnummer som du vill skicka ett meddelande till.

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Skicka ett 1: N SMS-meddelande med alternativ
Om du vill skicka ett SMS-meddelande till en lista över mottagare anropar du `send` metoden med en lista över mottagarens telefonnummer. Du kan också skicka valfria parametrar för att ange om leverans rapporten ska vara aktive rad och för att ange anpassade taggar.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Du bör ersätta `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number-1>` `<to-phone-number-2>` med telefonnummer som du vill skicka ett meddelande till.

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 14255550123).

`setDeliveryReportEnabled`Metoden används för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.

`setTag`Metoden används för att tillämpa en tagg för leverans rapporten.

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