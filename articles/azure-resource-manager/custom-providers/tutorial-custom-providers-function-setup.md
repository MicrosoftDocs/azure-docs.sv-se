---
title: Konfigurera Azure Functions
description: Den här självstudien går igenom hur du skapar en Azure Function-app och konfigurerar den så att den fungerar med Azure-anpassade leverantörer
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737327"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure Functions för Azure-anpassade leverantörer

En anpassad Provider är ett kontrakt mellan Azure och en slut punkt. Med anpassade providers kan du ändra arbets flöden i Azure. Den här självstudien visar hur du konfigurerar en Azure Function-app så att den fungerar som en anpassad Provider-slutpunkt.

## <a name="create-the-azure-function-app"></a>Skapa Azure Function-appen

> [!NOTE]
> I den här självstudien skapar du en enkel tjänst slut punkt som använder en Azure Function-app. En anpassad Provider kan dock använda en offentligt tillgänglig slut punkt. Alternativen omfattar Azure Logic Apps, Azure API Management och Web Apps funktionen i Azure App Service.

För att starta den här självstudien bör du först följa självstudien [skapa din första Azure Function-app i Azure Portal](../../azure-functions/functions-get-started.md). Den här självstudien skapar en .NET Core-webhook-funktion som kan ändras i Azure Portal. Det är även grunden för den aktuella självstudien.

## <a name="install-azure-table-storage-bindings"></a>Installera lagrings bindningar för Azure Table

Så här installerar du Azure Table Storage-bindningar:

1. Gå till fliken **integrera** för HttpTrigger.
1. Välj **+ ny Indatatyp**.
1. Välj **Azure-Table Storage**.
1. Installera Microsoft. Azure. WebJobs. Extensions. Storage-tillägget om det inte redan är installerat.
1. I rutan **tabell parameter namn** anger du **tableStorage**.
1. Skriv **myCustomResources** i rutan **tabell namn** .
1. Välj **Spara** för att spara den uppdaterade indataparametern.

![Översikt över anpassad Provider med tabell bindningar](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Uppdatera RESTful HTTP-metoder

Konfigurera Azure-funktionen så att den inkluderar den anpassade providern RESTful förfrågnings metoder:

1. Gå till fliken **integrera** för HttpTrigger.
1. Under **valda HTTP-metoder** väljer du **Hämta**, **post**, **ta bort** och **Placera**.

![Översikt över anpassad Provider visar HTTP-metoder](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Lägg till Azure Resource Manager NuGet-paket

> [!NOTE]
> Om projekt filen C# saknas i projekt katalogen kan du lägga till den manuellt. Eller så kommer det att visas efter att Microsoft. Azure. WebJobs. Extensions. Storage-tillägget har installerats i Function-appen.

Uppdatera sedan C#-projektfilen för att inkludera användbara NuGet-bibliotek. De här biblioteken gör det lättare att parsa inkommande begär Anden från anpassade providers. Följ stegen för att [lägga till tillägg från portalen](../../azure-functions/functions-bindings-register.md) och uppdatera C#-projekt filen så att den innehåller följande paket referenser:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Följande XML-element är ett exempel på en C#-projekt fil:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure Function-app som fungerar som en Azure-anpassad Provider-slutpunkt.

Information om hur du skapar en RESTful anpassad Provider-slutpunkt finns i [Självstudier: skapa en RESTful anpassad Provider-slutpunkt](./tutorial-custom-providers-function-authoring.md).