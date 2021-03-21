---
title: Skapa, redigera eller utöka Logic app JSON-arbetsflöde definitioner
description: Hur du skriver, redigerar och utökar din Logic Apps JSON Workflow-definitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014405"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Skapa, redigera eller utöka JSON för logikappars definitioner av arbetsflöden i Azure Logic Apps

När du skapar lösningar för företags integrering med automatiserade arbets flöden i [Azure Logic Apps](../logic-apps/logic-apps-overview.md)använder de underliggande Logic app-definitionerna enkla och deklarativ JavaScript Object Notation (JSON) tillsammans med [WDL-schemat (Workflow Definition Language)](../logic-apps/logic-apps-workflow-definition-language.md) för deras beskrivning och verifiering. De här formaten gör att Logic app-definitioner blir lättare att läsa och förstå utan att veta mer om kod.
När du vill automatisera skapandet och distributionen av logi Kap par kan du inkludera Logic app-definitioner som [Azure-resurser](../azure-resource-manager/management/overview.md) inuti [Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md).
För att skapa, hantera och distribuera Logi Kap par kan du sedan använda [Azure PowerShell](/powershell/module/az.logicapp), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)eller [Azure Logic Apps REST-API: er](/rest/api/logic/).

Om du vill arbeta med Logic app-definitioner i JSON öppnar du kodvyn när du arbetar i Azure Portal eller i Visual Studio eller kopierar definitionen till valfritt redigerings program.
Om du inte har använt Logic Apps igen kan du läsa [hur du skapar din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Vissa Azure Logic Apps funktioner, till exempel att definiera parametrar och flera utlösare i Logic app-definitioner, är bara tillgängliga i JSON, inte i Logic Apps designer.
> För dessa uppgifter måste du arbeta i kodvyn eller någon annan redigerare.

## <a name="edit-json---azure-portal"></a>Redigera JSON – Azure Portal

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

2. Välj **alla tjänster** på den vänstra menyn.
I sökrutan söker du efter "Logic Apps" och väljer sedan din Logi Kap par i resultaten.

3. På din Logic Apps-meny, under **utvecklingsverktyg**, väljer du **vyn Logic app-kod**.

   Kodvyn öppnas och visar din Logic app-definition i JSON-format.

## <a name="edit-json---visual-studio"></a>Redigera JSON – Visual Studio

Innan du kan arbeta med din Logic app-definition i Visual Studio kontrollerar du att du har [installerat de verktyg som krävs](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Om du vill skapa en Logic-app med Visual Studio kan du läsa [snabb start: automatisera uppgifter och processer med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

I Visual Studio kan du öppna Logi Kap par som skapats och distribuerats antingen direkt från Azure Portal eller som Azure Resource Manager projekt från Visual Studio.

1. Öppna Visual Studio-lösningen eller [Azure Resource Group](../azure-resource-manager/management/overview.md) -projektet som innehåller din Logic app.

2. Hitta och öppna din Logic Apps-definition, som standard visas i en [Resource Manager-mall](../azure-resource-manager/templates/overview.md)med namnet **LogicApp.jspå**.
Du kan använda och anpassa den här mallen för distribution till olika miljöer.

3. Öppna snabb menyn för din Logic app-definition och-mall.
Välj **Öppna med Logic App Designer**.

   ![Öppna Logic-appen i en Visual Studio-lösning](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

4. Välj **kodvyn** längst ned i designern.

   Kodvyn öppnas och visar din Logic app-definition i JSON-format.

5. Om du vill återgå till designvyn väljer du **design** längst ned i kodvyn.

## <a name="parameters"></a>Parametrar

Distributions livs cykeln har vanligt vis olika miljöer för utveckling, testning, mellanlagring och produktion. När du har värden som du vill återanvända i din Logic app utan hårdkoda eller som varierar beroende på dina distributions behov, kan du skapa en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md) för arbets flödes definitionen så att du även kan automatisera Logic app-distributionen.

Följ dessa allmänna steg för att *Parameterisera*, eller definiera och använda parametrar för, dessa värden i stället. Du kan sedan ange värdena i en separat parameter fil som skickar dessa värden till din mall. På så sätt kan du enkelt ändra dessa värden utan att behöva uppdatera och distribuera om din Logic app. Fullständig information finns i [Översikt: Automatisera distribution av logi Kap par med Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. I mallen definierar du mallparametrar och parametrar för arbets flödes definitioner för att acceptera de värden som ska användas vid distribution respektive körning.

   Mallparametrar definieras i ett parameter avsnitt som ligger utanför arbets flödes definitionen, medan parametrarna för arbets flödes definition definieras i ett parameter avsnitt som finns i arbets flödes definitionen.

1. Ersätt hårdkodad-värdena med uttryck som refererar till dessa parametrar. Mall uttryck använder syntax som skiljer sig från definitions uttryck för arbets flöden.

   Undvik att komplicera koden genom att inte använda mall uttryck som utvärderas vid distribution, i definitions uttryck för arbets flöde, som utvärderas vid körning. Använd endast mall uttryck utanför arbets flödes definitionen. Använd endast uttryck för arbets flödes definition i arbets flödes definitionen.

   När du anger värden för parametrarna för arbets flödes definitionen kan du referera till mallparametrar genom att använda avsnittet parametrar som ligger utanför arbets flödes definitionen, men fortfarande inuti resurs definitionen för din Logic app. På så sätt kan du skicka parameter värden för mallen till dina parametrar för arbets flödes definitionerna.

1. Lagra värdena för parametrarna i en separat [parameter fil](../azure-resource-manager/templates/parameter-files.md) och inkludera filen i distributionen.

## <a name="process-strings-with-functions"></a>Bearbeta strängar med Functions

Logic Apps har olika funktioner för att arbeta med strängar.
Anta till exempel att du vill skicka ett företags namn från en order till ett annat system.
Men du är inte säker på korrekt hantering av tecken kodning.
Du kan utföra base64-kodning för den här strängen, men för att undvika Escape i URL: en kan du ersätta flera tecken i stället. Dessutom behöver du bara en under sträng för företags namnet eftersom de första fem tecknen inte används.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Dessa steg beskriver hur det här exemplet bearbetar den här strängen, som arbetar från insidan till utsidan:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Hämta [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) för företags namnet så att du får det totala antalet tecken.

2. Om du vill få en kortare sträng subtraherar du `5` .

3. Hämta nu en [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) .
Starta vid index `5` och gå till resten av strängen.

4. Konvertera den här del strängen till en [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) sträng.

5. Nu [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) är alla `+` tecken med `-` tecken.

6. Slutligen [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) alla `/` tecken med `_` tecken.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mappa List objekt till egenskaps värden och Använd sedan Maps som parametrar

Om du vill få olika resultat baserat på egenskapens värde kan du skapa en karta som matchar varje egenskaps värde till ett resultat och sedan använda den kartan som en parameter.

Det här arbets flödet definierar till exempel vissa kategorier som parametrar och en karta som matchar de kategorierna med en viss URL.
Först hämtar arbets flödet en lista över artiklar. Sedan använder arbets flödet kartan för att hitta URL: en som matchar kategorin för varje artikel.

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md)Funktionen kontrollerar om kategorin matchar en känd definierad kategori.

*   När en matchande kategori har hämtats hämtar exemplet objektet från kartan med hakparenteser: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Hämta data med datum funktioner

Om du vill hämta data från en data källa som inte har inbyggt stöd för *utlösare* kan du använda datum funktioner för att arbeta med tider och datum i stället.
Det här uttrycket hittar till exempel hur länge arbets flödets steg tar, som arbetar från insidan till utsidan:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. `order`Extrahera i åtgärden `startTime` .
2. Hämta den aktuella tiden med `utcNow()` .
3. Subtrahera en sekund:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Du kan använda andra tidsenheter, t `minutes` . ex `hours` . eller.

3. Nu kan du jämföra dessa två värden.

   Om det första värdet är mindre än det andra värdet har fler än en sekund gått sedan ordern först placerades.

Du kan använda String-formaterare för att formatera datum. Om du till exempel vill hämta RFC1123 använder du [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) .
Läs mer om [datum format](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorliga uttryck)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserade på olika värden (Switch-instruktioner)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Köra och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Köra eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärds status (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Läs mer om [språk schema för arbets flödes definition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Lär dig mer om [arbets flödes åtgärder och utlösare för Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
