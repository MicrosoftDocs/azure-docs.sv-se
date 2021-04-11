---
title: Skapa ett Azure Container-erbjudande – Azure Marketplace
description: Lär dig hur du skapar och publicerar ett behållar erbjudande på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: 01370a8d7710292858fe28d263fe5456b012c40f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107388"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Skapa ett Azure Container-erbjudande på Azure Marketplace

Den här artikeln beskriver hur du skapar och publicerar ett behållar erbjudande för Azure Marketplace. Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.

3. På sidan Översikt väljer du **+ nytt erbjudande** för  >  **Azure-behållare**.

   ![Visar menyn till vänster-navigering.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> När ett erbjudande har publicerats visas bara ändringar i Partner Center i onlinebutiker när du har publicerat om erbjudandet. Se till att du alltid publicerar igen när du har gjort ändringar.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1**, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
- Detta kan inte ändras när du har valt **skapa**.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-overview"></a>Erbjudande översikt

På sidan **erbjudande översikt** visas en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och kommande) och hur lång tid varje steg ska ta att slutföra.

Den här sidan visar olika länkar baserat på erbjudandet aktuella status. Exempel:

- Om erbjudandet är ett utkast – ta bort utkast erbjudande
- Om erbjudandet är Live- [sluta att sälja erbjudandet](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version- [Go-Live](review-publish-offer.md#previewing-and-approving-your-offer)
- Om du inte har slutfört Publisher-utloggning – [Avbryt publicering.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Erbjudande konfiguration

Följ dessa steg för att konfigurera erbjudandet.

### <a name="customer-leads--optional"></a>Kund ledare – valfritt

När du publicerar erbjudandet till den kommersiella marknads platsen med partner Center kan du ansluta det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. **Välj ett mål för lead där du vill att vi ska skicka kund leads**. Partner Center stöder följande CRM-system:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Om ditt CRM-system inte finns med i listan ovan använder du [Azure Table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) eller [https-slutpunkten](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) för att lagra kundens lead-data och sedan exportera data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

Här följer några ytterligare resurser för ledar hantering:

- [Kundleads från ditt erbjudande på den kommersiella marknadsplatsen](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor om ledar hantering](lead-management-faq.md#common-questions-about-lead-management)
- [Felsöka fel vid lead-konfiguration](lead-management-faq.md#publishing-config-errors)
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (se till att blockering av popup-fönster är inaktiverat).

Välj **Spara utkast** innan du fortsätter.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på Marketplace och de juridiska kontrakt som har stöd för ditt erbjudande.

#### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](gtm-offer-listing-best-practices.md). Behållare visas alltid under **behållare** och sedan kategorin **behållar avbildningar** .

#### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Det finns två alternativ:

- Använd standard kontraktet för Microsofts kommersiella marknads plats.
- Ange egna villkor.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard kontrakt för Microsofts kommersiella marknads plats

Vi erbjuder en standard kontrakts mal len som hjälper till att under lätta transaktioner i den kommersiella marknads platsen. Du kan välja att erbjuda din lösning under standard kontraktet, som kunder bara behöver kontrol lera och godkänna en gång. Det här är ett bra alternativ om du inte vill skapa anpassade villkor.

Mer information om standard kontraktet finns i standard- [kontrakt för Microsoft Commercial Marketplace](standard-contract.md). Du kan också ladda ned [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att blockering av popup-fönster är inaktiverat).

Om du vill använda standard kontraktet väljer du * * Använd standard kontraktet för Microsofts kommersiella Marketplace] (... /standard-contract.md)

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för Microsoft Marketplace kan du inte använda dina egna anpassade villkor. Erbjud antingen lösningen enligt standard kontraktet eller enligt dina egna villkor.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustrerar kryss rutan Använd standard kontraktet för Microsofts kommersiella Marketplace.":::

##### <a name="your-own-terms-and-conditions"></a>Egna villkor

Om du vill ange egna anpassade villkor anger du dem i rutan **allmänna** villkor. Du kan ange ett obegränsat antal tecken text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, erbjud listan.

## <a name="offer-listing"></a>Erbjudande lista

På den här sidan kan du definiera information om erbjudandet som visas på den kommersiella marknads platsen. Detta inkluderar namn, beskrivning och avbildningar för erbjudandet.

> [!NOTE]
> Erbjudande information krävs inte på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [icke-engelska]". Det går också att tillhandahålla en användbar länk för att erbjuda innehåll på ett annat språk än det som används i listan med erbjudande information.

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-och Copyright-symboler).
- Får inte vara mer än 50 tecken långt.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

En kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

### <a name="long-summary"></a>Lång Sammanfattning

En mer detaljerad beskrivning av ditt erbjudande. Detta kan vara upp till 256 tecken långt och används i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange webb adressen till din organisations sekretess policy. Du är ansvarig för att se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du ansvarar också för att publicera en giltig sekretess policy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Ge kompletterande online-dokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Title** – kunderna kommer att se detta på ditt erbjudandes informations sida.
- **Länk (URL)** – ange en länk till kunder för att visa ditt online-dokument. Länken måste börja med http://eller https://.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men den är tillgänglig för Microsoft. Den kan också tillhandahållas av CSP-partner (Cloud Solution Provider).

- Support kontakt (krävs): för allmänna supportfrågor.
- Teknisk kontakt (krävs): för tekniska frågor och certifierings problem.
- Kontakta CSP-programmet (valfritt): för åter försäljar frågor som är relaterade till CSP-programmet.

I avsnittet **support kontakt** anger du **Support webbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i Global Azure, Azure Government eller båda.

I avsnittet för **CSP** -programavsnittet anger du länken (**CSP-programmet marknadsförings material**) där CSP-partner kan hitta marknadsförings material för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

Om du vill veta mer om att skapa erbjudande listor, se [erbjudande lista med bästa praxis](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Medel** (90 x 90 BPT, valfritt)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Skärm dum par (valfritt)

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Ange videons namn, webb adressen och en miniatyr bild av videon med 1280 x 720 pixlar i storlek.

#### <a name="offer-examples"></a>Exempel på erbjudanden

I följande exempel visas hur fälten för erbjudande listan visas på olika ställen i erbjudandet.

Visar sidan för **erbjudande listan** på Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Visar sidan med erbjudande listan i Azure Marketplace." :::

Detta visar Sök resultat på Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Visar Sök resultatet i Azure Marketplace.":::

Visar sidan för **erbjudande listan** i Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Visar sidan med erbjudande listan i Azure Portal.":::

Detta visar Sök resultat i Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Visar Sök resultatet i Azure Portal.":::

## <a name="preview"></a>Förhandsgranskning

På fliken förhands granskning kan du välja en begränsad **förhands gransknings grupp** för att verifiera ditt erbjudande innan du publicerar det Live.

> [!IMPORTANT]
> När du har granskat erbjudandet i för **hands versionen** måste du välja **Go Live** för att publicera ditt erbjudande till allmänheten.

Ange din förhands gransknings mål med ID: n för Azure-prenumerations-ID, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt prenumerations-ID för Azure på sidan prenumerationer i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID: n bestämmer du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du välja en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.

## <a name="plan-overview"></a>Plan översikt

På den här fliken kan du ange olika prenumerations alternativ i samma erbjudande. Planer (tidigare kallade SKU: er) kan variera beroende på vilka moln som är tillgängliga, till exempel globala moln, offentliga moln och den avbildning som planen refererar till. Du måste konfigurera minst en plan för att få en lista över erbjudandet i den kommersiella marknads platsen.

Du kan skapa upp till 100-planer för varje erbjudande: upp till 45 av dessa kan vara privata. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](private-offers.md).

När du har skapat dina planer visas fliken **plan översikt** :

- Planera namn
- Prismodell
- Azure-regioner (globala eller myndigheter)
- Aktuell publicerings status
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i plan översikten varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast** – om planens status är ett utkast.
- **Stoppa Sälj plan** – om plan statusen publiceras Live.

### <a name="create-new-plan"></a>Skapa ny plan

Välj **Skapa ny plan**. Dialog rutan **nytt plan** visas.

I rutan **plan-ID** skapar du en unik plan identifierare för varje plan i det här erbjudandet. Detta ID visas för kunder i produktens webb adress. Använd bara gemena bokstäver och siffror, bindestreck eller under streck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID när du har valt **skapa**.

I rutan **plan namn** anger du ett namn för den här planen. Kunderna ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda ett erbjudande namn för **Windows Server** med planer för **Windows Server 2016** och **Windows Server 2019**.

### <a name="plan-setup"></a>Planera installationen

På den här fliken kan du välja vilka moln som planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="azure-regions"></a>Azure-regioner

Alla planer för Azure Container-erbjudanden görs automatiskt tillgängliga i **Azure Global**.  Din prenumeration kan användas av kunder i alla globala Azure-regioner som använder den kommersiella marknads platsen. Mer information finns i [stöd för geografisk tillgänglighet och valuta](marketplace-geo-availability-currencies.md).

Välj alternativet [Azure Government](../azure-government/documentation-government-welcome.md) för att se till att din lösning visas här. Det här är ett offentligt community-moln med kontrollerad till gång för kunder från amerikanska federala, statliga och lokala myndigheter eller stambaserad myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare är du ansvarig för alla kontroller, säkerhets åtgärder och bästa praxis för moln gruppen. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA). Innan du [publicerar](../azure-government/documentation-government-manage-marketplace-partners.md) till Azure Government ska du testa och bekräfta din lösning inom det här avsnittet eftersom resultatet kan skilja sig. Du kan skapa och testa din lösning genom att begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> När planen har publicerats och är tillgänglig i en speciell region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet kan bara visas om **Azure Government** har valts under **Azure-regioner**.

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS.

Om du vill visa dina certifieringar för dessa program kan du ange upp till 100 länkar som beskriver dem. Det kan vara länkar till dina program tablåer direkt eller till din egen webbplats. Dessa länkar är bara synliga för Azure Government kunder.

### <a name="plan-listing"></a>Plan lista

På den här fliken visas särskild information för varje plan i det aktuella erbjudandet.

### <a name="plan-name"></a>Plan namn

Detta är ifyllt med det namn som du gav din plan när du skapade det. Du kan ändra det här namnet efter behov. Det kan vara upp till 50 tecken långt. Det här namnet visas som rubrik för den här planen i Azure Marketplace och Azure Portal. Den används som standardmodulens namn när planen är klar att användas.

### <a name="plan-summary"></a>Plan Sammanfattning

En kort sammanfattning av din program varu plan (inte erbjudandet). Den här sammanfattningen visas i Sök resultaten i Azure Marketplace och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Beskrivning av plan

Beskriv vad som gör den här program varu planen unik, samt skillnader mellan planer i erbjudandet. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas på Azure Marketplace och i Azure Portal på sidan **erbjudande lista** . Det kan vara samma innehåll som du angav i plan sammanfattningen och innehåller upp till 2 000 tecken.

Välj **Spara** när du är klar med fälten.

#### <a name="plan-examples"></a>Planera exempel

I följande exempel visas hur plan List fälten visas i olika vyer.

Detta är fälten på Azure Marketplace när du visar plan information:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Visar de fält som visas när du visar plan information på Azure Marketplace.":::

Detta är plan information på Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Visar plan information på Azure Portal.":::

### <a name="plan-availability"></a>Planera tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunder inte kan söka, bläddra i eller köpa det på Marketplace, markerar du kryss rutan **Dölj plan** på fliken **tillgänglighet** .

Det här fältet används när:

- Erbjudandet är avsett att användas indirekt när det hänvisas till ett annat program.
- Erbjudandet ska inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongs erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

Behållar avbildningar måste ligga i ett privat [Azure Container Registry](https://azure.microsoft.com/services/container-registry/). På fliken **teknisk konfiguration** anger du referensinformation för din behållar avbildnings lagrings plats i Azure Container Registry.

När erbjudandet har publicerats kopieras din behållar avbildning till Azure Marketplace i ett särskilt offentligt behållar register. Alla begär Anden att använda behållar avbildningen hanteras från Azure Marketplace offentlig behållar register, inte ditt privata namn. Mer information finns i [förbereda Azure Container Technical-tillgångar](create-azure-container-technical-assets.md).

### <a name="image-repository-details"></a>Information om avbildnings lager

Ange följande information på fliken **avbildnings lagrings information** .

**Azure-prenumerations-ID** – ange det PRENUMERATIONS-ID där användning rapporteras och tjänster debiteras för den Azure Container Registry som innehåller din behållar avbildning. Du hittar detta ID på [sidan prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal.

**Namn på Azure-resurs grupp** – ange namnet på den [resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md) som innehåller Azure Container Registry med din behållar avbildning. Resurs gruppen måste vara tillgänglig i prenumerations-ID (ovan). Du hittar namnet på sidan [resurs grupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure Portal.

**Azure Container Registry namn** – ange namnet på den [Azure Container Registry](../container-registry/container-registry-intro.md) som har behållar avbildningen. Behållar registret måste finnas i Azure-resurs gruppen du angav tidigare. Inkludera bara register namnet, inte det fullständiga inloggnings Server namnet. Var noga med att utelämna **azurecr.io** från namnet. Du hittar register namnet på [sidan behållar register](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) i Azure Portal.

**Administratörens användar namn för Azure Container Registry** – ange [administratörens användar namn](../container-registry/container-registry-authentication.md#admin-account)) som är länkat till Azure Container Registry som har behållar avbildningen. Användar namn och lösen ord krävs för att se till att ditt företag har åtkomst till registret. Om du vill hämta administratörens användar namn och lösen ord anger du egenskapen **admin-Enabled** till **True** med Azure Command-Line Interface (CLI). Alternativt kan du ange **Administratörs användare** som ska **aktive ras** i Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Visar dialog rutan uppdatera container Registry.":::

**Lösen ord för Azure Container Registry** – ange lösen ordet för det administratörs användar namn som är associerat med Azure Container Registry och har behållar avbildningen. Användar namn och lösen ord krävs för att se till att ditt företag har åtkomst till registret. Du kan hämta lösen ordet från Azure Portal genom att gå till **container Registry**  >  **åtkomst nycklar** eller med Azure CLI med [Kommandot show](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Visar menyn snabb tangent.":::

**Databas namn inom Azure Container Registry**. Ange namnet på Azure Container Registry lagrings platsen som innehåller din avbildning. Inkludera namnet på lagrings platsen när du push-överför avbildningen till registret. Du kan hitta namnet på lagrings platsen genom att gå till sidan [container Registry](https://azure.microsoft.com/services/container-registry/)-  >  **databaser** . Mer information finns i [Visa behållare register Arkiv i Azure Portal](../container-registry/container-registry-repositories.md).

> [!NOTE]
> När du har angett namnet kan du inte ändra det. Använd ett unikt namn för varje erbjudande i ditt konto.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunderna måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna stanna kvar på en angiven version av avbildningen. Du kan göra detta genom att lägga till nya avbildnings etiketter varje gången du gör en uppdatering av avbildningen.

### <a name="image-tag"></a>Bildtagg

Det här fältet måste innehålla en **senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Det måste också innehålla en versions tagg (till exempel från xx. xx. xx, där xx är ett tal). Kunderna bör använda [manifest Taggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla Taggar som en manifest tag refererar till måste också läggas till så att vi kan ladda upp dem.

Alla manifest Taggar (förutom den senaste taggen) måste börja med antingen X. Y **-** eller X. y. Z – där X, y och Z är heltal. Om till exempel en **senaste** tagg pekar på 1.0.1-linux-x64, 1.0.1-Linux-ARM32 och 1.0.1-Windows-ARM32, måste dessa sex Taggar läggas till i det här fältet. Mer information finns i [förbereda Azure Container Technical-tillgångar](create-azure-container-technical-assets.md).

> [!NOTE]
> Kom ihåg att lägga till en test-tagg till din avbildning så att du kan identifiera avbildningen under testningen.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka det till granskning och publicering.

I det övre högra hörnet av portalen väljer du **Granska och** **publicera**.

På sidan Granska kan du:

- Se slut för ande status för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har marker ATS som slutförda.
  - **Inte startat** – har inte startats och måste slutföras.
  - **Ofullständig** – innehåller fel som behöver åtgärdas eller som kräver att du anger mer information. Se avsnittet tidigare i det här dokumentet för hjälp.
  - **Complete** – innehåller alla data som krävs utan fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- Tillhandahåll test instruktioner till certifierings teamet för att se till att ditt erbjudande testas korrekt. Ange också eventuella kompletterande kommentarer som hjälper dig att förstå ditt erbjudande.

Om du vill skicka publicerings erbjudandet väljer du **publicera**.

Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas.

Om du vill publicera ditt erbjudande till allmänheten går du till Partner Center och väljer **Go-Live**.

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](./partner-center-portal/update-existing-offer.md)