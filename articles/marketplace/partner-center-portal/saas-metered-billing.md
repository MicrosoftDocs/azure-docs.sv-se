---
title: Avgiftsbelagd fakturering för SaaS-erbjudanden med Microsofts tjänst för extern Marketplace-avläsning
description: Lär dig mer om flexibla fakturerings modeller för SaaS-erbjudanden med den kommersiella tjänsten för avläsning av marknads platser.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92892761"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Mätning av fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser

Med den kommersiella tjänsten för avläsning av marknads platser kan du skapa SaaS-erbjudanden (Software as a Service) som debiteras enligt andra enheter än standard enheter. Innan du publicerar ett SaaS-erbjudande till den kommersiella Marketplace definierar du de fakturerings dimensioner som bandbredd, biljetter eller e-postmeddelanden som bearbetas.  Kunderna betalar sedan enligt deras användning av dessa dimensioner, med ditt system som informerar Microsoft via den kommersiella tjänsten för avläsning av program vara i Commerce Marketplace för fakturerbara händelser när de inträffar.  

## <a name="prerequisites-for-metered-billing"></a>Krav för fakturering av mätare

För ett SaaS-erbjudande för att använda avgiftsbelagd fakturering måste den först:

- Uppfyll alla erbjudande krav för en [försäljning via Microsoft-erbjudandet](../plan-saas-offer.md#listing-options) som beskrivs i [skapa ett SaaS-erbjudande i den kommersiella Marketplace](../create-new-saas-offer.md).
- Integrera med [API: er för SaaS-utförande](./pc-saas-fulfillment-api-v2.md) för kunder för att etablera och ansluta till ditt erbjudande.  
- Konfigureras för pris sättnings modellen med **fast pris** när kunderna debiteras för din tjänst.  Dimensioner är ett valfritt tillägg till pris sättnings modellen för fasta priser. 

Sedan kan SaaS-erbjudandet integreras med [API: er för nytto Last i Commerce Marketplace](./marketplace-metering-service-apis.md) för att informera Microsoft om fakturerbara händelser.

>[!Note]
>Marketplace för avläsning av program vara är bara tillgänglig för fakturerings modellen för fasta priser och gäller inte för fakturerings modellen per användare.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hur avgiftsbelagd fakturering passar in med priser

Att förstå erbjudandet är viktigt när det gäller att definiera erbjudandet tillsammans med dess pris sättnings modeller.

- Varje SaaS-erbjudande är konfigurerat för att sälja antingen via Microsoft eller inte.  Det går inte att ändra det här alternativet när ett erbjudande har publicerats.
- Varje SaaS-erbjudande, konfigurerat att sälja via Microsoft, kan ha en eller flera planer.  En användare prenumererar på SaaS-erbjudandet, men det köps via Microsoft inom ramen för en plan.
- Varje plan har en pris modell kopplad till sig: **fast** pris eller **per användare**. Alla planer i ett erbjudande måste vara kopplade till samma pris modell. Det kan till exempel vara ett erbjudande som inte har några planer för en pris sättnings modell med fast pris och en annan pris modell per användare.
- I varje plan som kon figurer ATS för en fast fakturerings modell ingår minst en återkommande avgift (som kan vara $0):
    - Återkommande **månads** avgift: fast månads avgift som betalas för varje månad när användaren köper planen.
    - Återkommande **årlig** avgift: fast årlig avgift som betalas för varje år när användaren köper planen.
- Förutom de återkommande avgifterna kan en plan för fast pris även omfatta valfria anpassade dimensioner som används för att debitera kunder för överförbruknings användningen som inte ingår i den fasta taxan.  Varje dimension representerar en fakturerbar enhet som din tjänst kommer att kommunicera med Microsoft med hjälp av [API: et för nytto](./marketplace-metering-service-apis.md)Last i nytto Last tjänsten.

## <a name="sample-offer"></a>Exempel erbjudande

Contoso är till exempel en utgivare med en SaaS-tjänst som heter Contoso Notification Services (CNS). CNS låter kunderna skicka meddelanden via e-post eller text. Contoso är registrerat som en utgivare i Partner Center för det kommersiella Marketplace-programmet för att publicera SaaS-erbjudanden till Azure-kunder.  Det finns två planer kopplade till CNS, som beskrivs nedan:

- Basic-plan
    - Skicka 10000 e-postmeddelanden och 1000 text för $0/månad (fast månads avgift)
    - Utöver 10000 e-postmeddelanden betalar du $1 för varje 100 e-post
    - Utöver 1000-texterna betalar du $0,02 för varje text

    [![Pris för Basic-plan](./media/saas-basic-pricing.png "Klicka för förstora vy")](./media/saas-basic-pricing.png)

- Premiumplan
    - Skicka 50000 e-postmeddelanden och 10000 text för $350/månad eller 5 M e-post och 1 miljon texter för $3500 per år
    - Utöver 50000 e-postmeddelanden betalar du $0,5 för varje 100 e-post
    - Utöver 10000-texterna betalar du $0,01 för varje text

    [![Pris för Premium plan](./media/saas-premium-pricing.png "Klicka för förstora vy")](./media/saas-premium-pricing.png)

- Företags plan
    - Skicka ett obegränsat antal e-postmeddelanden och 50000 texter i $400/månad
    - Utöver 50000-texterna betalar $0,005 för varje txt

    [![Priser för Enterprise plan](./media/saas-enterprise-pricing.png "Klicka för förstora vy")](./media/saas-enterprise-pricing.png)

Baserat på den valda planen kommer en Azure-kund som köper prenumeration på CNS SaaS att kunna skicka den inkluderade mängden text och e-postmeddelanden per prenumerations period (månad eller år som visas i prenumerations informationen-StartDate och endDate).  Contoso räknar användningen upp till den inkluderade kvantiteten i bas utan att skicka några användnings händelser till Microsoft. När kunderna förbrukar mer än den mängd som ingår behöver de inte ändra några planer eller göra något annat.  Contoso mäter överförbrukningen utöver den mängd som ingår och startar sändning av användnings händelser till Microsoft för att debitera överförbruknings användningen med hjälp av [API: et för avläsning av kommersiella marknads platser](./marketplace-metering-service-apis.md).  Microsoft kommer att debitera kunden för överförbrukningen enligt vad som anges av utgivaren i de anpassade dimensionerna. Debitering för överanvändning görs vid nästa fakturerings period (varje månad, men kan vara kvartals vis eller tidigt för vissa kunder).  För en månatlig plan med fasta kostnader görs överbetalningen för varje månad där överanvändning har skett.  För en årlig plan för fast pris, kommer all ytterligare användning som genereras av den anpassade mätaren att faktureras som överförbrukning under varje fakturerings period (varje månad) fram till slutet av prenumerationens års period.

## <a name="billing-dimensions"></a>Fakturerings dimensioner

Varje fakturerings dimension definierar en anpassad enhet enligt vilken ISV kan generera användnings händelser.  Fakturerings dimensioner används också för att kommunicera med kunden om hur de kommer att faktureras för användning av program varan.  De definieras enligt följande:

- **ID**: det oföränderliga dimensions-ID som refererades vid sändning av användnings händelser.
- **Visnings namn**: det visnings namn som är associerat med dimensionen, till exempel "textmeddelanden har skickats".
- Mått **enhet**: en beskrivning av fakturerings enheten, till exempel "per textmeddelande" eller "per 100-e-post".
- **Pris per enhet i USD**: priset för en enhet i dimensionen.  Det kan vara 0. 
- **Månatlig kvantitet som ingår i bas**: den kvantitet dimension som ingår per månad för kunder som betalar den återkommande månads avgiften måste vara ett heltal. Det kan vara 0 eller obegränsat.
- **Årlig kvantitet som ingår i bas**: den kvantitet dimension som ingår per år för kunder som betalar den återkommande årliga avgiften måste vara ett heltal. Kan vara 0 eller obegränsad.

Fakturerings dimensioner delas i alla planer för ett erbjudande.  Vissa attribut gäller för dimensionen i alla planer, och andra attribut är plan-/regionsspecifika.

Attributen, som definierar själva dimensionen, delas i alla planer för ett erbjudande.  Innan du publicerar erbjudandet påverkar en ändring av dessa attribut från kontexten för varje plan dimensions definitionen för alla planer.  När du har publicerat erbjudandet kan dessa attribut inte längre redige ras.  Dessa attribut är:

- ID
- Visningsnamn
- Måttenhet

De andra attributen för en dimension är speciella för varje plan och kan ha olika värden för att planera.  Innan du publicerar planen kan du redigera dessa värden och endast den här planen kommer att påverkas.  När du publicerar planen går dessa attribut inte längre att redige ras.  Dessa attribut är:

- Pris per enhet i USD
- Månatlig kvantitet som ingår i bas  
- Årlig kvantitet som ingår i baseIncluded  

Dimensioner har också två särskilda begrepp, "aktiverade" och "oändliga":

- **Aktiverat** anger att den här planen ingår i den här dimensionen.  Om du skapar en ny plan som inte skickar användnings händelser baserat på den här dimensionen kanske du vill lämna det här alternativet omarkerat.  Dessutom visas nya dimensioner som lagts till efter att en plan först publicerats som "inte aktive rad" i den redan publicerade planen.  En inaktive rad dimension visas inte i några listor med dimensioner för en plan som visas av kunderna.
- **Oändlig** representerad av oändlighets symbolen "∞", anger att den här planen ingår i den här dimensionen, men genererar inte användning mot den här dimensionen.  Om du vill ange för dina kunder att de funktioner som representeras av den här dimensionen ingår i planen, men utan någon begränsad användning.  En dimension med oändlig användning visas i listor över dimensioner för en plan som ses av kunder, med en indikation om att den aldrig debiteras för den här planen.

>[!Note] 
>Följande scenarier stöds uttryckligen: <br> – Du kan lägga till en ny dimension till en ny plan.  Den nya dimensionen kommer inte att aktive ras för redan publicerade planer. <br> – Du kan publicera en plan för **fast pris** utan några dimensioner och sedan lägga till en ny plan och konfigurera en ny dimension för planen. Den nya dimensionen kommer inte att aktive ras för redan publicerade planer.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Ange dimensions pris per enhet per marknad som stöds

Precis som med fast pris taxa kan fakturerings dimensions priser anges per land eller region som stöds. Du måste använda funktionen Importera och exportera prissättnings data i Partner Center på följande sätt.

1. Definiera önskade dimensioner och markera vilka marknader som stöds. 
1. Exportera dessa data till en fil.
1. Lägg till rätt priser per land/region och importera filen i Partner Center.

Användar gränssnittet för mätaren ändras så att det visar att dimensionernas priser endast kan visas i filen.

[![mått för tjänsten för avläsning av handels platser](media/metering-service-dimensions.png "Klicka för förstora vy")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Privat plan

Precis som fasta priser kan en plan med dimensioner anges som privat plan, som endast kan nås av planens definierade mål grupp.

## <a name="constraints"></a>Villkor

### <a name="trial-behavior"></a>Utvärderings beteende

Mätning av fakturering med hjälp av den kommersiella tjänsten för avläsning av program vara är inte kompatibel med erbjudande om en kostnads fri utvärderings version.  Det går inte att konfigurera en plan för att använda både avgiftsbelagd fakturering och en kostnads fri utvärderings version.

### <a name="locking-behavior"></a>Låsnings beteende

Eftersom en dimension som används med den kommersiella tjänsten för avläsning av program vara representerar en förståelse för hur en kund ska betala för tjänsten, är all information om en dimension inte längre redigerbar när du har publicerat den.  Det är viktigt att du har definierat dina dimensioner fullständigt för en plan innan du publicerar.

När ett erbjudande publiceras med en dimension kan informationen om erbjudande nivå för den dimensionen inte längre ändras:

- ID
- Visningsnamn
- Måttenhet

När en plan har publicerats kan informationen på Plans nivå inte längre ändras:

- Pris per enhet i USD
- Månatlig kvantitet som ingår i bas
- Årlig kvantitet som ingår i bas
- Om dimensionen är aktive rad för planen eller inte

### <a name="upper-limits"></a>Övre gränser

Det maximala antalet dimensioner som kan konfigureras för ett enda erbjudande är 30 unika dimensioner.

## <a name="get-support"></a>Få support

Om du har något av följande problem kan du öppna ett support ärende.

- Tekniska problem med API för avläsning av tjänst i Marketplace.
- Ett problem som måste eskaleras på grund av ett fel eller fel på sidan (t. ex. fel användnings händelse).
- Eventuella andra problem som rör mätning av fakturering.

Om du vill förstå support alternativen för Publisher och öppna ett support ärende med Microsoft, följer du instruktionerna i [Support för programmet för kommersiella marknads platser i Partner Center](../support.md).

## <a name="next-steps"></a>Nästa steg

- [API:er för Marketplace Metering Service](./marketplace-metering-service-apis.md)
