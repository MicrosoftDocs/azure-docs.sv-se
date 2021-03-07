---
title: Hotinformationsrapporter i Azure Security Center | Microsoft Docs
description: På den här sidan får du hjälp att använda Azure Security Center hot informations rapporter under en undersökning för att hitta mer information om säkerhets aviseringar
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: ec6d227059c3f4fd1285f224e13169a2479bc65f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438241"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center hot informations rapport

På den här sidan förklaras hur Azure Security Centers hot informations rapporter kan hjälpa dig att lära dig mer om ett hot som utlöser en säkerhets avisering.


## <a name="what-is-a-threat-intelligence-report"></a>Vad är en hotinformationsrapport?

Security Center hot skydd fungerar genom att övervaka säkerhets information från dina Azure-resurser, nätverket och anslutna partner lösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Mer information finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

När Security Center identifierar ett hot utlöser det en [säkerhets avisering](security-center-managing-and-responding-alerts.md)som innehåller detaljerad information om händelsen, inklusive förslag på åtgärder. För att hjälpa incident hanterings team att undersöka och åtgärda hot, ger Security Center Hot information-rapporter som innehåller information om identifierade hot. Rapporten innehåller information som:

* Angriparens identitet eller associationer (om den här informationen är tillgänglig)
* Angriparens mål
* Aktuella och historiska attackkampanjer (om den här informationen är tillgänglig)
* Angripares taktiker, verktyg och procedurer
* Associerade indikatorer för kompromettering, till exempel URL:er och filhashvärden
* Viktimologi, vilket syftar på information om utbredningen inom en bransch och geografisk region som kan hjälpa dig att avgöra om dina Azure-resurser är utsatta för risk
* Information om rekommenderade åtgärder

> [!NOTE]
> Mängden information i en viss rapport varierar. Detaljnivån beror på den skadliga kodens aktivitet och spridning.

Security Center tillhandahåller tre typer av hotrapporter, som kan variera beroende på attacken. De tillgängliga rapporterna är:

* **Aktivitets grupps rapport**: ger djup dykningar till angripare, deras mål och taktiker.
* **Kampanjrapport**: Fokuserar på information om specifika attackkampanjer.
* **Hotsammanfattningsrapport**: Omfattar alla objekt i de föregående två rapporterna.

Den här typen av information är användbar under incident svars processen, där det finns en pågående undersökning för att förstå Angreppets källa, angriparens motivation och vad du behöver göra för att undvika det här problemet i framtiden.



## <a name="how-to-access-the-threat-intelligence-report"></a>Hur kommer du åt hotinformationsrapporten?

1. Från Security Centers sid panelen öppnar du sidan **säkerhets aviseringar** .
1. Välj en avisering. 
    Sidan aviserings information öppnas med mer information om aviseringen. Nedan visas sidan med **Utpressnings** bara aviserings information.

    [![Aviserings information om utpressnings visare identifierad](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Välj länken till rapporten så öppnas en PDF-fil i din standard webbläsare.

    [![Informations sidan om potentiellt osäker åtgärds avisering](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Du kan också hämta PDF-rapporten. 

    >[!TIP]
    > Mängden information som är tillgänglig för varje säkerhetsvarning varierar beroende på typen av avisering.



## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras hur du öppnar hot informations rapporter när du undersöker säkerhets aviseringar. Relaterad information finns på följande sidor:

* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Hantera säkerhets incidenter i Azure Security Center](security-center-incident.md)