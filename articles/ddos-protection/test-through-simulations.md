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
ms.openlocfilehash: 55692122461ef1b22b43b0def43e826ac7aeae30
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813793"
---
# <a name="test-through-simulations"></a>Testa genom simuleringar

Det är en bra idé att testa dina antaganden om hur dina tjänster kommer att svara på ett angrepp genom att utföra periodiska simuleringar. Under testningen kontrollerar du att tjänsterna eller programmen fortsätter att fungera som förväntat och att användar upplevelsen inte störs. Identifiera luckor från både en teknik och ett process syn och införliva dem i DDoS-svars strategin. Vi rekommenderar att du utför sådana tester i tillfälliga miljöer eller under låg belastnings tider för att minimera påverkan på produktions miljön.

Vi samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), en självbetjänings generator för att skapa ett gränssnitt där Azure-kunder kan generera trafik mot DDoS Protection-aktiverade offentliga slut punkter för simuleringar. Du kan använda simuleringen för att:

- Verifiera hur Azure DDoS Protection hjälper till att skydda dina Azure-resurser från DDoS-attacker.
- Optimera incident svars processen under DDoS-angrepp.
- Dokumentera DDoS-efterlevnad.
- Träna dina nätverks säkerhets team.

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md) med skyddade offentliga IP-adresser.
- Du måste först skapa ett konto med [BreakingPoint-molnet](http://breakingpoint.cloud/). 

## <a name="configure-a-ddos-test-attack"></a>Konfigurera en DDoS test attack

1. Ange eller Välj följande värden och välj sedan **Starta test**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Mål-IP-adress           | Ange en offentlig IP-adress som du vill testa.                     |
    |Portnummer   | Ange _443_.                       |
    |DDoS-profil | Välj **TCP syn-översvämmare**.|
    |Test storlek       | Välj **funktion PPS, 100 Mbit/s och 8 käll-IP: er.**                                  |
    |Test varaktighet | Välj **10 minuter**.|

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

Det här [API-skriptet](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Breaking%20Point%20SDK) kan användas för att automatisera DDoS-testning genom att köra en gång eller använda cron för att schemalägga vanliga tester. Detta är användbart för att verifiera att loggningen är korrekt konfigurerad och att identifierings-och svars procedurerna är effektiva. Skripten kräver ett Linux-operativsystem (testat med Ubuntu 18,04-LTS) och python 3. Installera nödvändiga komponenter och API-klienten med hjälp av det inkluderade skriptet eller med hjälp av dokumentationen på [BreakingPoint Cloud](http://breakingpoint.cloud/) -webbplatsen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [visar och konfigurerar DDoS Protection-telemetri](telemetry.md).
- Lär dig hur du [visar och konfigurerar DDoS diagnostisk loggning](diagnostic-logging.md).
- Lär dig att [engagera DDoS Rapid Response](ddos-rapid-response.md).
