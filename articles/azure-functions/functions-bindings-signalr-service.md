---
title: Bindningar för Azure Functions SignalR-tjänst
description: Förstå hur du använder signalerar tjänst bindningar med Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763497"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

I den här uppsättningen artiklar förklaras hur du autentiserar och skickar meddelanden i real tid till klienter som är anslutna till [Azure SignalR-tjänsten](https://azure.microsoft.com/services/signalr-service/) med hjälp av SignalR tjänst bindningar i Azure Functions. Azure Functions stöder indata- och utdatabindningar för SignalR Service.

| Åtgärd | Typ |
|---------|---------|
| Hantera meddelanden från SignalR-tjänsten | [Utlös bindning](./functions-bindings-signalr-service-trigger.md) |
| Returnera URL för tjänst slut punkt och åtkomsttoken | [Binda in](./functions-bindings-signalr-service-input.md) |
| Skicka signal tjänst meddelanden |[Utgående bindning](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg] rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Mer information om hur du konfigurerar och använder SignalR-tjänsten och Azure Functions tillsammans finns i [Azure Functions utveckling och konfiguration med Azure SignalR-tjänsten](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Kommentar bibliotek (endast Java)

Om du vill använda Signalerare för signalering i Java-funktioner måste du lägga till ett beroende till artefakten *Azure-Functions Java-Library-signaler* (version 1,0 eller senare) till *pom.xml* -filen.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Nästa steg

- [Hantera meddelanden från SignalR-tjänsten (Utlös bindning)](./functions-bindings-signalr-service-trigger.md)
- [Returnera URL: en för tjänstens slut punkt och åtkomsttoken (ingående bindning)](./functions-bindings-signalr-service-input.md)
- [Skicka signal tjänst meddelanden (utgående bindning)](./functions-bindings-signalr-service-output.md)