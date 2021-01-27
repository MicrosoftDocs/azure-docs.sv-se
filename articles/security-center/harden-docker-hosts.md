---
title: Använd Azure Security Center för att torka Docker-värdarna och skydda behållarna
description: Så här skyddar du Docker-värdarna och kontrollerar att de är kompatibla med CIS Docker-benchmark
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916242"
---
# <a name="harden-your-docker-hosts"></a>Härda Docker-värdarna

Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med [Center for Internet Security (CIS) Docker-benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När det hittar felkonfigurationer genererar Security Center säkerhets rekommendationer. Använd Security Center **rekommendationer** för att se rekommendationer och åtgärda problem.

När sårbarheter hittas grupperas de i en enda rekommendation.

>[!NOTE]
> Dessa CIS-benchmark-kontroller kan inte köras på AKS-hanterade instanser eller Databricks-hanterade virtuella datorer.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md)|
|Nödvändiga roller och behörigheter:|**Läsare** på arbets ytan som värden ansluter till|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identifiera och åtgärda säkerhets problem i Docker-konfigurationen

1. Från Security Center menyn öppnar du sidan **rekommendationer** .

1. Filtrera efter **säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas** och välj rekommendationen.

    Sidan rekommendation visar de berörda resurserna (Docker-värdar). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Rekommendation för att åtgärda sårbarheter i säkerhets konfigurationerna för behållare ":::

1. Om du vill visa och åtgärda CIS-kontroller som en speciell värd har misslyckats, väljer du den värd som du vill undersöka. 

    > [!TIP]
    > Om du har startat på sidan till gångs inventering och kommit till rekommendationen från där, tumintervall du knappen **ta med åtgärd** på rekommendations sidan.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Utför Åtgärds knappen för att starta Log Analytics":::

    Log Analytics öppnar med en anpassad åtgärd som är klar att köras. Den anpassade standard frågan innehåller en lista över alla misslyckade regler som utvärderades, tillsammans med rikt linjer som hjälper dig att lösa problemen.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics sidan med frågan som visar alla misslyckade CIS-kontroller":::

1. Justera frågeparametrar vid behov.

1. När du är säker på att kommandot är lämpligt och klart för värden väljer du **Kör**.


## <a name="next-steps"></a>Nästa steg

Docker härdning är bara en aspekt av Security Center behållar säkerhetsfunktionerna. 

Lär dig mer [om behållar säkerhet i Security Center](container-security.md).