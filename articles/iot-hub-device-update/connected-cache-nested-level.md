---
title: Microsoft Connected cache är två nivåer kapslade Azure IoT Edge Gateway med utgående oautentiserad proxy | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Connected cache två nivåer kapslade Azure IoT Edge Gateway med självstudier för utgående oautentiserad proxy
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0128d0de4f078b62bc9571c8758d80cb26585354
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615388"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Exempel på distributions scenario med Microsoft Connected cache Preview: två nivåer kapslad Azure IoT Edge Gateway med utgående oautentiserad proxy

I det här scenariot visas en Azure IoT Edge gateway och en underordnad Azure IoT Edge enhet, en Azure IoT Edge gateway som är överordnad en annan Azure IoT Edge gateway och en proxyserver på IT-DMZ. Nedan visas ett exempel på de Microsoft-variabler för anslutna cache-miljöer som skulle anges i Azure Portal UX för båda de MCC-moduler som distribueras till Azure IoT Edge-gatewayer. Exemplet visar konfigurationen för två nivåer med Azure IoT Edge gatewayer, men det finns ingen gräns för djupet för överordnade värdar som Microsoft Connected cache kommer att stödja. Det finns ingen skillnad i MCC container Create Options från föregående exempel.

Mer information om hur du konfigurerar lager distributioner av Azure IoT Edge gateways finns i dokumentationen [Anslut underordnade IoT Edge enheter – Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-connect-downstream-iot-edge-device?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true) . Observera att när du distribuerar Azure IoT Edge, Microsoft Connected cache och anpassade moduler måste alla moduler finnas i samma behållar register.

Diagrammet nedan beskriver scenariot där en Azure IoT Edge gateway som direkt åtkomst till CDN-resurser fungerar som överordnad till en annan Azure IoT Edge gateway som fungerar som överordnad till en Azure IoT-enhet, till exempel en Raspberry Pi. Endast den överordnade Azure IoT Edge-gatewayen har Internet anslutning till CDN-resurser och både Azure IoT Edge underordnad och Azure IoT-enhet är Internet isolerade. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft anslöt cache-kapslad" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Överordnad gateway-konfiguration

1. Lägg till Microsoft Connected cache-modulen till din Azure IoT Edge Gateway enhets distribution i Azure IoT Hub.
2. Lägg till miljövariablerna för distributionen. Nedan visas ett exempel på miljövariabler.

    **Miljövariabler**

    | Name                 | Värde                                       |
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

## <a name="child-gateway-configuration"></a>Konfiguration av underordnad Gateway

>[!Note]
>Om du har replikerat behållare som används i din konfiguration i ditt eget privata register, måste du göra ändringar i inställningarna för config. toml och körnings inställningarna i modulen. Mer information finns i [självstudier – skapa en hierarki med IoT Edge enheter – Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-nested-iot-edge?view=iotedge-2020-11&tabs=azure-portal&preserve-view=true#deploy-modules-to-the-lower-layer-device) för mer information.

1. Ändra avbildnings Sök vägen för Edge-agenten så som visas i exemplet nedan:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Ändra inställningarna för Edge Hub och Edge-agentens körning i Azure IoT Edge distributionen som visas i det här exemplet:
    
    * Skriv i fältet bild under Edge Hub ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Skriv i fältet bild under Edge-agent ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Lägg till Microsoft Connected cache-modulen till din Azure IoT Edge Gateway enhets distribution i Azure IoT Hub.

   * Välj ett namn för modulen: ```ConnectedCache```
   * Ändra avbildnings-URI: n: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Lägg till samma miljövariabler och behållar skapande alternativ som används i den överordnade distributionen.

För en validering av korrekt fungerande Microsoft-ansluten cache kör du följande kommando i terminalen av den IoT Edge enhet som är värd för modulen eller en enhet i nätverket.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```