---
title: Konfigurera API-protokoll – Azure Event Grid IoT Edge | Microsoft Docs
description: Lär dig mer om de möjliga protokoll konfigurationerna för en Event Grid modul.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91322588"
---
# <a name="configure-event-grid-api-protocols"></a>Konfigurera Event Grid API-protokoll

Den här guiden innehåller exempel på möjliga protokoll konfigurationer för en Event Grid modul. Modulen Event Grid visar API: er för hanterings-och körnings åtgärder. I följande tabell inhämtas protokollen och portarna.

| Protokoll | Port | Beskrivning |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Inaktive rad som standard. Används bara vid testning. Passar inte för produktions arbets belastningar.
| HTTPS | 4438 | Standardvärde

Se [säkerhets-och autentiserings](security-authentication.md) guide för alla möjliga konfigurationer.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>Exponera HTTPS för IoT-moduler i samma Edge-nätverk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>Aktivera HTTPS till andra IoT-moduler och arbets belastningar som inte är IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> I avsnittet **PortBindings** kan du mappa interna portar till portar för behållar värden. Den här funktionen gör det möjligt att komma åt Event Grid modulen utanför IoT Edge behållar nätverket, om IoT Edge-enheten kan kontaktas offentligt.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>Exponera HTTP och HTTPS i IoT-moduler på samma Edge-nätverk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>Aktivera HTTP och HTTPS för andra IoT-moduler och arbets belastningar som inte är IoT

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Varje IoT-modul ingår som standard i IoT Edge runtime som skapats av brygga nätverket. Det gör att olika IoT-moduler i samma nätverk kan kommunicera med varandra. Med **PortBindings** kan du mappa en intern port för behållare till värddatorn så att alla kan komma åt Event Grid modulens port utanför.

>[!IMPORTANT]
> Även om portarna kan göras tillgängliga utanför IoT Edge nätverket tvingar klientautentisering som faktiskt har tillåtelse att göra anrop till modulen.
