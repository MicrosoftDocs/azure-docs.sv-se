---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001222"
---
Fluent är en data insamlare med öppen källkod för enhetlig loggning. `Fluentd`Inställningarna hanterar behållarens anslutning till en [Fluent](https://www.fluentd.org) -Server. Behållaren innehåller en upplösnings leverantör som gör att din behållare kan skriva loggar och, om du vill, Metric-data till en ansvars bara server.

I följande tabell beskrivs de konfigurations inställningar som stöds i `Fluentd` avsnittet.

| Name | Datatyp | Beskrivning |
|------|-----------|-------------|
| `Host` | Sträng | IP-adressen eller DNS-värdnamnet för den sammanslagna servern. |
| `Port` | Integer | Porten för den Fluent-servern.<br/> Standardvärdet är 24224. |
| `HeartbeatMs` | Integer | Pulsslags intervallet, i millisekunder. Om ingen händelse trafik har skickats innan det här intervallet går ut skickas ett pulsslag till den distribuerade servern. Standardvärdet är 60000 millisekunder (1 minut). |
| `SendBufferSize` | Integer | Utrymmet för nätverket i byte allokerat för sändnings åtgärder. Standardvärdet är 32768 byte (32 kilobyte). |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | Timeout, i millisekunder, för att upprätta en SSL/TLS-anslutning med den upprättade servern. Standardvärdet är 10000 millisekunder (10 sekunder).<br/> Om `UseTLS` är inställt på falskt ignoreras värdet. |
| `UseTLS` | Boolesk | Anger om behållaren ska använda SSL/TLS för att kommunicera med den upprättade servern. Standardvärdet är false. |