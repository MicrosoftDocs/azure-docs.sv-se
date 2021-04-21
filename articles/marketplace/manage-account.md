---
title: Hantera ett konto för den kommersiella marknadsplatsen i Microsoft Partner Center – Azure Marketplace
description: Lär dig hur du hanterar ett konto för den kommersiella marknadsplatsen i Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 6b721e7acb7907743c0696aff6c11ad59f5ceba9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812578"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Hantera ditt commercial marketplace-konto i Partnercenter

**Lämpliga roller**

- Ägare
- Ansvarig

När du har skapat [ett Partnercenter-konto kan](./create-account.md)du använda instrumentpanelen för [den kommersiella marknadsplatsen](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) för att hantera ditt konto och dina erbjudanden.

## <a name="access-your-account-settings"></a>Få åtkomst till dina kontoinställningar

Om du inte redan har gjort det bör du (eller din organisations administratör) komma åt [kontoinställningarna för](https://partner.microsoft.com/dashboard/account/management) ditt Partnercenter-konto.

1. Logga in på [instrumentpanelen för den kommersiella](https://partner.microsoft.com/dashboard/commercial-marketplace) marknadsplatsen i Partnercenter med det konto som du vill komma åt. Om du ingår i flera konton och har loggat in med en annan kan du [byta konto.](switch-accounts.md)
1. Längst upp till höger väljer du **Inställningar** (kugghjulsikonen) och sedan **Kontoinställningar.**

    [![Skärmbild av menyn kontoinställningar i Partnercenter. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. Under **Kontoinställningar väljer** du **Juridiskt.** Välj sedan fliken **Utvecklare** för att visa information om ditt kommersiella Marketplace-konto.

    [![Skärmbild av fliken Utvecklare på den juridiska sidan i Kontoinställningar. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Sidan Kontoinställningar

När du väljer **Inställningar** och **expanderar Kontoinställningar** är standardvyn **Juridisk information**. Den här sidan kan ha upp till tre flikar, beroende på vilka program du har registrerat i: _Partner,_ _Återförsäljare_ och _Utvecklare._

Fliken **Partner** för partner som registrerats i MPN innehåller:

- All juridisk företagsinformation, till exempel registrerat juridiskt namn och adress för ditt företag
- Primär kontakt
- Företagsplatser.

Fliken **Reseller (Återförsäljare)** för partner som utför CSP-verksamhet innehåller:

- Primär kontaktinformation
- Kundsupportprofil
- Programinformation

Fliken **Utvecklare** innehåller följande information för partner som har registrerats i alla utvecklingsprogram:

- **Kontoinformation:** Kontotyp och Kontostatus
- **Utgivar-ID:** Säljar-ID, användar-ID, utgivar-ID, Azure AD-klientorganisation med mera
- **Kontaktinformation:** Utgivarens visningsnamn, säljarkontakt (namn, e-postadress, telefon och adress) och företags godkännare (namn, e-postadress, telefon)

### <a name="account-settings---developer-tab"></a>Kontoinställningar – fliken Utvecklare

Följande information beskriver informationen på fliken Utvecklare.

#### <a name="account-details"></a>Kontoinformation

I _avsnittet Kontoinformation_  på fliken Utvecklare kan du se grundläggande information, till exempel kontotyp **(företag** eller enskild) och **verifieringsstatus** för ditt konto. Under kontoverifieringsprocessen visar inställningarna varje steg som krävs, inklusive e-postverifiering, anställningsverifiering och företagsverifiering.

#### <a name="publisher-ids"></a>Utgivar-ID:er

I avsnittet Utgivar-ID:n kan du se ditt **Symantec-ID** (om tillämpligt), **säljar-ID,** **användar-ID,** **MPN-ID** och **Azure AD-klienter.** Dessa värden tilldelas av Microsoft för att unikt identifiera ditt utvecklarkonto och kan inte redigeras.

Om du inte har ett Symantec-ID kan du välja länken för att begära ett.

### <a name="contact-info"></a>Kontaktuppgifter

I _avsnittet_ Kontaktinformation visas utgivarens **visningsnamn,** kontaktuppgifter till säljare (kontaktnamn, e-postadress, telefonnummer och adress för företagets säljare) och Företags godkännare **(namn,** e-postadress och telefonnummer för den person som har behörighet att godkänna företagets beslut). 

Du kan också välja länken **Uppdatera om** du vill ändra din kontaktinformation, till exempel utgivarens visningsnamn och e-postadress.

### <a name="account-settings-identifiers"></a>Identifierare för kontoinställningar

Under **Kontoinställningar**  >  **Organisationsprofil** väljer du **Identifierare** för att se följande information:

- **MPN-ID:er:** ALLA MPN-ID:er som är associerade med ditt konto
- **CSP:** MPN-ID:er som är associerade med CSP-programmet för det här kontot.
- **Utgivare:** Säljar-ID:er som är associerade med ditt konto
- **Spårnings-GUID:** Alla spårnings-GUID:er som är associerade med ditt konto

#### <a name="tracking-guids"></a>Spåra GUID:er

Globalt unika identifierare (GUID) är unika referensnummer (med 32 hexadecimala siffror) som kan användas för att spåra din Azure-användning.

Om du vill skapa GUID:er för spårning bör du använda en GUID-generator. Teamet Azure Storage har skapat ett [GUID-generatorformulär](https://aka.ms/StoragePartners) som skickar ett GUID med rätt format via e-post och som kan återanvändas i de olika spårningssystemen.

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributionskanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributionskanaler om du inte vill att rapporteringen ska delas upp.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub kan du skapa och registrera två distinkta GUID:er:

- Produkt A i Azure Marketplace
- Produkt A på GitHub

Rapporteringen görs av partnervärdet (Microsoft Partner-ID) och GUID:erna. Du kan också spåra GUID:er på en mer detaljerad nivå som överensstämmer med varje plan i ditt erbjudande.

Mer information finns i Spåra [Azure-kundanvändning med GUID:er](azure-partner-customer-usage-attribution.md#faq)vanliga frågor och svar ).

### <a name="agreements"></a>Avtal

På **sidan** Avtal kan du visa en lista över de publiceringsavtal som du har godkänt. Dessa avtal listas enligt namn och versionsnummer, inklusive det datum då det godkändes och namnet på den användare som godkände avtalet.

Så här kommer du till sidan Avtal:

1. Logga in på [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Längst upp till höger väljer du **Inställningar**  >  **Kontoinställningar.**
1. Under **Kontoinställningar** väljer du **Avtal.**

**Åtgärder som** krävs kan visas överst på den här sidan om det finns avtalsuppdateringar som behöver din uppmärksamhet. Om du vill godkänna ett uppdaterat avtal läser du först den länkade avtalsversionen och väljer **sedan Acceptera avtal.**

## <a name="set-up-a-payout-profile"></a>Konfigurera en utbetalningsprofil

En utbetalningsprofil är det bankkonto som fortsätter att skickas från din försäljning. Det här bankkontot måste finnas i samma land eller region där du registrerade ditt Partnercenter-konto. Mer information om en utbetalningsprofil finns i Skapa och hantera incitament för utbetalnings- och skatteprofiler i [Partnercenter](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) och Konfigurera ditt [utbetalningskonto och skatteformulär.](/partner-center/set-up-your-payout-account)

Så här ställer du in din utbetalningsprofil:

1. Gå till [översiktssidan för den kommersiella marknadsplatsen](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partnercenter.
2. I avsnittet **Profil** bredvid Utbetalningsprofil **väljer** du **Uppdatera**.
3. **Välj en betalningsmetod:** Bankkonto eller PayPal.
4. **Lägg till betalningsinformation:** Detta kan omfatta att välja en kontotyp (kontroll eller sparande), ange kontoinnehavarens namn, kontonummer och routningsnummer, faktureringsadress, telefonnummer eller PayPal-e-postadress. Mer information om hur du använder PayPal som betalningsmetod för ditt konto och om du vill ta reda på om det stöds i din marknad eller region finns [i PayPal info](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Om du ändrar ditt utbetalningskonto kan du fördröja dina betalningar med upp till en betalningscykel. Den här fördröjningen beror på att vi behöver verifiera kontoändringen, precis som vi gör när vi först ställer in utbetalningskontot. Du får fortfarande betalt för hela beloppet när ditt konto har verifierats. eventuella betalningar som förfaller för den aktuella betalningscykeln läggs till i nästa.  

## <a name="tax-profile"></a>Skatteprofil

Granska din aktuella skatteprofilstatus och bekräfta att rätt **entitetstyp** **och skattecertifikatinformation** visas. Välj **Redigera** för att uppdatera eller fylla i alla formulär som krävs.

För att kunna fastställa din skattestatus måste du ange ditt land eller din region för hem och ursprungsland och fylla i lämpliga skatteformulär som är associerade med ditt land eller din region.

Oavsett land eller region måste du fylla i skatteformulär USA att sälja erbjudanden via Microsoft. Partner som uppfyller vissa USA måste fylla i ett IRS W-9-formulär. Andra partner utanför USA måste fylla i ett IRS W-8-formulär. Du kan fylla i dessa formulär online när du fyller i din skatteprofil.

Ett USA individuellt identifieringsnummer (eller ITIN) behöver inte ta emot betalningar från Microsoft eller kräva skatteförmåner.

Du kan fylla i och skicka skatteformulär elektroniskt i Partnercenter; I de flesta fall behöver du inte skriva ut och skicka några formulär via e-post.

Olika länder och regioner har olika skattekrav. Exakt hur mycket du måste betala i skatt beror på vilka länder och regioner där du säljer dina erbjudanden. Microsoft erbjuder försäljning och använder skatt åt dig i vissa länder och regioner. Dessa länder och regioner identifieras när du listar ditt erbjudande. I andra länder och regioner, beroende på var du är registrerad, kan du behöva överföra försäljning och använda skatt för din försäljning direkt till den lokala skatteutfärdaren. Dessutom kan försäljningen du får vara momsbar som inkomst. Vi rekommenderar starkt att du kontaktar relevant myndighet för ditt land eller din region som bäst kan hjälpa dig att fastställa rätt skatteinformation för dina Microsoft-försäljningstransaktioner.

Mer information om en skatteprofil finns i Skapa och hantera incitament för utbetalnings- och skatteprofiler i [Partnercenter](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) och Konfigurera ditt [utbetalningskonto och skatteformulär.](/partner-center/set-up-your-payout-account)

### <a name="withholding-rates"></a>Källskattefrekvens

Den information som du skickar i dina skatteformulär avgör lämplig källskatt. Källskattesatsen gäller endast för försäljning som du gör i USA; försäljning som görs till icke-amerikanska platser omfattas inte av källskatt. Källskattefrekvensen varierar, men för de flesta utvecklare som registrerar sig utanför USA är standardfrekvensen 30 %. Du kan välja att minska den här frekvensen om ditt land eller din region har samtyckt till en inkomstskatt med USA.

### <a name="tax-treaty-benefits"></a>Förmåner för skatteförmåner

Om du är utanför USA kan du eventuellt dra nytta av skatteförmåner. Dessa förmåner varierar från land/region till land/region och kan göra att du kan minska mängden skatter som Microsoft håller inne. Du kan begära skatteförmåner genom att fylla i del II av W-8BEN-formuläret. Vi rekommenderar att du kommunicerar med lämpliga resurser i ditt land eller din region för att avgöra om dessa förmåner gäller för dig.

[Läs mer om skatteinformation för Windows-app-/spelutvecklare och Azure Marketplace utgivare.](/windows/uwp/publish/tax-details-for-paid-apps)

### <a name="payout-hold-status"></a>Status för utbetalnings hold

Som standard skickar Microsoft betalningar per månad. Du kan dock välja att vänta med dina betalningar, vilket förhindrar att betalningar skickas till ditt konto. Om du väljer att vänta med dina utbetalningsbetalningar fortsätter vi att registrera eventuella intäkter som du tjänar och ange informationen i din **utbetalningssammanfattning.** Vi skickar dock inga betalningar till ditt konto förrän du tar bort stödet.

**Så här placerar du dina betalningar i betalningsanklagning:**

1. Gå till **Kontoinställningar**. 
1. I det vänstra navigeringsfältet **expanderar du Utbetalning och skatt** och väljer **Utbetalnings- och skatteprofiler.**
1. Välj det program som du vill hålla betalningar för och markera sedan **kryssrutan Håll min** betalning.

Du kan ändra statusen för utbetalnings hold när som helst, men tänk på att ditt beslut påverkar nästa månadsutbetalning. Om du till exempel vill hålla aprilutbetalningen ska du se till att ställa in din utbetalningsstatus på **På** innan slutet av mars.

När du har ställt in din utbetalningsstatus på **På** behålls alla utbetalningar tills du växlar skjutreglaget tillbaka till **Av**. När du gör det inkluderas du under nästa månatliga utbetalningscykel (förutsatt att tillämpliga betalningströsklar har uppnåtts). Om du till exempel har fått dina utbetalningar väntande, men vill ha en utbetalning som genereras i juni, ska du se till att ändra utbetalningsstatusen till **Av** innan slutet av maj.

> [!NOTE]
> Ditt **val av utbetalningsstatus** gäller för alla intäktskällor som betalas via Microsoft Partner Center, inklusive Azure Marketplace, Microsoft AppSource, Microsoft Store, annonsering och så vidare.).  Du kan inte välja olika kvarställningsstatusar för varje intäktskälla.

## <a name="devices"></a>Enheter

Inställningarna för enhetshantering gäller endast för UWP-publicering (Universal Windows Platform). [Läs mer](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Skapa en faktureringsprofil

Om du publicerar ett [erbjudande för Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) eller Dynamics [365 for Operations](./partner-center-portal/create-new-operations-offer.md) måste du slutföra *faktureringsprofilen*.

Faktureringsadressen är ifylld i förväg från din juridiska enhet och du kan uppdatera den här adressen senare. Fälten moms och momsregistreringsnummer krävs för vissa länder och valfria för andra. Namnet på landet/regionen och företagets namn kan inte redigeras.

1. Gå till **Kontoinställningar.**
1. Expandera sedan Organisationsprofil i det vänstra **navigeringsfältet och** välj **Faktureringsprofil.**


## <a name="multi-user-account-management"></a>Hantering av flera användarkonton

Partnercenter använder [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) för åtkomst och hantering av flera användarkonton. Din organisations Azure AD associeras automatiskt med ditt Partnercenter-konto som en del av registreringsprocessen.

## <a name="next-steps"></a>Nästa steg

- [Lägga till och hantera användare](add-manage-users.md)
