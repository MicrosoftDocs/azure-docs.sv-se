---
title: Hantera ett kommersiellt marknads plats konto i Microsoft Partner Center
description: Lär dig hur du hanterar ett kommersiellt marknads plats konto i Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: cc853285e193dd3ac4e348525b8bf0db9f8cb023
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533952"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Hantera ditt kommersiella Marketplace-konto i Partner Center

När du har [skapat ett partner Center-konto](./create-account.md)kan du använda [instrument panelen för extern Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) för att hantera ditt konto och erbjudanden.

I den här artikeln får du lära dig hur du hanterar ditt partner Center-konto, inklusive hur du:

- [Åtkomst till dina konto inställningar för partner Center](#access-your-account-settings)
- [Hitta ditt utgivar-ID, Symantec-ID, säljar-ID, användar-ID, MPN-ID och Azure AD-klienter](#account-details)
- [Uppdatera kontakt information](#contact-info)
- [Konfigurera spårnings-GUID för övervakning av kund användning](#tracking-guids)
- [Hantera användare](#manage-users)
- [Hantera grupper](#manage-groups)
- [Hantera Azure AD-program](#manage-azure-ad-applications)
- [Definiera användarroller och behörigheter](#define-user-roles-and-permissions)
- [Hantera Azure AD-klienter (arbets konton)](#manage-tenants)
- [Hantera Partner Center-avtal](#agreements)

## <a name="access-your-account-settings"></a>Komma åt dina konto inställningar

Om du inte redan har gjort det ska du (eller din organisations administratör) komma åt [konto inställningarna](https://partner.microsoft.com/dashboard/account/management) för ditt partner Center-konto för att:

- Kontrol lera företagets konto verifierings status.
- Bekräfta ditt Symantec-ID, ditt säljar-ID, Microsoft Partner Network (MPN) ID, utgivar-ID och kontakt information, inklusive företagets god kännare och säljar kontakt.
- Skapa användar konton för alla som ska använda ditt företags konto i Partner Center.

### <a name="open-account-settings"></a>Öppna konto inställningar

Välj **Inställningar** (kugg hjuls ikon) i det övre högra hörnet och sedan på **konto inställningar** på [instrument panelen för extern Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) i Partner Center.

![Menyn konto inställningar i Partner Center](./media/dashboard-developer-settings.png)

I **konto inställningar** kan du Visa dina:

- **Konto information**: konto typ och konto status
- **Utgivar**-ID: säljar-ID, användar-ID, utgivarens ID, Azure AD-klienter och så vidare
- **Kontakt information**: utgivarens visnings namn, säljarens kontakt namn, e-post, telefon och adress
- **Spåra GUID**: alla spårnings-GUID som är kopplade till ditt konto

### <a name="account-details"></a>Konto information

I avsnittet konto information kan du se grundläggande information, t. ex. **konto typen** (företag eller person) och kontots **verifierings status** . Under ditt konto verifierings process visar de här inställningarna varje steg som krävs, inklusive e-postverifiering, personal verifiering och verksamhets verifiering. Du kan också uppdatera e-postmeddelandet här och skicka om verifieringen om det behövs.

### <a name="publisher-ids"></a>Utgivar-ID

I avsnittet Publisher-ID kan du se ditt **Symantec-ID**, **ditt säljar-** ID, **användar-ID**, **MPN-ID** och **Azure AD-klienter**. Dessa värden tilldelas av Microsoft för att unikt identifiera ditt Developer-konto och kan inte redige ras.

### <a name="contact-info"></a>Kontaktuppgifter

I avsnittet kontakt information ser du **visnings namnet för utgivaren**, **säljarens kontakt information** (kontakt namn, e-postadress, telefonnummer och adress för företags säljaren) och **företagets god kännare** (namn, e-postadress och telefonnummer till den person som har behörighet att godkänna beslut för företaget).

#### <a name="payout-account"></a>Konto utbetalning

Ett utbetalnings konto är Bank kontot som fortsätter skickas från din försäljning. Det här bank kontot måste finnas i samma land/region där du registrerade ditt partner Center-konto.

Så här konfigurerar du ditt utbetalnings konto:

1. Gå till [sidan för extern Marketplace-översikt](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partner Center.
2. I avsnittet profil, bredvid **utbetalnings profil** väljer du **Uppdatera**.
3. **Välj en betalnings metod**: bank konto eller PayPal.
4. **Lägg till betalnings information**: Detta kan vara att välja en kontotyp (kontroll eller besparingar), ange konto innehavarens namn, konto nummer och routing number, fakturerings adress, telefonnummer eller PayPal-e-postadress. * Mer information om hur du använder PayPal som din konto Betalnings Metod och hur du tar reda på om den stöds i din marknads region finns i [PayPal-information](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Att ändra ditt utbetalnings konto kan försena dina betalningar med upp till en betalnings cykel. Den här fördröjningen inträffar eftersom vi behöver verifiera konto ändringen, precis som vi gör när du först konfigurerar kontot för utbetalning. Du betalar fortfarande för hela beloppet när ditt konto har verifierats. alla betalningar som förfaller för den aktuella betalnings cykeln läggs till nästa.  

#### <a name="tax-profile"></a>Skatte profil

Granska din aktuella moms profil status och bekräfta att rätt **typ av entitetstyp** och **information om skatte certifikat** visas. Välj **Redigera** för att uppdatera eller fylla i nödvändiga formulär.

För att kunna upprätta din skatte status måste du ange land/region för bosättning och medborgarskap och fylla i lämpliga skatte formulär som är kopplade till ditt land/din region.

Oavsett land/region för bosättning eller medborgarskap måste du fylla i USA skatte former för att sälja erbjudanden via Microsoft. Partner som uppfyller vissa USA placering krav måste fylla i ett IRS W-9-formulär. Andra partner utanför USA måste fylla i ett IRS-W-8-formulär. Du kan fylla i dessa formulär online när du har slutfört din skatte profil.

En USA individuellt Taxpayer Identification Number (eller ITIN) krävs inte för att få betalningar från Microsoft eller för att göra anspråk på bidrag för skatte fördrag.

Du kan slutföra och skicka in dina skatte formulär elektroniskt i Partner Center. i de flesta fall behöver du inte skriva ut och skicka e-post till några formulär.

Olika länder och regioner har olika skatte krav. Den exakta mängd som du måste betala i skatter beror på de länder och regioner där du säljer dina erbjudanden. Microsoft remitterar försäljning och använder skatt för din räkning i vissa länder/regioner. Dessa länder/regioner identifieras i den process där ditt erbjudande visas. I andra länder/regioner, beroende på var du är registrerad, kan du behöva betala försäljnings-och användnings skatt för din försäljning direkt till den lokala beskattnings-utfärdaren. Dessutom kan de försäljnings vinster du får vara skattepliktiga. Vi rekommenderar starkt att du kontaktar den behöriga myndigheten för ditt land eller din region som kan hjälpa dig att fastställa rätt skatte information för dina Microsoft Sales-transaktioner.

##### <a name="withholding-rates"></a>Käll frekvens

Den information som du skickar i dina skatte formulär avgör vilken käll avgift som behövs. Käll priset gäller endast för försäljning som du gör i USA. försäljning som görs till platser utanför USA omfattas inte av indrag. Käll priserna varierar, men för de flesta utvecklare som registrerar utanför USA är standard priset 30%. Du kan välja att minska den här hastigheten om ditt land/din region har samtyckt till ett inkomst skatte avtal med USA.

##### <a name="tax-treaty-benefits"></a>Förmåner för skatte fördrag

Om du befinner dig utanför USA kanske du kan dra nytta av fördelarna med moms avtal. De här fördelarna varierar från land/region till land/region, och kan göra att du kan minska den mängd av skatter som Microsoft drar. Du kan göra anspråk på skatte förmåner genom att fylla i del II i formuläret W-8BEN. Vi rekommenderar att du kommunicerar med lämpliga resurser i ditt land eller din region för att avgöra om dessa förmåner gäller dig.

[Läs mer om skatte information för Windows app/Game-utvecklare och Azure Marketplace-utgivare](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status för utbetalning

Som standard skickar Microsoft betalningar per månad. Du kan dock välja att spärra dina inbetalningar, vilket förhindrar att betalningar skickas till ditt konto. Om du väljer att spärra dina inbetalningar kommer vi att fortsätta att registrera alla intäkter som du får och ange informationen i din **utbetalnings Sammanfattning**. Vi kommer dock inte att skicka några betalningar till ditt konto förrän du tar bort spärren.

Om du vill spärra dina betalningar går du till **konto inställningar**. Under **finansiell information** går du till avsnittet Retail **Hold status** och växlar skjutreglaget till **på**. Du kan ändra din utbetalnings status när som helst, men tänk på att ditt beslut påverkar nästa månads utbetalning. Om du till exempel vill ha en utbetalning av april, se till att ställa in din utbetalnings status **till före slutet** av mars.

När du har angett statusen för din utbetalnings spärr till **på**, kommer alla utbetalningar att hållas kvar tills du växlar skjutreglaget tillbaka till **av**. När du gör det kommer du att inkluderas under nästa månads utbetalnings cykel (förutsatt att alla tillämpliga betalnings trösklar har uppfyllts). Om du till exempel har haft dina utbetalningar som är spärrade, men vill ha en utbetalning som genererats i juni, **ser du till** att växla över-statusen för utbetalningen är intill innan slutet av maj.

> [!NOTE]
> Ditt val av **utbetalnings status** gäller för **alla** intäkts källor som betalas via Microsoft Partner Center, inklusive Azure Marketplace, AppSource, Microsoft Store, annonsering osv.). Du kan inte välja olika spärr status för varje intäkts källa.

### <a name="devices"></a>Enheter

Inställningarna för enhets hantering gäller endast för UWP-publicering. [Läs mer](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Spåra GUID

Globalt unika identifierare (GUID) är unika referens nummer (med 32 hexadecimala siffror) som kan användas för att spåra din Azure-användning. 

Om du vill skapa GUID för spårning bör du använda en GUID-Generator. Azure Storages teamet har skapat ett [GUID Generator-formulär](https://aka.ms/StoragePartners) som kommer att skicka ett GUID till rätt format och kan återanvändas över olika spårnings system.

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributions kanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributions kanaler om du inte vill att rapportering ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera två olika GUID:

- Produkt A på Azure Marketplace
- Produkt A på GitHub

Rapportering görs av partner värdet (Microsoft partner-ID) och GUID. Du kan också spåra GUID på en mer detaljerad nivå som justeras efter varje plan i erbjudandet.

Mer information finns i [vanliga frågor och svar om att spåra Azures kund användning med GUID](../azure-partner-customer-usage-attribution.md#faq).

## <a name="create-a-billing-profile"></a>Skapa en fakturerings profil

Om du publicerar en [Dynamics 365 för kund engagemang & Power Apps](./create-new-customer-engagement-offer.md) eller [Dynamics 365 for Operations](./create-new-operations-offer.md) -erbjudandet måste du slutföra din **fakturerings profil**.

Fakturerings adressen är förifylld från din juridiska organisation och du kan uppdatera den här adressen senare. Fälten skatte-och moms-ID är valfria.  Det går inte att redigera lands-/region namn och företags namn.

## <a name="multi-user-account-management"></a>Hantering av flera användar konton

Partner Center använder [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) för åtkomst och hantering av flera användares konton. Organisationens Azure AD associeras automatiskt med ditt partner Center-konto som en del av registrerings processen.

## <a name="manage-users"></a>Hantera användare

I avsnittet **användare** i Partner Center (under **konto inställningar**) kan du använda Azure AD för att hantera de användare, grupper och Azure AD-program som har åtkomst till ditt partner Center-konto. Ditt konto måste ha behörigheter på [**Administratörs nivå**](#define-user-roles-and-permissions) för det [arbets konto (Azure AD-klient)](./company-work-accounts.md) som du vill lägga till eller redigera användare i. Om du vill hantera användare inom ett annat arbets konto/klient organisation måste du logga ut och sedan logga in igen som en användare med **chefs** behörigheter för det arbets kontot/klient organisationen.

När du har loggat in med ditt arbets konto (Azure AD-klient) kan du:

- [Lägg till eller ta bort användare](#add-existing-users)
- [Ändra ett användar lösen ord](#change-a-user-password)
- [Lägg till eller ta bort grupper](#manage-groups)
- [Lägga till eller ta bort Azure AD-program](#add-new-azure-ad-applications)
- [Hantera nycklar för ett Azure AD-program](#manage-keys-for-an-azure-ad-application)
- [Definiera användarroller och behörigheter](#define-user-roles-and-permissions)

Tänk på att alla partner Center-användare (inklusive grupper och Azure AD-program) måste ha ett aktivt arbets konto i en [Azure AD-klient](#manage-tenants) som är kopplat till ditt partner Center-konto.

### <a name="add-existing-users"></a>Lägg till befintliga användare

Så här lägger du till användare till ditt partner Center-konto som redan finns i företagets [arbets konto (Azure AD-klient)](./company-work-accounts.md):

1. Gå till **användare** (under **konto inställningar**) och välj **Lägg till användare**.
2. Välj en eller flera användare i listan som visas. Du kan använda sökrutan för att söka efter vissa användare.
* Om du väljer fler än en användare som ska läggas till i ditt partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera användare med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
3. När du är färdig med att välja användare väljer du **Lägg till markerade**.
4. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda användarna.
5. Välj **Spara**.

### <a name="create-new-users"></a>Skapa nya användare

För att skapa helt nya användar konton måste du ha ett konto med [**globala administratörs**](../../active-directory/roles/permissions-reference.md) behörigheter.

1. Gå till **användare** (under **konto inställningar**), Välj **Lägg till användare** och välj sedan **skapa nya användare**.
1. Ange förnamn, efter namn och användar namn för varje ny användare. 
1. Om du vill att den nya användaren ska ha ett globalt administratörs konto i din organisations katalog markerar du kryss rutan **gör användaren till global administratör i din Azure AD med fullständig kontroll över alla katalog resurser**. Detta ger användaren fullständig åtkomst till alla administrativa funktioner i ditt företags Azure AD. De kan lägga till och hantera användare i din organisations arbets konto (Azure AD-klient), men inte i Partner Center, om du inte beviljar kontot rätt roll/behörigheter.
1. Om du har markerat kryss rutan för att **göra den här användaren till global administratör** måste du ange ett **e-postmeddelande för återställning av lösen ord** för att användaren ska kunna återställa sina lösen ord vid behov.
1. I avsnittet **grupp medlemskap** väljer du de grupper som du vill att den nya användaren ska tillhöra.
1. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för användaren.
1. Välj **Spara**.

När du skapar en ny användare i Partner Center skapas även ett konto för den användaren i arbets kontot (Azure AD-klienten) som du är inloggad på. Om du gör ändringar i en användares namn i Partner Center görs samma ändringar i din organisations arbets konto (Azure AD-klient).

### <a name="invite-new-users-by-email"></a>Bjud in nya användare via e-post

Om du vill bjuda in användare som för närvarande inte är en del av ditt företags arbets konto (Azure AD-klient) via e-post måste du ha ett konto med [**globala administratörs**](../../active-directory/roles/permissions-reference.md) behörigheter.

1. Gå till **användare** (under **konto inställningar**), Välj **Lägg till användare** och välj **Bjud in användare via e-post**.
2. Ange en eller flera e-postadresser (upp till 10), avgränsade med kommatecken eller semikolon.
3. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för användaren.
4. Välj **Spara**.

De användare som du har bjudit in får en e-postinbjudan om att ansluta till ditt partner Center-konto. Ett nytt gäst användar konto kommer att skapas i ditt arbets konto (Azure AD-klient). Varje användare måste acceptera sin inbjudan innan de kan komma åt ditt konto.

Om du behöver skicka en inbjudan igen går du till sidan **användare** , söker efter inbjudan i listan med användare, väljer e-postadress (eller texten som säger att *inbjudan väntar*). Välj sedan **Skicka inbjudan igen** på sidan längst ned på sidan.

> [!NOTE]
> Om din organisation använder [katalog integrering](/previous-versions/azure/azure-services/jj573653(v=azure.100)) för att synkronisera den lokala katalog tjänsten med din Azure AD kan du inte skapa nya användare, grupper eller Azure AD-program i Partner Center. Du (eller en annan administratör i din lokala katalog) måste skapa dem direkt i den lokala katalogen innan du kan se och lägga till dem i Partner Center.

### <a name="remove-a-user"></a>Ta bort en användare

Om du vill ta bort en användare från ditt arbets konto (Azure AD-klient) går du till **användare** (under **konto inställningar**) väljer du den användare som du vill ta bort med hjälp av kryss rutan längst till höger och väljer sedan **ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du bekräftar att du vill ta bort de markerade användarna.

### <a name="change-a-user-password"></a>Ändra ett användar lösen ord

Om en av dina användare behöver ändra sitt lösen ord kan de göra det själva om du har angett ett **e-postmeddelande för återställning av lösen ord** när du skapar användar kontot. Du kan också uppdatera en användares lösen ord genom att följa stegen nedan. Om du vill ändra en användares lösen ord på ditt företags arbets konto (Azure AD-klient) måste du vara inloggad på ett konto med [**globala administratörs**](../../active-directory/roles/permissions-reference.md) behörigheter. Detta kommer att ändra användarens lösen ord i din Azure AD-klient, tillsammans med lösen ordet som de använder för att komma åt Partner Center.

1. Från sidan **användare** (under **konto inställningar**) väljer du namnet på det användar konto som du vill redigera.
2. Välj knappen **Återställ lösen ord** längst ned på sidan.
3. En bekräftelse sida visas för att Visa inloggnings information för användaren, inklusive ett tillfälligt lösen ord. Se till att skriva ut eller kopiera den här informationen och ge den till användaren, eftersom du inte kan komma åt det tillfälliga lösen ordet när du har lämnat den här sidan.

## <a name="manage-groups"></a>Hantera grupper

Med grupper kan du styra flera användar roller och behörigheter tillsammans.

### <a name="add-an-existing-group"></a>Lägg till en befintlig grupp

Så här lägger du till en grupp som redan finns i din organisations arbets konto (Azure AD-klient) till ditt partner Center-konto:

1. Välj **Lägg till grupper** på sidan **användare** (under **konto inställningar**).
2. Välj en eller flera grupper i listan som visas. Du kan söka efter vissa grupper med hjälp av sökrutan.
Om du väljer fler än en grupp som ska läggas till i ditt partner Center-konto måste du tilldela dem samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera grupper med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
3. När du är färdig med att välja grupper väljer du **Lägg till markerade**.
4. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda grupperna. Alla medlemmar i gruppen kommer att ha åtkomst till ditt partner Center-konto med de behörigheter som du tillämpar på gruppen, oavsett vilka roller och behörigheter som är kopplade till sitt eget konto.
5. Välj **Spara**.

När du lägger till en befintlig grupp kommer alla användare som är medlemmar i gruppen att kunna komma åt ditt partner Center-konto, med de behörigheter som är kopplade till gruppens tilldelade roll.

### <a name="add-a-new-group"></a>Lägga till en ny grupp

Så här lägger du till en helt ny grupp till ditt partner Center-konto:

1. Välj **Lägg till grupper** på sidan **användare** (under **konto inställningar**).
2. Välj **ny grupp** på nästa sida.
3. Ange visnings namnet för den nya gruppen.
4. Ange roll (er) eller anpassade behörigheter för gruppen. Alla medlemmar i gruppen kommer att ha åtkomst till ditt partner Center-konto med de behörigheter som du använder här, oavsett vilka roller/behörigheter som är kopplade till sitt eget konto.
5. Välj användare för den nya gruppen i listan som visas. Du kan använda sökrutan för att söka efter vissa användare.
6. När du är färdig med att välja användare väljer du **Lägg till markerade** för att lägga till dem i den nya gruppen.
7. Välj **Spara**.

Den nya gruppen skapas i din organisations arbets konto (Azure AD-klient) också, inte bara i ditt partner Center-konto.

### <a name="remove-a-group"></a>Ta bort en grupp

Om du vill ta bort en grupp från ditt arbets konto (Azure AD-klient) går du till **användare** (under **konto inställningar**) markerar du den grupp som du vill ta bort med hjälp av kryss rutan längst till höger och väljer sedan **ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du bekräftar att du vill ta bort de valda grupperna.

## <a name="manage-azure-ad-applications"></a>Hantera Azure AD-program

Du kan tillåta att program eller tjänster som ingår i ditt företags Azure AD får åtkomst till ditt partner Center-konto.

### <a name="add-existing-azure-ad-applications"></a>Lägg till befintliga Azure AD-program

Så här lägger du till program som redan finns i företagets Azure Active Directory:

1. Från sidan **användare** (under **konto inställningar**) väljer du **Lägg till Azure AD-program**.
2. Välj ett eller flera Azure AD-program i listan som visas. Du kan använda sökrutan för att söka efter särskilda Azure AD-program. Om du väljer mer än ett Azure AD-program som ska läggas till i ditt partner Center-konto måste du tilldela samma roll eller uppsättning anpassade behörigheter. Om du vill lägga till flera Azure AD-program med olika roller/behörigheter upprepar du de här stegen för varje roll eller uppsättning anpassade behörigheter.
3. När du är färdig med att välja Azure AD-program väljer du **Lägg till markerade**.
4. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för de valda Azure AD-programmen.
5. Välj **Spara**.

### <a name="add-new-azure-ad-applications"></a>Lägg till nya Azure AD-program

Om du vill bevilja åtkomst till Partner Center till ett helt nytt Azure AD-programkonto kan du skapa ett i avsnittet **användare** . Då skapas ett nytt konto i ditt företags arbets konto (Azure AD-klient), inte bara i ditt partner Center-konto. Om du huvudsakligen använder Azure AD-programmet för partner Center-autentisering och inte behöver användare för att komma åt den direkt, kan du ange en giltig adress för **svars-URL:** en och **app-ID-URI: n**, förutsatt att dessa värden inte används av något annat Azure AD-program i katalogen.

1. Från sidan **användare** (under **konto inställningar**) väljer du **Lägg till Azure AD-program**.
2. På nästa sida väljer du **nytt Azure AD-program**.
3. Ange **svars-URL** för det nya Azure AD-programmet. Detta är URL: en där användarna kan logga in och använda ditt Azure AD-program (kallas ibland även för appens URL eller Sign-On-URL). **Svars-URL: en** får inte vara längre än 256 tecken och måste vara unik i din katalog.
4. Ange **app-ID-URI** för det nya Azure AD-programmet. Detta är en logisk identifierare för Azure AD-programmet som presenteras när en begäran om enkel inloggning skickas till Azure AD. **App-ID-URI: n** måste vara unik för varje Azure AD-program i din katalog. Detta ID får inte vara längre än 256 tecken. Mer information om app-ID-URI: n finns i [integrera program med Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. I avsnittet **roller** anger du roll (er) eller anpassade behörigheter för Azure AD-programmet.
6. Välj **Spara**.

När du har lagt till eller skapat ett Azure AD-program kan du gå tillbaka till avsnittet **användare** och välja program namnet för att granska inställningarna för programmet, inklusive klient-ID, klient-ID, svars-URL och app-ID-URI.

### <a name="remove-an-azure-ad-application"></a>Ta bort ett Azure AD-program

Om du vill ta bort ett program från ditt arbets konto (Azure AD-klient) går du till **användare** (under **konto inställningar**), väljer det program som du vill ta bort med hjälp av kryss rutan längst till höger och väljer sedan **ta bort** från de tillgängliga åtgärderna. Ett popup-fönster visas där du bekräftar att du vill ta bort de valda programmen.

### <a name="manage-keys-for-an-azure-ad-application"></a>Hantera nycklar för ett Azure AD-program

Om ditt Azure AD-program läser och skriver data i Microsoft Azure AD behöver den en nyckel. Du kan skapa nycklar för ett Azure AD-program genom att redigera dess information i Partner Center. Du kan också ta bort nycklar som inte längre behövs.

1. Från sidan **användare** (under **konto inställningar**) väljer du namnet på Azure AD-programmet. Du ser alla aktiva nycklar för Azure AD-programmet, inklusive det datum då nyckeln skapades och när den upphör att gälla 50.
2. Om du vill ta bort en nyckel som inte längre behövs väljer du **ta bort**.
3. Om du vill lägga till en ny nyckel väljer du **Lägg till ny nyckel**.
4. En skärm bild som visar **klient-ID** och **nyckel värden** visas. Se till att skriva ut eller kopiera den här informationen, eftersom du inte kan komma åt den igen när du lämnar den här sidan.
5. Om du vill skapa fler nycklar väljer du **Lägg till en annan nyckel**.

## <a name="define-user-roles-and-permissions"></a>Definiera användarroller och behörigheter

Ditt företags användare kan tilldelas följande roller och behörigheter för programmet för affärs marknads platser i Partner Center:

- **Ägare**
  - Detta är den person som först skapade kontot och har fullständig åtkomst till det, inklusive möjligheten att skapa och redigera alla konto användare och ändra alla finansiella och konto inställningar. Ett konto har vanligt vis bara en ägare.
- **Ansvarig**
  - Kan komma åt alla Microsoft-konto funktioner utom skatte-och utbetalnings inställningar
  - Kan hantera användare, roller och arbets konton (klient organisationer)
- **Utvecklare**
  - Kan hantera och publicera erbjudanden
  - Kan visa vissa Publisher-rapporter

> [!NOTE]
> För programmet för kommersiella marknads platser används inte rollerna global administratör, affärs deltagare, ekonomi bidrag och marknads deltagare. Att tilldela dessa roller till användare har ingen påverkan. Endast chefs-och utvecklare-rollerna beviljar behörigheter till användare.

Mer information om hur du hanterar roller och behörigheter i andra delar av Partner Center, till exempel Azure Active Directory (AD), Cloud Solution Provider (CSP), kontroll panels leverantör (CPV), gäst användare eller Microsoft Partner Network (MPN) finns i [Tilldela användar roller och behörigheter i Partner Center](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Hantera klienter

En Azure Active Directory-klient (AD), som även kallas ditt "arbets konto" i den här dokumentationen, är en representation av organisationen som är konfigurerad i Azure Portal och hjälper dig att hantera en särskild instans av Microsofts moln tjänster för dina interna och externa användare. Om din organisation prenumererar på en moln tjänst från Microsoft, till exempel Azure, Microsoft Intune eller Microsoft 365, upprättades en Azure AD-klient åt dig.

Du kan konfigurera flera klienter som ska användas med partner Center. Alla användare med rollen **chef** i Partner Center-kontot kan välja att lägga till och ta bort Azure AD-klienter från kontot.  

### <a name="add-an-existing-tenant"></a>Lägg till en befintlig klient

Koppla en annan Azure AD-klient till ditt partner Center-konto:

1. Från sidan **innehavare** (under **konto inställningar**) väljer du **associera en annan Azure AD-klient**.
2. Ange dina autentiseringsuppgifter för Azure AD för den klient som du vill koppla.
3. Granska organisations-och domän namnet för din Azure AD-klient. Slutför kopplingen genom att välja **Bekräfta**.

Om kopplingen lyckas är du redo att lägga till och hantera konto användare i avsnittet **användare** i Partner Center.

### <a name="create-a-new-tenant"></a>Skapa en ny klientorganisation

För att skapa en helt ny Azure AD-klient med ditt partner Center-konto:

1. Från sidan **innehavare** (under **konto inställningar**) väljer du **skapa en ny Azure AD-klient**.
2. Ange katalog informationen för din nya Azure AD:
    - **Domän namn**: det unika namn som vi använder för din Azure AD-domän, tillsammans med ". onmicrosoft.com". Om du till exempel har angett "example" är din Azure AD-domän "example.onmicrosoft.com".
    - **Kontakta e-** postadressen: en e-postadress där vi kan kontakta dig om ditt konto om det behövs.
    - **Användar konto information för global administratör**: förnamn, efter namn, användar namn och lösen ord som du vill använda för det nya globala administratörs kontot.
3. Välj **skapa** för att bekräfta den nya domänen och konto informationen.
4. Logga in med ditt nya Azure AD global-administratörs användar namn och lösen ord för att börja [lägga till och hantera användare](#manage-users).

Mer information om hur du skapar nya klienter i din Azure Portal, i stället för via partner Center-portalen, finns i artikeln [skapa en ny klient i Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Ta bort en klient

Om du vill ta bort en klient från ditt partner Center-konto letar du reda på namnet på sidan **innehavare** (i **konto inställningar**) och väljer sedan **ta bort**. Du uppmanas att bekräfta att du vill ta bort klient organisationen. När du gör det kommer inga användare i den klienten att kunna logga in på Partner Center-kontot, och alla behörigheter som du har konfigurerat för dessa användare kommer att tas bort.

När du tar bort en klient kommer alla användare som har lagts till i Partner Center-kontot från den klienten inte längre att kunna logga in på kontot.

> [!TIP]
> Du kan inte ta bort en klient om du är inloggad på Partner Center med ett konto i samma klient organisation. Om du vill ta bort en klient måste du logga in på Partner Center som en **hanterare** för en annan klient som är kopplad till kontot. Om det bara finns en klient som är associerad med kontot, kan klienten bara tas bort när du har loggat in med Microsoft-konto som öppnade kontot.

## <a name="agreements"></a>Avtal

I avsnittet **avtal** i Partner Center (under **konto inställningar**) kan du Visa en lista över de publicerings avtal som du har auktoriserat. Dessa avtal visas enligt namn och versions nummer, inklusive datumet då det godkändes och namnet på den användare som har godkänt avtalet.

De **åtgärder som krävs** kan visas överst på den här sidan om det finns avtals uppdateringar som kräver din uppmärksamhet. Om du vill godkänna ett uppdaterat avtal läser du först den länkade avtals versionen och väljer sedan **Godkänn avtal**.
