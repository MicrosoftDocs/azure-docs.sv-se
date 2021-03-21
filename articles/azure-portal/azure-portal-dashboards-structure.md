---
title: Strukturen för Azure-instrumentpaneler
description: Gå igenom JSON-strukturen för en Azure-instrumentpanel med hjälp av ett exempel på en instrument panel. Innehåller en referens till resurs egenskaper.
ms.topic: conceptual
ms.date: 12/20/2019
ms.openlocfilehash: d37e2fd9c9f6ef6e7ddea6dea002f26f20cd66a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745969"
---
# <a name="the-structure-of-azure-dashboards"></a>Strukturen för Azure-instrumentpaneler
Det här dokumentet vägleder dig genom strukturen i en Azure-instrumentpanel med hjälp av följande instrument panel som exempel:

![exempelinstrumentpanel](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Eftersom delade [Azure-instrumentpaneler är resurser](../azure-resource-manager/management/overview.md)kan den här instrument panelen visas som JSON.  Följande JSON representerar den instrument panel som visualiseras ovan.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Gemensamma resurs egenskaper

Nu ska vi dela upp relevanta avsnitt i JSON.  Egenskaperna på den översta nivån, __ID__, __namn__, __typ__, __plats__ och __taggar__ delas mellan alla typer av Azure-resurser. Det vill säga att de inte har mycket att göra med instrument panelens innehåll.

### <a name="the-id-property"></a>Egenskapen ID

Azure-resurs-ID, omfattas [av namngivnings konventionerna för Azure-resurser](/azure/architecture/best-practices/resource-naming). När portalen skapar en instrument panel väljer den vanligt vis ett ID i form av ett GUID, men du kan använda ett giltigt namn när du skapar dem program mässigt. 

### <a name="the-name-property"></a>Egenskapen namn
Namnet är segmentet för resurs-ID: t som inte innehåller information om prenumeration, resurs typ eller resurs grupp. Det är i stort sett det sista segmentet i resurs-ID: t.

### <a name="the-type-property"></a>Egenskapen Type
Alla instrument paneler är av typen __Microsoft. Portal/instrument paneler__.

### <a name="the-location-property"></a>Egenskapen Location
Till skillnad från andra resurser har instrument paneler inte någon körnings komponent.  För instrument paneler anger platsen den primära geografiska platsen som lagrar instrument panelens JSON-representation. Värdet ska vara en av de plats koder som kan hämtas med hjälp av [locations-API: et i prenumerations resursen](/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>Egenskapen Taggar
Taggar är en vanlig funktion i Azure-resurser som gör att du kan organisera din resurs efter godtyckliga namn värde par. För instrument paneler finns det en särskild tagg som kallas __dold rubrik__. Om din instrument panel har den här egenskapen ifylld används den som visnings namn för instrument panelen i portalen. Det går inte att byta namn på Azure-resurs-ID: n, men taggar kan. Den här taggen ger dig ett sätt att ha ett renamable-visnings namn för din instrument panel.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Objektet egenskaper
Egenskaps-objektet innehåller två egenskaper, __linser__ och __metadata__. Egenskapen __linser__ innehåller information om panelerna på instrument panelen.  Egenskapen __metadata__ finns där för potentiella framtida funktioner.

### <a name="the-lenses-property"></a>Egenskapen linser
Egenskapen __linser__ innehåller instrument panelen. Observera att linser-objektet i det här exemplet innehåller en enskild egenskap med namnet "0". Linser är ett grupperingsintervall som för närvarande inte är implementerat i instrument paneler. För närvarande har alla dina instrument paneler denna egenskap på objektet lins, återigen, med namnet "0".

### <a name="the-lens-object"></a>Lins-objektet
Objektet under "0" innehåller två egenskaper, __ordning__ och __delar__.  I den aktuella versionen av instrument paneler är __ordningen__ alltid 0. Egenskapen __delar__ innehåller ett objekt som definierar de enskilda delarna (kallas även paneler) på instrument panelen.

Objektet __delar__ innehåller en egenskap för varje del, där namnet på egenskapen är ett tal. Talet är inte signifikant. 

### <a name="the-part-object"></a>Del objekt
Varje enskilt del objekt har en __position__ och __metadata__.

### <a name="the-position-object"></a>Objektet position
Egenskapen __position__ innehåller storlek och plats information för delen uttryckt som __x__, __y__, __rowSpan__ och __colSpan__. Värdena är vad gäller rutnäts enheter. Dessa rutnäts enheter visas när instrument panelen är i anpassa-läget som visas här. Om du vill att en panel ska ha en bredd på två rutnäts enheter, en höjd av en rutnäts enhet och en plats i det övre vänstra hörnet på instrument panelen, ser objektet position ut så här:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![Skärm bild som visar en närbild av rutnätet med en enhet med kvadratiska rutnät markerade.](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Metadataobjektet
Varje del har en metadata-egenskap, ett objekt har endast en obligatorisk egenskap som kallas __typ__. Den här strängen visar portalen vilken panel som ska visas. I vårt exempel instrument panel används följande typer av paneler:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – Används för att Visa övervaknings mått
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – Används för att visa text eller bilder med grundläggande formatering för listor, länkar osv.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – Används för att visa videor från YouTube, channel9 och någon annan typ av video som fungerar i en HTML-video-tagg.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – Används för att visa namn och status för en virtuell Azure-dator.

Varje typ av del har en egen konfiguration. De möjliga konfigurations egenskaperna kallas __indata__, __Inställningar__ och __till gång__. 

### <a name="the-inputs-object"></a>Objektet Inputs
Objektet Inputs innehåller vanligt vis information som binder en panel till en resurs instans.  Den virtuella dator delen i vårt exempel instrument panel innehåller en enda inmatare som använder Azures resurs-ID för att uttrycka bindningen.  Detta resurs-ID-format är konsekvent för alla Azure-resurser.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Mått diagram delen har en enda indata som uttrycker resursen att binda till, samt information om de mått som visas. Här är indatamängden för den panel som visar nätverket i och nätverks mått.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>Settings-objektet
Objektet Settings innehåller de konfigurerbara elementen för en del.  I vårt exempel instrument panel använder MARKDOWN-delen inställningar för att lagra det anpassade markdown-innehållet samt en konfigurerbar rubrik och under rubrik.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

På samma sätt har video panelen sina egna inställningar som innehåller en pekare till videon som ska spelas upp, en inställning för automatisk uppspelning och valfri rubrik information.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Objektet till gång
Paneler som är kopplade till första klass hanterbara Portal objekt (kallas till gångar) har den här relationen uttrycks via objektet till gång.  I vårt exempel på en instrument panel innehåller den virtuella datorn den här till gångs beskrivningen.  Egenskapen __idInputName__ anger portalen att ID-indatamängden innehåller den unika identifieraren för till gången, i det här fallet resurs-ID. De flesta typer av Azure-resurser har till gångar definierade i portalen.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`