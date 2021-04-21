---
title: Så här riktar du Azure Functions körningsversioner
description: Azure Functions stöder flera versioner av körningen. Lär dig hur du anger körningsversionen av en funktionsapp som finns i Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 9a9f8fca1c39fd0251df3e3a8da3d789aae4d3d6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779281"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här riktar du Azure Functions körningsversioner

En funktionsapp körs på en specifik version av Azure Functions-körningen. Det finns tre huvudversioner: [3.x, 2.x och 1.x.](functions-versions.md) Som standard skapas funktionsappar i version 3.x av körningen. I den här artikeln förklaras hur du konfigurerar en funktionsapp i Azure så att den körs på den version du väljer. Information om hur du konfigurerar en lokal utvecklingsmiljö för en viss version finns i [Koda och testa Azure Functions lokalt.](functions-run-local.md)

Hur du manuellt riktar in dig på en viss version beror på om du kör Windows eller Linux.

## <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versionsuppdateringar

_Det här avsnittet gäller inte när du kör funktionsappen [på Linux](#manual-version-updates-on-linux)._

Azure Functions kan du ange en specifik version av körningen i Windows som mål med hjälp av `FUNCTIONS_EXTENSION_VERSION` programinställningen i en funktionsapp. Funktionsappen behålls på den angivna huvudversionen tills du uttryckligen väljer att flytta till en ny version. Om du bara anger huvudversionen uppdateras funktionsappen automatiskt till nya mindre versioner av körningen när de blir tillgängliga. Nya mindre versioner bör inte introducera större ändringar. 

Om du anger en delversion (till exempel "2.0.12345") fästs funktionsappen på den specifika versionen tills du uttryckligen ändrar den. Äldre mindre versioner tas regelbundet bort från produktionsmiljön. Om delversionen tas bort går funktionsappen tillbaka till att köras på den senaste versionen i stället för den version som angetts i `FUNCTIONS_EXTENSION_VERSION` . Därför bör du snabbt lösa eventuella problem med funktionsappen som kräver en specifik delversion. Sedan kan du gå tillbaka till huvudversionen som mål. Mindre versionsborttagningar tillkännages [i App Service meddelanden](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Om du fäster en viss huvudversion av Azure Functions och sedan försöker publicera till Azure med hjälp av Visual Studio visas ett dialogfönster där du uppmanas att uppdatera till den senaste versionen eller avbryta publiceringen. Undvik detta genom att lägga till `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` egenskapen i `.csproj` filen.

När en ny version är offentligt tillgänglig ger en uppmaning i portalen dig möjlighet att flytta upp till den versionen. När du har flyttat till en ny version kan du alltid använda `FUNCTIONS_EXTENSION_VERSION` programinställningen för att gå tillbaka till en tidigare version.

I följande tabell visas värdena `FUNCTIONS_EXTENSION_VERSION` för varje huvudversion för att aktivera automatiska uppdateringar:

| Högre version | `FUNCTIONS_EXTENSION_VERSION` Värde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

En ändring i körningsversionen gör att en funktionsapp startas om.

>[!NOTE]
>.NET-funktionsappar fästa för `~2.0` att välja bort den automatiska uppgraderingen till .NET Core 3.1. Mer information finns i Functions [v2.x-överväganden.](functions-dotnet-class-library.md#functions-v2x-considerations)  

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körningsversionen

_Det här avsnittet gäller inte när du kör funktionsappen [på Linux](#manual-version-updates-on-linux)._

Du kan ändra körningsversionen som används av funktionsappen. På grund av risken för större ändringar kan du bara ändra körningsversionen innan du har skapat några funktioner i funktionsappen. 

> [!IMPORTANT]
> Även om körningsversionen bestäms av inställningen bör du bara göra den här ändringen i Azure Portal inte `FUNCTIONS_EXTENSION_VERSION` genom att ändra inställningen direkt. Det beror på att portalen verifierar dina ändringar och gör andra relaterade ändringar efter behov.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Med Azure Portal kan du inte ändra körningsversionen för en funktionsapp som redan innehåller funktioner.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Du kan också visa och ange `FUNCTIONS_EXTENSION_VERSION` från Azure CLI.  

Använd Azure CLI och visa den aktuella körningsversionen med kommandot [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

I den här koden `<function_app>` ersätter du med namnet på din funktionsapp. Ersätt också `<my_resource_group>` med namnet på resursgruppen för funktionsappen. 

Du ser `FUNCTIONS_EXTENSION_VERSION` i följande utdata, som har trunkerats för tydlighetens skull:

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

Du kan uppdatera `FUNCTIONS_EXTENSION_VERSION` inställningen i funktionsappen med kommandot [az functionapp config appsettings set.](/cli/azure/functionapp/config/appsettings)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Ersätt `<FUNCTION_APP>` med namnet på din funktionsapp. Ersätt också `<RESOURCE_GROUP>` med namnet på resursgruppen för funktionsappen. Ersätt också `<VERSION>` med antingen en specifik version, `~3` eller , eller `~2` `~1` .

Välj **Prova i** föregående kodexempel för att köra kommandot i [Azure Cloud Shell](../cloud-shell/overview.md). Du kan också köra [Azure CLI lokalt för att](/cli/azure/install-azure-cli) köra det här kommandot. När du kör lokalt måste du först köra [az login för](/cli/azure/reference-index#az_login) att logga in.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Använd följande Azure Functions för att kontrollera körningskörningen: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Ersätt `<FUNCTION_APP>` med namnet på din funktionsapp och `<RESOURCE_GROUP>` . Det aktuella värdet för `FUNCTIONS_EXTENSION_VERSION` inställningen returneras i hash-tabellen.

Använd följande skript för att ändra Functions-körningen:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Som tidigare ersätter `<FUNCTION_APP>` du med namnet på din funktionsapp och med namnet på `<RESOURCE_GROUP>` resursgruppen. Ersätt också `<VERSION>` med den specifika versionen eller huvudversionen, till exempel eller `~2` `~3` . Du kan kontrollera det uppdaterade värdet för inställningen `FUNCTIONS_EXTENSION_VERSION` i den returnerade hash-tabellen. 

---

Funktionsappen startas om efter att ändringen har gjorts i programinställningen.

## <a name="manual-version-updates-on-linux"></a>Manuella versionsuppdateringar i Linux

Om du vill fästa en Linux-funktionsapp till en specifik värdversion anger du avbildnings-URL:en i fältet "LinuxFxVersion" i platskonfigurationen. Exempel: om vi vill fästa en nod 10-funktionsapp till exempel värdversion 3.0.13142 –

För **Linux App Service/elastiska Premium-appar** – Ställ in `LinuxFxVersion` på `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

För **appar med Linux-förbrukning** – Ställ in `LinuxFxVersion` på `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .

# <a name="portal"></a>[Portal](#tab/portal)

Visning och ändring av platskonfigurationsinställningar för funktionsappar stöds inte i Azure Portal. Använd Azure CLI i stället.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Du kan visa och ange `LinuxFxVersion` med hjälp av Azure CLI.  

Om du vill visa den aktuella körningsversionen använder du med [kommandot az functionapp config show.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

I den här koden `<function_app>` ersätter du med namnet på funktionsappen. Ersätt även `<my_resource_group>` med namnet på resursgruppen för funktionsappen. Det aktuella värdet för `linuxFxVersion` returneras.

Om du vill `linuxFxVersion` uppdatera inställningen i funktionsappen använder du [kommandot az functionapp config set.](/cli/azure/functionapp/config)

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Ersätt `<FUNCTION_APP>` med namnet på din funktionsapp. Ersätt även `<RESOURCE_GROUP>` med namnet på resursgruppen för funktionsappen. Ersätt också med `<LINUX_FX_VERSION>` värdet för en specifik bild enligt beskrivningen ovan.

Du kan köra det här kommandot [från Azure Cloud Shell](../cloud-shell/overview.md) genom att **välja Prova** i föregående kodexempel. Du kan också använda [Azure CLI lokalt för att](/cli/azure/install-azure-cli) köra det här kommandot när du har kört az [login](/cli/azure/reference-index#az_login) för att logga in.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure PowerShell kan inte användas för att ange `linuxFxVersion` just nu. Använd Azure CLI i stället.

---

Funktionsappen startas om när ändringen har gjorts i platskonfigurationen.

> [!NOTE]
> För appar som körs i en förbrukningsplan kan `LinuxFxVersion` inställningen till en viss bild öka kallstartstiderna. Det beror på att om du fäster på en viss bild förhindrar Functions från att använda några kallstartsoptimeringar. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Rikta in 2.0-körningen i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se Versionsanteckningar för körningsversioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
