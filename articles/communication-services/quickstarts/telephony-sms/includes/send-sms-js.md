---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: fc20396053dee32ac7976139a634b4592389ab5f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488351"
---
Kom igång med Azure Communication Services med hjälp av Java Script SMS-klientprogrammet för kommunikations tjänster för att skicka SMS-meddelanden.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktiva LTS-och underhålls LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett SMS-aktiverat telefonnummer. [Hämta ett telefonnummer](../get-phone-number.md).

### <a name="prerequisite-check"></a>Krav kontroll

- I en terminal-eller kommando fönster kör `node --version` du för att kontrol lera att Node.js är installerat.
- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js program

Börja med att öppna Terminal-eller kommando fönstret, skapa en ny katalog för din app och navigera till den.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Kör `npm init -y` för att skapa en **package.jspå** en fil med standardinställningar.

```console
npm init -y
```

Använd en text redigerare för att skapa en fil med namnet **send-sms.js** i projektets rot Katalog. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

### <a name="install-the-package"></a>Installera paketet

Använd `npm install` kommandot för att installera Azure Communication Services SMS-klient biblioteket för Java Script.

```console
npm install @azure/communication-sms --save
```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS-klient biblioteket för Node.js.

| Namn                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Den här klassen krävs för alla SMS-funktioner. Du instansierar det med din prenumerations information och använder den för att skicka SMS-meddelanden. |
| SendSmsOptions | Det här gränssnittet innehåller alternativ för att konfigurera leverans rapportering. Om `enable_delivery_report` är inställt på `true` , genereras en händelse när leveransen lyckades. |
| SendMessageRequest | Det här gränssnittet är modellen för att skapa SMS-begäran (t. ex. Konfigurera till och från telefonnummer och SMS-innehåll). |

## <a name="authenticate-the-client"></a>Autentisera klienten

Importera **SmsClient** från klient biblioteket och instansiera det med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../../create-communication-resource.md#store-your-connection-string)för din resurs.

Lägg till följande kod i **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande

Skicka ett SMS-meddelande genom att anropa- `send` metoden. Lägg till den här koden i slutet av **send-sms.js**:

```javascript
async function main() {
  await smsClient.send({
    from: "<leased-phone-number>",
    to: ["<to-phone-number>"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true //Optional parameter
  });
}

main();
```

Ersätt `<leased-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number>` med telefonnumret som du vill skicka ett meddelande till.

`enableDeliveryReport`Parametern är en valfri parameter som du kan använda för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.

## <a name="run-the-code"></a>Kör koden

Använd `node` kommandot för att köra den kod som du lade till i **send-sms.js** -filen.

```console

node ./send-sms.js

```
