---
title: Transact-funktioner för Microsofts kommersiella Marketplace
description: Den här artikeln beskriver pris-, fakturerings-, fakturerings-och utbetalnings överväganden för Transact-alternativet för kommersiella Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0a25e1b50455cad5bdbe5b76b2a291f2a1c11940
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107014"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Transact-funktioner för kommersiella Marketplace

Den här artikeln beskriver pris-, fakturerings-, fakturerings-och utbetalnings överväganden för Microsofts kommersiella Marketplace.

## <a name="transactions-by-listing-option"></a>Alternativ för transaktioner per lista

Antingen utgivaren eller Microsoft ansvarar för att hantera program licens transaktioner för erbjudanden på den kommersiella marknaden. Det List alternativ som du väljer för ditt erbjudande avgör vem som hanterar transaktionen. Tillgänglighet och förklaringar för varje publicerings alternativ finns i [Introduktion till List alternativ](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Kontakta mig, kostnads fri utvärdering och BYOL-alternativ

Utgivare kan välja _kontakten mig_ och den _kostnads fria utvärderings versionen_, alternativ för kampanj-och användar förvärv. För vissa erbjudande typer kan utgivare välja alternativet _ta med din egen licens_ (BYOL) för att göra det möjligt för kunder att köpa en prenumeration på ditt erbjudande med hjälp av en licens som de har köpt direkt från dig. Med dessa alternativ deltar inte Microsoft direkt i utgivarens program licens transaktioner och det finns ingen kopplad transaktions avgift, så att utgivare behåller alla intäkter.

Med de här alternativen ansvarar utgivare för att stödja alla aspekter av program licens transaktionen. Detta omfattar men är inte begränsat till order, uppfyllelse, avläsning, fakturering, fakturering, betalning och insamling. Med alternativet kontakta mig List behåller utgivaren 100% av licens avgifterna för Publisher-programvaran som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för Transact-publicering

Att välja att sälja via Microsoft utnyttjar Microsoft Commerce-funktionerna och ger en komplett upplevelse från identifiering och utvärdering av inköp och implementering. Ett _transactable_ -erbjudande är ett där Microsoft fören klar utbytet av pengar för en program varu licens för utgivarens räkning. Transact-erbjudanden faktureras mot en befintlig Microsoft-prenumeration eller ett kredit kort, vilket gör att Microsoft kan vara värd för moln Marketplace-transaktioner på uppdrag av utgivaren.

Du väljer alternativet Transact när du skapar ett nytt erbjudande i Partner Center. Det här alternativet visas bara om Transact är tillgängligt för din erbjudande typ.

## <a name="transact-overview"></a>Översikt över Transact

När du använder Transact-alternativet gör Microsoft det möjligt att sälja program från tredje part och distribution av vissa erbjudande typer till kundens Azure-prenumeration. Utgivaren måste beakta faktureringen av infrastruktur avgifter och dina egna licens avgifter för program vara när du väljer en pris sättnings modell för ett erbjudande.

Alternativet för Transact-publicering stöds för närvarande för följande erbjudande typer:

| Erbjudandetyp | Fakturerings takt | Avgiftsbelagd fakturering | Prismodell |
| ------------ | ------------- | ------------- | ------------- |
| Azure Application<br>(Hanterat program) | Varje månad | Ja | Användning-baserad |
| Virtuell Azure-dator | Hyres | Inga | Användning-baserad, BYOL |
| Programvara som en tjänst (SaaS) | Månatlig och årlig | Ja | Fast pris, per användare, användnings-baserade. |
|||||

`*` Virtuella Azure-datorer erbjuder support användnings fakturerings planer. Dessa planer faktureras per månad för varje timmes användning av prenumerationen baserat på per kärna, per kärn storlek eller per marknad och kärn storlek.

### <a name="metered-billing"></a>Avgiftsbelagd fakturering

Med _Marketplace-tjänsten för avläsning_ av program vara kan du ange avgifter enligt principen betala per användning (förbruknings-based) utöver de månatliga eller årliga avgifter som ingår i kontraktet (berättigande). Du kan debitera användnings kostnader för dimensionerna för marknads mätnings tjänsten som du anger, t. ex. bandbredd, biljetter eller e-postmeddelanden som bearbetas. Mer information om avgiftsbelagd fakturering för SaaS-erbjudanden finns i [mäta fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser](./partner-center-portal/saas-metered-billing.md). Mer information om avgiftsbelagd fakturering för Azure Application erbjudanden finns i [hanterad fakturering för program](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Kostnader för fakturerings infrastruktur

För **virtuella datorer** och **Azure-program** debiteras Azure Infrastructure Usage avgifter till kundens Azure-prenumeration. Avgifter för infrastrukturanvändning prissätts och presenteras separat från programvaruleverantörens licensavgifter på kundens faktura.

För **SaaS-appar** måste utgivaren redovisa Azure-infrastrukturens användnings avgifter och program licens avgifter som ett enda kostnads objekt. Den representeras som en fast avgift för kunden. Användningen av Azure-infrastrukturen hanteras och debiteras för utgivaren direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare väljer vanligt vis att paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. Avgifterna för program varu licenser är inte avgiftsbelagda eller baseras på användar förbrukning.

## <a name="pricing-models"></a>Prissättningsmodeller

Beroende på vilket transaktions alternativ som används är prenumerations avgifterna följande:

- **Hämta nu (kostnads fritt)**: ingen avgift för program varu licenser. Kostnads fria erbjudanden kan inte konverteras till ett betalt erbjudande. Kunderna måste beställa ett betalt erbjudande.
- **Bring Your Own License** (BYOL): om ett erbjudande visas i den kommersiella marknads platsen, hanteras alla tillämpliga avgifter för program varu licenser direkt mellan utgivaren och kunden. Microsoft debiterar bara avgifter för Azures infrastruktur användning till kundens Azure-prenumerations konto.
- **Prenumerations priser**: licens avgifter för program vara presenteras som en månatlig eller årlig, återkommande prenumerations avgift som faktureras som en fast taxa eller per plats. Nya prenumerations avgifter beräknas inte proportionellt för uppsägningar på medel lång sikt eller oanvända tjänster. Nya prenumerations avgifter kan debiteras om kunden uppgraderar eller nedgraderar prenumerationen i mitten av prenumerations perioden.
- **Användnings-baserade priser**: för erbjudanden för virtuella Azure-datorer debiteras kunderna utifrån omfattningen av deras användning av erbjudandet. För avbildningar av virtuella datorer debiteras kunder en Tim avgift för Azure Marketplace som anges av utgivaren för användning av virtuella datorer som distribueras från VM-avbildningarna. Tim avgiften kan vara enhetlig eller variera mellan storleken på virtuella datorer. Delar av timmar debiteras per minut. Planer faktureras per månad.
- **Avgiftsbelagd prissättning**: för Azure Application erbjudanden och SaaS-erbjudanden kan utgivare använda [Marketplace-avläsning](./partner-center-portal/marketplace-metering-service-apis.md) för att debitera för förbrukning baserat på de anpassade mätar dimensioner som de konfigurerar. Dessa ändringar gäller utöver de månatliga eller årliga avgifter som ingår i kontraktet (berättigande). Exempel på anpassade mätar dimensioner är bandbredd, biljetter eller e-postmeddelanden som bearbetas. Utgivare kan definiera en eller flera mätnings dimensioner för varje plan, men högst 30 per erbjudande. Utgivare ansvarar för att spåra individuell kund användning, med varje mätare som definierats i erbjudandet. Händelser ska rapporteras till Microsoft inom en timme. Microsoft debiterar kunderna baserat på användnings information som rapporteras av utgivare för den aktuella fakturerings perioden.
- **Kostnads fri utvärderings version**: ingen avgift för program varu licenser som sträcker sig från 30 dagar upp till sex månader, beroende på typen av erbjudande. Om utgivare tillhandahåller en kostnads fri utvärderings version av flera planer inom samma erbjudande kan kunderna byta till en kostnads fri utvärderings version i ett annat avtal, men utvärderings perioden startar inte om. För erbjudanden om virtuella datorer debiteras kunderna Azures infrastruktur kostnader för användning av erbjudandet under en utvärderings period. När utvärderings perioden har löpt ut debiteras kunderna automatiskt för den senaste planen de försökte baseras på standardpriser, såvida de inte annulleras före utvärderings periodens slut.

> [!NOTE]
> Erbjudanden som debiteras enligt förbrukningen när en lösning har använts är inte berättigad till åter betalningar.

Utgivare som vill ändra de användnings avgifter som är kopplade till ett erbjudande bör först ta bort erbjudandet (eller den speciella planen i erbjudandet) från den kommersiella marknads platsen. Borttagningen bör göras i enlighet med kraven i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560). Sedan kan utgivare publicera ett nytt erbjudande (eller planera inom ett erbjudande) som innehåller de nya användnings avgifterna. Information om hur du tar bort ett erbjudande eller en plan finns i [sluta sälja ett erbjudande eller en plan](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Kostnads fritt, kontakta mig och ta med din egen licens (BYOL)-prissättning

När du publicerar ett erbjudande med alternativet Hämta nu (kostnads fri), kontakta mig eller BYOL, spelar Microsoft ingen roll för att under lätta försäljnings transaktionen för dina licens avgifter för program vara. Utgivaren behåller 100% av licens avgifterna för program varan.

### <a name="usage-based-and-subscription-pricing"></a>Användnings-och prenumerations priser

När du publicerar ett erbjudande som en användnings-eller prenumerations transaktion tillhandahåller Microsoft teknik och tjänster för att bearbeta inköp av program varu licenser, returer och debitering. I det här scenariot godkänner utgivaren att Microsoft agerar som en agent i detta syfte. Utgivaren gör det möjligt för Microsoft att under lätta licens transaktionen för program varan. Utgivaren behåller din beteckning som säljare, leverantör, distributör och licens givare.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda program varan enligt villkoren i både Microsofts kommersiella marknads plats och ditt licens avtal för slutanvändare. Du måste antingen ange ditt eget licens avtal för slutanvändare eller välja [standard kontraktet](./standard-contract.md) när du skapar erbjudandet.

### <a name="free-software-trials"></a>Kostnads fria program varu försök

För scenario publicerings scenarier kan du göra en program varu licens kostnads fritt i 30 till 120 dagar, beroende på prenumerationen. Kunderna kommer att ändras för lämplig användning av Azure-infrastrukturen.

### <a name="examples-of-pricing-and-store-fees"></a>Exempel på priser och lagrings avgifter

**Användning-baserad**

Användnings priset har följande kostnads struktur:

| **Din licens kostnad** | **$1,00 per timme** |
|---------|---------|
| Användnings kostnad i Azure (D1/1-kärna) | $0,14 per timme |
| _Kunden debiteras av Microsoft_ | _$1,14 per timme_ |
||

I det här scenariot faktureras Microsoft $1,14 per timme för användning av den publicerade VM-avbildningen.

| **Microsoft-räkningar** | **$1,14 per timme**  |
|---------|---------|
| Microsoft betalar 80% av din licens kostnad | $0,80 per timme |
| Microsoft behåller 20% av din licens kostnad  |  $0,20 per timme |
| Microsoft håller 100% av Azures användnings kostnad | $0,14 per timme |
||

**Ta med din egen licens (BYOL)**

BYOL har följande kostnads struktur:

| **Din licens kostnad** | **Licens avgift förhandlad och debiteras av dig** |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
| _Kunden debiteras av Microsoft_ | _$0,14 per timme_ |
||

I det här scenariot faktureras Microsoft $0,14 per timme för användning av den publicerade VM-avbildningen.

| **Microsoft-räkningar** | **$0,14 per timme** |
|---------|---------|
| Microsoft håller Azures användnings kostnad | $0,14 per timme |
| Microsoft behåller 0% av din licens kostnad | $0,00 per timme |
||

**SaaS-app-prenumeration**

SaaS-prenumerationer kan prisas enligt en fast taxa eller per användare per månad eller årlig basis. Om du aktiverar alternativet  **Sälj via Microsoft** för ett SaaS-erbjudande har du följande kostnads struktur:

| **Din licens kostnad** | **$100,00 per månad** |
|--------------|---------|
| Användnings kostnad i Azure (D1/1-kärna) | Debiteras direkt till utgivaren, inte hos kunden |
| _Kunden debiteras av Microsoft_ | _$100,00 per månad (utgivare måste redovisa alla kostnader som uppstått eller genom att överföra infrastruktur i licens avgiften)_ |
||

I det här scenariot fakturerar Microsoft $100,00 för din program varu licens och utvärderar $80,00 eller $90,00 till dig, beroende på om erbjudandet kvalificeras för en minskad lagrings tjänst avgift.

| **Microsoft-räkningar** | **$100,00 per månad** |
|---------|---------|
| Microsoft betalar 80% av din licens kostnad <br> \* Microsoft betalar 90% av din licens kostnad för kvalificerade SaaS-appar | $80,00 per månad <br> \* $90,00 per månad |
| Microsoft behåller 20% av din licens kostnad <br> \* Microsoft behåller 10% av din licens kostnad för alla kvalificerade SaaS-appar. | $20,00 per månad <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Service avgifter för handels platser

Vi debiterar en 20% standard avgift för Store-tjänster när kunderna köper ditt Transact-erbjudande från den kommersiella marknads platsen. Mer information om den här avgiften finns i avsnitt 5c i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560).

För vissa Transact-erbjudanden som du publicerar till den kommersiella marknads platsen kan du kvalificera dig för en minskad lagrings avgift på 10%. För att ett erbjudande ska kvalificeras måste det ha utsetts av Microsoft som _Azure IP Co-Sälj-motiverade_. Berättigande måste uppfyllas minst fem arbets dagar före slutet av varje kalender månad för att ta emot den minskade service avgiften för Marketplace. När behörigheten är upprättad tilldelas den minskade tjänste avgiften till alla transaktioner som gäller från den första dagen i följande månad till dess att _Azure IP-motiverade_ status går förlorad. Information om IP-samförsäljnings behörighet finns i [krav för medförsäljnings status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

Den minskade service avgiften för Marketplace gäller för Azure IP Co-Sälj motiverade SaaS, VM, hanterade appar och andra kvalificerade IaaS-lösningar som görs tillgängliga via den kommersiella Marketplace. Betalda SaaS-erbjudanden som är kopplade till en Microsoft Teams-app eller minst två Microsoft 365-tillägg (Excel, PowerPoint, Word, Outlook och SharePoint) och publiceras till Microsoft AppSource kan också kvalificera sig för den här rabatten.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kund fakturering, betalning, fakturering och samlingar

**Fakturering och betalning**: du kan använda kundens önskade fakturerings metod för att leverera prenumerations-eller [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) program licens avgifter.

**Enterprise-avtal**: om kundens fakturerings metod är Microsoft Enterprise-avtal debiteras licens avgifterna för program varan med hjälp av fakturerings metoden som en specificerad kostnad, separat från alla Azure-specifika användnings kostnader.

**Kredit kort och månads faktura**: kunder kan betala med hjälp av ett kredit kort och en månads faktura. I det här fallet debiteras licens avgifterna för program varan precis som Enterprise-avtal scenariot, som en specificerad kostnad, separat från alla Azure-särskilda användnings kostnader.

**Kostnads fria krediter och betalnings åtagande**: vissa kunder väljer att förskottsbetala Azure med ett betalnings åtagande i Enterprise-avtal eller ha fått kostnads fria krediter för användning i Azure. Även om de här krediterna kan användas för att betala för Azure-användning, kan de inte användas för att betala för licens avgifter för Publisher-programvaran.

**Fakturering och samlingar**: licens faktureringen för Publisher-programvaran presenteras med kunden vald fakturerings metod och följer fakturerings tids linjen. Kunder utan en Enterprise-avtal på plats faktureras per månad för program varu licenser för Marketplace. Kunder med en Enterprise-avtal faktureras per månad via en faktura som presenteras kvartals vis.

När prenumerations modellen för prenumerationer eller betala per användning (även kallat användnings nivå) är markerad, fungerar Microsoft som utgivarens agent och ansvarar för alla aspekter av fakturering, betalning och insamling.

### <a name="publisher-payout-and-reporting"></a>Utbetalning och rapportering av utgivare

Alla licens avgifter för program vara som samlas in av Microsoft som en agent omfattas av 20% transaktions avgift om inget annat anges och dras av vid utbetalning av utgivare.

Kunderna köper vanligt vis med Enterprise-avtal eller ett kredit kort aktiverat avtal för betala per användning. Avtals typen bestämmer fakturerings-, fakturerings-, samlings-och utbetalnings tiden.

>[!NOTE]
>All rapportering och insikter för alternativet för Transact-publicering är tillgängliga via Analytics-avsnittet i Partner Center.

#### <a name="billing-questions-and-support"></a>Fakturerings frågor och support

Mer information och juridiska principer finns i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560). Om du vill ha hjälp med fakturerings frågor kan du kontakta [supporten för Publisher Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-krav

Det här avsnittet beskriver Transact-krav för olika erbjudande typer.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjudande typer

- En Microsoft-konto och ekonomisk information krävs för alternativet för Transact-publicering, oavsett pris sättnings modell för erbjudandet.
- Obligatorisk finansiell information inkluderar utbetalnings konto och skatte profil.

Mer information om hur du konfigurerar dessa konton finns i [Hantera ditt kommersiella Marketplace-konto i Partner Center](manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Krav för särskilda erbjudande typer

Möjligheten att använda Transact via Microsoft är endast tillgänglig för följande erbjudande typer för kommersiella Marketplace. Den här listan innehåller kraven för att göra dessa erbjudande typer i ett transactable i den kommersiella marknads platsen.

- **Azure-program (lösnings mal len och hanterade program planer**: i vissa fall skickas Azure Infrastructure Usage avgifter till kunden separat från licens avgifter för program vara, men på samma fakturerings instruktion. Men om du konfigurerar en hanterad app-plan för kostnader för ISV-infrastruktur debiteras Azure-resurserna för utgivaren och kunden får en fast avgift som omfattar kostnaden för infrastruktur, program varu licenser och hanterings tjänster.

- **Virtuell Azure-dator**: Välj mellan kostnads fria, BYOL eller användnings pris modeller. På kundens Azure-faktura presenterar Microsoft utgivarens licens avgifter separat från de underliggande avgifterna för Azure-infrastrukturen. Avgifterna för Azure-infrastrukturen styrs av användningen av utgivarens program vara.

- **SaaS-program**: måste vara en lösning för flera innehavare, använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för autentisering och integrera med [API: er för SaaS-utförande](partner-center-portal/pc-saas-fulfillment-api-v2.md). Användningen av Azure-infrastrukturen hanteras och faktureras direkt till dig (utgivaren), så du måste kontots användnings avgifter för Azure-infrastrukturen och licens avgifter för program vara som ett enda kostnads objekt. Detaljerad vägledning finns i [så här planerar du ett SaaS-erbjudande för den kommersiella marknads platsen](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Privata planer

Du kan skapa en privat plan för ett erbjudande, komplettera med förhandlad, detaljerad prissättning eller anpassade konfigurationer.

Med privata planer kan du tillhandahålla högre eller lägre priser för vissa kunder än det offentligt tillgängliga erbjudandet. Privata planer kan användas för att rabattera eller lägga till en Premium till ett erbjudande. Privata planer kan göras tillgängliga för en eller flera kunder genom att lista sina Azure-prenumerationer på plan-nivå.

## <a name="next-steps"></a>Nästa steg

- Granska publicerings mönstren per onlinebutik för att få exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
- [Publicerings guide efter erbjudande typ](publisher-guide-by-offer-type.md).
