---
title: Skapa en Dynamics 365 för kund engagemang & PowerApps-erbjudande på Microsofts kommersiella marknads plats
description: Så här skapar du en Dynamics 365 för kund engagemang & PowerApps-erbjudande i Microsoft AppSource. Visa eller Sälj ditt erbjudande i AppSource eller via program varan för Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 0c220daab0d1d9ae7d50d37d9303d6677bd52cc1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360313"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Skapa ett erbjudande för Dynamics 365 for Customer Engagement & PowerApps

Den här artikeln beskriver hur du skapar en ny Dynamics 365 för kund engagemang & PowerApps-erbjudande. Alla appar för Dynamics 365 för kund engagemang (PowerApps, Sales, service, Project service och Field Service) måste gå igenom vår certifierings process som kontrollerar din lösning för standard krav, kompatibilitet och korrekt praxis. Med utvärderings versionen kan användarna distribuera din lösning till en Live Dynamics 365-miljö.

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

>[!NOTE]
> När ett erbjudande har publicerats uppdateras bara ändringar i erbjudandet i Partner Center och onlinebutiken när du har skickat om erbjudandet för publicering.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Dynamics 365 för kund engagemang & PowerApps**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Erbjudande-ID: t kombinerat med utgivar-ID: t måste vara under 40 tecken långt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inga blank steg. Om ditt utgivar-ID till exempel är `testpublisherid` och du anger **test-erbjudande-1**, kommer webb adressen för erbjudandet att vara `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Detta ID kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra det här namnet när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="alias"></a>Alias

Ange ett beskrivande namn som vi ska använda för att referera till det här erbjudandet enbart i Partner Center. Det här namnet (förifyllt med vad du angav när du skapade erbjudandet) används inte i Marketplace och skiljer sig från namnet på erbjudandet som visas för kunderna. Om du vill uppdatera erbjudande namnet senare går du till sidan [erbjudande lista](#offer-listing) .

### <a name="setup-details"></a>Installations information

För **Hur vill du att potentiella kunder ska kunna interagera med det här registrerings erbjudandet?** väljer du det alternativ som du vill använda för det här erbjudandet.

- **Hämta nu (kostnads fritt)** – lista ditt erbjudande till kunder kostnads fritt.
- **Kostnads fri utvärderings version (lista)** – lista ditt erbjudande till kunder med en länk till en kostnads fri utvärderings version. Erbjudande om kostnads fria utvärderings versioner skapas, hanteras och konfigureras av din tjänst och har inga prenumerationer som hanteras av Microsoft.

    > [!NOTE]
    > De token som programmet tar emot via din utvärderings länk kan bara användas för att hämta användar information via Azure Active Directory (Azure AD) för att automatisera skapandet av konton i din app. Microsoft-konton stöds inte för autentisering med denna token.

- **Kontakta mig** – samla in kund kontakt information genom att ansluta ditt CRM-system (Customer Relations hip Management). Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namnet, ID: t och Marketplace-källan där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [kund leads](#customer-leads).

### <a name="test-drive"></a>Test enhet

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. Om du vill veta mer kan du börja med [Vad är Test Drive](../what-is-test-drive.md).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

### <a name="customer-leads"></a>Kund ledare

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande.

### <a name="categories"></a>Kategorier

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Branscher

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Tillämpliga Dynamics 365-produkter

Välj alla de Dynamics 365-produkter som det här erbjudandet gäller för.

### <a name="app-version"></a>Appversion

Ange versions numret för ditt erbjudande. Kunderna kommer att se den här versionen på erbjudandets detalj sida.<!-- If you are only updating the version number due to marketing/descriptive changes, check the **Marketing only change** box. This option allows the offer to bypass the certification and provisioning stages.-->

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor här. Du kan också ange adressen där du hittar dina villkor. Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details for each language/market here, such as offer name, description, and images. Select the language/market name to provide this information.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Lämna information om erbjudandet på ett språk. Det är inte nödvändigt att vara på engelska, så länge erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource (de priser som visas i listan är endast avsedda att avspegla faktiska kostnader):
<!-- update screen? -->
:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
1. Produkter
1. Kategorier
1. Support adress (länk)
1. Villkor för användning
1. Erbjudandets namn
1. Beskrivning
1. Skärm bilder/videor

### <a name="marketplace-details"></a>Information om Marketplace

Det **namn** som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

Ange en kort beskrivning av erbjudandet, upp till 100 tecken, för sammanfattning av **Sök Resultat**. Beskrivningen kan användas i Sök resultaten för Marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Du kan också ange upp till tre **Sök Nyckelord** för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du också använda dessa nyckelord i beskrivningen.

Ange upp till tre produkt namn om du vill att kunderna ska kunna se vilka **produkter appen fungerar med**.

### <a name="helpprivacy-urls"></a>Hjälp/sekretess-URL: er

Ange **Hjälp länken för din app** (URL) där kunderna kan lära sig mer om ditt erbjudande. Din hjälp-URL får inte vara samma som Support-URL: en.

Ange en **länk till sekretess policyn** (URL) till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="contact-information"></a>Kontaktinformation

Ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** anger du support- **URL: en** där CSP-partner kan hitta support för ditt erbjudande. Support-URL: en får inte vara samma som din hjälp-URL.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett (och upp till tre) relaterade marknadsförings dokument här, till exempel fakta blad, broschyrer, check listor eller presentationer i PDF-format.

### <a name="marketplace-media"></a>Marketplace-Media

Ange logo typer och avbildningar som ska användas när ditt erbjudande till kunder visas. Alla bilder måste vara i PNG-format.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="logos"></a>Typerna

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa andra nödvändiga storlekar. Du kan också ersätta detta med en annan avbildning senare.

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Minst en skärm bild krävs, och du kan lägga till upp till fem. Alla skärm dum par måste vara 1280 x 720 pixlar och i PNG-format.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fyra videor som demonstrerar ditt erbjudande. Video måste finnas på en extern plats. För var och en anger du videons namn, dess adress och en miniatyr bild av videon (1280 x 720 bild punkter).

Mer information om resurser för Marketplace finns i [metod tips för Marketplace-erbjudanden](../gtm-offer-listing-best-practices.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="availability"></a>Tillgänglighet

På den här sidan kan du definiera var och hur du vill göra ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknaden

Om du vill ange de marknader där ditt erbjudande ska vara tillgängligt väljer du **Redigera marknader** för att visa popup-fönstret för **marknads val** .

Välj minst en marknad. Välj **Välj alla** om du vill göra ditt erbjudande tillgängligt på alla möjliga marknader, eller välj bara de valda marknaderna. När du är klar väljer du **Spara**.

Dina val gäller endast för nya förvärv. om någon redan har din app på en viss marknad och du senare tar bort den marknaden, kan de personer som redan har erbjudandet på marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla alla lokala juridiska krav, även om dessa krav inte finns med i listan här eller i Partner Center. Även om du väljer alla marknader, lokala lagar, begränsningar eller andra faktorer kan förhindra att vissa erbjudanden visas i vissa länder och regioner.

### <a name="preview-audience"></a>Förhandsgranska mål grupp

Innan du publicerar erbjudandet Live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **förhands gransknings publik**. Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din förhands visnings publik kan använda denna Dölj-nyckel som en token för att visa en förhands granskning av ditt erbjudande i Marketplace.

När du är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för för hands versionen måste du ta bort **Dölj-tangenten** och publicera igen.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar de tekniska uppgifter som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi tillhandahålla ditt erbjudande för slutanvändaren om de väljer att förvärva det.

### <a name="offer-information"></a>Erbjudande information

Den **grundläggande licens modellen** avgör hur kunderna tilldelas ditt program i administrations centret för CRM. Välj **resurs** för instansbaserade licenser eller **användare** om licenser tilldelas en per klient.

Kryss rutan **kräver utgående S2S-och CRM-åtkomst för säker lagring** möjliggör konfigurationen av åtkomst till en säker databas (S2S) i CRM eller server-till-server. Den här funktionen kräver specialiserad uppmärksamhet från Dynamics 365-teamet under certifierings fasen. Microsoft kommer att kontakta dig för att slutföra ytterligare steg för att stödja den här funktionen.

Lämna **URL för program konfiguration** tomt; den används för framtida bruk.

### <a name="crm-package"></a>CRM-paket

Ange URL: en för ett Azure Blob Storage-konto som innehåller den överförda CRM Package. zip-filen för URL: en för **din paket plats**. Ta med en skrivskyddad SAS-nyckel i URL: en så att Microsoft kan hämta ditt paket för verifiering.

> [!IMPORTANT]
> För att undvika ett publicerings block ser du till att utgångs datumet i URL: en för blob-lagringen inte har gått ut. Du kan ändra datumet genom att komma åt din princip. Vi rekommenderar att **förfallo tiden infaller** minst en månad i framtiden.

Markera kryss rutan **det finns mer än ett CRM-paket i paket filen om det** är tillämpligt. I så fall, se till att inkludera alla paket i. zip-filen.

Detaljerad information om hur du skapar paketet och uppdaterar dess struktur finns i [steg 3: skapa ett AppSource-paket för din app](/powerapps/developer/common-data-service/create-package-app-appsource).

### <a name="crm-package-availability"></a>Tillgänglighet för CRM-paket

Välj **+ Lägg till region** för att ange de geografiska regioner där ditt CRM-paket ska vara tillgängligt för kunderna. Distribution till följande suveräna regioner kräver särskilda behörigheter och verifiering under certifierings processen: [Tyskland](../../germany/index.yml), [amerikanska myndigheter](../../azure-government/documentation-government-welcome.md)och tips.

Som standard används den **URL för program konfiguration** som du angav ovan för varje region. Om du vill kan du ange en separat program konfigurations-URL för en eller flera specifika regioner.

Välj **Spara utkast** innan du fortsätter.

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing. -->

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information som hjälper oss att validera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="key-usage-scenario"></a>Scenario för nyckel användning

Ladda upp en PDF-fil som visar dina erbjudandens nyckel användnings scenarier. Alla scenarier kan verifieras av vårt validerings team innan vi godkänner ditt erbjudande för Marketplace.

Välj **Spara utkast** innan du fortsätter.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - **Inte startat** -avsnittet har inte vidrör ATS och måste slutföras.
    - **Ofullständig** -avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - **Slutfört** avsnitt har slutförts, alla data som krävs har angetts och det finns inga fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- I avsnittet **Obs!-certifiering** anger du test instruktioner till certifierings teamet för att kontrol lera att appen är korrekt testad, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi kommer att e-posta dig när en för hands version av erbjudandet är tillgänglig för granskning och godkännande. Gå tillbaka till Partner Center och välj **Go-Live** för att publicera ditt erbjudande till allmänheten.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
