---
title: Mallens funktioner – datum
description: Beskriver de funktioner som används i en Azure Resource Manager mall (ARM-mall) för att arbeta med datum.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 58d865f109ecca2629b89eeb55e554743824c195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920494"
---
# <a name="date-functions-for-arm-templates"></a>Datum funktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att arbeta med datum i din Azure Resource Manager-mall (ARM-mall):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Lägger till en tids period till ett bas värde. ISO 8601-format förväntas.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| base | Ja | sträng | Start-datetime-värdet för additionen. Använd [formatet ISO 8601-tidsstämpel](https://en.wikipedia.org/wiki/ISO_8601). |
| varaktighet | Ja | sträng | Det tids värde som ska läggas till i basen. Det kan vara ett negativt värde. Använd [varaktighets formatet ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Inga | sträng | Utdataformatet för datum/tid-resultatet. Om inget värde anges används formatet för bas värdet. Använd antingen [standard format strängar](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Returvärde

Det datetime-värde som är resultatet av att lägga till duration-värdet i bas värdet.

### <a name="examples"></a>Exempel

Följande exempel-mall visar olika sätt att lägga till tids värden.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]"
    }
  },
  "variables": {
    "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
    "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
    "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [],
  "outputs": {
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

När föregående mall distribueras med en bas tid av `2020-04-07 14:53:14Z` , är utdata:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| add3YearsOutput | Sträng | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | Sträng | 3/29/2020 2:53:14 PM |
| add1HourOutput | Sträng | 4/7/2020 3:53:14 PM |

I nästa exempel-mall visas hur du ställer in start tiden för ett Automation-schema.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "omsAutomationAccountName": {
      "type": "string",
      "defaultValue": "demoAutomation",
      "metadata": {
        "description": "Use an existing Automation account."
      }
    },
    "scheduleName": {
      "type": "string",
      "defaultValue": "demoSchedule1",
      "metadata": {
        "description": "Name of the new schedule."
      }
    },
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]",
      "metadata": {
        "description": "Schedule will start one hour from this time."
      }
    }
  },
  "variables": {
    "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Returnerar det aktuella (UTC) datetime-värdet i det angivna formatet. Om inget format anges används formatet ISO 8601 ( `yyyyMMddTHHmmssZ` ). **Den här funktionen kan endast användas i standardvärdet för en parameter.**

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| format |Inga |sträng |Det URI-kodade värdet som ska konverteras till en sträng. Använd antingen [standard format strängar](/dotnet/standard/base-types/standard-date-and-time-format-strings) eller [anpassade format strängar](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Kommentarer

Du kan bara använda den här funktionen i ett uttryck för standardvärdet för en parameter. Om du använder den här funktionen någon annan stans i en mall returneras ett fel. Funktionen tillåts inte i andra delar av mallen eftersom den returnerar ett annat värde varje gång den anropas. Att distribuera samma mall med samma parametrar skulle inte tillförlitligt producera samma resultat.

Om du använder [alternativet för att återställa vid fel](rollback-on-error.md) till en tidigare lyckad distribution, och den tidigare distributionen innehåller en parameter som använder utcNow, utvärderas inte parametern om. I stället återanvänds parametervärdet från den tidigare distributionen automatiskt i återställnings distributionen.

Var noga med att distribuera om en mall som förlitar sig på utcNow-funktionen för ett standardvärde. När du distribuerar om och inte anger något värde för parametern utvärderas funktionen om. Om du vill uppdatera en befintlig resurs i stället för att skapa en ny, måste du skicka värdet i parametervärdet från den tidigare distributionen.

### <a name="return-value"></a>Returvärde

Aktuellt UTC-slutdatum värde.

### <a name="examples"></a>Exempel

I följande exempel mall visas olika format för datetime-värdet.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    },
    "utcShortValue": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "utcCustomValue": {
      "type": "string",
      "defaultValue": "[utcNow('M d')]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "utcOutput": {
      "type": "string",
      "value": "[parameters('utcValue')]"
    },
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShortValue')]"
    },
    "utcCustomOutput": {
      "type": "string",
      "value": "[parameters('utcCustomValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

Resultatet från föregående exempel varierar för varje distribution, men ser ut ungefär så här:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| utcOutput | sträng | 20190305T175318Z |
| utcShortOutput | sträng | 03/05/2019 |
| utcCustomOutput | sträng | 3 5 |

I nästa exempel visas hur du använder ett värde från funktionen när du anger ett tagg värde.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcShort": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "rgName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "westeurope",
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2018-05-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en ARM-mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
