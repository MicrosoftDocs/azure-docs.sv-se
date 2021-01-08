---
title: Distribuera resurser med REST API och mall
description: Använd Azure Resource Manager-och Resource Manager-REST API för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 77192aff9ed4fe33269b5e11891c30e15bc312dd
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028972"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Distribuera resurser med ARM-mallar och Azure Resource Manager REST API

Den här artikeln förklarar hur du använder Azure Resource Manager REST API med Azure Resource Manager mallar (ARM-mallar) för att distribuera dina resurser till Azure.

Du kan antingen inkludera din mall i begär ande texten eller länka till en fil. När du använder en fil kan det vara en lokal fil eller en extern fil som är tillgänglig via en URI. När din mall finns i ett lagrings konto kan du begränsa åtkomsten till mallen och tillhandahålla en SAS-token (signatur för delad åtkomst) under distributionen.

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta din distribution till en resurs grupp, Azure-prenumeration, hanterings grupp eller klient organisation. Beroende på distributionens omfattning använder du olika kommandon.

- Använd [distributioner – skapa](/rest/api/resources/deployments/createorupdate)för att distribuera till en **resurs grupp**. Begäran skickas till:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

- Om du vill distribuera till en **prenumeration** använder du [distributioner – skapa vid prenumerations omfång](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Begäran skickas till:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

- Om du vill distribuera till en **hanterings grupp** använder du [distributioner – skapa i hanterings gruppens omfång](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Begäran skickas till:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

- Om du vill distribuera till en **klient** använder du [distributioner – skapa eller uppdatera på klientens omfång](/rest/api/resources/deployments/createorupdateattenantscope). Begäran skickas till:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Mer information om distributioner på klient nivå finns i [Skapa resurser på klient nivå](deploy-to-tenant.md).

I exemplen i den här artikeln används resurs grupps distributioner.

## <a name="deploy-with-the-rest-api"></a>Distribuera med REST API

1. Ange [vanliga parametrar och rubriker](/rest/api/azure/), inklusive autentiseringstoken.

1. Om du distribuerar till en resurs grupp som inte finns skapar du resurs gruppen. Ange ditt prenumerations-ID, namnet på den nya resurs gruppen och den plats som du behöver för din lösning. Mer information finns i [skapa en resurs grupp](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   Med en begär ande text som:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Innan du distribuerar din mall kan du förhandsgranska de ändringar som mallen kommer att göra i din miljö. Använd [åtgärden konsekvens](template-deploy-what-if.md) för att kontrol lera att mallen gör de ändringar som du förväntar dig. Vad händer om även validerar mallen för fel.

1. Om du vill distribuera en mall anger du ditt prenumerations-ID, namnet på resurs gruppen, namnet på distributionen i begärande-URI: n.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   I begär ande texten anger du en länk till mallen och parameter filen. Mer information om parameterfilen finns i [Skapa en parameterfil för Resource Manager](parameter-files.md).

   Observera `mode` att är inställt på **stegvis**. Om du vill köra en fullständig distribution väljer `mode` du **Slutför**. Var försiktig när du använder det fullständiga läget eftersom du oavsiktligt kan ta bort resurser som inte finns i din mall.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Om du vill logga svars innehåll, begära innehåll eller både och, ta med `debugSetting` i begäran.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Du kan konfigurera ditt lagrings konto så att det använder en SAS-token (signatur för delad åtkomst). Mer information finns i [Delegera åtkomst med en signatur för delad åtkomst](/rest/api/storageservices/delegate-access-with-shared-access-signature).

    Om du behöver ange ett känsligt värde för en parameter (till exempel ett lösen ord) lägger du till värdet i ett nyckel valv. Hämta nyckel valvet under distributionen som visas i föregående exempel. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](key-vault-parameter.md).

1. I stället för att länka till filer för mallen och parametrarna kan du ta med dem i begär ande texten. I följande exempel visas begär ande texten med mallen och den infogade parametern:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Om du vill hämta distributionens status använder du [distributioner-get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>Distributions namn

Du kan ge distributionen ett namn, till exempel `ExampleDeployment` .

Varje gång du kör en distribution läggs en post till i resurs gruppens distributions historik med distributions namnet. Om du kör en annan distribution och ger den samma namn ersätts den tidigare posten med den aktuella distributionen. Om du vill behålla unika poster i distributions historiken ger du varje distribution ett unikt namn.

Om du vill skapa ett unikt namn kan du tilldela ett slumpmässigt nummer. Eller Lägg till ett datum värde.

Om du kör samtidiga distributioner till samma resurs grupp med samma distributions namn slutförs bara den senaste distributionen. Alla distributioner med samma namn som inte har avslut ATS ersätts av den senaste distributionen. Om du till exempel kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , distribuerar du bara ett lagrings konto. Det resulterande lagrings kontot namnges `storage2` .

Men om du kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` och omedelbart efter att det har slutförts, kör du en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , så har du två lagrings konton. En är namngiven `storage1` och den andra heter `storage2` . Men du har bara en post i distributions historiken.

När du anger ett unikt namn för varje distribution kan du köra dem samtidigt utan konflikter. Om du kör en distribution med namnet `newStorage1` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage2` som distribuerar ett lagrings konto med namnet `storage2` , har du två lagrings konton och två poster i distributions historiken.

För att undvika konflikter med samtidiga distributioner och för att säkerställa unika poster i distributions historiken ger du varje distribution ett unikt namn.

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Läs mer om hur du hanterar asynkrona REST-åtgärder i [spåra asynkrona Azure-åtgärder](../management/async-operations.md).
- Mer information om mallar finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
