---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 190524251d139e1421c1aac93d5a4dd523068a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958223"
---
## <a name="local-settings-file"></a>Fil för lokala inställningar

local.settings.jspå fil lagrar inställningar för appar, anslutnings strängar och inställningar som används av lokala utvecklingsverktyg. Inställningarna i local.settings.jspå filen används endast när du kör projekt lokalt. Den lokala inställnings filen har den här strukturen:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

De här inställningarna stöds när du kör projekt lokalt:

| Inställning      | Beskrivning                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | När den här inställningen är inställd på `true` krypteras alla värden med en lokal dator nyckel. Används med- `func settings` kommandon. Standardvärdet är `false`. Du kanske vill kryptera local.settings.jsfilen på den lokala datorn när den innehåller hemligheter, t. ex. tjänst anslutnings strängar. Värden dekrypterar automatiskt inställningarna när den körs. Använd `func settings decrypt` kommandot innan du försöker läsa lokalt krypterade inställningar. |
| **`Values`** | Matris med program inställningar och anslutnings strängar som används när ett projekt körs lokalt. Dessa nyckel värdes par (sträng sträng) motsvarar program inställningarna i din Function-app i Azure, t [`AzureWebJobsStorage`] . ex.. Många utlösare och bindningar har en egenskap som refererar till en app-inställning för anslutnings strängen, till exempel `Connection` för [Blob Storage-utlösaren](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). För dessa egenskaper behöver du en program inställning som definierats i `Values` matrisen. I följande tabell visas en lista över de inställningar som används ofta. <br/>Värden måste vara strängar och inte JSON-objekt eller matriser. Inställnings namn får inte innehålla kolon ( `:` ) eller dubbel understrykning ( `__` ). Dubbla understryknings tecken reserveras av körnings miljön och kolonet är reserverat för att stödja [beroende inmatning](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | Inställningarna i det här avsnittet anpassar funktionens värd process när du kör projekt lokalt. De här inställningarna är separata från host.jspå Inställningar, som också gäller när du kör projekt i Azure. |
| **`LocalHttpPort`** | Anger standard porten som används när du kör den lokala funktionens värd ( `func host start` och `func run` ). `--port`Kommando rads alternativet har företräde framför den här inställningen. När du till exempel kör i Visual Studio IDE kan du ändra port numret genom att gå till fönstret "projekt egenskaper-> fel sökning" och uttryckligen ange port numret i ett `host start --port <your-port-number>` kommando som kan anges i fältet "program argument". |
| **`CORS`** | Definierar de ursprung som tillåts för [resurs delning mellan ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Ursprung anges som en kommaavgränsad lista utan blank steg. Jokertecknet ( \* ) stöds, vilket tillåter begär Anden från alla ursprung. |
| **`CORSCredentials`** |  När den är inställd på `true` tillåter `withCredentials` begär Anden. |
| **`ConnectionStrings`** | En samling. Använd inte den här samlingen för de anslutnings strängar som används av funktions bindningarna. Den här samlingen används endast av ramverk som vanligt vis hämtar anslutnings strängar från `ConnectionStrings` avsnittet i en konfigurations fil, t. ex. [Entity Framework](/ef/ef6/). Anslutnings strängar i det här objektet läggs till i miljön med providertypen för [system. data. SqlClient](/dotnet/api/system.data.sqlclient). Objekt i den här samlingen publiceras inte i Azure med andra AppData. Du måste uttryckligen lägga till de här värdena i `Connection strings` samlingen av dina funktions program inställningar. Om du skapar en [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) i funktions koden bör du lagra värdet för anslutnings strängen med dina andra anslutningar i **program inställningarna** i portalen. |

Följande program inställningar kan inkluderas i **`Values`** matrisen när de körs lokalt:

| Inställning | Värden | Beskrivning |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Anslutnings sträng för lagrings konto eller<br/>`UseDevelopmentStorage=true`| Innehåller anslutnings strängen för ett Azure Storage-konto. Krävs när du använder andra utlösare än HTTP. Mer information finns i [`AzureWebJobsStorage`] referensen.<br/>När du har installerat [Azure Storage-emulatorn](../articles/storage/common/storage-use-emulator.md) lokalt och du har angett [`AzureWebJobsStorage`] `UseDevelopmentStorage=true` , använder kärn verktygen emulatorn. Emulatorn är användbar under utvecklingen, men du bör testa med en faktisk lagrings anslutning före distributionen.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Om du vill inaktivera en funktion när den körs lokalt lägger `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` du till i samlingen, där `<FUNCTION_NAME>` är namnet på funktionen. Mer information finns i [så här inaktiverar du funktioner i Azure Functions](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Anger mål språket för functions-körningen. Krävs för version 2. x och högre av Functions-körningen. Den här inställningen genereras för ditt projekt enligt Core-verktyg. Mer information finns i [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) referensen.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Anger att PowerShell 7 används när det körs lokalt. Om den inte anges används PowerShell Core 6. Den här inställningen används bara när du kör lokalt. När du kör i Azure bestäms PowerShell runtime-versionen av `powerShellVersion` plats konfigurations inställningen, som kan [ställas in i portalen](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
