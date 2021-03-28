---
title: CreateUiDefinition.jspå fil för fönstret Portal
description: Beskriver hur du skapar användar gränssnitts definitioner för Azure Portal. Används när du definierar Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/26/2021
ms.author: tomfitz
ms.openlocfilehash: 586237c6dd909312780163cf316220d2f3fddd8c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641654"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition.json för Azure-upplevelsen för att skapa hanterade program

I det här dokumentet beskrivs de viktigaste begreppen i **createUiDefinition.jsi** filen. Azure Portal använder den här filen för att definiera användar gränssnittet när du skapar ett hanterat program.

Mallen är följande

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "isWizard": false,
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

En `CreateUiDefinition` innehåller alltid tre egenskaper:

* protokollhanteraren
* version
* parametrar

Hanteraren bör alltid vara `Microsoft.Azure.CreateUIDef` och den senaste versionen som stöds är `0.1.2-preview` .

Schemat för Parameters-egenskapen är beroende av kombinationen av den angivna hanteraren och versionen. För hanterade program är de egenskaper som stöds `config` , `basics` ,, `steps` och `outputs` . Du `config` kan bara använda när du behöver åsidosätta standard beteendet för `basics` steget. Egenskaperna grundläggande och steg innehåller [elementen](create-uidefinition-elements.md) , t. ex. text rutor och list rutor, som ska visas i Azure Portal. Egenskapen outputs används för att mappa utmatnings värden för de angivna elementen till parametrarna i Azure Resource Manager-mallen.

Inklusive `$schema` rekommenderas, men valfritt. Om det anges måste värdet för `version` matcha versionen i `$schema` URI: n.

Du kan använda en JSON-redigerare för att skapa din createUiDefinition och sedan testa den i [sand boxen createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) för att förhandsgranska den. Mer information om sandbox finns i [Testa ditt Portal gränssnitt för Azure Managed Applications](test-createuidefinition.md).

## <a name="config"></a>Konfigurera

`config`Egenskapen är valfri. Använd den för att antingen åsidosätta standard beteendet för grundläggande steg, eller så kan du ställa in gränssnittet som en steg-för-steg-guide. Om `config` används är det den första egenskapen i avsnittet **createUiDefinition.jspå** filen `parameters` . I följande exempel visas tillgängliga egenskaper.

```json
"config": {
    "isWizard": false,
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(subscription().displayName, 'Test'))]",
                        "message": "Can't use test subscription."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualmachines/write",
                        "message": "Must have write permission for the virtual machine."
                    },
                    {
                        "permission": "Microsoft.Compute/virtualMachines/extensions/write",
                        "message": "Must have write permission for the extension."
                    }
                ]
            },
            "resourceProviders": [
                "Microsoft.Compute"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[not(contains(resourceGroup().name, 'test'))]",
                        "message": "Resource group name can't contain 'test'."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

För `isValid` egenskapen skriver du ett uttryck som matchar antingen sant eller falskt. `permission`Ange en av [resurs leverantörens åtgärder](../../role-based-access-control/resource-provider-operations.md)för egenskapen.

### <a name="wizard"></a>Konfigurationsguide

`isWizard`Egenskapen gör det möjligt att verifiera varje steg innan du fortsätter till nästa steg. När `isWizard` egenskapen inte anges är standardvärdet **false** och stegvis verifiering krävs inte.

När `isWizard` är aktiverat, är inställt på **Sant**, är fliken **grundläggande** tillgängligt och alla andra flikar är inaktiverade. När knappen **Nästa** är markerad anger flikens ikon om en fliks verifiering lyckades eller misslyckades. När en fliks obligatoriska fält har slutförts och verifierats kan **Nästa** knapp navigera till nästa flik. När alla flikar klarar valideringen kan du gå till sidan **Granska och skapa** och välja knappen **skapa** för att starta distributionen.

:::image type="content" source="./media/create-uidefinition-overview/tab-wizard.png" alt-text="Fliken guide":::

### <a name="override-basics"></a>Åsidosätt grunderna

Med grunderna i konfigurationen kan du anpassa steget grunderna.

För `description` , anger du en markdown-aktiverad sträng som beskriver din resurs. Flera rader och länkar stöds.

Med `subscription` `resourceGroup` elementen och kan du ange fler verifieringar. Syntaxen för att ange verifieringar är identisk med [text rutan](microsoft-common-textbox.md)anpassad verifiering för text. Du kan också ange `permission` valideringar för prenumerationen eller resurs gruppen.  

Prenumerations kontrollen accepterar en lista över resurs leverantörens namn områden. Du kan till exempel ange **Microsoft. Compute**. Ett fel meddelande visas när användaren väljer en prenumeration som inte stöder resurs leverantören. Felet uppstår när resurs leverantören inte är registrerad på den prenumerationen och användaren inte har behörighet att registrera resurs leverantören.  

Resurs grupps kontrollen har ett alternativ för `allowExisting` . När `true` kan användarna välja resurs grupper som redan har resurser. Den här flaggan gäller mest för Solution templates, där standard beteende användare måste välja en ny eller tom resurs grupp. I de flesta andra scenarier är det inte nödvändigt att ange den här egenskapen.  

För `location` anger du egenskaperna för den plats kontroll som du vill åsidosätta. Alla egenskaper som inte åsidosätts har angetts till standardvärdena. `resourceTypes` accepterar en sträng mat ris som innehåller fullständigt kvalificerade resurs typs namn. Plats alternativen är begränsade till endast regioner som stöder resurs typerna.  `allowedValues`   accepterar en matris med regions strängar. Endast de regionerna visas i list rutan.Du kan ange både `allowedValues`   och  `resourceTypes` . Resultatet är skärningen mellan båda listorna. Slutligen `visible` kan egenskapen användas för att villkorligt eller helt inaktivera List rutan plats.  

## <a name="basics"></a>Grundläggande inställningar

**Grundläggande** steg är det första steget som genereras när Azure Portal tolkar filen. Som standard kan du med hjälp av grundläggande steg välja prenumeration, resurs grupp och plats för distribution.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Standard för grundläggande":::

Du kan lägga till fler element i det här avsnittet. När det är möjligt kan du lägga till element som frågar om globala parametrar, t. ex. namnet på ett kluster eller administratörsautentiseringsuppgifter.

I följande exempel visas en text ruta som har lagts till i standard elementen.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="steps"></a>Steg

Egenskapen steg innehåller noll eller flera steg att visa efter grunderna. Varje steg innehåller ett eller flera element. Överväg att lägga till steg per roll eller nivå för det program som distribueras. Du kan till exempel lägga till ett steg för primära indata från en nod och ett steg för arbetsnoder i ett kluster.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Utdata

Azure Portal använder `outputs` egenskapen för att mappa element från `basics` och `steps` till parametrarna i mallen för Azure Resource Manager distribution. Nycklarna för den här ord listan är namnen på mallparametrar och värdena är egenskaper för de utgående objekten från de refererade elementen.

Om du vill ange resurs namnet för den hanterade appen måste du inkludera ett värde med namnet `applicationResourceName` i egenskapen outputs. Om du inte anger det här värdet tilldelar programmet ett GUID för namnet. Du kan inkludera en text ruta i användar gränssnittet som begär ett namn från användaren.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resurstyper

Om du vill filtrera tillgängliga platser till endast de platser som har stöd för de resurs typer som ska distribueras, anger du en matris av resurs typerna. Om du anger mer än en resurs typ returneras bara de platser som stöder alla resurs typer. Den här egenskapen är valfri.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition innehåller [funktioner](create-uidefinition-functions.md) för att arbeta med element indata och utdata, samt funktioner som till exempel villkor. Dessa funktioner liknar funktionerna i både syntax och funktioner i Azure Resource Manager mallar.

## <a name="next-steps"></a>Nästa steg

Själva filen createUiDefinition.jshar ett enkelt schema. Det verkliga djupet i den kommer från alla element och funktioner som stöds. Dessa objekt beskrivs mer detaljerat på:

- [Element](create-uidefinition-elements.md)
- [Funktioner](create-uidefinition-functions.md)

Ett aktuellt JSON-schema för createUiDefinition finns här: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Ett exempel på en användar gränssnitts fil finns i [createUiDefinition.jspå](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
