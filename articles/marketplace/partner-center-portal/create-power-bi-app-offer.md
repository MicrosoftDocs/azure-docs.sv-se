---
title: Skapa ett Power BI app-erbjudande i Microsoft AppSource
description: Lär dig hur du skapar och publicerar ett Power BI app-erbjudande i Microsoft AppSource.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: d5eb253fb24f463106866f8b0fe17f634e805cbb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107485"
---
# <a name="create-a-power-bi-app-offer"></a>Skapa erbjudande om Power BI-app

I den här artikeln beskrivs hur du skapar och publicerar ett Power BI app-erbjudande i [Microsoft AppSource](https://appsource.microsoft.com/).

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](../create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Power BI tjänst program**.

   ![Visar menyn till vänster-navigering.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> När ett erbjudande har publicerats visas bara ändringar i Partner Center i onlinebutiker när du har publicerat om erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

> [!IMPORTANT]
> Om **Power BI tjänst program** inte visas eller aktive ras har ditt konto inte behörighet att skapa den här erbjudande typen. Kontrol lera att du har uppfyllt alla [krav](create-power-bi-app-overview.md) för den här erbjudande typen, inklusive registrering av ett Developer-konto.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudandet-1** här, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Det går inte att ändra namnet på erbjudandet när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-overview"></a>Erbjudande översikt

På den här sidan visas en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

Den innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Exempel:

- Om erbjudandet är ett utkast – ta bort utkast erbjudande
- Om erbjudandet är Live- [sluta att sälja erbjudandet](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version- [Go-Live](../review-publish-offer.md#previewing-and-approving-your-offer)
- Om du inte har slutfört Publisher-utloggning – [Avbryt publicering.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="customer-leads"></a>Kund ledare

När du publicerar ditt erbjudande till Marketplace med partner Center måste du ansluta det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Om ditt CRM-system inte finns med i listan använder du [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) eller och [https-slutpunkten](commercial-marketplace-lead-management-instructions-https.md) för att lagra kundens lead-data. Exportera sedan data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

Här följer några ytterligare resurser för ledar hantering:

- [Kundleads från ditt erbjudande på den kommersiella marknadsplatsen](commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor om ledar hantering](../lead-management-faq.md#common-questions-about-lead-management)
- [Felsöka fel vid lead-konfiguration](../lead-management-faq.md#publishing-config-errors)
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (se till att blockering av popup-fönster är inaktiverat).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Industri

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Juridisk information

#### <a name="terms-and-conditions"></a>Villkor

Om du vill ange egna anpassade villkor anger du upp till 10 000 tecken i rutan **allmänna** villkor. Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, erbjud listan.

## <a name="offer-listing"></a>Erbjudande lista

Här definierar du de erbjudande uppgifter som visas i Marketplace. Detta inkluderar erbjudandets namn, beskrivning, bilder och så vidare.

### <a name="language"></a>Språk

Välj det språk som ditt erbjudande ska visas i. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Definiera Marketplace-information (t. ex. namn, beskrivning och avbildningar för erbjudandet) för varje språk/marknad. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande information behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det går också att tillhandahålla en användbar länk för att erbjuda innehåll på ett annat språk än det som används i erbjudande listan.

Här är ett exempel på hur information om erbjudandet visas i Microsoft AppSource (de priser som visas i listan är endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Produkter
3. Kategorier
4. Branscher
5. Support adress (länk)
6. Villkor för användning
7. Sekretesspolicy
8. Erbjudandets namn
9. Sammanfattning
10. Beskrivning
11. Skärm bilder/videor

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler).
- Får inte vara mer än 50 tecken långt.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Sök efter nyckelord

Ange upp till tre valfria Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du också använda dessa nyckelord i beskrivningen.

### <a name="helpprivacy-web-addresses"></a>Hjälp/sekretess-webbadresser

Tillhandahåller länkar som hjälper kunder att bättre förstå ditt erbjudande.

#### <a name="help-link"></a>Hjälp länk

Ange webb adressen där kunderna kan lära sig mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange webb adressen till din organisations sekretess policy. Du är ansvarig för att se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du ansvarar också för att publicera en giltig sekretess policy på din webbplats.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas av CSP-partner (Cloud Solution Provider).

- Support kontakt (krävs): för allmänna supportfrågor.
- Teknisk kontakt (krävs): för tekniska frågor och certifierings problem.
- Kontakta CSP-programmet (valfritt): för åter försäljar frågor som är relaterade till CSP-programmet.

I avsnittet **support kontakt** anger du webb adressen till **Support webbplatsen** där partner kan hitta support för ditt erbjudande.

### <a name="supporting-documents"></a>Stöd dokument

Ange minst ett och upp till tre relaterade marknadsförings dokument i PDF-format. Till exempel fakta blad, broschyrer, check listor eller presentationer.

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som `https://upload.xboxlive.com` används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** logo typ. Du kan också ersätta detta med en annan avbildning senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till minst en och upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Ange videons namn, webb adressen och miniatyr bilden av videon med 1280 x 720 pixlar i storlek.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

Om du vill veta mer om att skapa erbjudande listor, se [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Teknisk konfiguration

Flytta upp din app i Power BI tjänst till produktion och ange den Power BI App Installer-länken som gör det möjligt för kunderna att installera din app. Mer information finns i [publicera appar med instrument paneler och rapporter i Power BI](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Kompletterande innehåll

Ange ytterligare information om ditt erbjudande för att hjälpa oss att validera det. Den här informationen visas inte för kunder eller publiceras på Marketplace.

### <a name="validation-assets"></a>Validerings till gångar

Du kan också lägga till instruktioner (upp till 3 000 tecken) för att hjälpa Microsoft validerings teamet att konfigurera, ansluta och testa din app. Inkludera vanliga konfigurations inställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut data. Den här informationen visas bara för validerings teamet och används endast i validerings syfte.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka ditt erbjudande till granskning och publicering.

I det övre högra hörnet av portalen väljer du **Granska och publicera**.

På sidan Granska kan du:

- Se slut för ande status för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har marker ATS som slutförda.
  - **Inte startad** -avsnittet har inte startats och måste slutföras.
  - **Ofullständig** – avsnittet innehåller fel som måste åtgärdas eller kräver att du anger mer information. Se avsnittet tidigare i det här dokumentet för vägledning.
  - **Slutfört** -avsnittet innehåller alla data som krävs och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- Ange test instruktioner till certifierings teamet för att se till att appen testas korrekt. Ange också eventuella kompletterande kommentarer som hjälper dig att förstå ditt erbjudande.

Om du vill skicka publicerings erbjudandet väljer du **publicera**.

Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Om du vill publicera ditt erbjudande till allmänheten går du till Partner Center och väljer **Go-Live**.
