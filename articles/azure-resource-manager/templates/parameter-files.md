---
title: Skapa parameterfil
description: Skapa parameterfil för att skicka värden under distributionen av en Azure Resource Manager mall
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531219"
---
# <a name="create-resource-manager-parameter-file"></a>Skapa Resource Manager parameterfil

I stället för att skicka parametrar som infogade värden i skriptet kan du använda en JSON-fil som innehåller parametervärdena. Den här artikeln visar hur du skapar en parameterfil som du använder med en JSON-mall eller Bicep-fil.

## <a name="parameter-file"></a>Parameterfil

En parameterfil använder följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Observera att parameterfilen lagrar parametervärden som oformaterad text. Den här metoden fungerar för värden som inte är känsliga, till exempel en resurs-SKU. Oformaterad text fungerar inte för känsliga värden, till exempel lösenord. Om du behöver skicka en parameter som innehåller ett känsligt värde lagrar du värdet i ett nyckelvalv. Referera sedan till nyckelvalvet i parameterfilen. Det känsliga värdet hämtas säkert under distributionen.

Följande parameterfil innehåller ett oformaterad textvärde och ett känsligt värde som lagras i ett nyckelvalv.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Mer information om hur du använder värden från ett nyckelvalv finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen.](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Definiera parametervärden

Om du vill bestämma hur parameternamn och värden ska definieras öppnar du JSON- eller Bicep-mallen. Titta på parameteravsnittet i mallen. I följande exempel visas parametrarna från JSON- och Bicep-mallar.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

I parameterfilen är den första detaljen att lägga märke till namnet på varje parameter. Parameternamnen i parameterfilen måste matcha parameternamnen i mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Observera parametertypen. Parametertyperna i parameterfilen måste använda samma typer som mallen. I det här exemplet är båda parametertyperna strängar.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Kontrollera mallen för parametrar med ett standardvärde. Om en parameter har ett standardvärde kan du ange ett värde i parameterfilen, men det är inte obligatoriskt. Parameterfilvärdet åsidosätter mallens standardvärde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Kontrollera mallens tillåtna värden och eventuella begränsningar, till exempel maximal längd. Dessa värden anger det värdeintervall som du kan ange för en parameter. I det här exemplet `storagePrefix` kan innehålla högst 11 tecken och måste `storageAccountType` ange ett tillåtet värde.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Parameterfilen kan bara innehålla värden för parametrar som definieras i mallen. Om parameterfilen innehåller extra parametrar som inte matchar mallens parametrar får du ett felmeddelande.

## <a name="parameter-type-formats"></a>Format för parametertyper

I följande exempel visas formaten för olika parametertyper: sträng, heltal, boolesk, matris och objekt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-template-with-parameter-file"></a>Distribuera mall med parameterfil

Från Azure CLI skickar du en lokal parameterfil med `@` parameterfilnamnet och . Till exempel `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Mer information finns i Distribuera [resurser med ARM-mallar och Azure CLI.](./deploy-cli.md#parameters) Om du _vill distribuera .bicep-filer_ behöver du Azure CLI version 2.20 eller senare.

Från Azure PowerShell du en lokal parameterfil med `TemplateParameterFile` parametern .

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Mer information finns i Distribuera [resurser med ARM-mallar och Azure PowerShell](./deploy-powershell.md#pass-parameter-values). Om du _vill distribuera .bicep-filer_ Azure PowerShell version 5.6.0 eller senare.

> [!NOTE]
> Det går inte att använda en parameterfil med det anpassade mallbladet i portalen.

> [!TIP]
> Om du använder Azure-resursgruppsprojektet [i Visual Studio](create-visual-studio-deployment-project.md)kontrollerar du att parameterfilen har **build-åtgärden** inställd på **Innehåll**.

## <a name="file-name"></a>Filnamn

Den allmänna namngivningskonventionen för parameterfilen är att inkludera _parametrar_ i mallnamnet. Om mallen till exempel heter _azuredeploy.jspå_ får parameterfilen namnet _azuredeploy.parameters.jspå_. Den här namngivningskonventionen hjälper dig att se anslutningen mellan mallen och parametrarna.

Om du vill distribuera till olika miljöer skapar du fler än en parameterfil. När du ger parameterfilerna ett namn ska du identifiera hur de används, till exempel utveckling och produktion. Du kan till exempel _azuredeploy.parameters-dev.jspå_ ochazuredeploy.parameters-prod.js _vidare för_ att distribuera resurser.

## <a name="parameter-precedence"></a>Parameterns prioritet

Du kan använda infogade parametrar och en lokal parameterfil i samma distributionsåtgärd. Du kan till exempel ange vissa värden i den lokala parameterfilen och lägga till andra värden infogade under distributionen. Om du anger värden för en parameter i både den lokala parameterfilen och den infogade filen har det infogade värdet företräde.

Du kan använda en extern parameterfil genom att ange URI:en till filen. När du använder en extern parameterfil kan du inte skicka andra värden antingen infogade eller från en lokal fil. Alla infogade parametrar ignoreras. Ange alla parametervärden i den externa filen.

## <a name="parameter-name-conflicts"></a>Parameternamnkonflikter

Om mallen innehåller en parameter med samma namn som en av parametrarna i PowerShell-kommandot, visar PowerShell parametern från mallen med postfixet `FromTemplate` . En parameter med namnet i mallen är till exempel i konflikt med parametern i `ResourceGroupName` `ResourceGroupName` cmdleten [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Du uppmanas att ange ett värde för `ResourceGroupNameFromTemplate` . Undvik den här förvirringen genom att använda parameternamn som inte används för distributionskommandon.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du definierar parametrar i en mall finns i [Parametrar i ARM-mallar.](template-parameters.md)
- Mer information om hur du använder värden från ett nyckelvalv finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen.](key-vault-parameter.md)
