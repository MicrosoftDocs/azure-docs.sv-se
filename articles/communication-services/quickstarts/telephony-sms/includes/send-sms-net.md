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
ms.openlocfilehash: a118dfceb73aca0897ba0f116ce3c5462368f6c3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488368"
---
Kom igång med Azure Communication Services med hjälp av kommunikations tjänsterna C# SMS-klient biblioteket för att skicka SMS-meddelanden.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Den senaste versionen av [klient biblioteket för .net Core](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativ system.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett SMS-aktiverat telefonnummer. [Hämta ett telefonnummer](../get-phone-number.md).

### <a name="prerequisite-check"></a>Krav kontroll

- Kör kommandot i ett terminalfönster-eller kommando fönster `dotnet` för att kontrol lera att .net-klient biblioteket är installerat.
- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `SmsQuickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: **program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Ändra katalogen till den nya app-mappen och Använd `dotnet build` kommandot för att kompilera ditt program.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services SMS-klient biblioteket för .NET-paketet med hjälp av `dotnet add package` kommandot.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.3
```

Lägg till ett `using` direktiv överst i **program.cs** för att inkludera `Azure.Communication` namn området.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS-klient biblioteket för C#.

| Namn                                       | Beskrivning                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Den här klassen krävs för alla SMS-funktioner. Du instansierar det med din prenumerations information och använder den för att skicka SMS-meddelanden.                           |
| SendSmsOptions | Den här klassen innehåller alternativ för att konfigurera leverans rapportering. Om enable_delivery_report har angetts till True genereras en händelse när leveransen lyckades |

## <a name="authenticate-the-client"></a>Autentisera klienten

 Öppna **program.cs** i en text redigerare och ersätt bröd texten i- `Main` metoden med kod för att initiera en `SmsClient` med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../../create-communication-resource.md#store-your-connection-string)för din resurs.


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Skicka ett SMS-meddelande

Skicka ett SMS-meddelande genom att anropa Send-metoden. Lägg till den här koden i slutet av `Main` metoden i **program.cs**:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

Ersätt `<leased-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänst resursen och `<to-phone-number>` med telefonnumret som du vill skicka ett meddelande till.

`EnableDeliveryReport`Parametern är en valfri parameter som du kan använda för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `dotnet run` kommandot.

```console
dotnet run
```

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS)
