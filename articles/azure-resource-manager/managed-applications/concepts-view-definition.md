---
title: Översikt över vydefinitionen
description: Beskriver begreppet att skapa vydefinition för Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 55263d3c742d18cf03303f96f08fb9aa370c7af8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592064"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Visa definitions artefakt i Azure Managed Applications

View definition är en valfri artefakt i Azure Managed Applications. Det gör att du kan anpassa översikts sidan och lägga till fler vyer, till exempel mått och anpassade resurser.

Den här artikeln innehåller en översikt över visnings definitions artefakt och dess funktioner.

## <a name="view-definition-artifact"></a>Visa definitionsartefakt

Artefakten för visnings definitionen måste namnges **viewDefinition.jspå** och placeras på samma nivå som **createUiDefinition.jspå** och **mainTemplate.js** i. zip-paketet som skapar en definition för ett hanterat program. Information om hur du skapar zip-paketet och publicerar en definition för ett hanterat program finns i [publicera en definition för ett](publish-service-catalog-app.md) hanterat Azure-program

## <a name="view-definition-schema"></a>Visa definitions schema

**viewDefinition.jspå** filen har bara en egenskap på översta nivån `views` , som är en matris med vyer. Varje vy visas i användar gränssnittet för hanterade program som ett separat meny alternativ i innehålls förteckningen. Varje vy har en `kind` egenskap som anger typen av vy. Det måste anges till något av följande värden: [Översikt](#overview), [mått](#metrics), [CustomResources](#custom-resources), [associationer](#associations). Mer information finns i aktuellt [JSON-schema för viewDefinition.jspå](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Exempel-JSON för View definition:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Översikt

`"kind": "Overview"`

När du anger den här vyn i **viewDefinition.jspå**, åsidosätter den standard översikts sidan i det hanterade programmet.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|sidhuvud|No|Översikts sidans sidhuvud.|
|beskrivning|No|Beskrivningen av ditt hanterade program.|
|kommandon|No|Matrisen med ytterligare verktygsfälts knappar på översikts sidan finns i [kommandon](#commands).|

![Skärm bild som visar översikten för ett hanterat program med en test åtgärds kontroll för att köra ett demo program.](./media/view-definition/overview.png)

## <a name="metrics"></a>Mått

`"kind": "Metrics"`

Med vyn mått kan du samla in och samla in data från dina hanterade program resurser i [Azure Monitor mått](../../azure-monitor/essentials/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|No|Den visade rubriken för vyn.|
|version|No|Versionen av plattformen som används för att rendera vyn.|
|charts|Yes|Matrisen med diagram på sidan mått.|

### <a name="chart"></a>Diagram

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Yes|Diagrammets visade rubrik.|
|chartType|No|Visualiseringen som ska användas för det här diagrammet. Som standard använder den ett linje diagram. Diagram typer som stöds: `Bar, Line, Area, Scatter` .|
|metrics|Yes|Matrisen med mått som ska ritas upp i det här diagrammet. Mer information om mått som stöds i Azure Portal finns i [mått som stöds med Azure Monitor](../../azure-monitor/essentials/metrics-supported.md)|

### <a name="metric"></a>Metric

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|name|Yes|Namnet på måttet.|
|aggregationType|Yes|Den sammansättnings typ som ska användas för måttet. Sammansättnings typer som stöds: `none, sum, min, max, avg, unique, percentile, count`|
|namnområde|No|Ytterligare information som ska användas när du fastställer rätt mått Provider.|
|resourceTagFilter|No|Resurs tag gen mat ris (separeras med `or` ord) för vilka mått visas. Gäller över resurs typs filter.|
|resourceType|Yes|Den resurs typ som måtten ska visas för.|

![Skärm bild som visar en övervaknings sida som kallas det här är min mått-vy för ett hanterat program.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Anpassade resurser

`"kind": "CustomResources"`

Du kan definiera flera vyer av den här typen. Varje vy representerar en **unik** anpassad resurs typ från den anpassade provider som du definierade i **mainTemplate.jspå**. En introduktion till anpassade providers finns i [Översikt över Azure Custom providers Preview](../custom-providers/overview.md).

I den här vyn kan du utföra GET-, GET-, DELETE-och POST-åtgärder för din anpassade resurs typ. POST-åtgärder kan vara globala anpassade åtgärder eller anpassade åtgärder i en kontext för din anpassade resurs typ.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Yes|Den visade rubriken för vyn. Rubriken måste vara **unik** för varje CustomResources-vy i **viewDefinition.jspå**.|
|version|No|Versionen av plattformen som används för att rendera vyn.|
|resourceType|Yes|Anpassad resurs typ. Måste vara en **unik** anpassad resurs typ för din anpassade Provider.|
|storleken på|No|Ikonen i vyn. En lista med exempel ikoner definieras i [JSON-schemat](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Skapa ett GRÄNSSNITTs definitions schema för kommandot Skapa anpassad resurs. En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md)|
|kommandon|No|Matrisen med ytterligare verktygsfälts knappar i CustomResources-vyn finns i [kommandon](#commands).|
|kolumner|No|Matrisen med kolumner för den anpassade resursen. Om den inte är definierad `name` visas kolumnen som standard. Kolumnen måste innehålla `"key"` och `"displayName"` . För nyckel anger du nyckeln för den egenskap som ska visas i en vy. Om det är kapslat använder du punkt som avgränsare, till exempel `"key": "name"` eller `"key": "properties.property1"` . I visnings namn anger du visnings namnet för den egenskap som ska visas i en vy. Du kan också ange en `"optional"` egenskap. När värdet är True är kolumnen dold i en vy som standard.|

![Skärm bild som visar en resurs sida med namnet test anpassad resurs typ och åtgärden anpassad kontext åtgärd.](./media/view-definition/customresources.png)

## <a name="commands"></a>Kommandon

Kommandon är en matris med ytterligare verktygsfälts knappar som visas på sidan. Varje kommando representerar en POST-åtgärd från din Azure-anpassade provider som definierats i **mainTemplate.jspå**. En introduktion till anpassade providers finns i [Översikt över Azure-anpassade providers](../custom-providers/overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Yes|Det namn som visas på kommando knappen.|
|path|Yes|Åtgärds namnet för den anpassade providern. Åtgärden måste definieras i **mainTemplate.jspå**.|
|storleken på|No|Kommando knappens ikon. En lista med exempel ikoner definieras i [JSON-schemat](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Skapa GRÄNSSNITTs definitions schema för kommando. En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Typer

`"kind": "Associations"`

Du kan definiera flera vyer av den här typen. Med den här vyn kan du länka befintliga resurser till det hanterade programmet via den anpassade provider som du definierade i **mainTemplate.jspå**. En introduktion till anpassade providers finns i [Översikt över Azure Custom providers Preview](../custom-providers/overview.md).

I den här vyn kan du utöka befintliga Azure-resurser baserat på `targetResourceType` . När en resurs väljs kommer den att skapa en onboarding-begäran till den **offentliga** anpassade providern som kan tillämpa en sido effekt på resursen. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Egenskap|Krävs|Beskrivning|
|---------|---------|---------|
|displayName|Yes|Den visade rubriken för vyn. Rubriken måste vara **unik** för varje association i **viewDefinition.jspå**.|
|version|No|Versionen av plattformen som används för att rendera vyn.|
|targetResourceType|Yes|Mål resurs typen. Detta är den resurs typ som ska visas för resurs onboarding.|
|createUIDefinition|No|Skapa ett GRÄNSSNITTs definitions schema för kommandot Skapa Associations resurs. En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Söker efter hjälp

Om du har frågor om Azure Managed Applications, kan du försöka med att fråga [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). En liknande fråga kanske redan har blivit ombeddd och besvarad, så kontrol lera först innan du publicerar. Lägg till taggen `azure-managedapps` för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

- En introduktion till hanterade program finns i [Azure Managed Application overview](overview.md) (Översikt över Azure Managed Application).
- En introduktion till anpassade providers finns i [Översikt över Azure-anpassade providers](../custom-providers/overview.md).
- Information om hur du skapar ett Azure-hanterat program med Azure-anpassade leverantörer finns i [Självstudier: skapa hanterat program med anpassade Provider-åtgärder och resurs typer](tutorial-create-managed-app-with-custom-provider.md)
