---
title: Så här inaktiverar du funktioner i Azure Functions
description: Lär dig hur du inaktiverar och aktiverar funktioner i Azure Functions.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 1ad484804f66a2e2d4d0f1da4a37cf0d6c485f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584745"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Så här inaktiverar du funktioner i Azure Functions

Den här artikeln förklarar hur du inaktiverar en funktion i Azure Functions. Om du vill *inaktivera* en funktion innebär körningen att den automatiska utlösaren som definierats för funktionen ignoreras. På så sätt kan du förhindra att en speciell funktion körs utan att stoppa hela Function-appen.

Det rekommenderade sättet att inaktivera en funktion är med en app-inställning i formatet `AzureWebJobs.<FUNCTION_NAME>.Disabled` inställt på `true` . Du kan skapa och ändra den här program inställningen på flera olika sätt, inklusive genom att använda [Azure CLI](/cli/azure/) och från funktions fliken **Översikt** i [Azure Portal](https://portal.azure.com). 

> [!NOTE]  
> När du inaktiverar en HTTP-utlöst funktion med hjälp av metoderna som beskrivs i den här artikeln, kan slut punkten fortfarande vara tillgänglig när den körs på den lokala datorn.  

## <a name="disable-a-function"></a>Inaktivera en funktion

# <a name="portal"></a>[Portal](#tab/portal)

Använd knapparna **Aktivera** och **inaktivera** på funktionens **översikts** sida. Dessa knappar fungerar genom att ändra värdet för `AzureWebJobs.<FUNCTION_NAME>.Disabled` appens inställning. Den här användarspecifika inställningen skapas första gången den inaktive ras. 

![Funktions tillstånds växel](media/disable-function/function-state-switch.png)

Även när du publicerar till din Function-app från ett lokalt projekt kan du fortfarande använda portalen för att inaktivera funktioner i Function-appen. 

> [!NOTE]  
> Den Portal-integrerade test funktionen ignorerar `Disabled` inställningen. Det innebär att en inaktive rad funktion fortfarande körs när den startas från **test** fönstret i portalen. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

I Azure CLI använder du [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) kommandot för att skapa och ändra appens inställning. Följande kommando inaktiverar en funktion som heter genom att `QueueTrigger` skapa en app-inställning med namnet `AzureWebJobs.QueueTrigger.Disabled` och ställa in den på `true` . 

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Om du vill aktivera funktionen igen kör du samma kommando med värdet `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Kommandot lägger till eller uppdaterar en program inställning. Följande kommando inaktiverar en funktion som heter genom att `QueueTrigger` skapa en app-inställning med namnet `AzureWebJobs.QueueTrigger.Disabled` och ställa in den på `true` . 

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "true"}
```

Om du vill aktivera funktionen igen kör du samma kommando med värdet `false` .

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"AzureWebJobs.QueueTrigger.Disabled" = "false"}
```
---

## <a name="functions-in-a-slot"></a>Funktioner på en plats

Som standard gäller appinställningar även för appar som körs på distributions platser. Du kan dock åsidosätta den app-inställning som används av facket genom att ange en plats för en speciell app. Till exempel kanske du vill att en funktion ska vara aktiv i produktion, men inte under distributions testning, till exempel en timer-utlöst funktion. 

Så här inaktiverar du endast en funktion på mellanlagrings platsen:

# <a name="portal"></a>[Portal](#tab/portal)

Navigera till plats instansen för din Function-app genom att välja **distributions fack** under **distributionen**, välja plats och välja **funktioner** i plats instansen.  Välj din funktion och Använd knapparna **Aktivera** och **inaktivera** på funktionens **översikts** sida. Dessa knappar fungerar genom att ändra värdet för `AzureWebJobs.<FUNCTION_NAME>.Disabled` appens inställning. Den här användarspecifika inställningen skapas första gången den inaktive ras. 

Du kan också direkt lägga till appens inställningen `AzureWebJobs.<FUNCTION_NAME>.Disabled` med namnet med värdet `true` i **konfigurationen** för plats instansen. När du lägger till en plats bestämd app-inställning kontrollerar du att kryss rutan **distributions plats inställning** är markerad. Detta bibehåller inställnings värdet med facket vid växlingar.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=true
```
Om du vill aktivera funktionen igen kör du samma kommando med värdet `false` .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> --slot <SLOT_NAME> \
--slot-settings AzureWebJobs.QueueTrigger.Disabled=false
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell har för närvarande inte stöd för den här funktionen.

---

Läs mer i [Azure Functions distributions fack](functions-deployment-slots.md).

## <a name="localsettingsjson"></a>local.settings.json

Funktioner kan inaktive ras på samma sätt när de körs lokalt. Om du vill inaktivera en funktion med namnet `HttpExample` lägger du till en post i värden-samlingen i local.settings.jsi filen enligt följande:

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

Även om program inställnings metoden rekommenderas för alla språk och alla körnings versioner finns det flera andra sätt att inaktivera funktioner. Dessa metoder, som varierar beroende på språk-och körnings version, bevaras för bakåtkompatibilitet. 

### <a name="c-class-libraries"></a>C#-klass bibliotek

I en klass biblioteks funktion kan du också använda `Disable` attributet för att förhindra att funktionen utlöses. Med det här attributet kan du anpassa namnet på inställningen som används för att inaktivera funktionen. Använd versionen av attributet som låter dig definiera en konstruktor-parameter som refererar till en boolesk app-inställning, som visas i följande exempel:

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

Med den här metoden kan du aktivera och inaktivera funktionen genom att ändra appens inställning utan att kompilera om eller distribuera om. Om du ändrar en app-inställning startar Function-appen om, så att den inaktiverade tillstånds ändringen kan identifieras direkt.

Det finns också en konstruktor för parametern som inte accepterar en sträng för inställnings namnet. Den här versionen av attributet rekommenderas inte. Om du använder den här versionen måste du kompilera om och distribuera om projektet för att ändra funktionens inaktiverade tillstånd.

### <a name="functions-1x---scripting-languages"></a>Functions 1. x – skript språk

I version 1. x kan du också använda `disabled` egenskapen för *function.jspå* filen för att se till att körnings miljön inte utlöser en funktion. Den här metoden fungerar bara för skript språk som C#-skript och Java Script. `disabled`Egenskapen kan anges till `true` eller till namnet på en app-inställning:

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

I det andra exemplet inaktive ras funktionen när det finns en app-inställning med namnet IS_DISABLED och har värdet `true` eller 1.

>[!IMPORTANT]  
>Portalen använder program inställningar för att inaktivera v1. x-funktioner. När en program inställning står i konflikt med function.jsav filen kan ett fel uppstå. Du bör ta bort `disabled` egenskapen från function.jspå filen för att förhindra fel. 


## <a name="next-steps"></a>Nästa steg

Den här artikeln är att inaktivera automatiska utlösare. Mer information om utlösare finns i [utlösare och bindningar](functions-triggers-bindings.md).
