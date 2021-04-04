---
title: Azure IoT Device Provisioning service (DPS) TLS-stöd
description: Metod tips i använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Device Provisioning-tjänsten (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761302"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>TLS-stöd i Azure IoT Hub Device Provisioning Service (DPS)

DPS använder [Transport Layer Security (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) för att skydda anslutningar från IoT-enheter. 

De aktuella TLS-protokolls versioner som stöds av DPS är: 
* TLS 1.2

TLS 1,0 och 1,1 betraktas som äldre och planeras för utfasning. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>Begränsa anslutningar till TLS 1,2

För ökad säkerhet rekommenderar vi att du konfigurerar dina DPS-instanser så att de *bara* tillåter klient anslutningar för klienter som använder TLS version 1,2 och för att framtvinga användning av [rekommenderade chiffer](#recommended-ciphers).

Det gör du genom att etablera en ny DPS-resurs som anger `minTlsVersion` egenskapen till `1.2` i din Azure Resource Manager MALLs DPS-resurs specifikation. Följande exempel-mall-JSON anger `minTlsVersion` egenskapen för en ny DPS-instans.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Du kan distribuera mallen med följande Azure CLI-kommando. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Mer information om hur du skapar DPS-resurser med Resource Manager-mallar finns i, [Konfigurera DPS med en Azure Resource Manager mall](quick-setup-auto-provision-rm.md).

DPS-resursen som skapas med den här konfigurationen kommer att neka enheter som försöker ansluta med TLS-versionerna 1,0 och 1,1. På samma sätt kommer TLS-handskakningen att nekas om enhets klientens HELLO-meddelande inte listar några av de [rekommenderade chiffer](#recommended-ciphers).

> [!NOTE]
> `minTlsVersion`Egenskapen är skrivskyddad och kan inte ändras när din DPS-resurs har skapats. Det är därför viktigt att du testar och kontrollerar att *alla* IoT-enheter är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#recommended-ciphers) i förväg.


> [!NOTE]
> Vid redundans fortsätter `minTlsVersion` egenskapen för DPS att vara effektiv i den geo-kopplade regionen efter redundansväxlingen.

## <a name="recommended-ciphers"></a>Rekommenderade chiffer

DPS-instanser som har kon figurer ATS för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande chiffersviter:


| Rekommenderade TLS 1,2 cipher-paket |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Bakåtkompatibla chiffersviter 

Dessa chiffersviter stöds för närvarande inte av DPS men kommer att skrivas av. Använd de rekommenderade chiffersviter ovan om möjligt.

| Alternativ #1 (bättre säkerhet) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Alternativ #2 (bättre prestanda) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>Använd TLS 1,2 i IoT SDK: er

Använd länkarna nedan för att konfigurera TLS 1,2 och tillåtna chiffer i Azure IoT-klientens SDK: er.

| Språk | Versioner som stöder TLS 1,2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 eller senare            | [Länk](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 eller senare             | [Länk](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 eller senare            | [Länk](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 eller senare            | [Länk](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 eller senare            | [Länk](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>Använd TLS 1,2 med IoT Hub

IoT Hub kan konfigureras att använda TLS 1,2 vid kommunikation med enheter. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>Använd TLS 1,2 med IoT Edge

IoT Edge enheter kan konfigureras för att använda TLS 1,2 när de kommunicerar med IoT Hub och DPS. Mer information finns på [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
