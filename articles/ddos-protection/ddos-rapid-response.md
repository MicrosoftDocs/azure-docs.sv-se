---
title: Snabb svar för Azure DDoS
description: Lär dig att engagera DDoS-experter under en aktiv attack för specialiserad support.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8e860bf47420f2b58c44df695da7761bcc2aa0ce
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521789"
---
# <a name="azure-ddos-rapid-response"></a>Snabb svar för Azure DDoS

Under en aktiv åtkomst har Azure DDoS Protection standard kunder åtkomst till DRR-teamet (DDoS Rapid Response), som kan hjälpa till med angrepps undersökningen under ett angrepp och analys efter angrepp.

## <a name="prerequisites"></a>Förutsättningar

- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>När du ska engagera DRR

Du bör bara engagera DRR om: 

- Under en DDoS-attack om du upptäcker att prestandan för den skyddade resursen är allvarligt försämrad eller om resursen inte är tillgänglig. 
- Du tror att din resurs är utsatt för DDoS-attack, men DDoS Protection tjänsten minskar risken på ett effektivt sätt.
- Du planerar en virus händelse som ökar nätverks trafiken markant.
- För attacker som har stor inverkan på verksamheten.

## <a name="engage-drr-during-an-active-attack"></a>Engagera DRR under en aktiv attack

1. Från Azure Portal när du skapar en ny supportbegäran väljer du **typ av problem** som teknisk.
2. Välj **tjänst** som **DDoS skydd**.
3. Välj en resurs på den nedrullningsbara menyn resurs. _Du måste välja en DDoS-plan som är länkad till det virtuella nätverket som skyddas av DDoS Protection standard för att engagera DRR._

    ![Välj resurs](./media/ddos-rapid-response/choose-resource.png)

4. På sidan nästa **problem** väljer du **allvarlighets grad** som en kritisk effekt och **problem typ** som "under attack".

    ![PSeverity och problem typ](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Slutför ytterligare information och skicka in support förfrågan.

DRR följer Azure Rapid Response-Supportens modell. Se [support omfattning och svars](https://azure.microsoft.com/en-us/support/plans/response/) tid för mer information om snabba svar.

Läs [DDoS Protection standard dokumentationen](./ddos-protection-overview.md)om du vill veta mer.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [testar genom simuleringar](test-through-simulations.md).
- Lär dig hur du [visar och konfigurerar DDoS Protection-telemetri](telemetry.md).
- Lär dig hur du [visar och konfigurerar DDoS diagnostisk loggning](diagnostic-logging.md).
