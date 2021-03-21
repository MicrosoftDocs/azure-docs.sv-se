---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623189"
---
## <a name="add-to-your-functions-app"></a>Lägg till i functions-appen

### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

Om du arbetar med utlösaren och bindningarna måste du referera till rätt paket. NuGet-paketet används för .NET-klass bibliotek medan tilläggs paketet används för alla andra program typer.

| Språk                                        | Lägg till efter...                                   | Kommentarer 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installera [NuGet-paketet], version 3. x | |
| C#-skript, Java, Java Script, python, PowerShell | [Tilläggs paketet] registreras          | [Tillägget Azure-verktyg] rekommenderas för användning med Visual Studio Code. |
| C#-skript (endast online i Azure Portal)         | Lägga till en bindning                            | Om du vill uppdatera befintliga bindnings tillägg utan att behöva publicera om din Function-app, se [Uppdatera dina tillägg]. |

[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[paket för tillägg]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Uppdatera dina tillägg]: ../articles/azure-functions/functions-bindings-register.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs tillägget 5. x och högre

En ny version av tillägget Event Hubs bindningar är tillgänglig som ett [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1)för för hands versioner. I den här för hands versionen introduceras möjligheten att [ansluta med en identitet i stället för en hemlighet](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). För .NET-program ändras även de typer som du kan binda till, vilket ersätter typerna från `Microsoft.Azure.EventHubs` med nya typer från [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> För hands versionen ingår inte i ett tilläggs paket och måste installeras manuellt. Lägg till en referens till paketet för .NET-appar. För alla andra typer av appar, se [Uppdatera dina tillägg].

[core tools]: ./functions-run-local.md
[paket för tillägg]: ./functions-bindings-register.md#extension-bundles
[NuGet-paket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Uppdatera dina tillägg]: ./functions-bindings-register.md
[Tillägg för Azure-verktyg]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x-appar har automatiskt en referens till [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x.

## <a name="hostjson-settings"></a>host.jspå Inställningar
<a name="host-json"></a>

[host.js](../articles/azure-functions/functions-host-json.md#eventhub) filen innehåller inställningar som styr Event Hubs utlösnings beteende. Konfigurationen skiljer sig åt beroende på Azure Functions version.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]