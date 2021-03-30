---
title: Registrera Azure Functions bindnings tillägg
description: Lär dig att registrera ett Azure Functions bindnings tillägg baserat på din miljö.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88689581"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrera Azure Functions bindnings tillägg

Från och med Azure Functions version 2. x inkluderar Functions-körningen bara HTTP och timer-utlösare som standard. Andra [utlösare och bindningar](./functions-triggers-bindings.md) är tillgängliga som separata paket.

Program i .NET-klass biblioteks funktioner använder bindningar som är installerade i projektet som NuGet-paket. Med tilläggs paket kan non-.NET Functions-appar använda samma bindningar utan att behöva hantera .NET-infrastrukturen.

I följande tabell visas när och hur du registrerar bindningar.

| Utvecklingsmiljö |Registrering<br/> i funktioner 1. x  |Registrering<br/> i functions 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automatiskt|Autokorrigering<sup>*</sup>|
|Non-.NET-språk|Automatiskt|Använd [tilläggs paket](#extension-bundles) (rekommenderas) eller [installera tillägg uttryckligen](#explicitly-install-extensions)|
|C#-klass bibliotek med Visual Studio|[Använda NuGet-verktyg](#vs)|[Använda NuGet-verktyg](#vs)|
|C#-klass bibliotek med Visual Studio Code|Ej tillämpligt|[Använd .NET Core CLI](#vs-code)|

<sup>*</sup> Portalen använder tilläggs paket.

## <a name="access-extensions-in-non-net-languages"></a>Åtkomst tillägg på non-.NET språk

För Java-, Java Script-, PowerShell-, python-och anpassad hanterarmappning-funktioner rekommenderar vi att du använder tilläggs paket för att få åtkomst till bindningar. I de fall där tilläggs paket inte kan användas kan du uttryckligen installera bindnings tillägg.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Paket för tillägg

Paket för tillägg är ett sätt att lägga till en kompatibel uppsättning bindnings tillägg i din Function-app. Du aktiverar tilläggs paket i appens *host.js* fil.

Du kan använda tilläggs paket med version 2. x och senare versioner av Functions-körningen.

Tilläggs paketen har versions hantering. Varje version innehåller en bestämd uppsättning bindnings tillägg som verifieras för att fungera tillsammans. Välj en paket version baserat på de tillägg som du behöver i din app.

Lägg till ett tilläggs paket i din Function-app genom att lägga till `extensionBundle` avsnittet i *host.jspå*. I många fall kommer Visual Studio Code och Azure Functions Core Tools automatiskt att lägga till den åt dig.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

I följande tabell visas tillgängliga versioner av standard versionen av *Microsoft. Azure. functions. ExtensionBundle* och länkar till de tillägg som de innehåller.

| Paket version | Version i host.jspå | Inkluderade tillägg |
| --- | --- | --- |
| 1.x | `[1.*, 2.0.0)` | Se [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) som används för att generera paketet |
| 2x | `[2.*, 3.0.0)` | Se [extensions.js](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) som används för att generera paketet |

> [!NOTE]
> Även om du kan ange ett anpassat versions intervall i host.jspå rekommenderar vi att du använder ett versions värde från den här tabellen.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Installera tillägg uttryckligen

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Installera tillägg från NuGet på .NET-språk

För ett C#-projekt med klass biblioteks funktioner bör du installera tillägg direkt. Tilläggs paket är särskilt utformade för projekt som inte är C#-klass biblioteks-baserade.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> C- \# klass bibliotek med Visual Studio

I **Visual Studio** kan du installera paket från Package Manager-konsolen med kommandot [install-Package](/nuget/tools/ps-ref-install-package) , som du ser i följande exempel:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Namnet på det paket som används för en angiven bindning anges i referens artikeln för den bindningen. Ett exempel finns i [avsnittet paket i referens artikeln Service Bus bindning](functions-bindings-service-bus.md#functions-1x).

Ersätt `<TARGET_VERSION>` i exemplet med en angiven version av paketet, till exempel `3.0.0-beta5` . Giltiga versioner visas på de enskilda paket sidorna på [NuGet.org](https://nuget.org). De huvud versioner som motsvarar Functions Runtime 1. x eller 2. x anges i referens artikeln för bindningen.

Om du använder `Install-Package` för att referera till en bindning behöver du inte använda [tilläggs paket](#extension-bundles). Den här metoden är speciell för klass bibliotek som skapats i Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> C#-klass bibliotek med Visual Studio Code

I **Visual Studio Code** installerar du paket för ett C#-klass biblioteks projekt från kommando tolken med kommandot [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) i .net Core cli. Följande exempel visar hur du lägger till en bindning:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI kan bara användas för Azure Functions 2. x-utveckling.

Ersätt `<BINDING_TYPE_NAME>` med namnet på det paket som innehåller den bindning du behöver. Du hittar den önskade bindnings referens artikeln i [listan över bindningar som stöds](./functions-triggers-bindings.md#supported-bindings).

Ersätt `<TARGET_VERSION>` i exemplet med en angiven version av paketet, till exempel `3.0.0-beta5` . Giltiga versioner visas på de enskilda paket sidorna på [NuGet.org](https://nuget.org). De huvud versioner som motsvarar Functions Runtime 1. x eller 2. x anges i referens artikeln för bindningen.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Azure Function-utlösare och bindnings exempel](./functions-bindings-example.md)
