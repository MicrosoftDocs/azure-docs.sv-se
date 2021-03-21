---
title: 'Azure VPN Gateway: Konfigurera paket fångst'
description: Lär dig mer om paket insamlings funktioner som du kan använda på VPN-gatewayer för att begränsa orsaken till ett problem.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 0983139d1c9af235eba4c9f99da7bc9dea3f231b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726622"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Konfigurera paket fångst för VPN-gatewayer

Anslutnings-och prestanda relaterade problem är ofta komplicerade. Det kan ta lång tid och ansträngningar att bara minska orsaken till problemet. Paket fångst kan hjälpa dig att begränsa omfattningen av ett problem till vissa delar av nätverket. Det kan hjälpa dig att avgöra om problemet finns på kund sidan av nätverket, på Azure-sidan av nätverket eller någonstans i mellan. När du har begränsat problemet är det mer effektivt att felsöka och vidta åtgärder.

Det finns några vanliga verktyg för paket insamling. Att hämta relevanta paket insamlingar med dessa verktyg kan vara besvärligt, särskilt i trafik scenarier med stora volymer. De filtrerings funktioner som tillhandahålls av Azure VPN Gateway Packet Capture är en större differentiering. Du kan använda VPN Gateway paket avbildning tillsammans med vanliga tillgängliga paket insamlings verktyg.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filtrerings funktioner för VPN Gateway paket insamling

Du kan köra VPN Gateway paket avbildning på gatewayen eller en speciell anslutning, beroende på dina behov. Du kan också köra paket fångst på flera tunnlar samtidigt. Du kan fånga enkelriktad eller dubbelriktad trafik, IKE-och ESP-trafik och interna paket tillsammans med filtrering på en VPN-gateway.

Det är praktiskt att använda ett filter med fem tupler (käll under nät, mål under nät, källport, målport, protokoll) och TCP-flaggor (SYN, ACK, FIN, URG, PSH och per användning) när du isolerar problem med hög volym trafik.

Följande exempel på JSON och ett JSON-schema innehåller förklaringar av varje egenskap. Här följer några begränsningar som du bör tänka på när du kör paket fångster:

- I det schema som visas här är filtret en matris, men för närvarande kan endast ett filter användas i taget.
- Du kan inte köra flera paket avbildningar i hela gatewayen på samma gång.
- Du kan inte köra flera paket avbildningar på samma gång. Du kan köra flera paket insamlingar på olika anslutningar samtidigt.
- Högst fem paket avbildningar kan köras parallellt per Gateway. De här paket avbildningarna kan vara en kombination av paket-och paket fångster per anslutning.
- Enheten för MaxPacketBufferSize är byte och MaxFileSize är megabyte

### <a name="example-json"></a>Exempel-JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON-schema
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>Paket insamling – Portal

Du kan konfigurera paket fångst i Azure Portal.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="Skärm bild av paket fångst i portalen." lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>Paket insamling – PowerShell

I följande exempel visas PowerShell-kommandon som startar och stoppar paket fångster. Mer information om parameter alternativ finns i [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Starta paket fångst för en VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Du kan använda den valfria parametern `-FilterData` om du vill använda ett filter.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Stoppa paket fångst för en VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Starta paket fångst för en VPN gateway-anslutning

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Du kan använda den valfria parametern `-FilterData` om du vill använda ett filter.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Stoppa paket fångst på en VPN gateway-anslutning

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Viktiga överväganden

- Att köra paket fångst kan påverka prestandan. Kom ihåg att stoppa paket fångsten när du inte behöver den.
- Föreslagen minsta paket hämtnings tid är 600 sekunder. På grund av synkroniseringsproblem mellan flera komponenter på sökvägen kanske inte kortare paket insamlingar ger fullständiga data.
- Paket insamlings data genereras i PCAP-format. Använd wireshark eller andra program som ofta är tillgängliga för att öppna PCAP-filer.
- Paket fångster stöds inte på principbaserad gatewayer.
- Om `SASurl` parametern inte är korrekt konfigurerad, kan det hända att spårningen Miss känner till lagrings fel. Exempel på hur du skapar en parameter på rätt sätt `SASurl` finns i [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Nästa steg

Mer information om VPN Gateway finns i [VPN gateway?](vpn-gateway-about-vpngateways.md).
