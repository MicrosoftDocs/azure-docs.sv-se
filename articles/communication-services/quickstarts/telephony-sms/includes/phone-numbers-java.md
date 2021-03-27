---
ms.openlocfilehash: 06033f13b1f63849408646835a844c2eafa8d64f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629421"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) version 8 eller senare.
- [Apache maven](https://maven.apache.org/download.cgi). – en distribuerad kommunikations tjänst resurs och en anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).

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
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
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
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Autentisera telefonnumret-klienten

PhoneNumberClientBuilder har Aktiver ATS för att använda Azure Active Directory autentisering
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .httpClient(httpClient)
    .buildClient();
```

Du kan också använda slut punkten och åtkomst nyckeln från kommunikations resursen för att autentisera möjliga.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Hantera telefonnummer

### <a name="search-for-available-phone-numbers"></a>Sök efter tillgängliga telefonnummer

För att kunna köpa telefonnummer måste du först söka efter tillgängliga telefonnummer. Om du vill söka efter telefonnummer anger du rikt nummer, tilldelnings typ, [telefonnummerets kapacitet](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefonnummer typ](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)och kvantitet. Observera att det är valfritt att ange rikt nummer för det kostnads fria telefonnumret för nummer.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Köp telefonnummer

Resultatet av att söka efter telefonnummer är ett PhoneNumberSearchResult. Detta innehåller en `searchId` som kan skickas till inköps nummer-API: n för att hämta numren i sökningen. Observera att om du anropar API: t för inköps samtal debiteras ditt Azure-konto.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Hämta telefonnummer (er)

Efter ett inköps nummer kan du hämta det från klienten.
```java
AcquiredPhoneNumber phoneNumber = phoneNumberClient.getPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Du kan också hämta alla inköpta telefonnummer.
``` java
PagedIterable<AcquiredPhoneNumber> phoneNumbers = createPhoneNumberClient().listPhoneNumbers(Context.NONE);
AcquiredPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Uppdatera funktioner för telefonnummer

Med ett inköpt nummer kan du uppdatera funktionerna.
```java
PhoneNumberCapabilitiesRequest capabilitiesRequest = new PhoneNumberCapabilitiesRequest();
capabilitiesRequest
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND);
AcquiredPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+18001234567", capabilitiesRequest, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling());
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms());
```

### <a name="release-phone-number"></a>Versions nummer

Du kan släppa ett inköpt telefonnummer.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+18001234567", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
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

Utdata från appen beskriver varje åtgärd som slutförs:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->