---
title: Testning av inträngning | Microsoft Docs
description: Artikeln innehåller en översikt över inträngande testnings processen och hur du utför ett Pen-test mot din app som körs i Azure-infrastrukturen.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550209"
---
# <a name="penetration-testing"></a>Intrångstest

En av fördelarna med att använda Azure för program testning och distribution är att du snabbt kan hämta miljöer som skapats. Du behöver inte bekymra dig om rekvisitioner, förvärv och "rackering och stapling" av din egen lokala maskin vara.

Att snabbt skapa miljöer är bra – men du behöver fortfarande se till att du utför din normala noggrannhet för säkerhet. Ett av de saker som du förmodligen vill göra är inträngande testa de program som du distribuerar i Azure.

Vi utför inte inträngande tester av ditt program, men vi förstår att du vill och behöver testa dina egna program. Det är bra eftersom när du förbättrar säkerheten för dina program, så att du kan göra hela Azure-eko systemet säkrare.

Från och med den 15 juni 2017 kräver Microsoft inte längre förhandsgodkännande för att genomföra ett intrångstest mot Azure-resurser. Den här processen är bara relaterad till Microsoft Azure och gäller inte för någon annan Microsoft Cloud tjänst.

>[!IMPORTANT]
>När du meddelar Microsoft om Penn testnings aktiviteter behöver kunderna fortfarande följa [Microsoft Cloud enhetligt inträngande test regler för engagemang](https://technet.microsoft.com/mt784683).

Standard test som du kan utföra är:

* Tester på dina slut punkter för att få fram de [10 viktigaste säkerhets problemen i det öppna webb programmet säkerhets projekt (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzz-testning](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) för slutpunkter
* [Portskanning](https://en.wikipedia.org/wiki/Port_scanner) för slutpunkter

En typ av Penn test som du inte kan utföra är någon typ av dos [-attack (Denial of Service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Det här testet omfattar att initiera en DoS-attack eller utföra relaterade tester som kan fastställa, demonstrera eller simulera alla typer av DoS-attacker.

>[!Note]
>Microsoft samarbetar med BreakingPoint Cloud för att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simuleringar. Mer information om BreakingPoint Cloud simulering finns i [testa genom simuleringar](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [inträngande testnings regler för Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
