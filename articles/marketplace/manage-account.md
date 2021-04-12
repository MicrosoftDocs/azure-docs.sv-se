---
title: Så här hanterar du ett kommersiellt marknads plats konto i Microsoft Partner Center – Azure Marketplace
description: Lär dig hur du hanterar ett kommersiellt marknads plats konto i Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108472"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Hantera ditt kommersiella Marketplace-konto i Partner Center

**Lämpliga roller**

- Ägare
- Ansvarig

När du har [skapat ett partner Center-konto](./create-account.md)kan du använda [instrument panelen för extern Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) för att hantera ditt konto och erbjudanden.

## <a name="access-your-account-settings"></a>Komma åt dina konto inställningar

Om du inte redan har gjort det ska du (eller din organisations administratör) komma åt [konto inställningarna](https://partner.microsoft.com/dashboard/account/management) för ditt partner Center-konto.

1. Logga in på [instrument panelen för kommersiellt marknads platser](https://partner.microsoft.com/dashboard/commercial-marketplace) i Partner Center med det konto som du vill komma åt. Om du är en del av flera konton och har loggat in med ett annat, kan du [Växla konton](switch-accounts.md).
1. I det övre högra hörnet väljer du **Inställningar** (kugg hjuls ikon) och väljer sedan **konto inställningar**.

    [![Skärm bild av menyn konto inställningar i Partner Center. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. Välj **juridiskt** under **konto inställningar** . Välj fliken **utvecklare** för att visa information som är relaterad till ditt kommersiella marknads plats konto.

    [![Skärm bild av fliken Utvecklare på den juridiska sidan i konto inställningar. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Sidan konto inställningar

När du väljer **Inställningar** och expanderar **konto inställningar** är standardvyn **juridisk information**. Den här sidan kan ha upp till tre flikar, beroende på vilka program du har registrerat i: _partner_, _åter försäljare_ och _utvecklare_.

Fliken **partner** för partner som har registrerats i MPN innehåller:

- All juridisk information, till exempel registrerat juridiskt namn och adress för ditt företag
- Primär kontakt
- Företags platser.

Fliken **åter försäljare** för partner som utför CSP-verksamhet innehåller:

- Primär kontakt information
- Kund support profil
- Programinformation

Fliken **utvecklare** , för partner som har registrerats i ett program för utvecklare, har följande information:

- **Konto information**: konto typ och konto status
- **Utgivar**-ID: säljar-ID, användar-ID, utgivarens ID, Azure AD-klienter med mera
- **Kontakt information**: utgivarens visnings namn, säljar kontakt (namn, e-post, telefon och adress) och företags god kännare (namn, e-post, telefon)

### <a name="account-settings---developer-tab"></a>Konto inställningar – fliken Utvecklare

Följande information beskriver informationen på fliken Utvecklare.

#### <a name="account-details"></a>Konto information

I avsnittet _konto information_ på fliken _utvecklare_ kan du se grundläggande information, till exempel din **Kontotyp** (företag eller individ) och **verifierings statusen** för ditt konto. Under ditt konto verifierings process visar de här inställningarna varje steg som krävs, inklusive e-postverifiering, personal verifiering och verksamhets verifiering.

#### <a name="publisher-ids"></a>Utgivar-ID

I avsnittet Publisher-ID kan du se ditt **Symantec-ID** (om tillämpligt), **säljar-ID**, **användar-** ID, **MPN-ID** och **Azure AD-klienter**. Dessa värden tilldelas av Microsoft för att unikt identifiera ditt Developer-konto och kan inte redige ras.

Om du inte har ett Symantec-ID kan du välja länken för att begära ett.

### <a name="contact-info"></a>Kontaktuppgifter

I avsnittet _kontakt information_ ser du **visnings namnet för utgivaren**, **säljarens kontakt information** (kontakt namn, e-postadress, telefonnummer och adress för företags säljaren) och **företagets god kännare** (namn, e-postadress och telefonnummer till den person som har behörighet att godkänna beslut för företaget).

Du kan också välja länken **Uppdatera** om du vill ändra din kontakt information, till exempel visnings namn och e-postadress för Publisher.

### <a name="account-settings-identifiers"></a>Konto inställnings identifierare

Under **konto inställningar**  >  **organisations profil** väljer du **identifierare** för att se följande information:

- **MPN-ID**: MPN-ID: n som är kopplade till ditt konto
- **CSP**: MPN-ID som är kopplade till CSP-programmet för det här kontot.
- **Utgivare**: säljar-ID: n som är kopplade till ditt konto
- **Spåra GUID**: alla spårnings-GUID som är associerade med ditt konto

#### <a name="tracking-guids"></a>Spåra GUID

Globalt unika identifierare (GUID) är unika referens nummer (med 32 hexadecimala siffror) som kan användas för att spåra din Azure-användning.

Om du vill skapa GUID för spårning bör du använda en GUID-Generator. Azure Storages teamet har skapat ett [GUID Generator-formulär](https://aka.ms/StoragePartners) som kommer att skicka ett GUID till rätt format och kan återanvändas över olika spårnings system.

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributions kanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributions kanaler om du inte vill att rapportering ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera två olika GUID:

- Produkt A på Azure Marketplace
- Produkt A på GitHub

Rapportering görs av partner värdet (Microsoft partner-ID) och GUID. Du kan också spåra GUID på en mer detaljerad nivå som justeras efter varje plan i erbjudandet.

Mer information finns i [vanliga frågor och svar om att spåra Azures kund användning med GUID](azure-partner-customer-usage-attribution.md#faq).

### <a name="agreements"></a>Avtal

På sidan **avtal** kan du Visa en lista över de publicerings avtal som du har auktoriserat. Dessa avtal visas enligt namn och versions nummer, inklusive datumet då det godkändes och namnet på den användare som har godkänt avtalet.

Så här öppnar du avtals sidan:

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
1. I det övre högra hörnet väljer du **Inställningar**  >  **konto inställningar**.
1. Under **konto inställningar** väljer du **avtal**.

De **åtgärder som krävs** kan visas överst på den här sidan om det finns avtals uppdateringar som kräver din uppmärksamhet. Om du vill godkänna ett uppdaterat avtal läser du först den länkade avtals versionen och väljer sedan **Godkänn avtal**.

## <a name="set-up-a-payout-profile"></a>Konfigurera en utbetalnings profil

En utbetalnings profil är Bank kontot som fortsätter skickas från din försäljning. Det här bank kontot måste finnas i samma land eller region där du registrerade ditt partner Center-konto. Mer information om en utbetalnings profil finns i [skapa och hantera incitaments utbetalningar och skatte profiler i Partner Center](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) och [Konfigurera ditt utbetalnings konto och skatte formulär](/partner-center/set-up-your-payout-account).

Så här konfigurerar du din utbetalnings profil:

1. Gå till [sidan för extern Marketplace-översikt](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partner Center.
2. I avsnittet **profil** , bredvid **utbetalnings profil**, väljer du **Uppdatera**.
3. **Välj en betalnings metod**: bank konto eller PayPal.
4. **Lägg till betalnings information**: Detta kan vara att välja en kontotyp (kontroll eller besparingar), ange konto innehavarens namn, konto nummer och routing number, fakturerings adress, telefonnummer eller PayPal-e-postadress. Mer information om hur du använder PayPal som din konto Betalnings Metod och för att ta reda på om den stöds på din marknad eller region finns i [PayPal-information](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Att ändra ditt utbetalnings konto kan försena dina betalningar med upp till en betalnings cykel. Den här fördröjningen inträffar eftersom vi behöver verifiera konto ändringen, precis som vi gör när du först konfigurerar kontot för utbetalning. Du betalar fortfarande för hela beloppet när ditt konto har verifierats. alla betalningar som förfaller för den aktuella betalnings cykeln läggs till nästa.  

## <a name="tax-profile"></a>Skatte profil

Granska din aktuella moms profil status och bekräfta att rätt **typ av entitetstyp** och **information om skatte certifikat** visas. Välj **Redigera** för att uppdatera eller fylla i nödvändiga formulär.

För att kunna upprätta din skatte status måste du ange land eller region för bosättning och medborgarskap och fylla i lämpliga skatte formulär som är kopplade till ditt land eller din region.

Oavsett ditt land eller din region eller ditt medborgarskap måste du fylla i USA skatte formulär för att sälja erbjudanden via Microsoft. Partner som uppfyller vissa USA placering krav måste fylla i ett IRS W-9-formulär. Andra partner utanför USA måste fylla i ett IRS-W-8-formulär. Du kan fylla i dessa formulär online när du har slutfört din skatte profil.

En USA individuellt Taxpayer Identification Number (eller ITIN) krävs inte för att få betalningar från Microsoft eller för att göra anspråk på bidrag för skatte fördrag.

Du kan slutföra och skicka in dina skatte formulär elektroniskt i Partner Center. i de flesta fall behöver du inte skriva ut och skicka e-post till några formulär.

Olika länder och regioner har olika skatte krav. Den exakta mängd som du måste betala i skatter beror på de länder och regioner där du säljer dina erbjudanden. Microsoft remitterar försäljning och använder skatt för din räkning i vissa länder och regioner. Dessa länder och regioner identifieras i den process där ditt erbjudande visas. I andra länder och regioner, beroende på var du är registrerad, kan du behöva betala försäljnings-och användnings skatt för din försäljning direkt till den lokala beskattnings-utfärdaren. Dessutom kan de försäljnings vinster du får vara skattepliktiga. Vi rekommenderar starkt att du kontaktar den behöriga myndigheten för ditt land eller din region som kan hjälpa dig att fastställa rätt skatte information för dina Microsoft Sales-transaktioner.

Mer information om en skatte profil finns i [skapa och hantera incitaments utbetalningar och skatte profiler i Partner Center](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) och [Konfigurera ditt utbetalnings konto och skatte formulär](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Käll frekvens

Den information som du skickar i dina skatte formulär avgör vilken käll avgift som behövs. Käll priset gäller endast för försäljning som du gör i USA. försäljning som görs till platser utanför USA omfattas inte av indrag. Käll priserna varierar, men för de flesta utvecklare som registrerar utanför USA är standard priset 30%. Du kan välja att minska den här hastigheten om ditt land eller din region har samtyckt till ett inkomst skatte avtal med USA.

### <a name="tax-treaty-benefits"></a>Förmåner för skatte fördrag

Om du befinner dig utanför USA kanske du kan dra nytta av fördelarna med moms avtal. De här fördelarna varierar från land/region till land/region, och kan göra det möjligt för dig att minska den mängd som Microsoft drar nytta av. Du kan göra anspråk på skatte förmåner genom att fylla i del II i formuläret W-8BEN. Vi rekommenderar att du kommunicerar med lämpliga resurser i ditt land eller din region för att avgöra om dessa förmåner gäller dig.

[Läs mer om skatte information för Windows app/Game-utvecklare och Azure Marketplace-utgivare](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Status för utbetalning

Som standard skickar Microsoft betalningar per månad. Du kan dock välja att spärra dina inbetalningar, vilket förhindrar att betalningar skickas till ditt konto. Om du väljer att spärra dina inbetalningar kommer vi att fortsätta att registrera alla intäkter som du får och ange informationen i din **utbetalnings Sammanfattning**. Vi kommer dock inte att skicka några betalningar till ditt konto förrän du tar bort spärren.

**För att spärra dina betalningar**:

1. Gå till **konto inställningar**. 
1. I vänster-nav expanderar du **betalning och skatt** och väljer **utbetalnings-och skatte profiler**.
1. Välj det program som du vill lagra betalningar för och markera sedan kryss rutan **Behåll min betalning** .

Du kan ändra din utbetalnings status när som helst, men tänk på att ditt beslut påverkar nästa månads utbetalning. Om du till exempel vill ha en utbetalning av april, se till att ställa in din utbetalnings status **till före slutet** av mars.

När du har angett statusen för din utbetalnings spärr till **på**, kommer alla utbetalningar att hållas kvar tills du växlar skjutreglaget tillbaka till **av**. När du gör det kommer du att inkluderas under nästa månads utbetalnings cykel (förutsatt att alla tillämpliga betalnings trösklar har uppfyllts). Om du till exempel har haft dina utbetalningar som är spärrade, men vill ha en utbetalning som genererats i juni, **ser du till** att växla över-statusen för utbetalningen är intill innan slutet av maj.

> [!NOTE]
> Ditt val av **utbetalnings status** gäller för **alla** intäkts källor som betalas via Microsoft Partner Center, inklusive Azure Marketplace, Microsoft AppSource, Microsoft Store, annonsering och så vidare.). Du kan inte välja olika spärr status för varje intäkts källa.

## <a name="devices"></a>Enheter

Inställningarna för enhets hantering gäller endast för UWP-publicering (Universal Windows Platform). [Läs mer](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Skapa en fakturerings profil

Om du publicerar en [Dynamics 365 för kund engagemang & Power Apps](./partner-center-portal/create-new-customer-engagement-offer.md) eller [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) -erbjudandet måste du slutföra din *fakturerings profil*.

Fakturerings adressen är förifylld från din juridiska organisation och du kan uppdatera den här adressen senare. Fälten skatte-och moms-ID krävs för vissa länder och valfritt för andra. Det går inte att redigera lands-/region namn och företags namn.

1. Gå till **konto inställningar**.
1. Expandera **organisations profil** i det vänstra navigerings fältet och välj **fakturerings profil**.


## <a name="multi-user-account-management"></a>Hantering av flera användar konton

Partner Center använder [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) för åtkomst och hantering av flera användares konton. Organisationens Azure AD associeras automatiskt med ditt partner Center-konto som en del av registrerings processen.

## <a name="next-steps"></a>Nästa steg

- [Lägga till och hantera användare](add-manage-users.md)
