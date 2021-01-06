---
title: Så här riktar du Azure Functions runtime-versioner
description: Azure Functions stöder flera versioner av körnings miljön. Lär dig hur du anger körnings versionen av en Function-app som finns i Azure.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 46bf7849888033b2bbb7e9b9669ee3eae4de10e9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916532"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Så här riktar du Azure Functions runtime-versioner

En Function-App körs på en viss version av Azure Functions Runtime. Det finns tre huvud versioner: [1. x, 2. x och 3. x](functions-versions.md). Som standard skapas Function apps i version 3. x i körnings miljön. Den här artikeln förklarar hur du konfigurerar en Function-app i Azure så att den körs på den version du väljer. Information om hur du konfigurerar en lokal utvecklings miljö för en angiven version finns i [kod-och test Azure Functions lokalt](functions-run-local.md).

Hur du manuellt riktar in en speciell version beror på om du kör Windows eller Linux.

## <a name="automatic-and-manual-version-updates"></a>Automatiska och manuella versions uppdateringar

_Det här avsnittet gäller inte när du kör din Function-app [i Linux](#manual-version-updates-on-linux)._

Azure Functions låter dig rikta en specifik version av körningen i Windows med hjälp av `FUNCTIONS_EXTENSION_VERSION` program inställningen i en Function-app. Function-appen sparas på den angivna huvud versionen tills du uttryckligen väljer att flytta till en ny version. Om du bara anger huvud versionen uppdateras Function-appen automatiskt till nya lägre versioner av körnings miljön när de blir tillgängliga. Nya del versioner bör inte införa ändringar. 

Om du anger en lägre version (till exempel "2.0.12345") fästs Function-appen till den aktuella versionen tills du ändrar den explicit. Äldre del versioner tas regelbundet bort från produktions miljön. När detta inträffar körs din Function-app på den senaste versionen i stället för versionen som anges i `FUNCTIONS_EXTENSION_VERSION` . Därför bör du snabbt lösa eventuella problem med din Function-app som kräver en särskild del version, så att du kan använda den som mål i stället för den överordnade versionen. Mindre versions borttagningar visas i [app service meddelanden](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE]
> Om du fäster till en särskild huvud version av Azure Functions och sedan försöker publicera till Azure med hjälp av Visual Studio öppnas ett dialog fönster där du uppmanas att uppdatera till den senaste versionen eller avbryta publiceringen. Undvik detta genom att lägga till `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` egenskapen i `.csproj` filen.

När en ny version är offentligt tillgänglig ger en prompt i portalen dig chansen att flytta upp till den versionen. När du har flyttat till en ny version kan du alltid använda `FUNCTIONS_EXTENSION_VERSION` program inställningen för att gå tillbaka till en tidigare version.

I följande tabell visas `FUNCTIONS_EXTENSION_VERSION` värdena för varje huvud version för att aktivera automatiska uppdateringar:

| Högre version | `FUNCTIONS_EXTENSION_VERSION` värde |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2x  | `~2` |
| 1.x  | `~1` |

En ändring i körnings versionen leder till att en Function-app startar om.

## <a name="view-and-update-the-current-runtime-version"></a>Visa och uppdatera den aktuella körnings versionen

_Det här avsnittet gäller inte när du kör din Function-app [i Linux](#manual-version-updates-on-linux)._

Du kan ändra den körnings version som används av din Function-app. På grund av möjligheten att bryta ändringar kan du bara ändra kör tids versionen innan du har skapat några funktioner i din Function-app. 

> [!IMPORTANT]
> Även om körnings versionen bestäms av `FUNCTIONS_EXTENSION_VERSION` inställningen bör du göra den här ändringen i Azure Portal och inte genom att ändra inställningen direkt. Detta beror på att portalen validerar dina ändringar och gör andra relaterade ändringar efter behov.

# <a name="portal"></a>[Portal](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Med hjälp av Azure Portal kan du inte ändra körnings versionen för en Function-app som redan innehåller funktioner.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Du kan också visa och ställa in `FUNCTIONS_EXTENSION_VERSION` från Azure CLI.  

Använd Azure CLI för att visa den aktuella körnings versionen med kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

I den här koden ersätter du `<function_app>` med namnet på din Function-app. Ersätt också `<my_resource_group>` med namnet på resurs gruppen för din Function-app. 

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

Du kan uppdatera `FUNCTIONS_EXTENSION_VERSION` inställningen i Function-appen med kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) .

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

Ersätt `<FUNCTION_APP>` med namnet på din Function-app. Ersätt också `<RESOURCE_GROUP>` med namnet på resurs gruppen för din Function-app. Ersätt också `<VERSION>` med antingen en enskild version eller `~3` , `~2` , eller `~1` .

Du kan köra det här kommandot från [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kod exempel. Du kan också använda [Azure CLI lokalt](/cli/azure/install-azure-cli) för att köra det här kommandot när du har kört [AZ-inloggning](/cli/azure/reference-index#az-login) för att logga in.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Använd följande cmdlet för att kontrol lera Azure Functions Runtime: 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

Ersätt `<FUNCTION_APP>` med namnet på din Function-app och `<RESOURCE_GROUP>` . Det aktuella värdet för `FUNCTIONS_EXTENSION_VERSION` inställningen returneras i hash-tabellen.

Använd följande skript för att ändra funktions körningen:

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

Som tidigare ersätter du `<FUNCTION_APP>` med namnet på din Function-app och `<RESOURCE_GROUP>` med namnet på resurs gruppen. Ersätt även `<VERSION>` med den aktuella versionen eller huvud versionen, till exempel `~2` eller `~3` . Du kan kontrol lera det uppdaterade värdet för `FUNCTIONS_EXTENSION_VERSION` inställningen i den returnerade hash-tabellen. 

---

Function-appen startas om när ändringen har gjorts till program inställningen.

## <a name="manual-version-updates-on-linux"></a>Manuella versions uppdateringar på Linux

Om du vill fästa en Linux Function-app till en viss värd version anger du bild-URL i fältet ' LinuxFxVersion ' i plats konfiguration. Exempel: om vi vill fästa en Node 10 Function-app för att säga värd version 3.0.13142-

För **Linux app service/elastisk Premium Apps** – Ställ in `LinuxFxVersion` på `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice` .

För **användning av Linux-appar** – Ställ in `LinuxFxVersion` på `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10` .


# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-linux)

Du kan visa och ställa in `LinuxFxVersion` från Azure CLI.  

Använd Azure CLI för att visa den aktuella körnings versionen med kommandot [AZ functionapp config show](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group>
```

I den här koden ersätter du `<function_app>` med namnet på din Function-app. Ersätt också `<my_resource_group>` med namnet på resurs gruppen för din Function-app. 

Du ser `linuxFxVersion` i följande utdata, som har trunkerats för tydlighetens skull:

```output
{
  ...

  "kind": null,
  "limits": null,
  "linuxFxVersion": <LINUX_FX_VERSION>,
  "loadBalancing": "LeastRequests",
  "localMySqlEnabled": false,
  "location": "West US",
  "logsDirectorySizeLimit": 35,
   ...
}
```

Du kan uppdatera `linuxFxVersion` inställningen i Function-appen med kommandot [AZ functionapp config set](/cli/azure/functionapp/config) .

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

Ersätt `<FUNCTION_APP>` med namnet på din Function-app. Ersätt också `<RESOURCE_GROUP>` med namnet på resurs gruppen för din Function-app. Ersätt också `<LINUX_FX_VERSION>` med värdena som beskrivs ovan.

Du kan köra det här kommandot från [Azure Cloud Shell](../cloud-shell/overview.md) genom att välja **prova** i föregående kod exempel. Du kan också använda [Azure CLI lokalt](/cli/azure/install-azure-cli) för att köra det här kommandot när du har kört [AZ-inloggning](/cli/azure/reference-index#az-login) för att logga in.


På samma sätt startar Function-appen om när ändringen har gjorts till platsen config.

> [!NOTE]
> Observera att inställningen `LinuxFxVersion` för bild-URL direkt för förbruknings apparna kommer att välja ut från plats hållare och andra kall start optimeringar.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Rikta in dig på 2,0-körningsmiljön i din lokala utvecklings miljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för körnings versioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)
