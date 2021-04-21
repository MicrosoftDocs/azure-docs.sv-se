---
title: Skapa Azure-instrumentpaneler programmässigt
description: Använd en instrumentpanel i Azure Portal mall för att programmatiskt skapa Azure-instrumentpaneler. Innehåller JSON-referens.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: 416eeb772e347b28fcb4a4dcc93c746562ea3571
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767067"
---
# <a name="programmatically-create-azure-dashboards"></a>Skapa Azure-instrumentpaneler programmässigt

Den här artikeln beskriver steg för steg hur du programmatiskt skapar och publicerar Azure-instrumentpaneler. Instrumentpanelen som visas nedan refereras i hela dokumentet.

![exempelinstrumentpanel](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Översikt

Delade instrumentpaneler i [Azure Portal](https://portal.azure.com) är [resurser precis som](../azure-resource-manager/management/overview.md) virtuella datorer och lagringskonton. Du kan hantera resurser programmatiskt med hjälp [av Azure Resource Manager REST-API:er,](/rest/api/) [Azure CLI](/cli/azure)och [Azure PowerShell kommandon](/powershell/azure/get-started-azureps).

Många funktioner bygger på dessa API:er för att förenkla resurshanteringen. Vart och ett av dessa API:er och verktyg erbjuder sätt att skapa, lista, hämta, ändra och ta bort resurser. Eftersom instrumentpaneler är resurser kan du välja vilket API eller verktyg du vill använda.

Oavsett vilket verktyg du använder skapar du en JSON-representation av ditt instrumentpanelsobjekt för att skapa en instrumentpanel programmatiskt. Det här objektet innehåller information om panelerna på instrumentpanelen. Den innehåller storlekar, positioner, resurser som de är bundna till och eventuella användaranpassningar.

Det mest praktiska sättet att bygga upp det här JSON-dokumentet är att använda Azure Portal. Du kan lägga till och placera panelerna interaktivt. Exportera sedan JSON och skapa en mall från resultatet för senare användning i skript, program och distributionsverktyg.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Om du vill skapa en **instrumentpanel väljer** du [Instrumentpanel Azure Portal](https://portal.azure.com) menyn och väljer sedan **Ny instrumentpanel.**

![nytt instrumentpanelskommando](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Använd panelgalleriet för att hitta och lägga till paneler. Paneler läggs till genom att dra och släppa dem. Vissa paneler stöder storleksändring med hjälp av en dra-referens.

![dra referensen för att ändra storlek](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Andra har fasta storlekar att välja mellan på snabbmenyn.

![snabbmeny för storlekar för att ändra storlek](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Dela instrumentpanelen

När du har konfigurerat instrumentpanelen är nästa steg att publicera instrumentpanelen med hjälp av **kommandot** Dela.

![dela en instrumentpanel](./media/azure-portal-dashboards-create-programmatically/share-command.png)

Om **du** väljer Dela uppmanas du att välja vilken prenumeration och resursgrupp som du vill publicera till. Du måste ha skrivbehörighet till den prenumeration och resursgrupp som du väljer. Mer information finns i Tilldela [Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

![göra ändringar i delning och åtkomst](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Hämta JSON-representationen av instrumentpanelen

Publiceringen tar bara några sekunder. När det är klart är nästa steg att hämta JSON med kommandot **Ladda** ned.

![ladda ned JSON-representation](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Skapa en mall från JSON

Nästa steg är att skapa en mall från denna JSON. Använd mallen programmatiskt med lämpliga API:er för resurshantering, kommandoradsverktyg eller i portalen.

Du behöver inte helt förstå Instrumentpanelens JSON-struktur för att skapa en mall. I de flesta fall vill du bevara strukturen och konfigurationen för varje panel. Parameterisera sedan den uppsättning Azure-resurser som panelerna pekar på. Titta på din exporterade JSON-instrumentpanel och hitta alla förekomster av Azure-resurs-ID:er. Vår exempelinstrumentpanel har flera paneler som alla pekar på en enda virtuell Azure-dator. Det beror på att vår instrumentpanel bara tittar på den här enskilda resursen. Om du söker i JSON-exemplet, som ingår i slutet av dokumentet, för "/subscriptions" hittar du flera förekomster av detta ID.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Om du vill publicera den här instrumentpanelen för valfri virtuell dator i framtiden ska du parameterisera varje förekomst av strängen i JSON.

Det finns två metoder för API:er som skapar resurser i Azure:

* Imperativ API:er skapar en resurs i taget. Mer information finns i [Resurser](/rest/api/resources/resources).
* Ett mallbaserat distributionssystem som skapar flera beroende resurser med ett enda API-anrop. Mer information finns i  [Distribuera resurser med Resource Manager mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

Mallbaserad distribution stöder parameterisering och mallning. Vi använder den här metoden i den här artikeln.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Skapa en instrumentpanel programmässigt från mallen med hjälp av en malldistribution

Azure erbjuder möjligheten att samordna distributionen av flera resurser. Du skapar en distributionsmall som uttrycker den uppsättning resurser som ska distribueras och relationerna mellan dem.  JSON-formatet för varje resurs är detsamma som om du skulle skapa dem en i sig. Skillnaden är att mallspråket lägger till några begrepp som variabler, parametrar, grundläggande funktioner med mera. Den här utökade syntaxen stöds endast i samband med en malldistribution. Det fungerar inte om det används med imperativt API:er som diskuterats tidigare. Mer information finns i [Förstå strukturen och syntaxen för Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md).

Parameterisering bör göras med hjälp av mallens parametersyntax.  Du ersätter alla instanser av resurs-ID:t som vi hittade tidigare, som du ser här.

Exempel på JSON-egenskap med hårdkodat resurs-ID:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Exempel på JSON-egenskap som konverterats till en parametriserad version baserat på mallparametrar

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarera nödvändiga mallmetadata och parametrarna överst i JSON-mallen så här:

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
När du har konfigurerat mallen distribuerar du den med någon av följande metoder:

* [REST API:er](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az_group_deployment_create)
* [Sidan Azure Portal malldistribution](https://portal.azure.com/#create/Microsoft.Template)

Nu visas två versioner av vår exempelinstrumentpanels-JSON. Den första är den version som vi exporterade från portalen som redan var bunden till en resurs. Den andra är mallversionen som kan bindas programmatiskt till valfri virtuell dator och distribueras med hjälp av Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>JSON-representation av vår exempelinstrumentpanel före mallning

Det här exemplet visar vad du kan förvänta dig att se om du följde med i den här artikeln. Anvisningarna exporterade JSON-representationen av en instrumentpanel som redan har distribuerats. De hårdkodade resursidentifierarna visar att den här instrumentpanelen pekar på en specifik virtuell Azure-dator.

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

### <a name="template-representation-of-our-example-dashboard"></a>Mallrepresentation av vår exempelinstrumentpanel

Mallversionen av instrumentpanelen har definierat tre parametrar som kallas `virtualMachineName` `virtualMachineResourceGroup` , och `dashboardName` .  Med parametrarna kan du peka den här instrumentpanelen på en annan virtuell Azure-dator varje gång du distribuerar. Den här instrumentpanelen kan konfigureras programmässigt och distribueras så att den pekar på valfri virtuell Azure-dator. Om du vill testa den här funktionen kopierar du följande mall och klistrar in [den Azure Portal sidan för malldistribution](https://portal.azure.com/#create/Microsoft.Template).

Det här exemplet distribuerar en instrumentpanel på egen hand, men med mallspråket kan du distribuera flera resurser och paketa en eller flera instrumentpaneler längs med dem.

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

Nu när du har sett ett exempel på hur du använder en parametriserad mall för att distribuera en instrumentpanel kan du prova att distribuera mallen med hjälp av [rest-API:erna för Azure Resource Manager,](/rest/api/) [Azure CLI](/cli/azure) [eller Azure PowerShell kommandon](/powershell/azure/get-started-azureps).

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Skapa en instrumentpanel programmässigt med hjälp av Azure CLI

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- De här exemplen använder följande [ instrumentpanel:portal-dashboard-template-testvm.jspå](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Ersätt innehåll inom vinklade hakparenteser med dina värden.

Kör kommandot [az portal dashboard create för](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) att skapa en instrumentpanel:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Du kan uppdatera en instrumentpanel med hjälp av [kommandot az portal dashboard update:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Visa information om en instrumentpanel genom att köra [kommandot az portal dashboard show:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show)

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Om du vill se alla instrumentpaneler för den aktuella prenumerationen använder du [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Du kan också se alla instrumentpaneler för en resursgrupp:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om datorer finns i Hantera [Azure Portal inställningar.](set-preferences.md)

Mer information om Azure CLI-stöd för instrumentpaneler finns i [az portal dashboard](/cli/azure/ext/portal/portal/dashboard).
