---
title: Transact-funktioner för Microsofts kommersiella Marketplace
description: Den här artikeln beskriver pris-, fakturerings-, fakturerings-och utbetalnings överväganden för Transact-alternativet för kommersiella Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d602687ea623b90ea5dcae4ece09a1ccc438ea57
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822394"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Transact-funktioner för kommersiella Marketplace

Den här artikeln beskriver pris-, fakturerings-, fakturerings-och utbetalnings överväganden för Microsofts kommersiella Marketplace.

## <a name="transactions-by-listing-option"></a>Alternativ för transaktioner per lista

Antingen utgivaren eller Microsoft ansvarar för att hantera program licens transaktioner för erbjudanden på den kommersiella marknaden. Det List alternativ som du väljer för ditt erbjudande avgör vem som hanterar transaktionen. Tillgänglighet och förklaringar för varje publicerings alternativ finns i [Introduktion till List alternativ](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Kontakta mig, kostnads fri utvärdering och BYOL-alternativ

Utgivare kan välja _kontakten mig_ och den _kostnads fria utvärderings versionen_, alternativ för kampanj-och användar förvärv. För vissa erbjudande typer kan utgivare välja alternativet ta med din egen licens (BYOL) för att göra det möjligt för kunder att köpa en prenumeration på ditt erbjudande med en licens som de har köpt direkt från dig. Med dessa alternativ deltar inte Microsoft direkt i utgivarens program licens transaktioner och det finns ingen kopplad transaktions avgift.

Utgivare ansvarar för att stödja alla aspekter av program licens transaktionen. Detta omfattar men är inte begränsat till order, uppfyllelse, avläsning, fakturering, fakturering, betalning och insamling. Med alternativet kontakta mig List behåller utgivaren 100% av licens avgifterna för Publisher-programvaran som samlas in från kunden.

### <a name="transact-publishing-option"></a>Alternativ för Transact-publicering

Att välja att sälja via Microsoft utnyttjar Microsoft Commerce-funktionerna och ger en komplett upplevelse från identifiering och utvärdering av inköp och implementering. Ett erbjudande som är transactable är ett där Microsoft fören klar utbytet av pengar för en program varu licens för utgivarens räkning. Transactable-erbjudanden debiteras mot en befintlig Microsoft-prenumeration eller ett kredit kort, så att Microsoft kan vara värd för moln Marketplace-transaktioner för utgivarens räkning.

Du väljer alternativet Transact när du skapar ett nytt erbjudande i Partner Center. Med det här alternativet visas endast om Transact är tillgängligt för din erbjudande typ.

## <a name="transact-overview"></a>Översikt över Transact

När du använder Transact-alternativet gör Microsoft det möjligt att sälja program från tredje part och distribution av vissa erbjudande typer till kundens Azure-prenumeration. Utgivaren måste beakta faktureringen av infrastruktur avgifter och dina egna licens avgifter för program vara när du väljer en pris sättnings modell för ett erbjudande.

Alternativet för Transact-publicering stöds för närvarande för följande erbjudande typer:

- Virtuella datorer
- Azure-program
- SaaS-program

### <a name="billing-infrastructure-costs"></a>Kostnader för fakturerings infrastruktur

För **virtuella datorer** och **Azure-program** debiteras Azure Infrastructure Usage avgifter till kundens Azure-prenumeration. Avgifter för infrastruktur användning priss ätts och presenteras separat från program varu leverantörens licens avgifter på kundens faktura.

För **SaaS-appar** måste utgivaren beakta Azure-infrastrukturens användnings avgifter och program licens avgifter som ett enda kostnads objekt.  Den representeras som en fast avgift för kunden. Användningen av Azure-infrastrukturen hanteras och debiteras för utgivaren direkt. Den faktiska infrastruktur användnings avgiften ses inte av kunden. Utgivare väljer vanligt vis att paketera användnings avgifter för Azure-infrastruktur i sina priser för program varu licenser. Avgifterna för program varu licenser är inte avgiftsbelagda eller baseras på användar förbrukning.

## <a name="pricing-models"></a>Prissättningsmodeller

Beroende på vilket transaktions alternativ som används är prenumerations avgifterna följande:

- **Hämta nu (kostnads fritt)** – ingen avgift för program varu licenser. Kunder debiteras inte Azure Marketplace-avgifter för användning av ett kostnads fritt erbjudande. Kostnads fria erbjudanden kan inte konverteras till ett betalt erbjudande. Kunderna måste beställa ett betalt erbjudande.
- **Bring Your Own License** (BYOL) – alla tillämpliga avgifter för program varu licenser hanteras direkt mellan utgivaren och kunden. Microsoft passerar bara genom användnings avgifter för Azure-infrastruktur. Om ett erbjudande är listat på den kommersiella marknads platsen debiteras inte kunder som får åtkomst eller användning av erbjudandet utanför den kommersiella marknads platsen.
- **Prenumerations priser** – licens avgifter för program vara presenteras som en månatlig eller årlig, återkommande prenumerations avgift debiteras som en fast taxa eller per plats. Nya prenumerations avgifter beräknas inte proportionellt för uppsägningar på medel lång sikt eller oanvända tjänster. Nya prenumerations avgifter kan debiteras om kunden uppgraderar eller nedgraderar prenumerationen i mitten av prenumerations perioden.
- **Användnings-baserade priser** – för erbjudanden för virtuella Azure-datorer debiteras kunderna utifrån omfattningen av deras användning av erbjudandet. För avbildningar av virtuella datorer debiteras kunder en Tim avgift för Azure Marketplace, som anges av utgivare, för användning av virtuella datorer som distribueras från VM-avbildningarna. Tim avgiften kan vara enhetlig eller variera mellan storleken på virtuella datorer. Delar av timmar debiteras per minut. Planer faktureras per månad.
- **Avgiftsbelagd prissättning** – för Azure Application erbjudanden och SaaS-erbjudanden kan utgivare använda Marketplace för [avläsning](./partner-center-portal/marketplace-metering-service-apis.md) av program vara för att debiteras för förbrukning baserat på de mätnings dimensioner som de väljer. Till exempel bandbredd, biljetter eller e-postmeddelanden som bearbetas. Utgivare kan definiera en eller flera mätnings dimensioner för varje plan. Utgivare ansvarar för att spåra enskilda kunders användning, med varje mätare som definierats i erbjudandet. Händelser ska rapporteras till Microsoft inom en timme. Microsoft debiterar kunderna baserat på användnings information som rapporteras av utgivare för den aktuella fakturerings perioden.
- **Kostnads fri utvärdering** – ingen avgift för program varu licenser som sträcker sig från 30 dagar upp till sex månader, beroende på typen av erbjudande. Om utgivare tillhandahåller en kostnads fri utvärderings version av flera planer inom samma erbjudande kan kunderna byta till en kostnads fri utvärderings version på ett annat avtal, men utvärderings perioden startar inte om. För erbjudanden om virtuella datorer debiteras kunderna Azures infrastruktur kostnader för användning av erbjudandet under en utvärderings period. När utvärderings perioden har löpt ut debiteras kunderna automatiskt för den senaste planen de försökte baseras på standardpriser, såvida de inte annulleras före utvärderings periodens slut.

> [!NOTE]
> Erbjudanden som debiteras enligt förbrukningen när en lösning har använts är inte berättigad till åter betalningar.

Utgivare som vill ändra de användnings avgifter som är kopplade till ett erbjudande bör först ta bort erbjudandet (eller den speciella planen i erbjudandet) från den kommersiella marknads platsen. Borttagningen bör göras i enlighet med kraven i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560). Sedan kan utgivare publicera ett nytt erbjudande (eller planera inom ett erbjudande) som innehåller de nya användnings avgifterna. Information om hur du tar bort ett erbjudande eller en plan finns i [sluta sälja ett erbjudande eller en plan](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Kostnads fritt, kontakta mig och ta med din egen licens (BYOL)-prissättning

När du publicerar ett erbjudande med alternativet Hämta nu (kostnads fri), kontakta mig eller BYOL, spelar Microsoft ingen roll för att under lätta försäljnings transaktionen för dina licens avgifter för program vara. I likhet med publicerings alternativen lista och kostnads fri utvärdering behåller utgivaren 100% av licens avgifterna för program varan.

### <a name="usage-based-and-subscription-pricing"></a>Användnings-och prenumerations priser

När du publicerar ett erbjudande en användarbaserad eller prenumerations transaktion tillhandahåller Microsoft teknik och tjänster för att bearbeta inköp, returer och åter betalningar för program varu licenser. I det här scenariot godkänner utgivaren att Microsoft agerar som en agent i detta syfte. Utgivaren gör det möjligt för Microsoft att under lätta för program licens transaktionen, samtidigt som säljaren, leverantören, distributören och licens givaren behålls.

Microsoft gör det möjligt för kunder att beställa, licensiera och använda program varan enligt villkoren i både Microsofts kommersiella marknads plats och ditt licens avtal för slutanvändare. Du måste ange ett eget licens avtal för slutanvändare eller välja [standard kontraktet](./standard-contract.md) när du skapar erbjudandet.

### <a name="free-software-trials"></a>Kostnads fria program varu försök

För scenario publicerings scenarier kan du göra en program varu licens kostnads fritt i 30 till 120 dagar, beroende på prenumerationen. Den här rabatt funktionen omfattar inte kostnaden för Azures infrastruktur användning som styrs av partner lösningen.

### <a name="private-offers"></a>Privata erbjudanden

Förutom att använda erbjudande typer och fakturerings modeller för att betala ett erbjudande kan du använda Transact ett privat erbjudande, komplett med förhandlad, detaljerad prissättning eller anpassade konfigurationer. Privata erbjudanden stöds av alla tre alternativ för Transact-publicering.

Det här alternativet tillåter högre eller lägre priser än det offentligt tillgängliga erbjudandet. Du kan använda privata erbjudanden för rabatt eller lägga till en Premium till ett erbjudande. Du kan göra privata erbjudanden tillgängliga för en eller flera kunder genom att teckna en Azure-prenumeration på erbjudande nivån.

### <a name="commercial-marketplace-service-fees"></a>Service avgifter för handels platser

Vi debiterar en 20% standard avgift för Store-tjänster när kunderna köper ditt Transact-erbjudande från den kommersiella marknads platsen. Mer information om den här avgiften finns i avsnitt 5c i [Microsoft Publisher-avtalet](https://go.microsoft.com/fwlink/?LinkID=699560).

För vissa Transact-erbjudanden som du publicerar till den kommersiella marknads platsen kan du kvalificera dig för en minskad lagrings avgift på 10%. För att ett erbjudande ska kvalificeras måste det ha utsetts av Microsoft som Azure IP Co-Sälj-motiverade. Berättigande måste uppfyllas minst fem arbets dagar före slutet av varje kalender månad för att ta emot den minskade service avgiften för Marketplace. När behörigheten är upprättad tilldelas den minskade tjänste avgiften till alla transaktioner som gäller från den första dagen i följande månad till dess att Azure IP-motiverade status går förlorad. Information om IP-samförsäljnings behörighet finns i [krav för medförsäljnings status](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status).

Den minskade service avgiften för Marketplace gäller för Azure IP Co-Sälj motiverade SaaS, VM, hanterade appar och andra kvalificerade IaaS-lösningar som görs tillgängliga via den kommersiella Marketplace. Betalda SaaS-erbjudanden som är kopplade till en Microsoft Teams-app eller minst två Microsoft 365-tillägg (Excel, PowerPoint, Word, Outlook och SharePoint) och som publiceras till Microsoft AppSource också få rabatten.

### <a name="examples"></a>Exempel

**Användning-baserad** 

Användnings priset har följande kostnads struktur:

|Din licens kostnad  | $1,00 per timme   |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$1,14 per timme*       |
||

I det här scenariot faktureras Microsoft $1,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $1,14 per timme  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad|   $0,80 per timme     |
|Microsoft behåller 20% av din licens kostnad  |  $0,20 per timme       |
|Microsoft håller 100% av Azures användnings kostnad | $0,14 per timme |
||

**Bring Your Own License (BYOL)**

BYOL har följande kostnads struktur:

|Din licens kostnad  | Licens avgift förhandlad och debiteras av dig  |
|---------|---------|
|Användnings kostnad i Azure (D1/1-kärna)    |   $0,14 per timme     |
|*Kunden debiteras av Microsoft*    |  *$0,14 per timme*       |
||

I det här scenariot faktureras Microsoft $0,14 per timme för användning av den publicerade VM-avbildningen.

|Microsoft-räkningar  | $0,14 per timme  |
|---------|---------|
|Microsoft håller Azures användnings kostnad    |   $0,14 per timme     |
|Microsoft behåller 0% av din licens kostnad   |  $0,00 per timme       |
||

**SaaS-app-prenumeration**

Det här alternativet måste konfigureras att sälja via Microsoft och kan prisas enligt en fast taxa eller per användare per månad eller årlig basis. Om du aktiverar alternativet **Sälj via Microsoft** för ett SaaS-erbjudande har du följande kostnads struktur:

| Din licens kostnad       | $100,00 per månad  |
|--------------|---------|
| Användnings kostnad i Azure (D1/1-kärna)    | Debiteras direkt till utgivaren, inte hos kunden |
| *Kunden debiteras av Microsoft*    |  *$100,00 per månad (utgivare måste redovisa alla kostnader som uppstått eller genom att överföra infrastruktur i licens avgiften)*  |
||

I det här scenariot faktureras Microsoft $100,00 för din program varu licens och du betalar ut $80,00 till utgivaren.

I det här scenariot fakturerar Microsoft $100,00 för din program varu licens och utvärderar $90,00 till utgivaren:

|Microsoft-räkningar  | $100,00 per månad  |
|---------|---------|
|Microsoft betalar 80% av din licens kostnad <br> \* Microsoft betalar 90% av din licens kostnad för kvalificerade SaaS-appar   |   $80,00 per månad <br> \* $90,00 per månad    |
|Microsoft behåller 20% av din licens kostnad <br> \* Microsoft behåller 10% av din licens kostnad för alla kvalificerade SaaS-appar.  |  $20,00 per månad <br> \* $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Kund fakturering, betalning, fakturering och samlingar

**Fakturering och betalning** – du kan använda kundens önskade fakturerings metod för att leverera prenumerations-eller PAYGO program licens avgifter.

**Enterprise-avtal** – om kundens fakturerings metod är Microsoft Enterprise-avtal debiteras licens avgifterna för program varan med hjälp av fakturerings metoden som en specificerad kostnad, separat från alla Azure-specifika användnings kostnader.

**Kredit kort och månads faktura** – kunder kan också betala med ett kredit kort och en månads faktura. I det här fallet debiteras licens avgifterna för program varan precis som Enterprise-avtal scenariot, som en specificerad kostnad, separat från alla Azure-särskilda användnings kostnader.

**Kostnads fria krediter och betalnings åtagande** – vissa kunder väljer att förskottsbetala Azure med ett penning åtagande i Enterprise-avtal eller ha fått kostnads fria krediter för användning med Azure. Även om de här krediterna kan användas för att betala för Azure-användning, kan de inte användas för att betala för licens avgifter för Publisher-programvaran.

**Fakturering och samlingar** – licens fakturering för Publisher-programvara presenteras med kunden vald fakturerings metod och följer fakturerings tids linjen. Kunder utan en Enterprise-avtal på plats faktureras per månad för program varu licenser för Marketplace. Kunder med en Enterprise-avtal faktureras per månad via en faktura som presenteras kvartals vis.

När du har valt prenumerations modell eller pris sättning enligt principen betala per användning fungerar Microsoft som utgivarens agent och ansvarar för alla aspekter av fakturering, betalning och insamling.

### <a name="publisher-payout-and-reporting"></a>Utbetalning och rapportering av utgivare

Alla licens avgifter för program vara som samlas in av Microsoft som en agent omfattas av 20% transaktions avgift om inget annat anges och dras av vid utbetalning av utgivare.

Kunderna köper vanligt vis med Enterprise-avtal eller ett kredit kort aktiverat avtal för betala per användning. Avtals typen bestämmer fakturerings-, fakturerings-, samlings-och utbetalnings tiden.

>[!NOTE]
>All rapportering och insikter för alternativet för Transact-publicering är tillgängliga via Analytics-avsnittet i Partner Center.

#### <a name="billing-questions-and-support"></a>Fakturerings frågor och support

Mer information och juridiska principer finns i [Microsoft Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560) (tillgängligt i Partner Center).

Om du behöver hjälp med fakturerings frågor kontaktar du [supporten för support för Microsoft Marketplace](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact-krav

Det här avsnittet beskriver Transact-krav för olika erbjudande typer.

### <a name="requirements-for-all-offer-types"></a>Krav för alla erbjudande typer

- En Microsoft-konto och ekonomisk information krävs för alternativet för Transact-publicering, oavsett pris sättnings modell för erbjudandet.
- Obligatorisk finansiell information inkluderar utbetalnings konto och skatte profil.
- Utgivaren måste finnas i ett [land eller en region som stöds](sell-from-countries.md).

Mer information om hur du konfigurerar dessa konton finns i [Hantera ditt kommersiella Marketplace-konto i Partner Center](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Krav för särskilda erbjudande typer

Alternativet för att använda Transact-publicering är bara tillgängligt för användning med följande Marketplace-erbjudande typer:

- **Virtuell Azure-dator** – Välj från kostnads fria, egna licens-eller användnings pris modeller och som finns som planer definierade på erbjudande nivån. På kundens Azure-faktura presenterar Microsoft utgivarens licens avgifter separat från de underliggande avgifterna för Azure-infrastrukturen. Avgifterna för Azure-infrastrukturen styrs av användningen av utgivarens program vara.

- **Azure-program: lösnings mal len eller hanterad app** – du måste etablera en eller flera virtuella datorer och hämta genom summan av priserna för den virtuella datorn. För hanterade appar i en enda plan kan en månatlig månatlig prenumeration väljas som pris modell i stället för prissättningen för den virtuella datorn. I vissa fall skickas Azure Infrastructure användnings avgifter till kunden separat från licens avgifter för program vara, men på samma fakturerings instruktion. Men om du konfigurerar ett hanterat program erbjudande för ISV-infrastruktur debiteras Azure-resurserna för utgivaren och kunden får en fast avgift som omfattar kostnaden för infrastruktur, program varu licenser och hanterings tjänster.

- **SaaS-program** – måste vara en lösning för flera innehavare, använda [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för autentisering och integrera med [API: er för SaaS-utförande](partner-center-portal/pc-saas-fulfillment-api-v2.md). Användningen av Azure-infrastrukturen hanteras och faktureras direkt till dig (partnern), så du måste kontots användnings avgifter för Azure-infrastrukturen och licens avgifter för program vara som ett enda kostnads objekt. Detaljerad vägledning finns [i skapa ett nytt SaaS-erbjudande i den kommersiella Marketplace](./create-new-saas-offer.md).

## <a name="next-steps"></a>Nästa steg

- Granska behörighets kraven i avsnittet publicerings alternativ per erbjudande typ för att slutföra valet och konfigurationen av ditt erbjudande.
- Granska publicerings mönstren per onlinebutik för att få exempel på hur din lösning mappar till en erbjudande typ och konfiguration.
