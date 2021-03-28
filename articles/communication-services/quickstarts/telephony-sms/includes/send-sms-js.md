---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644348"
---
Kom igång med Azure Communication Services med hjälp av Java Script SMS SDK för kommunikations tjänster för att skicka SMS-meddelanden.

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

Använd `npm install` kommandot för att installera Azure Communication Services SMS SDK för Java Script.

```console
npm install @azure/communication-sms --save
```

I det här `--save` alternativet visas biblioteket som ett beroende i **package.jsi** filen.

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS SDK för Node.js.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Den här klassen krävs för alla SMS-funktioner. Du instansierar det med din prenumerations information och använder den för att skicka SMS-meddelanden. |
| SmsSendRequest | Det här gränssnittet är modellen för att skapa SMS-begäran (t. ex. Konfigurera till och från telefonnummer och SMS-innehåll). |
| SmsSendOptions | Det här gränssnittet innehåller alternativ för att konfigurera leverans rapportering. Om `enableDeliveryReport` är inställt på `true` , genereras en händelse när leveransen lyckas. |
| SmsSendResult               | Den här klassen innehåller resultatet från SMS-tjänsten.                                          |

## <a name="authenticate-the-client"></a>Autentisera klienten

Importera **SmsClient** från SDK och instansiera den med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur du [hanterar din resurs anslutnings sträng](../../create-communication-resource.md#store-your-connection-string).

Skapa och öppna en fil med namnet **send-sms.js** och Lägg till följande kod:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Skicka ett 1: N SMS-meddelande

Om du vill skicka ett SMS-meddelande till en lista över mottagare anropar du `send` funktionen från SmsClient med en lista över mottagarnas telefonnummer (om du vill skicka ett meddelande till en enskild mottagare ska du bara inkludera en siffra i listan). Lägg till den här koden i slutet av **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Ersätt `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number-1>` och `<to-phone-number-2>` med de telefonnummer som du vill skicka ett meddelande till.

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Skicka ett 1: N SMS-meddelande med alternativ

Du kan också skicka ett alternativ-objekt för att ange om leverans rapporten ska vara aktive rad och för att ange anpassade taggar.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Du bör ersätta `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen `<to-phone-number-1>` och `<to-phone-number-2>` med telefonnummer som du vill skicka ett meddelande till.

> [!WARNING]
> Observera att telefonnummer måste anges i formatet E. 164 internationellt standard. (t. ex.: + 14255550123).

`enableDeliveryReport`Parametern är en valfri parameter som du kan använda för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.
`tag` är en valfri parameter som du kan använda för att tillämpa en tagg i leverans rapporten.

## <a name="run-the-code"></a>Kör koden

Använd `node` kommandot för att köra den kod som du lade till i **send-sms.js** -filen.

```console

node ./send-sms.js

```
