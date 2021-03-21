---
title: Aktivera förhands gransknings läge för Azure IoT Hub
description: Lär dig hur du aktiverar förhands gransknings läget för IoT Hub, varför du vill och vissa varningar
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621713"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Aktivera förhands gransknings läge för IoT Hub prova att välja nya funktioner

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Nya funktioner finns i en allmänt tillgänglig för hands version för IoT Hub, inklusive:

- MQTT 5
- ECC-servercertifikat
- Längd förhandling för TLS-fragment
- Stöd för X509 CA-kedja för HTTPS/WebSocket

Dessa funktioner är förbättringar i IoT Hub protokoll-och autentiserings lager, så de är bara tillgängliga för **nya** IoT Hub för tillfället. De är *inte* tillgängliga för befintliga IoT-hubbar ännu. Om du vill förhandsgranska dessa funktioner måste IoT Hub skapas med förhands gransknings läge aktiverat.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Aktivera förhands gransknings läge för en ny IoT Hub

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Start sidan för Azure väljer du knappen **+ skapa en resurs** och anger sedan *IoT Hub* i fältet **Sök i Marketplace** .

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** , fyller du i fälten [som vanligt](iot-hub-create-through-portal.md) , förutom **region**. Välj något av följande regioner:
    
    - Central US
    - Europa, västra
    - Sydostasien

1. Välj fliken **hantering** och expandera sedan avsnittet **Avancerade inställningar** .

1. Välj **på** bredvid **förhands gransknings läge**. Granska varnings texten noggrant.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Bild som visar var du väljer alternativet förhands gransknings läge när du skapar en ny IoT-hubb":::

1. Välj **Nästa: granska + skapa** och sedan **skapa**.

När du har skapat en IoT Hub i förhands gransknings läget visas alltid denna banderoll, så att du kan använda den här IoT-hubben endast för för hands versioner: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Bild som visar banderoll för förhands gransknings läge IoT Hub":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Använda en IoT-hubb i förhands gransknings läge

Använd *inte* en IoT-hubb i förhands gransknings läge för produktion. Förhands gransknings läget är *endast* avsett för att förhandsgranska de valda funktioner som visas överst på den här sidan. Andra begränsningar i IoT Hub förhands gransknings läge

- Vissa befintliga IoT Hub funktioner som t. ex. IP-filter, privat länk, hanterad identitet, enhets strömmar och redundans kan fungera som de ska eller inte alls.
- En IoT-hubb i förhands gransknings läge kan inte ändras eller uppgraderas till en normal IoT-hubb.
- Vi kan inte garantera normal [IoT Hub SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) – används inte för produktion.

> [!TIP]
> Förhands gransknings läge krävs inte för [enhets strömmar](iot-hub-device-streams-overview.md) och [distribuerad spårning](iot-hub-distributed-tracing.md). Om du vill använda dessa äldre förhands gransknings funktioner följer du deras dokumentation som normalt. 

## <a name="next-steps"></a>Nästa steg

- För att förhandsgranska MQTT 5-supporten, se [Översikt över IoT Hub MQTT 5-support (för hands version)](iot-hub-mqtt-5.md)
- För att förhandsgranska ECC-servercertifikatet, se [TLS-certifikat (Elliptic Curve Cryptography) för Server (för hands version)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- För att förhandsgranska TLS-Fragmentets storleks förhandling, se [TLS maximal fragment längd Negotiation (för hands version)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)