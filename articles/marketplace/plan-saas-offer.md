---
title: Planera ett SaaS-erbjudande för Microsofts kommersiella marknads plats
description: Planera för ett nytt SaaS-erbjudande (Software as a Service) för att visa eller sälja i Microsoft AppSource, Azure Marketplace eller via program varan för Cloud Solution Provider (CSP) med hjälp av programmet för kommersiella marknads platser i Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 2f12f33bf6108784b2eec252fb12d03eebd2a45c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604947"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Planera ett SaaS-erbjudande för den kommersiella marknaden

I den här artikeln beskrivs de olika alternativen och kraven för att publicera SaaS-erbjudanden (Software as a Service) på Microsofts kommersiella marknads platser. Med SaaS-erbjudanden kan du leverera och licensiera program varu lösningar till dina kunder via online-prenumerationer. Som SaaS-utgivare kan du hantera och betala för den infrastruktur som krävs för att stödja kundernas användning av ditt erbjudande. I den här artikeln får du hjälp att förbereda ditt erbjudande för publicering till den kommersiella Marketplace med partner Center.

## <a name="listing-options"></a>Listalternativ

När du förbereder för att publicera ett nytt SaaS-erbjudande måste du bestämma vilka _list_ alternativ som ska väljas. Det List alternativ som du väljer avgör vilken ytterligare information du behöver för att kunna ange när du skapar erbjudandet i Partner Center. Du definierar ditt List alternativ på sidan  **erbjudande konfiguration** enligt beskrivningen i [så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace](create-new-saas-offer.md).

I följande tabell visas de olika List alternativen för SaaS-erbjudanden i den kommersiella marknads platsen.

| List alternativ | Transaktions process |
| ------------ | ------------- |
| Kontakta mig | Kunden kontaktar dig direkt från informationen i din lista.``*`` |
| Kostnadsfri utvärderingsversion | Kunden omdirigeras till mål-URL: en via Azure Active Directory (Azure AD).``*`` |
| Hämta nu (kostnads fritt) | Kunden omdirigeras till mål-URL: en via Azure AD.``*`` |
| Sälj via Microsoft  | Erbjudanden som säljs via Microsoft kallas för _transactable_ -erbjudanden. Ett erbjudande som är transactable är ett där Microsoft fören klar utbytet av pengar för en program varu licens för utgivarens räkning. Vi fakturerar SaaS-erbjudanden med den pris modell du väljer och hanterar kund transaktioner för din räkning. Azures infrastruktur användnings avgifter debiteras till dig, partnern direkt. Du bör ha ett konto för infrastruktur kostnader i pris sättnings modellen. Detta beskrivs mer detaljerat i [SaaS fakturering](#saas-billing) nedan.  |
|||

``*`` Utgivare ansvarar för att stödja alla aspekter av program licens transaktionen, inklusive men inte begränsat till order, uppfyllelse, avläsning, fakturering, fakturering, betalning och insamling.

Mer information om dessa List alternativ finns i [Transact-funktioner för kommersiella Marketplace](marketplace-commercial-transaction-capabilities-and-considerations.md).

När ditt erbjudande har publicerats visas det List alternativ som du har valt för ditt erbjudande som en knapp i det övre vänstra hörnet av ditt erbjudandes List sida. Följande skärm bild visar till exempel sidan erbjudande på Azure Marketplace med knappen **Hämta nu** .

![Visar en lista med erbjudanden i onlinebutiken.](./media/saas/listing-options-saas-full.png)

## <a name="technical-requirements"></a>Tekniska krav

De tekniska kraven varierar beroende på vilket List alternativ du väljer för ditt erbjudande.

List alternativet _kontakta mig_ har inga tekniska krav. Du kan välja att ansluta ett CRM-system (Customer Relations hip Management) för att hantera kund leads. Detta beskrivs i avsnittet [kund ledare](#customer-leads) senare i den här artikeln.

Alternativet _Hämta nu (kostnads fri)_, _kostnads fri utvärderings version_ och _försäljning via Microsoft_ List Options har följande tekniska krav:

- Ditt SaaS-program måste vara en lösning för flera innehavare.
- Du kan aktivera både Microsoft-konton (MSA) och [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) för att autentisera användare.
- Du måste skapa en landnings sida. När en användare har köpt ditt erbjudande dirigeras de till landnings sidan. Detta hjälper dem att slutföra ytterligare etablering eller konfiguration som krävs. Vägledning om hur du skapar landnings sidan finns i följande artiklar:
  - [Bygg in landnings sidan för ditt SaaS-erbjudande i kommersiellt marknads plats](azure-ad-transactable-saas-landing-page.md)
  - [Bygg in landnings sidan för ditt kostnads fria eller utvärderings SaaS-erbjudande på den kommersiella Marketplace](azure-ad-free-or-trial-landing-page.md)

Dessa ytterligare tekniska krav gäller endast för _försäljning via Microsoft_ (transactable) List alternativ:

- Azure AD med identitets hantering och autentisering med enkel inloggning (SSO) krävs för att köpa användare ska kunna komma åt landnings sidan. Detaljerad vägledning finns [i Azure AD och transactable SaaS-erbjudanden på den kommersiella marknaden](azure-ad-saas.md).
- Du måste använda [API: erna för SaaS-uppfyllelse](./partner-center-portal/pc-saas-fulfillment-api-v2.md) för att kunna integrera med Azure Marketplace och Microsoft AppSource. Du måste exponera en tjänst som kan interagera med SaaS-prenumerationen för att skapa, uppdatera och ta bort ett användar konto och en tjänste plan. Viktiga API-ändringar måste stödjas inom 24 timmar. Icke-kritiska API-ändringar frigörs regelbundet. Diagram och detaljerade förklaringar som beskriver användningen av de insamlade fälten finns i dokumentationen för API: [erna](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- Du måste skapa minst en plan för ditt erbjudande. Din plan priss ätts baserat på den pris modell du väljer innan du publicerar: _fast pris_ eller _per användare_. Mer information om [planer](#plans) finns längre fram i den här artikeln.
- Kunden kan när som helst säga upp erbjudandet.

### <a name="technical-information"></a>Teknisk information

Om du skapar ett transactable-erbjudande måste du samla in följande information för sidan **teknisk konfiguration** . Om du väljer att bearbeta transaktioner oberoende i stället för att skapa ett transactable-erbjudande hoppar du över det här avsnittet och går till [test enheter](#test-drives).

- **Landnings sidans URL**: URL: en för SaaS (t. ex.: `https://contoso.com/signup` ) som användarna dirigeras till efter att ha skaffat ditt erbjudande från den kommersiella Marketplace, vilket utlöser konfigurations processen från den nyligen skapade SaaS-prenumerationen. URL: en får en token som kan användas för att anropa API: er för att utföra etablerings information för din interaktiva registrerings sida.

  URL: en kommer att anropas med parametern Marketplace Purchase Identification Identification som unikt identifierar den specifika kundens SaaS-köp. Du måste byta denna token för motsvarande prenumerations information för SaaS med hjälp av [lösnings-API: et](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Informationen och alla andra som du vill samla in som en del av en kunds interaktiva webb sida kan användas för att komma igång med kundens onboarding-upplevelse, som måste ingå i ett aktiverings anrop för API: et för att starta prenumerations perioden. På den här sidan ska användaren registrera sig genom autentisering med ett klick genom att använda Azure Active Directory (Azure AD).

  Den här URL: en med parametern Marketplace för inköp av Marketplace kommer också att anropas när kunden startar en hanterad SaaS-upplevelse från Azure Portal eller Microsoft 365 administrations Center. Du bör hantera båda flödena: när token anges för första gången efter ett nytt kund inköp, och när det har tillhandahållits igen för en befintlig kund som hanterar sin SaaS-lösning.

    Landnings sidan som du konfigurerar bör vara igång 24/7. Detta är det enda sättet du får information om nya köp av dina SaaS-erbjudanden som görs i den kommersiella marknads platsen, eller konfigurations begär Anden för en aktiv prenumeration på ett erbjudande.

- **Anslutnings-webhook**: för alla asynkrona händelser som Microsoft behöver skicka till dig (till exempel när en SaaS-prenumeration har avbrutits) kräver vi att du anger en anslutning till webhook-URL. Vi kommer att anropa denna URL för att meddela dig om evenemanget.

  Webhooken som du tillhandahåller bör vara igång 24/7. Detta är det enda sättet att få information om uppdateringar om dina kunders SaaS prenumerationer som köpts via den kommersiella Marketplace.

  > [!NOTE]
  > I Azure Portal kräver vi att du skapar en app-registrering för en enda klient [Azure Active Directory (Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md). Använd appens registrerings information för att autentisera din lösning när du anropar Marketplace-API: er. Du hittar [klient-ID: t](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)genom att gå till din Azure Active Directory och välja **Egenskaper**. Leta sedan reda på katalog-ID-numret som visas. Till exempel `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory klient-ID**: (kallas även katalog-ID). I Azure Portal måste du [Registrera en Azure Active Directory (AD)-app](../active-directory/develop/howto-create-service-principal-portal.md) så att vi kan lägga till den i åtkomst kontrol listan (ACL) för API: et för att kontrol lera att du har behörighet att anropa den. Du hittar klient-ID: t för din Azure Active Directory (AD)-appen genom att gå till bladet [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Active Directory. I kolumnen **visnings namn** väljer du appen. Leta sedan efter den **katalog (klient) ID-** nummer som anges (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory program-ID**: du behöver också ditt [program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). Om du vill hämta värdet går du till bladet [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Active Directory. I kolumnen **visnings namn** väljer du appen. Leta sedan reda på det program-ID (klient) som visas (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).

  Azure AD-programmets ID är associerat med ditt utgivar-ID i ditt partner Center-konto. Du måste använda samma program-ID för alla erbjudanden i det kontot.

  > [!NOTE]
  > Om utgivaren har två eller flera olika konton i Partner Center kan du bara använda Azure AD-appens registrerings information i ett enda konto. Det finns inte stöd för att använda samma klient-ID, app-ID-par för ett erbjudande under ett annat utgivar konto.

## <a name="test-drives"></a>Provkörningar
Du kan välja att aktivera en testen het för SaaS-appen. Test enheter ger kunderna till gång till en förkonfigurerad miljö för ett fast antal timmar. Du kan aktivera test enheter för alla publicerings alternativ, men den här funktionen har ytterligare krav. Mer information om test enheter finns i [Vad är en test enhet?](what-is-test-drive.md). Information om hur du konfigurerar olika typer av test enheter finns i [teknisk konfiguration för test enhet](test-drive-technical-configuration.md).

> [!TIP]
> En testen het skiljer sig från en [kostnads fri utvärderings version](plans-pricing.md#free-trials). Du kan erbjuda en testenhet, en kostnads fri utvärderings version eller både och. De förser båda kunderna med din lösning under en fast period. Men en testenhet innehåller även en praktisk guidad rundtur i produktens viktiga funktioner och fördelar som visas i ett verkligt implementerings scenario.

## <a name="customer-leads"></a>Kund ledare

Du måste ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) för att samla in kund information. Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namn, ID och onlinebutik där de hittade ditt erbjudande, kommer att skickas till det CRM-system som du har konfigurerat. Den kommersiella Marketplace har stöd för en rad olika CRM-system, tillsammans med alternativet att använda en Azure-tabell eller konfigurera en HTTPS-slutpunkt med energi automatisering.

Du kan lägga till eller ändra en CRM-anslutning när som helst under eller efter att erbjudandet har skapats. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Välja en onlinebutik

När du publicerar ett SaaS-erbjudande visas det i Microsoft AppSource, Azure Marketplace eller båda. I varje onlinebutik används unika kund krav. AppSource är för affärs lösningar och Azure Marketplace är för IT-lösningar. Erbjudande typen, Transact-funktionerna och-kategorierna avgör var ditt erbjudande ska publiceras. Kategorier och under Kategorier mappas till varje onlinebutik utifrån lösnings typen. 

Om ditt SaaS-erbjudande är *både* en IT-lösning (Azure Marketplace) och en företags lösning (AppSource), väljer du en kategori och en under kategori som gäller för varje onlinebutik. Erbjudanden som publiceras i båda onlinebutiker bör ha ett värde som en IT-lösning *och* en affärs lösning.

> [!IMPORTANT]
> SaaS-erbjudanden med [avgiftsbelagda faktureringar](partner-center-portal/saas-metered-billing.md) är tillgängliga via Azure Marketplace och Azure Portal. SaaS-erbjudanden med enbart privata planer är tillgängliga via Azure Portal.

| Avgiftsbelagd fakturering | Offentligt abonnemang | Privat plan | Tillgängligt i: |
|---|---|---|---|
| Ja             | Ja         | Inga           | Azure Marketplace och Azure Portal |
| Ja             | Ja         | Ja          | Azure Marketplace och Azure Portal * |
| Ja             | Inga          | Ja          | Endast Azure Portal |
| Inga              | Inga          | Ja          | Endast Azure Portal |
|||||

&#42; privat plan för erbjudandet är endast tillgängligt via Azure Portal

Ett erbjudande med till exempel fakturering och enbart privat plan (ingen offentlig plan) kommer att köpas av kunder i Azure Portal. Lär dig mer om [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

Detaljerad information om List alternativ som stöds av onlinebutiker finns i [lista och pris alternativ per onlinebutik](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Mer information om kategorier och under kategorier finns [i kategorier och under Kategorier i den kommersiella marknads platsen](categories.md).

## <a name="legal-contracts"></a>Juridiska kontrakt

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt som du kan använda för dina erbjudanden på den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

Om du väljer att använda standard kontraktet har du möjlighet att lägga till generella ändrings villkor och upp till 10 anpassade ändringar i standard kontraktet. Du kan också använda egna villkor i stället för standard kontraktet. Du kommer att hantera informationen på sidan **Egenskaper** . Detaljerad information finns i [standard kontrakt för Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> När du har publicerat ett erbjudande med hjälp av standard avtalet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Det är ett "eller"-scenario. Du erbjuder antingen din lösning enligt standard avtalet eller dina egna villkor. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

## <a name="offer-listing-details"></a>Information om erbjudande lista

När du [skapar ett nytt SaaS-erbjudande](create-new-saas-offer.md) i Partner Center kan du ange text, bilder, valfria videor och annan information på sidan med **erbjudande listan** . Detta är den information som kunderna ser när de identifierar din erbjudande lista på den kommersiella marknaden, som du ser i följande exempel.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="Visar hur det här erbjudandet visas i Microsoft AppSource.":::

**Beskrivningar av anrop**

1. Logotyp
2. Kategorier
3. Branscher
4. Support adress (länk)
5. Villkor för användning
6. Sekretesspolicy
7. Erbjudandets namn
8. Sammanfattning
9. Description
10. Skärm bilder/videor
11. Dokument

I följande exempel visas en lista med erbjudanden i Azure Portal.

![Visar en erbjudande lista i Azure Portal.](./media/example-managed-service-azure-portal.png)

**Beskrivningar av anrop**

1. Rubrik
1. Beskrivning
1. Användbara länkar
1. Skärmbilder

> [!NOTE]
> Erbjudande för List innehåll måste inte vara på engelska om erbjudande beskrivningen börjar med frasen "det här programmet är endast tillgängligt på [språk som inte är engelska]".

För att hjälpa till att skapa ett erbjudande enklare kan du förbereda några av dessa objekt i förväg. Följande objekt krävs om inget annat anges.

- **Namn**: namnet visas som rubrik på din erbjudande lista på den kommersiella Marketplace. Namnet kan vara ett varumärke. Den får inte innehålla emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken.
- **Sammanfattning av Sök Resultat**: Beskriv syftet eller funktionen hos ditt erbjudande som en mening utan rad brytningar på högst 100 tecken. Den här sammanfattningen används i Sök resultaten för de kommersiella Marketplace-listorna.
- **Beskrivning**: den här beskrivningen visas i Översikt över de kommersiella Marketplace-listorna. Överväg att inkludera ett värde förslag, viktiga fördelar, avsedda användar Base, kategori-eller bransch associationer, inköps möjligheter för appar, obligatoriska uppgifter och en länk till mer information.

    Den här text rutan innehåller text redigerings kontroller som du kan använda för att göra din beskrivning mer engagerande. Du kan också använda HTML-taggar för att formatera beskrivningen. Du kan ange upp till 3 000 tecken text i den här rutan, inklusive HTML-kod. Ytterligare tips finns i [skriva en fantastisk app-Beskrivning](/windows/uwp/publish/write-a-great-app-description).

- **Komma igång instruktioner**: om du väljer att sälja ditt erbjudande via Microsoft (transactable-erbjudande) är det här fältet obligatoriskt. De här anvisningarna hjälper kunder att ansluta till ditt SaaS-erbjudande. Du kan lägga till upp till 3 000 tecken med text och länkar till mer detaljerad onlinedokumentationen.
- **Sök Nyckelord** (valfritt): ange upp till tre Sök nyckelord som kunder kan använda för att hitta ditt erbjudande i onlinebutiker. Du behöver inte inkludera erbjudandets **namn** och **Beskrivning**: den texten ingår automatiskt i sökningen.
- **Länk till sekretess policy**: URL: en för ditt företags sekretess policy. Du måste ange en giltig sekretess policy och du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter.
- **Kontakt information**: du måste tillhandahålla följande kontakter från din organisation:
  - **Support kontakt**: Ange namn, telefon och e-postadress för Microsoft-partner som ska användas när dina kunder öppnar biljetter. Du måste även ta med URL: en för din support webbplats.
  - **Teknisk kontakt**: Ange namn, telefon och e-postadress som Microsoft ska använda direkt när det är problem med erbjudandet. Den här kontakt informationen visas inte i den kommersiella marknads platsen.
  - **Kontakta CSP-programmet** (valfritt): Ange namn, telefon och e-postadress om du väljer CSP-programmet, så att dessa partner kan kontakta dig med frågor. Du kan också inkludera en URL till ditt marknadsförings material.
- **Användbara länkar** (valfritt): du kan ange länkar till olika resurser för användare av ditt erbjudande. Till exempel forum, FAQ och viktig information.
- **Stöd dokument**: du kan ge upp till tre kund motstående dokument, till exempel Whitepapers, broschyrer, check listor eller PowerPoint-presentationer.
- **Media – logo typer**: Ange en PNG-fil för **stor** logo typ. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

   - Stor (från 216 x 216 till 350 x 350 BPT, krävs)
   - Medel (90 x 90 BPT, valfritt)
   - Liten (48 x 48 BPT, valfritt)

  Dessa Logo typer används på olika platser i onlinebutiker:

  - Den lilla logo typen visas i Sök resultaten för Azure Marketplace och på sidan Microsoft AppSource huvud sida och Sök resultat.
  - Medie logo typen visas när du skapar en ny resurs i Microsoft Azure.
  - Den stora logo typen visas på sidan med din erbjudande lista i Azure Marketplace och Microsoft AppSource.

- **Medie skärm bilder**: du måste lägga till minst en och upp till fem skärm dum par med följande krav, som visar hur ditt erbjudande fungerar:
  - 1280 x 720 pixlar
  - . png-fil
  - Måste innehålla en beskrivning
- **Media-videor** (valfritt): du kan lägga till upp till fyra videor med följande krav som demonstrerar ditt erbjudande:
  - Name
  - URL: måste vara värd för YouTube eller Vimeo.
  - Miniatyr: 1280 x 720. png-fil

> [!Note]
> Ditt erbjudande måste uppfylla de allmänna [certifierings principerna för kommersiella marknads platser](/legal/marketplace/certification-policies#100-general) och [program vara som en tjänst](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) för att publiceras på den kommersiella marknads platsen.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat plan. En privat plan är en som du bara gör tillgänglig för en speciell mål grupp som du väljer. På så sätt kan du förhandla fram en anpassad plan med vissa kunder. Mer information finns i nästa avsnitt: planer.

Du kan skicka inbjudningar till Microsoft-konto (MSA) eller Azure Active Directory (Azure AD) e-postadresser. Lägg till upp till 10 e-postadresser manuellt eller importera upp till 20 med en. csv-fil. Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

## <a name="plans"></a>Planer

Transactable-erbjudanden kräver minst en plan. En plan definierar lösningens omfattning och begränsningar samt tillhör ande prissättning. Du kan skapa flera planer för ditt erbjudande för att ge dina kunder olika tekniska och pris alternativ. Om du väljer att bearbeta transaktioner oberoende av varandra i stället för att skapa ett transactable-erbjudande visas inte sidan **planer** . I så fall kan du hoppa över det här avsnittet och gå till [ytterligare affärs möjligheter](#additional-sales-opportunities).

Se [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md) för allmän vägledning om planer, inklusive pris modeller, kostnads fria utvärderingar och privata planer. I följande avsnitt beskrivs ytterligare information som är speciell för SaaS-erbjudanden.

### <a name="saas-pricing-models"></a>Prissättningsmodeller för SaaS

SaaS-erbjudanden kan använda en av två pris modeller för varje plan: antingen _fast pris_ eller _per användare_. Alla planer i samma erbjudande måste vara kopplade till samma pris modell. Ett erbjudande kan till exempel inte ha en plan som är fast priss taxa och en annan plan per användare.

**Fast** pris – ge åtkomst till ditt erbjudande med ett enda månatligt eller årligt schablon pris. Detta kallas ibland för platsbaserade priser. Med den här pris sättnings modellen kan du välja att definiera Mät planer som använder API: et för avläsning av program vara för att debitera kunder för användning som inte omfattas av den fasta taxan. Mer information om avgiftsbelagd fakturering finns i [mäta fakturering för SaaS med hjälp av den kommersiella tjänsten för avläsning av marknads platser](./partner-center-portal/saas-metered-billing.md). Du bör också använda det här alternativet om användnings beteendet för din SaaS-tjänst är i burst.

**Per användare** – aktivera åtkomst till ditt erbjudande med ett pris baserat på antalet användare som har åtkomst till erbjudandet eller har platser. Med den här användarbaserade modellen kan du ange det lägsta och högsta antalet användare som stöds av planen. Du kan skapa flera planer för att konfigurera olika pris punkter baserat på antalet användare. Dessa fält är valfria. Om alternativet inte är markerat kommer antalet användare att tolkas som att de inte har en gräns (minst 1 och högst lika många som din tjänst kan stödja). De här fälten kan redige ras som en del av en uppdatering av planen.

> [!IMPORTANT]
> När ditt erbjudande har publicerats kan du inte ändra pris sättnings modellen. Dessutom måste alla planer för samma erbjudande dela samma pris modell.

### <a name="saas-billing"></a>SaaS fakturering

För SaaS-appar som körs i din (utgivares) Azure-prenumeration debiteras du för infrastrukturs användningen direkt. kunderna ser inte faktiska avgifter för infrastruktur användning. Du bör paketera användnings avgifter för Azure-infrastruktur i pris nivån för program varu licenser för att kompensera för kostnaden för den infrastruktur som du har distribuerat för att köra lösningen.

SaaS app-erbjudanden som säljs via Microsoft har stöd för månatlig eller årlig fakturering baserat på en fast avgift, per användare eller förbruknings avgifter med hjälp av den [avgiftsbelagda fakturerings tjänsten](./partner-center-portal/saas-metered-billing.md). Den kommersiella marknads platsen fungerar på en byrå modell, där utgivare anger priser, Microsoft fakturerar kunder och Microsoft betalar intäkterna till utgivare samtidigt som en Agen tur avgift debiteras.

I följande exempel visas ett exempel på en uppdelning av kostnader och utbetalningar för att demonstrera byråns modell. I det här exemplet har Microsoft fakturerar $100,00 till kunden för din program varu licens och ger ut $80,00 till utgivaren.

| Din licens kostnad | $100 per månad |
| ------------ | ------------- |
| Användnings kostnad i Azure (D1/1-kärna) | Debiteras direkt till utgivaren, inte hos kunden |
| Kunden debiteras av Microsoft | $100,00 per månad (utgivare måste redovisa alla kostnader som uppstått eller genom att överföra infrastruktur i licens avgiften) |
| **Microsoft-räkningar** | **$100 per månad** |
| Microsoft betalar 80% av din licens kostnad<br>`*` För kvalificerade SaaS-appar betalar Microsoft 90% av din licens kostnad| $80,00 per månad<br>``*`` $90,00 per månad |
|||

**`*` Minskad service avgift för Marketplace** – för vissa SaaS-erbjudanden som du har publicerat på den kommersiella marknaden kommer Microsoft att minska sin service avgift för Marketplace från 20% (enligt beskrivningen i Microsoft Publisher Agreement) till 10%. För dina erbjudanden som ska kvalificeras måste ditt erbjudande ha utsetts av Microsoft som Azure IP Co-Sälj-motiverade. Berättigande måste uppfyllas minst fem (5) arbets dagar före slutet av varje kalender månad för att ta emot den minskade service avgiften för Marketplace. När rätten är uppfylld tilldelas den minskade tjänste avgiften till alla transaktioner som gäller den första dagen i följande månad och fortsätter att gälla tills Azure IP-motiverade status går förlorad. Information om IP-samförsäljnings behörighet finns i [krav för medförsäljnings status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Den minskade service avgiften för Marketplace gäller även för Azure IP Co-Sälj motiverade VM: ar, hanterade appar och andra kvalificerade IaaS-erbjudanden som görs tillgängliga via den kommersiella marknads platsen.

## <a name="preview-audience"></a>Förhandsgranska mål grupp

En förhands gransknings grupp kan komma åt ditt erbjudande innan det publiceras Live i onlinebutiker. De kan se hur ditt erbjudande kommer att se ut på den kommersiella Marketplace och testa slut punkt till slut punkt innan du publicerar det Live. 

På sidan för **hands versions mål** kan du definiera en begränsad förhands gransknings mål grupp. Den här inställningen är inte tillgänglig om du väljer att bearbeta transaktioner oberoende i stället för att sälja erbjudandet via Microsoft. I så fall kan du hoppa över det här avsnittet och gå till [ytterligare affärs möjligheter](#additional-sales-opportunities).

## <a name="test-offer"></a>Test erbjudande

Innan du publicerar erbjudandet Live bör du använda för hands versions funktionen för att utveckla teknisk implementering, testa och experimentera med olika pris modeller.

För att utveckla och testa ditt SaaS-erbjudande med den lägsta risken, rekommenderar vi att du skapar ett test-och utvecklings erbjudande (DEV) för experimentering och testning. DEV-erbjudandet är skilt från produktions erbjudandet (PROD).

För att förhindra oavsiktliga köp av DEV-erbjudandet ska du aldrig trycka på knappen **Go Live** för att publicera dev-erbjudandet Live.

![Visar sidan erbjudande översikt för ett erbjudande i Partner Center. Knappen gå live och för hands versions länkarna visas. Länken Visa verifierings rapport visas också under automatisk verifiering.](./media/review-publish-offer/publish-status-saas.png)

Här följer några orsaker till att skapa ett separat utvecklings erbjudande för utvecklings teamet som används för utveckling och testning av produktions erbjudandet:

- Undvik oförutsedda kund avgifter
- Utvärdera pris modeller
- Lägg inte till planer som inte riktar sig till faktiska kunder

### <a name="avoid-accidental-customer-charges"></a>Undvik oförutsedda kund avgifter

Genom att använda ett DEV-erbjudande i stället för produkt erbjudande och behandla dem som utvecklings-och produktions miljöer kan du undvika oförutsedda kostnader för kunderna.

Vi rekommenderar att du registrerar två olika Azure AD-appar för att anropa Marketplace-API: er. Utvecklare kommer att använda en Azure AD-App med inställningarna för DEV-erbjudandet, och drift teamet använder appens registrering i PROD. Genom att göra detta kan du isolera utvecklings teamet från att göra oavsiktliga misstag, till exempel anropa API: et för att avbryta en kunds prenumeration som betalar $100 000 per månad. Du kan också undvika att debitera en kund för mätnings användning som de inte förbrukade.

### <a name="evaluate-pricing-models"></a>Utvärdera pris modeller

Att testa pris modeller i DEV-erbjudandet minskar risken för utvecklare som experimenterar med olika pris modeller.

Utgivare kan skapa de planer som de behöver i utvecklings erbjudandet för att avgöra vilken pris modell som passar bäst för erbjudandet. Utvecklare kan vilja skapa flera planer i utvecklings erbjudandet för att testa olika pris kombinationer. Du kan till exempel skapa planer med olika uppsättningar med anpassade mått för mätning. Du kan skapa en annan plan med en blandning av fasta kostnader och anpassade mätnings dimensioner.

Om du vill testa flera pris alternativ måste du skapa en plan för varje unik pris modell. Mer information finns i [planer](#plans).

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>Lägg inte till planer som inte riktar sig till faktiska kunder

Genom att använda ett utvecklings erbjudande för utveckling och testning kan du minska onödigheten i produktions erbjudandet. Du kan till exempel inte ta bort planer som du skapar för att testa olika pris modeller eller tekniska konfigurationer (utan att arkivera ett support ärende). Så genom att skapa planer för testning i DEV-erbjudandet kan du minska det här erbjudandet i produktions erbjudandet.

Ett prydligt erbjudande finns i produkt-och marknadsförings grupper, eftersom alla planer förväntas riktas mot faktiska kunder. I synnerhet med stora team som är åtskilda och som alla vill att olika sand boxar ska arbeta med, ger att du skapar två erbjudanden två olika miljöer för utveckling och PROD. I vissa fall kanske du vill skapa flera DEV-erbjudanden för att stödja ett större team som har olika personer som kör olika test scenarier. Att låta olika team medlemmar arbeta i utvecklings erbjudandet separat från produktions erbjudandet, hjälper till att hålla produktions planer så nära produktion som möjligt.

Om du testar ett DEV-erbjudande kan du undvika den 30 anpassade mått gränsen per erbjudande. Utvecklare kan testa olika mätar kombinationer i DEV-erbjudandet utan att påverka den anpassade Mät dimensions gränsen i produktions erbjudandet.

## <a name="additional-sales-opportunities"></a>Ytterligare försäljnings möjligheter

Du kan välja att välja mellan marknadsförings-och försäljnings kanaler som stöds av Microsoft. När du skapar erbjudandet i Partner Center visas två flikar i slutet av processen:

- Att **sälja via CSP**: Använd det här alternativet om du vill att CSP-partner (Microsoft Cloud solution providers) ska kunna sälja din lösning som en del av ett sammanslaget erbjudande. Mer information om det här programmet finns i [program för Cloud Solution Provider](cloud-solution-providers.md).

- **Samförsäljning med Microsoft: med** det här alternativet kan Microsofts försäljnings team överväga din IP-samkunds kvalificerade lösning vid utvärdering av kundernas behov. Information om samförsäljnings behörighet finns i [krav för medförsäljnings status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Detaljerad information om hur du förbereder erbjudandet för utvärdering finns i [samförsäljnings alternativ i Partner Center](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Nästa steg

- [Så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace](create-new-saas-offer.md)
- [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)
