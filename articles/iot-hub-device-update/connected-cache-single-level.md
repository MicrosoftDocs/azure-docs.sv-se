---
title: Distributionsscenarioexempel Ansluten cache Förhandsversion av Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Självstudier Ansluten cache distributionsscenario för förhandsversionen av Microsoft Ansluten cache
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811732"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Distributionsscenarioexempel Ansluten cache Förhandsversion av Microsoft Ansluten cache

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>En enda Azure IoT Edge ingen proxy

Diagrammet nedan beskriver scenariot där en Azure IoT Edge-gateway som har direkt åtkomst till CDN-resurser och det finns en Azure IoT-lövenhet, till exempel en Raspberry PI som är en Internet-isolerad underordnad enhet för Azure IoT Edge-gatewayen. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft Ansluten cache frånkopplad enhetsuppdatering" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Lägg till Microsoft Ansluten cache-modulen i din Azure IoT Edge gatewayenhetsdistribution i Azure IoT Hub (se Stöd för [frånkopplade](connected-cache-disconnected-device-update.md) enheter för mer information om hur du hämtar modulen).
2. Lägg till miljövariablerna för distributionen. Nedan visas ett exempel på miljövariablerna.

    **Miljövariabler**
    
    | Name                          | Värde                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | Kund                   | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Lägg till alternativ för att skapa containrar för distributionen. Nedan visas ett exempel på alternativ för att skapa containrar.

### <a name="container-create-options"></a>Alternativ för att skapa containrar

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

Kör följande kommando i terminalen IoT Edge den enhet som är värd för modulen eller vilken enhet som helst i nätverket för att validera Microsoft Ansluten cache som fungerar korrekt. Ersätt \<Azure IoT Edge Gateway IP\> med IP-adressen eller värdnamnet för din IoT Edge gateway. (se information om miljövariabler för information om den här rapportens synlighet).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Gateway på Azure IoT Edge nivå med utgående oauthenticerad proxy

I det här scenariot finns en Azure IoT Edge Gateway som har åtkomst till CDN-resurser via en utgående oauthenticerad proxy. Microsoft Ansluten cache konfigureras för att cachelagra innehåll från en anpassad lagringsplats och sammanfattningsrapporten har gjorts synlig för alla i nätverket. Nedan visas ett exempel på de MCC-miljövariabler som skulle anges.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Ansluten cache proxy på enkel nivå" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Lägg till Microsoft Ansluten cache-modulen till din Azure IoT Edge gatewayenhetsdistribution i Azure IoT Hub.
2. Lägg till miljövariablerna för distributionen. Nedan visas ett exempel på miljövariablerna.

    **Miljövariabler**

    | Name                          | Värde                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | Kund                   | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | CUSTOMER_KEY                  | Se [beskrivningar av miljövariabler](connected-cache-configure.md) |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |
    | UPSTREAM_PROXY                | Proxyserverns IP-adress eller FQDN                                          |

3. Lägg till alternativ för att skapa containrar för distributionen. Det finns ingen skillnad i alternativen för att skapa MCC-containrar från föregående exempel. Nedan visas ett exempel på alternativ för att skapa containrar.

### <a name="container-create-options"></a>Alternativ för att skapa containrar

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

Om du vill validera att Microsoft Ansluten cache fungerar korrekt kör du följande kommando i terminalen för Azure IoT Edge som är värd för modulen eller någon annan enhet i nätverket. Ersätt \<Azure IoT Edge Gateway IP\> med IP-adressen eller värdnamnet för din IoT Edge gateway. (se information om miljövariabler för information om synligheten för den här rapporten).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
