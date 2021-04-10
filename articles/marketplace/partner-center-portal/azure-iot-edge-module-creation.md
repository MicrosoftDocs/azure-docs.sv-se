---
title: Skapa ett erbjudande för Azure IoT Edge-modul med partner Center på Azure Marketplace
description: Lär dig hur du skapar, konfigurerar och publicerar ett IoT Edge modul-erbjudande på Azure Marketplace med hjälp av Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 9f07a6376e3fdde46e9e0cb161acc97c5771d905
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166759"
---
# <a name="create-an-iot-edge-module-offer"></a>Skapa ett IoT Edge-modulerbjudande

Den här artikeln beskriver hur du skapar och publicerar en Sakernas Internet (IoT) Edge-modul för Azure Marketplace. Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md) , om du inte har gjort det än. Se till att ditt konto är registrerat i programmet för kommersiella marknads platser.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På menyn till vänster-navigerings väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan Översikt väljer du **+ ny erbjudande**  >  **IoT Edge modul**.

    ![Visar menyn till vänster-navigering.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
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

Den här sidan innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Exempel:

- Om erbjudandet är ett utkast – ta bort utkast erbjudande
- Om erbjudandet är Live- [sluta att sälja erbjudandet](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version- [Go-Live](../review-publish-offer.md#previewing-and-approving-your-offer)
- Om du inte har slutfört Publisher-utloggning – [Avbryt publicering.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Erbjudande konfiguration

Följ dessa steg för att konfigurera erbjudandet.

### <a name="customer-leads"></a>Kund ledare

När du publicerar ditt erbjudande till Marketplace med partner Center kan du också ansluta det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Om ditt CRM-system inte finns med i listan ovan använder du [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) eller [https-slutpunkten](commercial-marketplace-lead-management-instructions-https.md) för att lagra kundens lead-data och sedan exportera data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. Medan du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka köpa erbjudandet själv i för hands versionen.
4. Se till att anslutningen till målet för målet förblir uppdaterad så att du inte förlorar några leads.

Här följer några ytterligare resurser för ledar hantering:

- [Kundleads från ditt erbjudande på den kommersiella marknadsplatsen](commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor om ledar hantering](../lead-management-faq.md#common-questions-about-lead-management)
- [Felsöka fel vid lead-konfiguration](../lead-management-faq.md#publishing-config-errors)
- [Översikt över ledar hantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (se till att blockering av popup-fönster är inaktiverat).

Välj **Spara utkast** innan du fortsätter.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på Marketplace och de juridiska kontrakt som har stöd för ditt erbjudande.

#### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md). I Marketplace visas IoT Edge moduler alltid under kategorin  **Sakernas Internet**  >  **IoT Edge**   .

#### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Du kan välja mellan två alternativ:

- Använd standard kontraktet för Microsofts kommersiella marknads plats.
- Ange egna villkor.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard kontrakt för Microsofts kommersiella marknads plats

Vi erbjuder en standard kontrakts mal len som hjälper till att under lätta transaktioner i den kommersiella marknads platsen. Du kan välja att erbjuda din lösning under standard kontraktet, som kunder bara behöver kontrol lera och godkänna en gång. Det här är ett bra alternativ om du inte vill konfigurera anpassade villkor.

Mer information om standard kontraktet finns i standard- [kontrakt för Microsoft Commercial Marketplace](../standard-contract.md). Du kan också ladda ned [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (se till att blockering av popup-fönster är inaktiverat).

Om du vill använda standard kontraktet markerar du kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** och klickar sedan på **acceptera**.

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för Microsoft Marketplace kan du inte använda dina egna anpassade villkor. Erbjud antingen lösningen enligt standard kontraktet eller enligt dina egna villkor.

![Visar hur du använder standard kontraktet för Microsofts handels Marketplace-kryss ruta.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Egna villkor

Om du vill ange egna anpassade villkor anger du dem i rutan **allmänna** villkor. Du kan ange en obegränsad mängd tecken text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, erbjud listan.

## <a name="offer-listing"></a>Erbjudande lista

Här definierar du de erbjudande uppgifter som visas i Marketplace. Detta inkluderar erbjudandets namn, beskrivning, bilder och så vidare. Se till att följa de principer som beskrivs på Microsofts princip sida när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudande information behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det går också att tillhandahålla en användbar länk för att erbjuda innehåll på ett annat språk än det som används i listan med erbjudande information.

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler).
- Får inte vara mer än 50 tecken långt.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

### <a name="long-summary"></a>Lång Sammanfattning

Ange en mer detaljerad beskrivning av ditt erbjudande. Detta kan vara upp till 256 tecken långt och används i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge-modulens erbjudanden måste innehålla ett stycke med minsta maskin varu krav längst ned i beskrivningen, till exempel:

- Minimi krav för maskin vara: linux x64 och ARM32 OS, 1 GB RAM, 500 MB lagrings utrymme

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange webb adressen till din organisations sekretess policy. Du är ansvarig för att se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du ansvarar också för att publicera en giltig sekretess policy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Ge kompletterande online-dokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Title** – kunder kommer att se rubriken på ditt erbjudandes informations sida.
- **Länk (URL)** – ange en länk till kunder för att visa ditt online-dokument. Länken måste börja med `http://` eller `https://` .

Se till att lägga till minst en länk till din dokumentation och en länk till de kompatibla IoT Edge enheterna från [Azure IoT-katalogen](https://devicecatalog.azure.com/).

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt.** Den här informationen visas inte för kunderna. Den är tillgänglig för Microsoft och kan tillhandahållas av CSP-partner (Cloud Solution Provider).

- Support kontakt (krävs): för allmänna supportfrågor.
- Teknisk kontakt (krävs): för tekniska frågor och certifierings problem.
- Kontakta CSP-programmet (valfritt): för åter försäljar frågor som är relaterade till CSP-programmet.

I avsnittet **support kontakt** anger du webb adressen till **Support webbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i Global Azure, Azure Government eller båda.

I avsnittet för **CSP** -programavsnittet anger du länken (**CSP-programmet marknadsförings material**) där CSP-partner kan hitta marknadsförings material för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

Om du vill veta mer om att skapa erbjudande listor, se [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att avvisas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Medel** (90 x 90 BPT, valfritt)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Skärm dum par (valfritt)

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 pixlar i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Ange videons namn, webb adressen och en miniatyr bild av videon med 1280 x 720 pixlar i storlek.

#### <a name="marketplace--examples"></a>Marketplace-exempel

Här är ett exempel på hur information om erbjudandet visas på Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Stor logo typ
2. Kategorier
3. Support adress (länk)
4. Villkor
5. Sekretess policy adress (länk)
6. Name
7. Sammanfattning
8. Beskrivning
9. Användbara länkar
10. Skärm bilder/videor

<br>Här är ett exempel på hur information om erbjudandet visas i Sök resultaten för Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Visar hur det här erbjudandet visas i Sök resultaten för Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logo typ
2. Erbjudandets namn
3. Sammanfattning av Sök Resultat

<br>Här är ett exempel på hur information om erbjudandet visas i Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Name
2. Beskrivning
3. Användbara länkar
4. Skärmbilder

<br>Här är ett exempel på hur information om erbjudandet visas i Azure Portal Sök Resultat:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal Sök resultat.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logo typ
2. Erbjudandets namn
3. Sammanfattning av Sök Resultat

<br>Välj **Spara utkast** innan du fortsätter till nästa avsnitt, förhands granskning.

## <a name="preview"></a>Förhandsgranskning

På **fliken förhands granskning** kan du välja en begränsad **förhands gransknings grupp** för att verifiera ditt erbjudande innan du publicerar det Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> När du har granskat erbjudandet i för hands versionen måste du välja **Go Live** för att publicera ditt erbjudande till allmänheten.

Ange din förhands gransknings mål med ID: n för Azure-prenumerations-ID, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt prenumerations-ID för Azure på sidan prenumerationer i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID: n definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, plan översikt.

## <a name="plan-overview"></a>Plan översikt

På den här fliken kan du ange olika prenumerations alternativ i samma erbjudande i Partner Center. Planer (tidigare kallade SKU: er) kan variera beroende på vilka moln som är tillgängliga, till exempel globala moln, offentliga moln och den avbildning som planen refererar till. Om du vill visa ditt erbjudande i Marketplace måste du konfigurera minst en plan.

Du kan skapa upp till 100-planer för varje erbjudande: upp till 45 av dessa kan vara privata. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](../private-offers.md).

När du har skapat dina planer visas fliken **plan översikt** :

- Planera namn
- Prismodell
- Azure-regioner (globala eller myndigheter)
- Aktuell publicerings status
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i plan översikten varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast**: om planens status är ett utkast.
- **Stoppa Sälj plan**: om plan statusen publiceras Live.

### <a name="create-new-plan"></a>Skapa ny plan

Välj **Skapa ny plan**. Dialog rutan **nytt plan** visas.

Skapa ett unikt plan-ID för varje plan i det här erbjudandet i rutan **plan-ID** . Detta ID visas för kunder i produktens webb adress. Använd bara gemena bokstäver och siffror, bindestreck eller under streck och högst 50 tecken.

I rutan **plan namn** anger du ett namn för den här planen. Kunderna ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda ett erbjudande namn för **Windows Server** med planer för **Windows Server 2016** och **Windows Server 2019**.

> [!NOTE]
> Det går inte att ändra plan-ID när du har valt **skapa**.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera installationen

På den här fliken kan du konfigurera vilka moln som planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="azure-regions"></a>Azure-regioner

Alla planer för IoT Edge modul-erbjudanden görs automatiskt tillgängliga i **Azure Global**.  Din prenumeration kan användas av kunder i alla globala Azure-regioner som använder Marketplace. Mer information finns i [stöd för geografisk tillgänglighet och valuta](../marketplace-geo-availability-currencies.md).

Välj alternativet [Azure Government](../../azure-government/documentation-government-welcome.md) för att se till att din lösning visas här. Det här är ett offentligt community-moln med kontrollerad till gång för kunder från amerikanska federala, statliga och lokala myndigheter eller stambaserad myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare är du ansvarig för alla kontroller, säkerhets åtgärder och bästa praxis för moln gruppen. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA). Innan du [publicerar](../../azure-government/documentation-government-manage-marketplace-partners.md) till Azure Government ska du testa och bekräfta din lösning inom det här avsnittet eftersom resultatet kan skilja sig. Om du vill mellanlagra och testa din lösning kan du begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> När planen har publicerats och är tillgänglig i en speciell region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om **Azure Government** har valts under **Azure-regioner**.

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du tillhandahålla upp till 100 länkar som beskriver dina certifieringar. Det kan vara länkar till dina program tablåer direkt eller till din egen webbplats. Dessa länkar är bara synliga för Azure Government kunder.

### <a name="plan-listing"></a>Plan lista

På den här fliken visas särskild information för varje plan inom samma erbjudande.

### <a name="plan-name"></a>Plan namn

Detta är ifyllt med det namn som du gav din plan när du skapade det. Du kan ändra det här namnet om det behövs. Det kan vara upp till 50 tecken långt. Det här namnet visas som rubrik för den här planen i Azure Marketplace och Azure Portal. Den används som standardmodulens namn när planen är klar att användas.

### <a name="plan-summary"></a>Plan Sammanfattning

Ange en kort sammanfattning av planen (inte erbjudandet). Den här sammanfattningen visas i Sök resultaten i Azure Marketplace och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Beskrivning av plan

Beskriv vad som gör den här planen unik, samt skillnader mellan planer i erbjudandet. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas på Azure Marketplace och i Azure Portal på sidan erbjudande lista. Det kan vara samma innehåll som du angav i plan sammanfattningen och innehåller upp till 2 000 tecken.

Välj **Spara utkast** när du har slutfört fälten.

#### <a name="plan-examples"></a>Planera exempel

Här är ett exempel på Azure Marketplace-plan information (alla noterade priser är till exempel endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Visar information om Azure Marketplace-planer.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Erbjudandets namn
2. Plan namn
3. Beskrivning av plan

<br>Här är ett exempel på Azure Portal Plans information (alla noterade priser är till exempel endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Visar Azure Portal Plans information.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Erbjudandets namn
2. Plan namn
3. Beskrivning av plan

### <a name="availability"></a>Tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunder inte kan söka, bläddra i eller köpa det på Marketplace, markerar du kryss rutan **Dölj plan** på fliken tillgänglighet.

Det här fältet används ofta när:

- Erbjudandet är avsett att användas endast indirekt när det hänvisas till ett annat program.
- Erbjudandet ska inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongs erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

Typen **IoT Edge module** -erbjudande är en speciell typ av behållare som körs på en IoT Edge enhet. På fliken **teknisk konfiguration** tillhandahåller du referensinformation för din behållar avbildnings lagrings plats i [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), tillsammans med konfigurations inställningar som gör att kunderna enkelt kan använda modulen.

När erbjudandet har publicerats kopieras din IoT Edge behållar avbildning till Azure Marketplace i ett särskilt offentligt behållar register. Alla begär Anden från Azure-användare till att använda modulen hanteras från Azure Marketplace-registret för offentliga behållare, inte ditt privata behållar register.

Du kan rikta flera plattformar och tillhandahålla flera versioner av modulens behållar avbildning med hjälp av taggar. Mer information om taggar och versioner finns i [förbereda din IoT Edge-modul tekniska till gångar](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Information om avbildnings lager

Du kommer att ange följande information på fliken **avbildnings lagrings information** .

**Välj avbildnings källa**: välj alternativet **Azure Container Registry** .

**Azure-prenumerations-ID**: Ange det PRENUMERATIONS-ID där resursanvändningen rapporteras och tjänsterna debiteras för den Azure Container Registry som innehåller din behållar avbildning. Du hittar detta ID på [sidan prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal.

**Namn på Azure-resurs grupp**: Ange det [resurs grupps](../../azure-resource-manager/management/manage-resource-groups-portal.md) namn som innehåller Azure Container Registry med din behållar avbildning. Resurs gruppen måste vara tillgänglig i prenumerations-ID (ovan). Du hittar namnet på sidan [resurs grupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure Portal.

**Azure Container Registry-namn**: Ange namnet på [Azure Container Registry](../../container-registry/container-registry-intro.md) som innehåller din behållar avbildning. Behållar registret måste finnas i den Azure-resurs grupp du angav tidigare. Ange bara register namnet, inte det fullständiga inloggnings Server namnet. Var noga med att utelämna **azurecr.io** från namnet. Du hittar register namnet på [sidan behållar register](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) i Azure Portal.

**Administratörens användar namn för Azure Container Registry**: ange [administratörens användar namn](../../container-registry/container-registry-authentication.md#admin-account)) som är associerat med den Azure Container Registry som har behållar avbildningen. Användar namn och lösen ord krävs för att se till att ditt företag har åtkomst till registret. Om du vill hämta administratörens användar namn och lösen ord anger du egenskapen **admin-Enabled** till **True** med Azure Command-Line Interface (CLI). Du kan också ange att **Administratörs användare** ska kunna **Aktivera** i Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Visar dialog rutan uppdatera container Registry.":::

#### <a name="call-out-description"></a>Beskrivning av anrop

1. Administratörsanvändare

<br>**Lösen ord för Azure Container Registry**: Ange lösen ordet för det administratörs användar namn som är associerat med Azure Container Registry och har behållar avbildningen. Användar namn och lösen ord krävs för att se till att ditt företag har åtkomst till registret. Du kan hämta lösen ordet från Azure Portal genom att gå till **container Registry**  >  **åtkomst nycklar** eller med Azure CLI med [Kommandot show.](/cli/azure/acr/credential#az-acr-credential-show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Visar åtkomst nyckel skärmen i Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Åtkomstnycklar
2. Användarnamn
3. Lösenord

**Databas namn inom Azure Container Registry**. Ange namnet på Azure Container Registry lagrings platsen som innehåller din avbildning. Du anger namnet på lagrings platsen när du push-överför avbildningen till registret. Du kan hitta namnet på lagrings platsen genom att gå till sidan [container Registry](https://azure.microsoft.com/services/container-registry/)-  >  **databaser**. Mer information finns i [Visa behållare register Arkiv i Azure Portal](../../container-registry/container-registry-repositories.md). När du har angett namnet kan du inte ändra det. Använd ett unikt namn för varje erbjudande i ditt konto.

> [!NOTE]
> Vi stöder inte krypterad Azure Container Registry för certifiering av Edge-modul. Azure Container Registry ska skapas utan kryptering aktiverat.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunderna måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna stanna kvar på en angiven version av avbildningen. Du kan göra detta genom att lägga till nya avbildnings etiketter varje gången du gör en uppdatering av avbildningen.

**Bildtagg**. Det här fältet måste innehålla en **senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Det måste också innehålla en versions tagg (till exempel från xx. xx. xx, där xx är ett tal). Kunderna bör använda [manifest Taggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla Taggar som en manifest tag refererar till måste också läggas till så att vi kan ladda upp dem. Alla manifest Taggar (förutom den senaste taggen) måste börja med antingen X. Y-eller X. Y. Z – där X, Y och Z är heltal. Om till exempel en senaste tagg pekar på 1.0.1-linux-x64, 1.0.1-Linux-ARM32 och 1.0.1-Windows-ARM32, måste dessa sex Taggar läggas till i det här fältet. Mer information om taggar och versioner finns i [förbereda din IoT Edge module tekniska till gångar.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Standard distributions inställningar (valfritt)

Definiera de vanligaste inställningarna för att distribuera IoT Edge-modulen. Optimera kund distributioner genom att låta dem starta IoT Edge modulen med dessa standardinställningar.

**Standard vägar**. IoT Edge Hub hanterar kommunikation mellan moduler, IoT Hub och enheter. Du kan ange vägar för indata och utdata mellan moduler och IoT Hub, vilket ger dig flexibiliteten att skicka meddelanden där de behöver gå utan att ytterligare tjänster krävs för att bearbeta meddelanden eller skriva ytterligare kod. Vägar konstrueras med hjälp av namn/värde-par. Du kan definiera upp till fem standard väg namn, med upp till 512 tecken.

Se till att du använder rätt [Route-syntax](../../iot-edge/module-composition.md#declare-routes)) i ditt väg värde (definieras vanligt vis från/meddelande/* i $upstream). Det innebär att alla meddelanden som skickas av moduler går till din IoT Hub. Om du vill referera till din modul använder du dess standardmodulnamn, som är **namnet på erbjudandet**, utan blank steg eller specialtecken. Om du vill referera till andra moduler som ännu inte är kända använder du <FROM_MODULE_NAME> konvention för att låta dina kunder veta att de behöver uppdatera den här informationen. Mer information om IoT Edge vägar finns i [deklarera vägar](../../iot-edge/module-composition.md#declare-routes).

Om modul ContosoModule till exempel lyssnar efter indata på ContosoInput och utdata från ContosoOutput, är det klokt att definiera följande två standard vägar:

- Namn #1: ToContosoModule
- Värde #1: från/Messages/modules/<FROM_MODULE_NAME>/outputs/* till BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Namn #2: FromContosoModuleToCloud
- Värde #2: från/messages/modules/ContonsoModule/outputs/ContosoOutput till $upstream

**Standardmodul, dubbla önskade egenskaper**. En modul i är ett JSON-dokument i IoT Hub som lagrar Tillståndsinformation för en modul-instans, inklusive önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera konfiguration eller villkor för modulen. Lösningens Server del kan ange önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot ändrings meddelanden i önskade egenskaper. Önskade egenskaper skapas med upp till fem namn/värde-par och varje standardvärde måste innehålla färre än 512 tecken. Du kan definiera upp till fem namn/värde-dubbla önskade egenskaper. Värdena för de dubbla önskade egenskaperna måste vara giltiga JSON, icke-Escaped, utan matriser med en maximal kapslad hierarki på fyra nivåer. I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kund Server) kan du lägga till en parameter som standardvärde. Mer information om de dubbla önskade egenskaperna finns i [definiera eller uppdatera önskade egenskaper](../../iot-edge/module-composition.md#define-or-update-desired-properties).

Om en modul till exempel stöder dynamiskt konfigurerbara uppdaterings hastigheter med hjälp av dubbla önskade egenskaper, är det klokt att definiera följande standardvärden för den dubbla önskade egenskapen:

- Namn #1: RefreshRate
- Värde #1:60

**Standardvariabler för miljön**. Miljövariabler ger kompletterande information till en modul som hjälper dig att konfigurera processen. Miljövariabler skapas med hjälp av namn/värde-par. Varje standard miljö variabel namn och-värde måste innehålla färre än 512 tecken och du kan definiera upp till fem. När en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kund Server) kan du lägga till en parameter som standardvärde.

Om en modul till exempel måste godkänna användnings villkoren innan den startas, kan du definiera följande miljö variabel:

- Namn #1: ACCEPT_EULA
- Värde #1: Y

**Standard alternativ för att skapa behållare**. Med alternativ för att skapa behållare är det enkelt att skapa IoT Edge modulens Docker-behållare. IoT Edge stöder alternativ för Docker-motorn för att skapa behållare. Se alla alternativ i [list behållare.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Fältet för att skapa alternativ måste vara giltigt JSON, icke-Escaped och färre än 512 tecken.

Om en modul till exempel kräver port bindning definierar du följande alternativ för att skapa:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka det till granskning och publicering.

I det övre högra hörnet av portalen väljer du **Granska och publicera**.

På sidan Granska kan du se publicerings status:

- Se slut för ande status för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har marker ATS som slutförda.
    - **Inte startad** -avsnittet har inte startats och måste slutföras.
    - **Ofullständig** – avsnittet innehåller fel som måste åtgärdas eller kräver att du anger mer information. Se avsnittet tidigare i det här dokumentet för vägledning.
    - **Slutfört** -avsnittet innehåller alla data som krävs och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- Tillhandahåll test instruktioner till certifierings teamet för att se till att ditt erbjudande testas korrekt. Ange också eventuella kompletterande kommentarer som hjälper dig att förstå ditt erbjudande.

Om du vill skicka publicerings erbjudandet väljer du **publicera**.

Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Om du vill publicera ditt erbjudande till allmänheten går du till Partner Center och väljer **Go-Live**.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](update-existing-offer.md)
