---
title: Skapa ett Erbjudande för Dynamics 365 Business Central i Microsoft AppSource
description: Så här skapar du ett Erbjudande för Dynamics 365 for Business Central i Microsoft AppSource. Lista eller sälja ditt erbjudande i AppSource eller via programmet Molnlösningsleverantör (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501441"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Skapa ett erbjudande för Dynamics 365 for Business Central

I den här artikeln beskrivs hur du skapar ett nytt erbjudande för Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) är en ERP-tjänst (Enterprise Resource Planning) som hanterar en mängd olika affärsprocesser, inklusive ekonomi, drift, leveranskedja, CRM, projekthantering och elektronisk handel. Premium-paket stöder även klassisk distributionsmodell och tillverkning. Alla erbjudanden för Dynamics 365 Business Central måste gå igenom vår certifieringsprocess.

Innan du börjar [skapar du ett Commercial Marketplace-konto i Partnercenter](../create-account.md) om du inte har gjort det ännu. Kontrollera att ditt konto har registrerats i programmet för den kommersiella marknadsplatsen.

>[!NOTE]
> När ett erbjudande har publicerats uppdateras ändringar av erbjudandet endast i Partnercenter och onlinebutiken när du har gjort en ny publicering av erbjudandet.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [Partner Center.](https://partner.microsoft.com/dashboard/home)
2. I den vänstra navigeringsmenyn väljer du **Översikt över Kommersiell**  >  **marknadsplats.**
3. På sidan Översikt väljer du **+ Nytt erbjudande Dynamics**  >  **365 Business Central.**

    ![Visar den vänstra navigeringsmenyn.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID.** Det här värdet är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Erbjudande-ID i kombination med utgivar-ID måste vara under 40 tecken långt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och understreck, men inga blanksteg. Om ditt utgivar-ID till exempel `testpublisherid` är och du anger **test-offer-1** blir erbjudandets webbadress `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Det går inte att ändra det här ID:t när du har valt **Skapa**.

Ange ett **erbjudandealias**. Det här är det namn som används för erbjudandet i Partnercenter.

- Det här namnet används inte på Marketplace och skiljer sig från erbjudandets namn och andra värden som visas för kunder.
- Det går inte att ändra det här namnet när du har valt **Skapa**.

Välj **Skapa** för att generera erbjudandet och fortsätta.

## <a name="offer-setup"></a>Konfiguration av erbjudande

### <a name="alias"></a>Alias

Ange ett beskrivande namn som vi använder för att referera till det här erbjudandet enbart i Partnercenter. Det här namnet (i förväg ifylld med det du angav när du skapade erbjudandet) används inte på Marketplace och skiljer sig från det erbjudandenamn som visas för kunderna. Om du vill uppdatera erbjudandets namn senare går du till sidan [Erbjudandelista.](#offer-listing)

### <a name="setup-details"></a>Installationsinformation

Välj den **pakettyp** som gäller för ditt erbjudande:

- En **tilläggsapp utökar** upplevelsen och de befintliga funktionerna i Dynamics 365 Business Central. Mer information finns [i Tilläggsprogram.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)
- En **Connect-app** kan användas i scenariot där det måste upprättas en punkt-till-punkt-anslutning mellan Dynamics 365 Business Central och en lösning eller tjänst från tredje part. Mer information finns i [Ansluta appar.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)

För Hur vill du att potentiella kunder ska interagera med det **här erbjudandet?** väljer du det alternativ som du vill använda för det här erbjudandet.

- **Hämta nu (kostnadsfritt)** – Visa ett erbjudande för kunder utan kostnad.
- **Kostnadsfri utvärderingsversion (lista)** – Ange ditt erbjudande för kunder med en länk till en kostnadsfri utvärderingsversion. Erbjudande som listar kostnadsfria utvärderingsversioner skapas, hanteras och konfigureras av din tjänst och har inte prenumerationer som hanteras av Microsoft.

    > [!NOTE]
    > De token som programmet får via utvärderingslänken kan bara användas för att hämta användarinformation via Azure Active Directory (Azure AD) för att automatisera kontoskapandet i din app. Microsoft-konton stöds inte för autentisering med denna token.

- **Kontakta mig** – Samla in kundkontaktinformation genom att ansluta ditt CRM-system (Customer Relationship Management). Kunden uppmanas att ge tillstånd att dela sin information. Dessa kunduppgifter, tillsammans med erbjudandets namn, ID och marketplace-källa där de hittade ditt erbjudande, skickas till det CRM-system som du har konfigurerat. Mer information om hur du konfigurerar din CRM finns i [Kund leads](#customer-leads).

### <a name="test-drive"></a>Provkörning

En provkörning är ett bra sätt att demonstrera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och generering av högt kvalificerade leads. Om du vill veta mer börjar du [med Vad är testkörning.](../what-is-test-drive.md)

Om du vill aktivera en testenhet under en viss tidsperiod markerar du **kryssrutan Aktivera en provkörning.** Om du vill ta bort testenheten från ditt erbjudande avmarkerar du den här kryssrutan.

### <a name="customer-leads"></a>Kund leads

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i Översikt [över leadhantering.](./commercial-marketplace-get-customer-leads.md)

Välj **Spara utkast innan** du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, din appversion och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="categories"></a>Kategorier

Välj kategorier och underkategorier för att placera ditt erbjudande i lämpliga sökområden på Marketplace. Se till att beskriva hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två underkategorier för varje primär och/eller sekundär kategori. Om ingen underkategori gäller för ditt erbjudande väljer du **Ej tillämpligt.**

Se den fullständiga listan över kategorier och underkategorier i [Metodtips för erbjudandelistor.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Branscher

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>Appversion

Ange versionsnumret för ditt erbjudande. Kunderna ser den här versionen på erbjudandets informationssida.

### <a name="terms-and-conditions"></a>Villkor

Ange dina egna juridiska villkor här. Du kan också ange den adress där dina allmänna villkor finns. Kunder måste godkänna dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast innan** du fortsätter.

## <a name="offer-listing"></a>Erbjudandelista

På den här sidan kan du definiera erbjudandeinformation, till exempel erbjudandenamn, beskrivning, länkar och kontakter.

> [!NOTE]
> Ange erbjudandeinformation endast på ett språk. Det måste inte vara på engelska, så länge erbjudandebeskrivningen börjar med frasen "Det här programmet är endast tillgängligt på [icke-engelska språk]." Det går också bra att ange en *användbar länk-URL för* att erbjuda innehåll på ett annat språk än det som används i erbjudandelistans innehåll.

Här är ett exempel på hur erbjudandeinformation visas i Microsoft AppSource (angivna priser är endast exempel och är inte avsedda att återspegla faktiska kostnader):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Produkter
3. Kategorier
4. Supportadress (länk)
5. Villkor för användning
6. Sekretesspolicy
7. Erbjudandets namn
8. Sammanfattning
9. Beskrivning
10. Skärmbilder/videor

### <a name="marketplace-details"></a>Marketplace-information

Det **Namn** som du anger här visas för kunderna som rubrik för din erbjudandelista. Det här fältet fylls i i förväg med den text som du angav för **Erbjudandealias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara varumärken (och du kan inkludera varumärken eller upphovsrättssymboler). Namnet får inte vara längre än 50 tecken och får inte innehålla emojis.

Ange en kort beskrivning av ditt erbjudande, upp till 100 tecken, för **sammanfattningen av sökresultatet.** Den här beskrivningen kan användas i Sökresultat på Marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Om du vill kan du ange upp till tre **söknyckelord** för att hjälpa kunderna att hitta ditt erbjudande på Marketplace. För bästa resultat bör du även använda dessa nyckelord i beskrivningen.

Om du vill meddela kunderna vilka produkter **din app fungerar med** anger du upp till tre produktnamn.

### <a name="helpprivacy-urls"></a>Hjälp-/sekretess-URL:er

Ange **hjälplänken för din app** (URL) där kunderna kan lära sig mer om ditt erbjudande. Din hjälp-URL får inte vara samma som din support-URL.

Ange **länken (URL)** för sekretesspolicyn till din organisations sekretesspolicy. Du ansvarar för att säkerställa att din app följer sekretesslagar och -föreskrifter och att tillhandahålla en giltig sekretesspolicy.

### <a name="contact-information"></a>Kontaktinformation

Ange namn, e-postadress och telefonnummer för en **supportkontakt och** en **teknisk kontakt.** Den här informationen visas inte för kunder, men kommer att vara tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **Supportkontakt anger** du support-URL:en **där** CSP-partner kan hitta support för ditt erbjudande. Din support-URL får inte vara samma som din hjälp-URL.

### <a name="supporting-documents"></a>Verifikationer

Ange minst ett (och upp till tre) relaterade marknadsföringsdokument här, till exempel faktablad, presentationer, checklistor eller presentationer i PDF-format.

### <a name="marketplace-media"></a>Marketplace-media

Ange logotyper och bilder som ska användas när du visar ditt erbjudande för kunder. Alla bilder måste vara i PNG-format.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten `https://upload.xboxlive.com` som används av Partnercenter.

#### <a name="logos"></a>Logotyper

Ange en PNG-fil för **logotypen stor** storlek. Partnercenter använder den här inledande filen för att skapa andra storlekar som krävs. Du kan också ersätta den ändrade bilden med din egen bild senare.

Dessa logotyper används på olika platser i listan:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärmbilder som visar hur ditt erbjudande fungerar. Minst tre skärmbilder krävs och du kan lägga till upp till fem. Alla skärmbilder måste vara 1280 x 720 bildpunkter och i PNG-format.

#### <a name="videos"></a>Video

Om du vill kan du lägga till upp till fyra videor som visar ditt erbjudande. Videor måste finnas på en extern webbplats. Ange videons namn, adress och en miniatyrbild av videon (1 280 x 720 bildpunkter) för var och en.

Mer information om marketplace-listresurser finns [i Metodtips för marketplace-erbjudanden.](../gtm-offer-listing-best-practices.md)

Välj **Spara utkast innan** du fortsätter.

## <a name="availability"></a>Tillgänglighet

På den här sidan kan du definiera var och hur du gör ditt erbjudande tillgängligt.

### <a name="markets"></a>Marknader

Om du vill ange de marknader där ditt erbjudande ska vara tillgängligt väljer du **Redigera marknader** för att visa **popup-fönstret För val** av marknad.

Välj minst en marknad. Välj **Markera alla** för att göra ditt erbjudande tillgängligt på alla möjliga marknader eller välj bara de specifika marknader som du vill ha. När du är klar väljer du **Spara**.

Dina val här gäller endast för nya förvärv. Om någon redan har din app på en viss marknad och du senare tar bort den marknaden kan de personer som redan har erbjudandet på den marknaden fortsätta att använda den, men inga nya kunder på den marknaden kommer att kunna få ditt erbjudande.

> [!IMPORTANT]
> Det är ditt ansvar att uppfylla eventuella lokala juridiska krav, även om dessa krav inte anges här eller i Partnercenter. Även om du väljer alla marknader kan lokala lagar, begränsningar eller andra faktorer förhindra att vissa erbjudanden listas i vissa länder och regioner.

### <a name="preview-audience"></a>Målgrupp för förhandsversion

Innan du publicerar ditt erbjudande live på det bredare Marketplace-erbjudandet måste du först göra det tillgängligt för en begränsad **målgrupp för förhandsversionen.** Ange en **Dölj nyckel** (valfri sträng med endast gemener och/eller siffror) här. Medlemmar i din målgrupp för förhandsversionen kan använda den här döljnyckeln som en token för att visa en förhandsgranskning av ditt erbjudande på Marketplace.

När du sedan är redo att göra ditt erbjudande tillgängligt och ta bort begränsningen för förhandsversionen måste du ta bort **nyckeln Dölj** och publicera igen.

Välj **Spara utkast innan** du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Den här sidan definierar den tekniska information som används för att ansluta till ditt erbjudande. Med den här anslutningen kan vi etablera ditt erbjudande för slutanvändaren om de väljer att skaffa det.

Tillägg som skickas för ditt erbjudande måste uppfylla kraven som anges i checklistan [för teknisk validering.](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission)

### <a name="file-upload"></a>Filuppladdning

Om du tidigare **valde Lägg** till på , där du överför erbjudandets paketfil, tillsammans med paketfilerna för alla tillägg som det har beroenden för.

#### <a name="extension-package-file"></a>Fil för tilläggspaketet

Ladda upp tilläggspaketets fil (.app) för ditt erbjudande.

#### <a name="library-extension-package-file"></a>Paketfil för bibliotekstillägg

Krävs om ditt erbjudande måste installeras tillsammans med ett annat tillägg som inte publiceras på Marketplace. I så exempel laddar du upp dess .app-fil här.

>[!NOTE]
>Beroendepaketfilen används inte längre. Ladda upp en bibliotekstilläggspaketfil i stället.

Välj **Spara utkast innan** du fortsätter.

## <a name="supplemental-content"></a>Kompletterande innehåll

På den här sidan kan du ange ytterligare information som hjälper oss att verifiera ditt erbjudande. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="target-release"></a>Målversion

Ange vilken version av Microsoft Dynamics Business Central som din lösning har som mål: **Current**, **Next Major** eller Next **Minor**. Med den här informationen kan vi testa din lösning på rätt sätt.

### <a name="supported-editions"></a>Versioner som stöds

Om ditt erbjudande kräver Premium-versionen av Microsoft Dynamics 365 Business Central väljer du **endast Premium.** Annars väljer du både **Essentials** och **Premium**.

### <a name="key-usage-scenario"></a>Scenario för nyckelanvändning

Ladda upp en PDF-fil som visar en lista över viktiga användningsscenarier för ditt erbjudande. Alla scenarier som anges här kan verifieras av vårt valideringsteam innan vi godkänner ditt erbjudande för marketplace.

### <a name="test-accounts"></a>Testkonton

Om ett testkonto behövs för att vårt certifieringsteam ska kunna granska ditt erbjudande korrekt laddar du upp en .pdf-, .doc- eller .docx-fil med **din testkontoinformation.**

### <a name="app-tests-automation"></a>Apptestautomatisering

Om ditt erbjudande är en tilläggsapp måste du ladda upp en **automationsfil för apptester** (.app). Den här filen gäller inte för Connect-appar.

Välj **Spara utkast innan** du fortsätter.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till förhandsversion

När du har slutfört alla nödvändiga avsnitt i erbjudandet **väljer du Granska och** publicerar i det övre högra hörnet av portalen.

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
  - **Inte startad** – Avsnittet har inte använts och måste slutföras.
  - **Ofullständig** – avsnittet innehåller fel som måste åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera den.
  - **Slutför** – Avsnittet är klart, alla nödvändiga data har angetts och det finns inga fel. Alla delar av erbjudandet måste vara i ett fullständigt tillstånd innan du kan skicka erbjudandet.
- I avsnittet **Kommentarer för certifiering** ger du certifieringsteamet testinstruktioner för att säkerställa att din app testas korrekt, förutom eventuella kompletterande anteckningar som är användbara för att förstå din app.
- Skicka erbjudandet för publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande när en förhandsversion av erbjudandet är tillgänglig för granskning och godkännande. Gå tillbaka till Partnercenter och **välj Go-live** för att publicera ditt erbjudande till allmänheten.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
