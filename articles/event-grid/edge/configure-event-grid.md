---
title: Konfiguration – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfiguration i Event Grid på IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171677"
---
# <a name="event-grid-configuration"></a>Event Grid konfiguration

Event Grid innehåller många konfigurationer som kan ändras per miljö. Följande avsnitt är en referens till alla tillgängliga alternativ och standardvärden.

## <a name="tls-configuration"></a>TLS-konfiguration

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Exempel på användningen finns i [den här artikeln](configure-api-protocol.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| TLS-princip för Event Grid-modulen. Standardvärdet är endast HTTPS.
|`inbound__serverAuth__serverCert__source`| Källa för Server certifikat som används av Event Grid-modulen för dess TLS-konfiguration. Standardvärdet är IoT Edge.

## <a name="incoming-client-authentication"></a>Inkommande klientautentisering

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-client-auth.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Aktivera/inaktivera certifikatbaserad klientautentisering. Standardvärdet är True.
|`inbound__clientAuth__clientCert__source`| Källa för validering av klient certifikat. Standardvärdet är IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Princip för att tillåta ett självsignerat klient certifikat. Standardvärdet är True.
|`inbound__clientAuth__sasKeys__enabled`| Aktivera/inaktivera SAS-baserad klientautentisering. Standardvärdet är inaktiverat.
|`inbound__clientAuth__sasKeys__key1`| Ett av värdena för att validera inkommande begär Anden.
|`inbound__clientAuth__sasKeys__key2`| Valfritt andra värde för att validera inkommande begär Anden.

## <a name="outgoing-client-authentication"></a>Utgående klientautentisering
Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-identity-auth.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Aktivera/inaktivera anslutning av ett identitets certifikat för utgående begär Anden. Standardvärdet är True.
|`outbound__clientAuth__clientCert__source`| Källa för att hämta Event Grid modulens utgående certifikat. Standardvärdet är IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-händelse hanterare

Mer information om klientautentisering i allmänhet finns i [säkerhet och autentisering](security-authentication.md). Du hittar exempel i [den här artikeln](configure-webhook-subscriber-auth.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Princip för att kontrol lera om endast HTTPS-prenumeranter kommer att tillåtas. Standardvärdet är sant (endast HTTPS).
|`outbound__webhook__skipServerCertValidation`| Flagga för att kontrol lera om prenumerantens certifikat ska verifieras. Standardvärdet är True.
|`outbound__webhook__allowUnknownCA`| Princip för att kontrol lera om ett självsignerat certifikat kan visas av en prenumerant. Standardvärdet är True. 

## <a name="delivery-and-retry"></a>Leverans och nytt försök

Läs mer om den här funktionen i allmänhet i avsnittet om [leverans och försök igen](delivery-retry.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximalt antal försök att leverera en händelse. Standardvärdet är 30.
| `broker__defaultEventTimeToLiveInSeconds` | TTL (Time-to-Live) i sekunder efter vilken en händelse tas bort om den inte levereras. Standardvärdet är  **7200** sekunder

## <a name="output-batching"></a>Batchbearbetning av utdata

Om du vill lära dig mer om den här funktionen i allmänhet, se [leverans-och utmatnings batch](delivery-output-batching.md).

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Högsta tillåtna värde för `ApproxBatchSizeInBytes` ratten. Standardvärdet är `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Högsta tillåtna värde för `MaxEventsPerBatch` ratten. Standardvärdet är `50`.
| `broker__defaultMaxBatchSizeInBytes` | Maximal storlek för leverans begär anden när endast `MaxEventsPerBatch` anges. Standardvärdet är `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maximalt antal händelser som ska läggas till i en batch när endast `MaxBatchSizeInBytes` anges. Standardvärdet är `10`.

## <a name="metrics"></a>Mått

Information om hur du använder mått med Event Grid på IoT Edge finns i [övervaka ämnen och prenumerationer](monitor-topics-subscriptions.md)

| Egenskapens namn | Beskrivning |
| ---------------- | ------------ |
| `metrics__reporterType` | Rapporterings typ för Mät slut punkt. Standardvärdet är `none` och inaktiverar mått. Inställning för att `prometheus` Aktivera Mät värden i Prometheus-format.