---
ms.openlocfilehash: 956c92c5c020f892b8148e9d43d403b1099fbdba
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113032"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Krav kontroll

- I en terminal-eller kommando fönster kör `node --version` du för att kontrol lera att Node.js är installerat.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js program

Börja med att öppna Terminal-eller kommando fönstret, skapa en ny katalog för din app och navigera till den.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

Skapa en fil med namnet **phone-numbers-quickstart.js** i roten för den katalog som du nyss skapade. Lägg till följande kodfragment:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera klient biblioteket för Azure Communication Services-telefonnumret för Java Script.

```console
npm install @azure/communication-phone-numbers --save
```

`--save`Alternativet lägger till biblioteket som ett beroende i **package.jsi** filen.

## <a name="authenticate-the-client"></a>Autentisera klienten

Importera **PhoneNumbersClient** från klient biblioteket och instansiera det med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur du [hanterar din resurs anslutnings sträng](../../create-communication-resource.md#store-your-connection-string).

Lägg till följande kod överst i **phone-numbers-quickstart.js**:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Hantera telefonnummer

### <a name="search-for-available-phone-numbers"></a>Sök efter tillgängliga telefonnummer

För att kunna köpa telefonnummer måste du först söka efter tillgängliga telefonnummer. Om du vill söka efter telefonnummer anger du rikt nummer, tilldelnings typ, [telefonnummerets kapacitet](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefonnummer typ](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)och kvantitet. Observera att det är valfritt att ange rikt nummer för det kostnads fria telefonnumret för nummer.

Lägg till följande kodfragment i din `main` funktion:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Inköps telefonnummer

Resultatet av att söka efter telefonnummer är en `PhoneNumberSearchResult` . Detta innehåller en `searchId` som kan skickas till inköps nummer-API: n för att hämta numren i sökningen. Observera att om du anropar API: t för inköps samtal debiteras ditt Azure-konto.

Lägg till följande kodfragment i din `main` funktion:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Uppdatera funktioner för telefonnummer

Med ett telefonnummer som nu har köpts kan du lägga till följande kod för att uppdatera dess funktioner:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Hämta inköps telefonnummer

Efter ett inköps nummer kan du hämta det från klienten. Lägg till följande kod i din `main` funktion för att hämta telefonnumret som du precis har köpt:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Du kan också hämta alla inköpta telefonnummer.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Versions nummer

Nu kan du släppa det köpta telefonnumret. Lägg till kodfragmentet nedan till din `main` funktion:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Kör koden

Använd `node` kommandot för att köra den kod som du lade till i **phone-numbers-quickstart.js** -filen.

```console
node phone-numbers-quickstart.js
```