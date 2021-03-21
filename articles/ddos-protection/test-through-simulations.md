---
title: Azure DDoS Protection simulerings testning
description: Lär dig hur du testar genom simuleringar
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 3be8682b3a12f06aa83db1791cb0ed125bbcee7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719244"
---
# <a name="test-through-simulations"></a>Testa genom simuleringar

Det är en bra idé att testa dina antaganden om hur dina tjänster kommer att svara på ett angrepp genom att utföra periodiska simuleringar. Under testningen kontrollerar du att tjänsterna eller programmen fortsätter att fungera som förväntat och att användar upplevelsen inte störs. Identifiera luckor från både en teknik och ett process syn och införliva dem i DDoS-svars strategin. Vi rekommenderar att du utför sådana tester i tillfälliga miljöer eller under låg belastnings tider för att minimera påverkan på produktions miljön.

Vi samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), en självbetjäningsgenerator för att skapa ett gränssnitt där Azure-kunder kan generera trafik mot DDoS Protection-aktiverade offentliga slutpunkter för simuleringar. Du kan använda simuleringen för att:

- Validera hur Azure DDoS Protection hjälper till att skydda dina Azure-resurser från DDoS-attacker.
- Optimera incidenthanteringsprocessen under DDoS-angrepp.
- Dokumentera DDoS-efterlevnad.
- Träna dina nätverkssäkerhetsgrupper.

> [!NOTE]
> BreakingPoint-molnet är endast tillgängligt för det offentliga molnet.

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md) med skyddade offentliga IP-adresser.
- Du måste först skapa ett konto med [BreakingPoint-molnet](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Konfigurera en DDoS test attack

1. Ange eller Välj följande värden och välj sedan **Starta test**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Mål-IP-adress           | Ange en offentlig IP-adress som du vill testa.                     |
    |Portnummer   | Ange _443_.                       |
    |DDoS-profil | Möjliga värden är,,,,,,,,,,, `DNS Flood` `NTPv2 Flood` `SSDP Flood` `TCP SYN Flood` `UDP 64B Flood` `UDP 128B Flood` `UDP 256B Flood` `UDP 512B Flood` `UDP 1024B Flood` `UDP 1514B Flood` `UDP Fragmentation` `UDP Memcached` .|
    |Test storlek       | Möjliga värden är `100K pps, 50 Mbps and 4 source IPs` , `200K pps, 100 Mbps and 8 source IPs` , `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Test varaktighet | Möjliga värden är,,,, `10 Minutes` `15 Minutes` `20 Minutes` `25 Minutes` `30 Minutes` .|

Den bör nu se ut så här:

![Exempel på DDoS-attack: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Övervaka och validera

1. Logga in på https://portal.azure.com och gå till din prenumeration.
1. Välj den offentliga IP-adress som du testade attacken på.
1. Gå till **Övervakning** och välj **Mått**.
1. För **mått** väljer du _under DDoS-attack eller inte_.

När resursen är utsatt för angrepp bör du se att värdet ändras från **0** till **1**, som följande bild:

![Exempel på DDoS attack-simulering: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud API-skript

Det här [API-skriptet](https://aka.ms/ddosbreakingpoint) kan användas för att automatisera DDoS-testning genom att köra en gång eller använda cron för att schemalägga vanliga tester. Detta är användbart för att verifiera att loggningen är korrekt konfigurerad och att identifierings-och svars procedurerna är effektiva. Skripten kräver ett Linux-operativsystem (testat med Ubuntu 18,04-LTS) och python 3. Installera nödvändiga komponenter och API-klienten med hjälp av det inkluderade skriptet eller med hjälp av dokumentationen på [BreakingPoint Cloud](http://breakingpoint.cloud/) -webbplatsen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [visar och konfigurerar DDoS Protection-telemetri](telemetry.md).
- Lär dig hur du [visar och konfigurerar DDoS diagnostisk loggning](diagnostic-logging.md).
- Lär dig att [engagera DDoS Rapid Response](ddos-rapid-response.md).
