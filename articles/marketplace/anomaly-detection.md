---
title: Avvikelse identifiering för fakturering av mätare | Azure Marketplace
description: Lär dig hur automatisk avvikelse identifiering för avgiftsbelagda fakturor hjälper till att se till att dina kunder debiteras korrekt för avgiftsbelagd användning av ditt kommersiella marknads erbjudande.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 1/09/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d4fb88854359dcd6e383b47d2a8ce4e9c91f867a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989529"
---
# <a name="anomaly-detection-for-metered-billing"></a>Avvikelse identifiering för avlästa fakturor

Den här artikeln innehåller information om Marketplace-mätnings tjänsten och den tillhör ande automatiska avvikelse identifierings funktionen som hjälper dig att fakturera kunder på rätt sätt för deras avgiftsbelagda användning. Det avgiftsbelagda fakturerings alternativet är för närvarande tillgängligt för SaaS-erbjudanden ( [Software as a service](plan-saas-offer.md) ) och [Azure-program](plan-azure-application-offer.md#types-of-plans) med en hanterad program plan. Med det här alternativet kan partners skapa erbjudanden i det kommersiella Marketplace-programmet som debiteras enligt andra enheter än standard enheter.

Partner som har anpassade mätare som distribueras för SaaS och hanterade program kan se avvikelse från förväntat användnings beteende som avvikelser för överförbruknings _händelser_ för vissa _anpassade mätare_ i Partner Center. För att minska risken använder Partner Center en avvikelse identifierings tjänst som använder Machine Learning-algoritmer för att fastställa det normala fakturerings beteendet för mätning, analysera den avgiftsbelagda fakturerings användningen och upptäcka avvikelser med minimal inblandning av användaren. Med hjälp av _avvikelse identifierings modeller_ på data uppsättningar för mätning av data trafik, syftar Partner Center på att informera utgivaren när den rapporterade användningen överskrider den förväntade användningen.

## <a name="usability-experience"></a>Användbarhets upplevelse

Microsoft förlitar sig på partnern för att rapportera kundens överförbrukning av sina SaaS eller Azure Managed Application-erbjudanden innan Microsoft fakturerar kunden. Om fel användning rapporteras kan kunden potentiellt få en felaktig faktura, undervinna både Microsofts och partnerns trovärdighet.

För att minimera detta finns en automatisk avvikelse identifierings funktion för både SaaS-appar och program som hanteras av Azure-program. Den här funktionen är en maskin inlärnings modell som proaktivt övervakar användningen mot avgiftsbelagd fakturering och förutsäger det förväntade användning svärdet inom det förväntade intervallet. Om användningen ligger utanför det förväntade intervallet behandlas det som en avvikelse och ett aviserings meddelande visas för partnern på sidan erbjudande översikt i programmet för kommersiell Marketplace i Partner Center.

Machine Learning-modellen analyserar förbrukning per dag. Utgivaren kan se alla avvikelser som rapporter ATS mot sina kunders överförbruknings förbrukning för varje erbjudandes anpassade mätar dimensioner.

### <a name="view-and-manage-metered-usage-anomalies"></a>Visa och hantera avvikelser vid användning av mätare

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
