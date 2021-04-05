---
title: Stöd för Azure IoT Hub TLS
description: Lär dig mer om att använda säkra TLS-anslutningar för enheter och tjänster som kommunicerar med IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: d36a7917693aef9063ade473759f2f451d3a677f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234026"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Stöd för Transport Layer Security (TLS) i IoT Hub

IoT Hub använder Transport Layer Security (TLS) för att skydda anslutningar från IoT-enheter och-tjänster. Tre versioner av TLS-protokollet stöds för närvarande, nämligen version 1,0, 1,1 och 1,2.

TLS 1,0 och 1,1 betraktas som äldre och planeras för utfasning. Mer information finns i [inaktuella TLS 1,0 och 1,1 för IoT Hub](iot-hub-tls-deprecating-1-0-and-1-1.md). Undvik framtida problem genom att använda TLS 1,2 som den enda TLS-versionen när du ansluter till IoT Hub.

## <a name="iot-hubs-server-tls-certificate"></a>IoT Hubs serverns TLS-certifikat

Under en TLS-handskakning visar IoT Hub RSA-kodade Server certifikat för att ansluta klienter. Roten är rot certifikat utfärdaren för Baltimore CyberTrust. Nyligen har vi lanserat en ändring av vårt TLS-servercertifikat så att det nu utfärdas av nya, mellanliggande certifikat utfärdare (ICA). Mer information finns i [IoT Hub TLS-certifikat uppdatering](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>TLS-certifikat (Elliptic Curve Cryptography) för Server (för hands version)

IoT Hub ECC-serverns TLS-certifikat är tillgängligt för en offentlig för hands version. Samtidigt som vi erbjuder liknande säkerhet för RSA-certifikat, använder ECC-certifikat verifiering (med endast ECC-chiffersviter) upp till 40% mindre beräkning, minne och bandbredd. Dessa besparingar är viktiga för IoT-enheter på grund av deras mindre profiler och minne, och för att ge stöd för användnings fall i begränsade miljöer med nätverks bandbredd. 

För hands version av IoT Hubens ECC-servercertifikat:

1. [Skapa en ny IoT-hubb med förhands gransknings läge på](iot-hub-preview-mode.md).
1. [Konfigurera klienten](#tls-configuration-for-sdk-and-iot-edge) så att den *endast* inkluderar ECDSA CHIFFER och *exkludera* alla RSA-paket. Detta är de chiffersviter som stöds för den offentliga för hands versionen av ECC-certifikat:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Anslut din klient till för hands versionen av IoT Hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>TLS 1,2-tvång är tillgängligt i utvalda regioner

För ytterligare säkerhet konfigurerar du IoT-hubbarna så att de *bara* tillåter klient anslutningar som använder TLS version 1,2 och för att framtvinga användningen av [chiffersviter](#cipher-suites). Den här funktionen stöds bara i följande regioner:

* East US
* USA, södra centrala
* USA, västra 2
* US Gov, Arizona
* US Gov, Virginia (TLS 1.0/1.1-stöd är inte tillgängligt i den här regionen-TLS 1,2-tvång måste vara aktiverat eller så Miss lyckas skapande av IoT Hub)

Om du vill aktivera TLS 1,2-tvång följer du stegen i [skapa IoT Hub i Azure Portal](iot-hub-create-through-portal.md), förutom

- Välj en **region** från ett i listan ovan.
- Under **hantering-> Advanced-> Transport Layer Security (TLS)-> lägsta TLS-version** väljer du **1,2**. Den här inställningen visas bara för IoT Hub som skapats i en region som stöds.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="Skärm bild som visar hur du aktiverar TLS 1,2-tvång när IoT Hub skapas":::

Om du vill använda ARM-mall för att skapa en ny IoT Hub i någon av de regioner som stöds och ange `minTlsVersion` egenskapen till `1.2` i resurs specifikationen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Den skapade IoT Hub-resursen som använder den här konfigurationen kommer att neka enhets-och tjänst klienter som försöker ansluta med TLS-versionerna 1,0 och 1,1. På samma sätt kommer TLS-handskakningen att nekas om `ClientHello` meddelandet inte listar några av de [rekommenderade chifferna](#cipher-suites).

> [!NOTE]
> `minTlsVersion`Egenskapen är skrivskyddad och kan inte ändras när IoT Hub resurs har skapats. Det är därför viktigt att du testar och kontrollerar att *alla* IoT-enheter och-tjänster är kompatibla med TLS 1,2 och de [rekommenderade chifferna](#cipher-suites) i förväg.
> 
> Vid redundans `minTlsVersion` fortsätter egenskapen för dina IoT Hub att vara effektiv i den geo-kopplade regionen efter redundansväxlingen.

## <a name="cipher-suites"></a>Chiffersviter

IoT-hubbar som är konfigurerade för att endast acceptera TLS 1,2 kommer också att framtvinga användning av följande rekommenderade chiffersviter:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

För IoT-hubbar som inte har kon figurer ATS för TLS 1,2 fungerar TLS 1,2 fortfarande med följande chiffersviter:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

En-klient kan föreslå en lista över höga chiffersviter som ska användas under `ClientHello` . Men vissa av dem kanske inte stöds av IoT Hub (till exempel `ECDHE-ECDSA-AES256-GCM-SHA384` ). I det här fallet försöker IoT Hub följa prioriteten för klienten, men kan till och med förhandla ned cipher Suite med `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>TLS-konfiguration för SDK och IoT Edge

Använd länkarna nedan för att konfigurera TLS 1,2 och tillåtna chiffer i IoT Hub klient-SDK: er.

| Språk | Versioner som stöder TLS 1,2 | Dokumentation |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 eller senare            | [Länk](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Version 2.0.0 eller senare             | [Länk](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Version 1.21.4 eller senare            | [Länk](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Version 1.19.0 eller senare            | [Länk](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | Version 1.12.2 eller senare            | [Länk](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge enheter kan konfigureras för att använda TLS 1,2 vid kommunikation med IoT Hub. För det här ändamålet använder du [sidan IoT Edge dokumentation](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Enhetsautentisering

Efter en lyckad TLS-handskakning kan IoT Hub autentisera en enhet med hjälp av en symmetrisk nyckel eller ett X. 509-certifikat. För certifikatbaserad autentisering kan detta vara alla X. 509-certifikat, inklusive ECC. IoT Hub validerar certifikatet mot det tumavtryck eller den certifikat utfärdare (CA) som du anger. Mer information finns i [X. 509-certifikat som stöds](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Maximal förhandling för fragmentering i fragment (för hands version)

IoT Hub stöder också TLS-maximal förhandling av fragment längd, som ibland kallas för förhandlingar om TLS-bildruta. Den här funktionen är en allmänt tillgänglig förhandsversion. 

Använd den här funktionen för att ange den maximala fragment längden i klartext till ett värde som är mindre än standard 2 ^ 14 byte. Efter förhandlat, IoT Hub och klienten påbörjar fragmentering av meddelanden för att se till att alla fragment är mindre än den förhandlade längden. Det här beteendet är användbart för att beräkna eller begränsa enheter. Mer information finns i [specifikationen för officiella TLS-tillägg](https://tools.ietf.org/html/rfc6066#section-4).

Statligt SDK-stöd för den här offentliga för hands versions funktionen är inte tillgänglig ännu. För att komma igång

1. [Skapa en ny IoT-hubb med förhands gransknings läge på](iot-hub-preview-mode.md).
1. När du använder OpenSSL anropar du [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) för att ange Fragmenteringens storlek.
1. Anslut din klient till förhands gransknings IoT Hub.

## <a name="next-steps"></a>Nästa steg

- Mer information om IoT Hub säkerhet och åtkomst kontroll finns i [kontrol lera åtkomst till IoT Hub](iot-hub-devguide-security.md).
- Mer information om hur du använder X509-certifikat för enhetsautentisering finns i [enhetsautentisering med X. 509 CA-certifikat](iot-hub-x509ca-overview.md)
