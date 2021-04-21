---
title: Microsoft Ansluten cache två nivåkapslade Azure IoT Edge Gateway med utgående oauticerade proxyservrar | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Ansluten cache självstudiekurs om kapslade Azure IoT Edge gateway med utgående oauthenticerad proxy
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811894"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Distributionsscenarioexempel Ansluten cache Microsoft Ansluten cache förhandsversion: Två nivåkapslade Azure IoT Edge Gateway med utgående oauticerad proxy

Diagrammet nedan beskriver scenariot där en Azure IoT Edge gateway har direkt åtkomst till CDN-resurser och fungerar som överordnad till en annan Azure IoT Edge gateway. Den underordnade IoT Edge gatewayen fungerar som överordnad till en Azure IoT-lövenhet, till exempel en Raspberry Pi. Både den Azure IoT Edge och Azure IoT-enheten är isolerade via Internet. Exemplet nedan visar konfigurationen för två nivåer av Azure IoT Edge-gatewayer, men det finns ingen gräns för djupet för överordnade värdar som Microsoft Ansluten cache stöder. Det finns ingen skillnad i Microsoft Ansluten cache alternativ för att skapa containrar från föregående exempel.

Se dokumentationen Connect [downstream IoT Edge devices - Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) (Anslut nedströmsenheter – Azure IoT Edge mer information om hur du konfigurerar flerlagersdistributioner av Azure IoT Edge-gatewayer. Observera också att när du distribuerar Azure IoT Edge, Microsoft Ansluten cache och anpassade moduler måste alla moduler finnas i samma containerregister.

>[!Note]
>När du Azure IoT Edge, Microsoft Ansluten cache och anpassade moduler måste alla moduler finnas i samma containerregister.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft Ansluten cache kapslad" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Konfiguration av överordnad gateway
1. Lägg till Microsoft Ansluten cache-modulen till din Azure IoT Edge gatewayenhetsdistribution i Azure IoT Hub [](connected-cache-disconnected-device-update.md) (mer information om hur du hämtar modulen finns i Stöd för frånkopplade enheter).
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

## <a name="child-gateway-configuration"></a>Konfiguration av underordnad gateway

>[!Note]
>Om du har replikerat containrar som används i konfigurationen i ditt eget privata register måste du ändra config.toml-inställningarna och körningsinställningarna i moduldistributionen. Mer information finns i Ansluta [nedströmsenheter IoT Edge enheter – Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) mer information.


1. Ändra bildsökvägen för Edge-agenten enligt exemplet nedan:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Ändra körningsinställningarna för Edge Hub och Edge-agenten i Azure IoT Edge distributionen enligt det som visas i det här exemplet:
    
    * Under Edge Hub går du till bildfältet och anger ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Under Edge-agent anger du i bildfältet ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Lägg till Microsoft Ansluten cache-modulen till din Azure IoT Edge gatewayenhetsdistribution i Azure IoT Hub.

   * Välj ett namn för modulen: ```ConnectedCache```
   * Ändra bild-URI: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Lägg till samma uppsättning miljövariabler och alternativ för containerskapning som används i den överordnade distributionen.
>[!Note]
>Den CACHE_NODE_ID ska vara unik.  Värdena CUSTOMER_ID och CUSTOMER_KEY är identiska med det överordnade. (se [Konfigurera Microsoft Ansluten cache](connected-cache-configure.md)

Om du vill validera att Microsoft Ansluten cache fungerar korrekt kör du följande kommando i terminalen för IoT Edge som är värd för modulen eller någon annan enhet i nätverket. Ersätt \<Azure IoT Edge Gateway IP\> med IP-adressen eller värdnamnet för din IoT Edge gateway. (se information om miljövariabler för information om synligheten för den här rapporten).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```