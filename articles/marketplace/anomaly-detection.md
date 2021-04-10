---
title: Hantera avlästa fakturerings avvikelser i Partner Center | Azure Marketplace
description: Lär dig hur automatisk avvikelse identifiering för avgiftsbelagda fakturor hjälper till att se till att kunderna debiteras korrekt för avgiftsbelagd användning av dina kommersiella Marketplace-erbjudanden.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092519"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>Hantera avgiftsbelagda fakturerings avvikelser i Partner Center

Det anpassade fakturerings alternativet för avläsning är för närvarande tillgängligt för SaaS-erbjudanden ( [Software as a service](plan-saas-offer.md) ) och [Azure-program](plan-azure-application-offer.md#types-of-plans) med en hanterad program plan.

Om du använder det avgiftsbelagda fakturerings alternativet för att skapa erbjudanden i programmet för kommersiella marknads platser som gör det möjligt att ta betalt för användning baserat på enheter som inte är standard måste du känna till när kunden har använt mer av en tjänst än förväntat.

## <a name="use-the-anomaly-detection-feature"></a>Använd funktionen för avvikelse identifiering

Microsoft använder dig av dig, partnern, för att rapportera dina kunders överförbrukning av sina SaaS eller Azure Managed Application-erbjudanden innan Microsoft fakturerar kunden. Om fel användning rapporteras kan kunden potentiellt få en felaktig faktura, undervinna både Microsofts och partnerns trovärdighet.

Du kan se till att dina kunder faktureras korrekt genom att använda funktionen för **avvikelse identifiering** för både SaaS-appar och hanterade program planer i Azure-program. Den här funktionen övervakar användningen mot avgiftsbelagd fakturering och förutsäger förväntat användnings värde inom det förväntade intervallet. Om användningen ligger utanför det förväntade intervallet behandlas det som oväntad, (en avvikelse) och du får ett varnings meddelande på sidan erbjudande översikt i programmet för kommersiell Marketplace i Partner Center. Du kan spåra kundernas användning dagligen för varje anpassad mätar dimension som du har angett.

## <a name="view-and-manage-metered-usage-anomalies"></a>Visa och hantera avvikelser vid användning av mätare

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
1. På den vänstra navigerings **menyn väljer du marknads plats**  >  **analys**.
1. Välj fliken **mätning av användnings avvikelser** .

    [![Visar fliken avgiftsbelagda användnings avvikelser på sidan användning.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Bild 1: fliken avvikelser vid användning av mätare***

1. När det gäller användnings avvikelser som identifieras mot avgiftsbelagd fakturering, blir du ombedd att undersöka och bekräfta om avvikelsen är sann eller inte. Välj **Markera som avvikelse** för att bekräfta diagnostiken.

     [![Visar dialog rutan Markera som avvikelse.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Bild 2: Markera som en avvikelse dialog ruta***

1. Om du tror att den överförbruknings avvikelse som vi har identifierat inte är äkta, kan du ge feedback genom att välja **inte en avvikelse** för partner Center flaggad avvikelse på den specifika överförbrukningen.

    [![Visar varför är det inte en avvikelse dialog ruta.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Bild 3: Varför är det inte en avvikelse? dialog ruta***

1. Du kan bläddra nedåt på sidan om du vill se en inventerings lista över ej accepterade avvikelser. Listan innehåller en förteckning över avvikelser som du inte har bekräftat. Du kan välja vilken som helst av partner centret flaggade avvikelser som äkta eller falskt.

   [![Visar listan över ej godkända avvikelser i Partner Center på sidan användning.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Bild 4: lista över ej godkända avvikelser i Partner Center***

1. Du ser också en avvikelse åtgärds logg som visar de åtgärder du vidtagit för överförbrukningen. I åtgärds loggen kommer du att kunna se vilka överförbruknings händelser som marker ATS som äkta eller falskt.

   [ ![ Visar avvikelse åtgärds loggen på användnings sidan.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Bild 5: logg för avvikelse åtgärd***

1. Partner Center Analytics har inte stöd för restatering av händelser med överförbruknings händelser i export rapporterna. Med partner Center kan du ange den korrigerade överförbrukningen för en avvikelse och informationen skickas vidare till Microsoft Teams för undersökning. Utifrån undersökningen kommer Microsoft att utfärda kredit åter betalningar till den överbelastade kunden, beroende på vad som är lämpligt. När du väljer någon av flaggade avvikelser kan du välja **Markera som avvikelse** för att markera användnings avvikelsen som äkta.

   [ ![ Visar en dialog ruta för att markera som en avvikelse.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Figur: 6: Markera som avvikelse dialog ruta***

Första gången en överförbruknings användning flaggas som oregelbunden i Partner Center får du ett fönster på 30 dagar från den instansen för att markera avvikelsen som äkta eller falskt. Efter den 30 dagar perioden skulle du inte kunna agera på avvikelserna.

> [!IMPORTANT]
> Under-rapporterad förbruknings enhet är inte berättigade till ombearbetning och ekonomisk justering.

Du kan se alla avvikelser (godkända eller på annat sätt) för den valda beräknings perioden. De olika beräknings perioderna är de senaste 30 dagarna, de senaste 60 dagarna och de senaste 90 dagarna.

> [!IMPORTANT]
> Du uppmanas att agera på flaggade avvikelser inom 30 dagar från den tidpunkt då avvikelserna först rapporteras i Partner Center.

Filtret modala i widgeten gör att du kan välja enskilda erbjudanden och enskilda anpassade mätare.

När du har markerat en överförbruknings användning som avvikande eller godkänt en modell som flaggade en avvikelse som äkta, kan du inte ändra markeringen till "inte en avvikelse".

> [!IMPORTANT]
> Du kan skicka om överförbrukningen om du överbelastar situationer.

## <a name="see-also"></a>Se även
- [Mätning av fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser](./partner-center-portal/saas-metered-billing.md)
- [Förvaltad fakturering för program](./partner-center-portal/azure-app-metered-billing.md)
- [Tjänst för att identifiera avvikelser vid förbrukningsbaserad fakturering](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
