---
title: Skapa Azure-instrumentpaneler program mässigt
description: Använd en instrument panel i Azure Portal som en mall för att program mässigt skapa Azure-instrumentpaneler. Inkluderar JSON-referens.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: bd56dc1c729c5aa7a77e79aa3af3366166fdcfea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095169"
---
# <a name="programmatically-create-azure-dashboards"></a>Skapa Azure-instrumentpaneler program mässigt

Den här artikeln vägleder dig genom processen för att skapa och publicera Azure-instrumentpaneler program mässigt. Den instrument panel som visas nedan refereras till i hela dokumentet.

![exempelinstrumentpanel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Översikt

Delade instrument paneler i [Azure Portal](https://portal.azure.com) är [resurser](../azure-resource-manager/management/overview.md) precis som virtuella datorer och lagrings konton. Du kan hantera resurser program mässigt med hjälp av [Azure Resource Manager REST-API: er](/rest/api/), [Azure CLI](/cli/azure)och [Azure PowerShell kommandon](/powershell/azure/get-started-azureps).

Många funktioner bygger på dessa API: er för att förenkla resurs hanteringen. Var och en av dessa API: er och verktyg erbjuder olika sätt att skapa, lista, Hämta, ändra och ta bort resurser. Eftersom instrument paneler är resurser kan du välja din favorit-API eller det verktyg som du vill använda.

Vilka verktyg du använder, för att skapa en instrument panel program mässigt, skapar du en JSON-representation av ditt instrument panels objekt. Det här objektet innehåller information om panelerna på instrument panelen. Den innehåller storlekar, positioner, resurser som de är kopplade till och eventuella användar anpassningar.

Det mest praktiska sättet att bygga det här JSON-dokumentet är att använda Azure Portal. Du kan lägga till och placera dina paneler interaktivt. Exportera sedan JSON och skapa en mall från resultatet för senare användning i skript, program och distributions verktyg.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Om du vill skapa en instrument panel väljer du **instrument panel** på [Azure Portal](https://portal.azure.com) menyn och väljer sedan **ny instrument panel**.

![nytt instrument panels kommando](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Använd panel galleriet för att hitta och lägga till paneler. Paneler läggs till genom att dra och släppa dem. Vissa paneler har stöd för storleks ändring med hjälp av ett drag handtag.

![Dra handtaget för att ändra storlek](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Andra har fasta storlekar att välja bland i deras snabb meny.

![ändra storlek på snabb menyn storlek](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Dela instrument panelen

När du har konfigurerat instrument panelen är nästa steg att publicera instrument panelen med kommandot **Share** .

![Dela en instrument panel](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Om du väljer **dela** blir du ombedd att välja vilken prenumeration och resurs grupp som ska publiceras. Du måste ha Skriv behörighet till den prenumeration och resurs grupp som du väljer. Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

![gör ändringar i delning och åtkomst](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Hämta JSON-representationen av instrument panelen

Publicering tar bara några sekunder. När det är färdigt är nästa steg att hämta JSON med kommandot **Download** .

![Hämta JSON-representation](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Skapa en mall från JSON

Nästa steg är att skapa en mall från denna JSON. Använd den mallen program mässigt med lämpliga resurs hanterings-API: er, kommando rads verktyg eller i portalen.

Du behöver inte fullständigt förstå JSON-strukturen för instrument panelen för att skapa en mall. I de flesta fall vill du bevara strukturen och konfigurationen för varje panel. Parameterisera sedan uppsättningen av Azure-resurser som panelerna pekar på. Titta på den exporterade JSON-instrumentpanelen och hitta alla förekomster av Azures resurs-ID. Exempel instrument panelen har flera paneler som alla pekar på en virtuell Azure-dator. Det beror på att vår instrument panel bara ser ut på den här resursen. Om du söker i exempel-JSON, som finns i slutet av dokumentet, för "/Subscriptions", hittar du flera förekomster av detta ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Om du vill publicera den här instrument panelen för en virtuell dator i framtiden Parameterisera varje förekomst av den här strängen i JSON.

Det finns två metoder för API: er som skapar resurser i Azure:

* Tvingande API: er skapa en resurs i taget. Mer information finns i [resurser](/rest/api/resources/resources).
* Ett mallbaserat distributions system som skapar flera beroende resurser med ett enda API-anrop. Mer information finns i  [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Mall baserad distribution stöder parameterisering och mall. Vi använder den här metoden i den här artikeln.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Skapa en instrument panel från din mall program mässigt med hjälp av en mall distribution

Azure ger möjlighet att dirigera distributionen av flera resurser. Du skapar en distributions mall som uttrycker den uppsättning resurser som ska distribueras och relationerna mellan dem.  JSON-formatet för varje resurs är detsamma som om du skapade dem en i taget. Skillnaden är att mallens språk lägger till några begrepp som variabler, parametrar, grundläggande funktioner och mycket annat. Den här utökade syntaxen stöds bara i kontexten för en mall distribution. Den fungerar inte om den används med de tvingande API: erna som beskrivs ovan. Mer information finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

Parameterisering bör göras med mallens parameter-syntax.  Du ersätter alla instanser av resurs-ID: t som vi tidigare hittade här.

Exempel på JSON-egenskap med hårdkodat resurs-ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Exempel-JSON-egenskap konverterad till en parameter version baserat på mallparametrar

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarera nödvändiga mall-metadata och parametrarna överst i JSON-mallen så här:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
När du har konfigurerat mallen kan du distribuera den med någon av följande metoder:

* [REST API:er](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Sidan distribution av Azure Portal mall](https://portal.azure.com/#create/Microsoft.Template)

Härnäst ser du två versioner av vårt exempel på instrument panels-JSON. Den första versionen som vi exporterade från portalen som redan var kopplad till en resurs. Den andra är den mall version som kan vara program mässigt kopplad till en virtuell dator och distribueras med hjälp av Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-representation av vårt exempel på en instrument panel innan mall

Det här exemplet visar vad du kan förväntar dig att se om du har följt den här artikeln. Anvisningarna exporterade JSON-representationen av en instrument panel som redan har distribuerats. De hårdkodade resurs identifierarna visar att den här instrument panelen pekar på en enskild virtuell Azure-dator.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>Representation av vår instrument panel för exempel

Mall versionen av instrument panelen har definierat tre parametrar som kallas `virtualMachineName` , `virtualMachineResourceGroup` , och `dashboardName` .  Med parametrarna kan du peka den här instrument panelen på en annan virtuell Azure-dator varje gång du distribuerar. Den här instrument panelen kan konfigureras program mässigt och distribueras så att den pekar på en virtuell Azure-dator. För att testa den här funktionen kopierar du följande mall och klistrar in den på [sidan distribution av Azure Portal mall](https://portal.azure.com/#create/Microsoft.Template).

I det här exemplet distribueras en instrument panel av sig själv, men med hjälp av språket i mallen kan du distribuera flera resurser och paketera en eller flera instrument paneler på sidan.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Nu när du har sett ett exempel på att använda en parametriserad mall för att distribuera en instrument panel kan du prova att distribuera mallen med hjälp av [Azure Resource Manager REST-API: er](/rest/api/), [Azure CLI](/cli/azure)eller [Azure PowerShell kommandon](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Skapa en instrument panel program mässigt med hjälp av Azure CLI

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- I de här exemplen används följande instrument panel: [portal-dashboard-template-testvm.jspå](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Ersätt innehåll i vinkelparenteser med dina värden.

Kör kommandot [AZ Portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) för att skapa en instrument panel:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Du kan uppdatera en instrument panel med hjälp av [AZ-portalens instrument panels uppdaterings](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) kommando:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Se information om en instrument panel genom att köra kommandot [AZ Portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Om du vill se alla instrument paneler för den aktuella prenumerationen använder du [AZ Portal Dashboard List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Du kan också se alla instrument paneler för en resurs grupp:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om Station ära datorer finns i [hantera Azure Portal inställningar och inställningar](set-preferences.md).

Mer information om Azure CLI-stöd för instrument paneler finns i [instrument panelen för AZ-portalen](/cli/azure/ext/portal/portal/dashboard).
