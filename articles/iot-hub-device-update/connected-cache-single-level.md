---
title: Exempel för exempel på distributions scenario för Microsoft Connected cache | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Självstudier för för hands versionen av Microsoft Connected cache Preview
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665031"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Exempel på distributions scenario med Microsoft Connected cache Preview

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>En nivå Azure IoT Edge Gateway utan proxy

Diagrammet nedan beskriver scenariot där en Azure IoT Edge-gateway som har direkt åtkomst till CDN-resurser och det finns en Azure IoT-enhet, till exempel en Raspberry PI som är ett isolerat Internet-enheter på Azure IoT Edge Gateway. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Enhets uppdatering från Microsoft Connected cache" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Lägg till Microsoft Connected cache-modulen till din Azure IoT Edge Gateway enhets distribution i Azure IoT Hub (se `MCC concepts` Mer information om hur du hämtar modulen).
2. Lägg till miljövariablerna för distributionen. Nedan visas ett exempel på miljövariabler.

    **Miljövariabler**
    
    | Namn                 | Värde                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Se beskrivning av miljö variabel ovan. |
    | CUSTOMER_ID                   | Se beskrivning av miljö variabel ovan. |
    | CUSTOMER_KEY                  | Se beskrivning av miljö variabel ovan. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Lägg till behållar skapande alternativen för distributionen. Nedan visas ett exempel på alternativet för att skapa behållare.

### <a name="container-create-options"></a>Alternativ för att skapa behållare

```markdown
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
```

För en validering av korrekt fungerande Microsoft-ansluten cache kör du följande kommando i terminalen av den IoT Edge enhet som är värd för modulen eller en enhet i nätverket.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Azure IoT Edge Gateway med en nivå med utgående oautentiserad proxy

I det här scenariot finns en Azure IoT Edge-gateway som har åtkomst till CDN-resurser via en utgående oautentiserad proxy. Microsoft Connected cache konfigureras för cachelagring av innehåll från en anpassad lagrings plats och sammanfattnings rapporten har gjorts synlig för alla i nätverket. Nedan visas ett exempel på de MCC-miljövariabler som skulle anges.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Connected Cache-proxy på en nivå" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Lägg till Microsoft Connected cache-modulen till din Azure IoT Edge Gateway enhets distribution i Azure IoT Hub.
2. Lägg till miljövariablerna för distributionen. Nedan visas ett exempel på miljövariabler.

    **Miljövariabler**

    | Namn                 | Värde                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Se beskrivning av miljö variabel ovan. |
    | CUSTOMER_ID                   | Se beskrivning av miljö variabel ovan. |
    | CUSTOMER_KEY                  | Se beskrivning av miljö variabel ovan. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Proxy Server-IP eller FQDN                     |

3. Lägg till behållar skapande alternativen för distributionen. Det finns ingen skillnad i MCC container Create Options från föregående exempel. Nedan visas ett exempel på alternativet för att skapa behållare.

### <a name="container-create-options"></a>Alternativ för att skapa behållare

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
```

För en validering av korrekt fungerande Microsoft-ansluten cache kör du följande kommando i terminalen av den Azure IoT Edge enhet som är värd för modulen eller en enhet i nätverket.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
