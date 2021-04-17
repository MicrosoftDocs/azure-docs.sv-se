---
title: Inaktivera funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ef3886c4b9b73f87238bf386d1320ecbac8ad181
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374904"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Inaktivera funktioner i Azure Functions

Den här artikeln förklarar hur du inaktiverar en funktion i Azure Functions. Om *du* vill inaktivera en funktion innebär det att körningen ignorerar den automatiska utlösare som har definierats för funktionen. På så sätt kan du förhindra att en viss funktion körs utan att stoppa hela funktionsappen.

Det rekommenderade sättet att inaktivera en funktion är med en appinställning i formatet `AzureWebJobs.<FUNCTION_NAME>.Disabled` inställt på `true` . Du kan skapa och ändra den här programinställningen på flera olika sätt, inklusive med hjälp av [Azure CLI](/cli/azure/) och från funktionens översiktsflik i [Azure Portal](https://portal.azure.com).  

> [!NOTE]  
> När du inaktiverar en HTTP-utlöst funktion med hjälp av metoderna som beskrivs i den här artikeln kan slutpunkten fortfarande vara tillgänglig när den körs på den lokala datorn.  

## <a name="disable-a-function"></a>Inaktivera en funktion

# <a name="portal"></a>[Portal](#tab/portal)

Använd knapparna **Aktivera** **och** Inaktivera på funktionens **översiktssida.** Dessa knappar fungerar genom att ändra värdet för `AzureWebJobs.<FUNCTION_NAME>.Disabled` appinställningen. Den här funktionsspecifika inställningen skapas första gången den inaktiveras. 

![Funktionstillståndsväxel](media/disable-function/function-state-switch.png)

Även om du publicerar till funktionsappen från ett lokalt projekt kan du fortfarande använda portalen för att inaktivera funktioner i funktionsappen. 

> [!NOTE]  
> Den portalintegrerade testfunktionen ignorerar `Disabled` inställningen. Det innebär att en inaktiverad funktion fortfarande körs när den startas från **testfönstret** i portalen. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

I Azure CLI använder du kommandot [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) för att skapa och ändra appinställningen. Följande kommando inaktiverar en funktion med namnet genom `QueueTrigger` att skapa en appinställning med namnet och `AzureWebJobs.QueueTrigger.Disabled` ange den till `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Om du vill återaktivera funktionen kör du samma kommando igen med värdet `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Kommandot [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) lägger till eller uppdaterar en programinställning. Följande kommando inaktiverar en funktion med namnet genom `QueueTrigger` att skapa en appinställning med namnet och `AzureWebJobs.QueueTrigger.Disabled` ange den till `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Om du vill återaktivera funktionen kör du samma kommando igen med värdet `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funktioner i ett fack

Som standard gäller appinställningarna även för appar som körs i distributionsfack. Du kan dock åsidosätta appinställningen som används av facket genom att ange en platsspecifik appinställning. Du kanske till exempel vill att en funktion ska vara aktiv i produktion men inte under distributionstestning, till exempel en funktion som utlöses av en timer. 

Så här inaktiverar du en funktion endast på mellanlagringsplatsen:

# <a name="portal"></a>[Portal](#tab/portal)

Navigera till platsinstansen för funktionsappen genom att välja **Distributionsfack** **under** Distribution, välja ditt fack och **sedan Funktioner** i platsinstansen.  Välj din funktion och använd sedan **knapparna Aktivera** **och** Inaktivera på funktionens **översiktssida.** Dessa knappar fungerar genom att ändra värdet för `AzureWebJobs.<FUNCTION_NAME>.Disabled` appinställningen. Den här funktionsspecifika inställningen skapas första gången den inaktiveras. 

Du kan också lägga till appinställningen med `AzureWebJobs.<FUNCTION_NAME>.Disabled` namnet med värdet för i Konfiguration `true` **för** platsinstansen. När du lägger till en fackspecifik appinställning ska du markera rutan **Distributionsfackinställning.** Detta behåller inställningsvärdet med facket under växlingar.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Om du vill återaktivera funktionen kör du samma kommando igen med värdet `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell stöder för närvarande inte den här funktionen.

---

Mer information finns i [Azure Functions distributionsfack](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

Funktioner kan inaktiveras på samma sätt när de körs lokalt. Om du vill inaktivera en funktion med namnet lägger du till en post i `HttpExample` samlingen Värden local.settings.jspå filen enligt följande:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>Andra metoder

Även om programinställningsmetoden rekommenderas för alla språk och alla körningsversioner finns det flera andra sätt att inaktivera funktioner. Dessa metoder, som varierar beroende på språk och körningsversion, underhålls för bakåtkompatibilitet. 

### <a name="c-class-libraries"></a>C#-klassbibliotek

I en klassbiblioteksfunktion kan du också använda `Disable` attributet för att förhindra att funktionen utlöses. Med det här attributet kan du anpassa namnet på den inställning som används för att inaktivera funktionen. Använd den version av attributet som gör att du kan definiera en konstruktorparameter som refererar till en boolesk appinställning, enligt följande exempel:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Med den här metoden kan du aktivera och inaktivera funktionen genom att ändra appinställningen, utan att kompilera om eller omdistribuera. Om du ändrar en appinställning startas funktionsappen om, så den inaktiverade tillståndsändringen känns igen omedelbart.

Det finns också en konstruktor för parametern som inte accepterar en sträng för inställningsnamnet. Den här versionen av attributet rekommenderas inte. Om du använder den här versionen måste du kompilera om och distribuera om projektet för att ändra funktionens inaktiverade tillstånd.

### <a name="functions-1x---scripting-languages"></a>Functions 1.x – skriptspråk

I version 1.x kan du också använda egenskapen för filenfunction.jsför att uppmana `disabled` körningen att inte utlösa en funktion.  Den här metoden fungerar bara för skriptspråk som C#-skript och JavaScript. Egenskapen `disabled` kan anges till eller till namnet på en `true` appinställning:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
eller 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

I det andra exemplet inaktiveras funktionen när det finns en appinställning med namnet IS_DISABLED är inställd på `true` eller 1.

>[!IMPORTANT]  
>Portalen använder programinställningar för att inaktivera v1.x-funktioner. När en programinställning står i konflikt function.jsfilen kan ett fel uppstå. Du bör ta bort `disabled` egenskapen från function.jspå filen för att förhindra fel. 


## <a name="next-steps"></a>Nästa steg

Den här artikeln handlar om att inaktivera automatiska utlösare. Mer information om utlösare finns i [Utlösare och bindningar.](functions-triggers-bindings.md)
