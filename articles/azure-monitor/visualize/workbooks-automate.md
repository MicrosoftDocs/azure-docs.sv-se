---
title: Azure Monitor arbets böcker och Azure Resource Manager mallar
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda Azure Monitor arbets böcker som distribueras via Azure Resource Manager mallar
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100624640"
---
# <a name="programmatically-manage-workbooks"></a>Hantera arbets böcker program mässigt

Resurs ägare har möjlighet att skapa och hantera sina arbets böcker via programmering via Resource Manager-mallar.

Detta kan vara användbart i scenarier som:
* Distribuera organisations-eller domänbaserade analys rapporter tillsammans med distributioner av resurser. Du kan till exempel distribuera org-/regionsspecifika prestanda-och arbets böcker för dina nya appar eller virtuella datorer.
* Distribuera standard rapporter eller instrument paneler med hjälp av arbets böcker för befintliga resurser.

Arbets boken kommer att skapas i önskad under-eller resurs grupp och med det innehåll som anges i Resource Manager-mallarna.

Det finns två typer av arbets boks resurser som kan hanteras program mässigt:
* [Mallar för arbets böcker](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Arbets boks instanser](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Azure Resource Manager mall för att distribuera en mall för arbets böcker

1. Öppna en arbets bok som du vill distribuera program mässigt.
2. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
3. Öppna _avancerad redigerare_ med _</>_ knappen i verktygsfältet.
4. Se till att du är på fliken _Galleri mal len_ .

    ![Fliken Galleri mal len](./media/workbooks-automate/gallery-template.png)
1. Kopiera JSON i Galleri mal len till Urklipp.
2. Nedan visas ett exempel på en Azure Resource Manager mall som distribuerar en arbetsboksmall till Azure Monitor arbets boks galleriet. Klistra in JSON-filen som du kopierade i stället för `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . En referens Azure Resource Manager mall som skapar en mall för arbets böcker finns [här](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. I- `galleries` objektet fyller du i `name` och- `category` nycklarna med dina värden. Läs mer om [parametrar](#parameters) i nästa avsnitt.
2. Distribuera denna Azure Resource Manager-mall med hjälp av antingen [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [kommando rads gränssnittet](../../azure-resource-manager/templates/deploy-cli.md), [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)osv.
3. Öppna Azure Portal och navigera till galleriet för arbets boken som valts i Azure Resource Manager-mallen. I exempel mal len navigerar du till galleriet Azure Monitor arbets bok:
    1. Öppna Azure Portal och navigera till Azure Monitor
    2. Öppna `Workbooks` i innehålls förteckningen
    3. Hitta din mall i galleriet under kategori `Deployed Templates` (är ett av de lila objekten).

### <a name="parameters"></a>Parametrar

|Parametrar                |Förklaring                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Namnet på arbets boks mal len resurs i Azure Resource Manager.                                  |
|`type`                    | Always Microsoft. Insights/workbooktemplates                                                            |
| `location`               | Den Azure-plats som arbets boken ska skapas i.                                               |
| `apiVersion`             | 2019-10-17 för hands version                                                                                     |
| `type`                   | Always Microsoft. Insights/workbooktemplates                                                            |
| `galleries`              | Uppsättningen gallerier som ska visa den här arbets boks mal len i.                                                |
| `gallery.name`           | Det egna namnet på arbets boks mal len i galleriet.                                             |
| `gallery.category`       | Gruppen i galleriet där mallen ska placeras.                                                     |
| `gallery.order`          | Ett tal som bestämmer ordningen för att Visa mallen i en kategori i galleriet. Lägre ordning innebär högre prioritet. |
| `gallery.resourceType`   | Den resurs typ som motsvarar galleriet. Detta är vanligt vis den resurs typs sträng som motsvarar resursen (till exempel Microsoft. operationalinsights/arbets ytor). |
|`gallery.type`            | Kallas för arbets boks typ, detta är en unik nyckel som särskiljer galleriet i en resurs typ. Application Insights kan till exempel ha typer `workbook` och `tsg` som motsvarar olika arbets boks gallerier. |

### <a name="galleries"></a>Gallerier

| Galleri                                        | Resurstyp                                      | Arbets boks typ |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Arbets böcker i Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| VM Insights i Azure Monitor                   | `Azure Monitor`                                    | `vm-insights` |
| Arbets böcker i Log Analytics-arbetsyta           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Arbets böcker i Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Fel söknings guider i Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Användning i Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Arbets böcker i Kubernetes-tjänsten                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Arbets böcker i resurs grupper                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Arbets böcker i Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| VM-insikter på virtuella datorer                | `microsoft.compute/virtualmachines`                | `insights`    |
| VM Insights i skalnings uppsättningar för virtuella datorer      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Azure Resource Manager mall för att distribuera en arbets boks instans

1. Öppna en arbets bok som du vill distribuera program mässigt.
2. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
3. Öppna _avancerad redigerare_ med _</>_ knappen i verktygsfältet.
4. I redigeraren byter du _Malltyp_ till _Resource Manager-mall_.
5. Resource Manager-mallen för att skapa visas i redigeraren. Kopiera innehållet och Använd det som är eller slå samman det med en större mall som också distribuerar mål resursen.

    ![Bild som visar hur du hämtar Resource Manager-mallen inifrån arbets bokens användar gränssnitt](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Exempel på Azure Resource Manager mall
Den här mallen visar hur du distribuerar en enkel arbets bok som visar Hello World!
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Mallparametrar

| Parameter | Förklaring |
| :------------- |:-------------|
| `workbookDisplayName` | Det egna namnet på arbets boken som används i galleriet eller sparade listor. Måste vara unikt i omfånget för resurs gruppen och källan |
| `workbookType` | Galleriet som arbets boken visas under. Värden som stöds är arbets bok, `tsg` Azure Monitor osv. |
| `workbookSourceId` | ID för resurs instansen som arbets boken ska associeras med. Den nya arbets boken visas som är relaterad till den här resurs instansen – till exempel i resursens innehålls förteckning under _arbets bok_. Om du vill att arbets boken ska visas i arbets boks galleriet i Azure Monitor använder du strängen _Azure Monitor_ i stället för ett resurs-ID. |
| `workbookId` | Unikt GUID för arbets boks instansen. Använd _[newGuid ()]_ för att automatiskt skapa ett nytt GUID. |
| `kind` | Används för att ange om den skapade arbets boken är delad eller privat. Använd värdet _delat_ för delade arbets böcker och _användare_ för privata. |
| `location` | Den Azure-plats där arbets boken kommer att skapas. Använd _[resourceGroup (). location]_ för att skapa den på samma plats som resurs gruppen |
| `serializedData` | Innehåller det innehåll eller den nytto last som ska användas i arbets boken. Använd Resource Manager-mallen från arbets bokens användar gränssnitt för att hämta värdet |

### <a name="workbook-types"></a>Arbets boks typer
Arbets boks typer ange vilken arbets boks Galleri typ den nya arbets boks instansen ska visas under. Alternativen är:

| Typ | Galleri plats |
| :------------- |:-------------|
| `workbook` | Standardvärdet används i de flesta rapporter, inklusive arbets boks galleriet för Application Insights, Azure Monitor osv.  |
| `tsg` | Galleriet för fel söknings guider i Application Insights |
| `usage` | Galleriet _mer_ under _användning_ i Application Insights |

### <a name="limitations"></a>Begränsningar
Av teknisk anledning kan den här mekanismen inte användas för att skapa arbets boks instanser i galleriet för _arbets böcker_ i Application Insights. Vi arbetar på att lösa den här begränsningen. Under tiden rekommenderar vi att du använder galleriet för fel söknings guiden (workbookType: `tsg` ) för att distribuera Application Insights relaterade arbets böcker.

## <a name="next-steps"></a>Nästa steg

Utforska hur arbets böcker används för att sätta nya [Azure Monitor för lagrings upplevelsen](../insights/storage-insights-overview.md).
