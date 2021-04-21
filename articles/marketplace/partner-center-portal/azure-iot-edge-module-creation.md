---
title: Skapa ett Azure IoT Edge-modulerbjudande med Partnercenter i Azure Marketplace
description: Lär dig hur du skapar, konfigurerar och publicerar ett IoT Edge-modulerbjudande i Azure Marketplace med partnercenter.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 12600cadaa84ae116818eec06459d5db0c05304a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773428"
---
# <a name="create-an-iot-edge-module-offer"></a>Skapa ett IoT Edge-modulerbjudande

Den här artikeln beskriver hur du skapar och publicerar ett Sakernas Internet(IoT) Edge-modulerbjudande för Azure Marketplace. Innan du börjar [skapar du ett Commercial Marketplace-konto i Partnercenter](../create-account.md) om du inte har gjort det ännu. Kontrollera att ditt konto har registrerats i programmet för den kommersiella marknadsplatsen.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [Partner Center.](https://partner.microsoft.com/dashboard/home)
2. I den vänstra navigeringsmenyn väljer du **Översikt över Kommersiell**  >  **marknadsplats.**
3. På sidan Översikt väljer du **+ Nytt erbjudande IoT Edge**  >  **modul**.

    ![Visar den vänstra navigeringsmenyn.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> När ett erbjudande har publicerats visas ändringar som gjorts i Partnercenter endast i onlinebutiker efter att erbjudandet har publicerats på nytt. Se till att du alltid publicerar på nytt när du har gjort ändringar.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID.** Det här är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och understreck, men inga blanksteg och är begränsat till 50 tecken. Om du till exempel anger **test-offer-1** blir erbjudandets webbadress `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID kan inte ändras när du har valt **Skapa.**

Ange ett **erbjudandealias.** Det här är det namn som används för erbjudandet i Partnercenter.

- Det här namnet används inte på Marketplace och skiljer sig från erbjudandets namn och andra värden som visas för kunder.
- Detta kan inte ändras när du har valt **Skapa**.

Välj **Skapa** för att generera erbjudandet och fortsätta.

## <a name="offer-overview"></a>Översikt över erbjudande

På **sidan Översikt över** erbjudande visas en visuell representation av de steg som krävs för att publicera erbjudandet (både slutförda och kommande) och hur lång tid varje steg bör ta att slutföra.

Den här sidan innehåller länkar för att utföra åtgärder med det här erbjudandet baserat på det val du gör. Exempel:

- Om erbjudandet är ett utkast – Ta bort utkasterbjudande
- Om erbjudandet är live – [Sluta sälja erbjudandet](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsversion – [Go-Live](../review-publish-offer.md#previewing-and-approving-your-offer)
- Om du inte har slutfört utgivarut logga ut – [Avbryt publicering.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguration av erbjudande

Följ de här stegen för att konfigurera ditt erbjudande.

### <a name="customer-leads"></a>Kundlyssnare

När du publicerar ditt erbjudande på Marketplace med Partnercenter kan du välja att ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du ta emot kundkontaktinformation så snart någon uttrycker intresse för eller använder din produkt.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partnercenter stöder följande CRM-system:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Om ditt CRM-system inte visas ovan använder du [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) eller [Https-slutpunkt](commercial-marketplace-lead-management-instructions-https.md) för att lagra kund leaddata och exporterar sedan data till ditt CRM-system.

2. Anslut ditt erbjudande till leadmålet när du publicerar i Partnercenter.
3. Bekräfta att anslutningen till leadmålet är korrekt konfigurerad. När du har publicerat den i Partnercenter verifierar vi anslutningen och skickar dig en test lead. När du förhandsgranskar erbjudandet innan det börjar live kan du även testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionsmiljön.
4. Kontrollera att anslutningen till leadmålet förblir uppdaterad så att du inte förlorar några leads.

Här är några ytterligare leadhanteringsresurser:

- [Kundleads från ditt erbjudande på den kommersiella marknadsplatsen](commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor om leadhantering](../lead-management-faq.md#common-questions-about-lead-management)
- [Felsöka konfigurationsfel för lead](../lead-management-faq.md#publishing-config-errors)
- [Översikt över leadhantering](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Kontrollera att blockering av popup-fönster är inaktiverat).

Välj **Spara utkast innan** du fortsätter.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på marknadsplatsen och de juridiska kontrakt som stöder ditt erbjudande.

#### <a name="category"></a>Kategori

Välj kategorier och underkategorier för att placera ditt erbjudande i lämpliga sökområden på Marketplace. Se till att beskriva hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två underkategorier för varje primär och/eller sekundär kategori. Om ingen underkategori gäller för ditt erbjudande väljer du **Ej tillämpligt.**

Se den fullständiga listan över kategorier och underkategorier i [Metodtips för erbjudandelista.](../gtm-offer-listing-best-practices.md) På marketplace visas IoT Edge moduler alltid under  **** Sakernas Internet  >  **IoT Edge**   modulkategorin.

#### <a name="legal"></a>Juridisk information

Du måste ange allmänna villkor för erbjudandet. Du kan välja mellan två alternativ:

- Använd Standardavtal Microsoft Commercial Marketplace.
- Ange dina egna villkor.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardavtal för Microsoft Commercial Marketplace

Vi erbjuder en Standardavtal mall för att underlätta transaktioner på den kommersiella marknadsplatsen. Du kan välja att erbjuda din lösning under Standardavtal, som kunderna bara behöver kontrollera och godkänna en gång. Det här är ett bra alternativ om du inte vill skapa anpassade villkor.

Mer information om Standardavtal finns i [Standardavtal för Microsoft Commercial Marketplace.](../standard-contract.md) Du kan också ladda [ned Standardavtal](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-filen (kontrollera att blockeringen av popup-fönster är inaktiverad).

Om du vill Standardavtal du kryssrutan **Använd Standardavtal för Microsofts** kommersiella marknadsplats och klickar sedan på **Acceptera.**

> [!NOTE]
> När du har publicerat ett erbjudande med standardavtalet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Du kan antingen erbjuda Standardavtal under Standardavtal eller enligt dina egna villkor.

![Visar med hjälp Standardavtal för Microsofts kommersiella marknadsplats.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Dina egna villkor

Ange dina egna anpassade villkor genom att ange dem i **rutan Allmänna** villkor. Du kan ange ett obegränsat antal tecken med text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Erbjudandelista.

## <a name="offer-listing"></a>Erbjudandelista

Här definierar du erbjudandeinformationen som visas på Marketplace. Detta inkluderar erbjudandenamn, beskrivning, bilder och så vidare. Se till att följa de principer som beskrivs på Microsofts principsida när du konfigurerar det här erbjudandet.

> [!NOTE]
> Erbjudandeinformation behöver inte vara på engelska om erbjudandebeskrivningen börjar med frasen "Det här programmet är endast tillgängligt på [icke-engelska språk]." Det är också okej att tillhandahålla en användbar länk för att erbjuda innehåll på ett språk som skiljer sig från det som används i informationen om erbjudandelistan.

### <a name="name"></a>Name

Det namn som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i med den text som du angav i **rutan Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärken (och du kan inkludera varumärken eller upphovsrättssymboler).
- Får inte vara längre än 50 tecken.
- Kan inte innehålla emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

Ange en kort beskrivning av ditt erbjudande. Det kan vara upp till 100 tecken långt och används i Sökresultat på Marketplace.

### <a name="long-summary"></a>Lång sammanfattning

Ge en mer detaljerad beskrivning av ditt erbjudande. Det kan vara upp till 256 tecken långt och används i Sökresultat på Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge måste innehålla ett stycke med minimikrav för maskinvara längst ned i beskrivningen, till exempel:

- Minimikrav på maskinvara: Linux x64 och arm32 OS, 1 GB RAM-minne, 500 MB lagringsutrymme

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>WEBBADRESS till sekretesspolicy

Ange webbadressen till din organisations sekretesspolicy. Du ansvarar för att se till att ditt erbjudande följer sekretesslagar och -föreskrifter. Du ansvarar också för att publicera en giltig sekretesspolicy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Ange kompletterande onlinedokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk **väljer du + Lägg till en** länk och fyller sedan i följande fält:

- **Rubrik** – Kunder ser rubriken på informationssidan för ditt erbjudande.
- **Länk (URL) –** Ange en länk för kunder för att visa ditt onlinedokument. Länken måste börja med `http://` eller `https://` .

Se till att lägga till minst en länk till dokumentationen och en länk till IoT Edge kompatibla enheter från [Azure IoT-enhetskatalogen.](https://devicecatalog.azure.com/)

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **supportkontakt och** en **teknisk kontakt.** Den här informationen visas inte för kunder. Den är tillgänglig för Microsoft och kan tillhandahållas till Molnlösningsleverantör-partner (CSP).

- Supportkontakt (krävs): För allmänna supportfrågor.
- Teknisk kontakt (krävs): För tekniska frågor och certifieringsproblem.
- CSP-programkontakt (valfritt): För återförsäljarfrågor som rör CSP-programmet.

I avsnittet **Supportkontakt** anger du webbadressen till **supportwebbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i globala Azure, Azure Government eller både och.

I **kontaktavsnittet för CSP-programmet** anger du länken (**CSP Program Marketing Materials**) där CSP-partner kan hitta marknadsföringsmaterial för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för Marketplace-listor

Mer information om hur du skapar erbjudandelistor finns i [Metodtips för erbjudandelistor.](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logotyper och bilder som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder avvisas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten https://upload.xboxlive.com som används av Partnercenter.

#### <a name="store-logos"></a>Butikslogotyp

Ange en PNG-fil för **logotypen Stor** storlek. Partner center använder detta för att skapa en **liten** och en **medelstor** logotyp. Du kan också ersätta dem med olika bilder senare.

- **Stor** (från 216 x 216 till 350 x 350 px, krävs)
- **Medel** (90 x 90 px, valfritt)
- **Liten** (48 x 48 px, valfritt)

Dessa logotyper används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Skärmbilder (valfritt)

Lägg till upp till fem skärmbilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 bildpunkter i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som visar ditt erbjudande. Ange videons namn, webbadress och en PNG-miniatyrbild av videon med storleken 1 280 x 720 bildpunkter.

#### <a name="marketplace--examples"></a>Marketplace-exempel

Här är ett exempel på hur erbjudandeinformation visas i Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Visar hur det här erbjudandet visas i Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av uppringning

1. Stor logotyp
2. Kategorier
3. Supportadress (länk)
4. Villkor
5. Sekretesspolicysadress (länk)
6. Name
7. Sammanfattning
8. Description
9. Användbara länkar
10. Skärmbilder/videor

<br>Här är ett exempel på hur erbjudandeinformation visas i Azure Marketplace sökresultat:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Visar hur det här erbjudandet visas Azure Marketplace i sökresultaten.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logotyp
2. Erbjudandets namn
3. Sammanfattning av sökresultat

<br>Här är ett exempel på hur erbjudandeinformation visas i Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Name
2. Beskrivning
3. Användbara länkar
4. Skärmbilder

<br>Här är ett exempel på hur erbjudandeinformation visas i Azure Portal sökresultat:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Illustrerar hur det här erbjudandet visas Azure Portal i sökresultaten.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logotyp
2. Erbjudandets namn
3. Sammanfattning av sökresultat

<br>Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Förhandsversion.

## <a name="preview"></a>Förhandsgranskning

På fliken **Förhandsgranska kan** du välja en begränsad målgrupp för förhandsversionen **för** att verifiera ditt erbjudande innan du publicerar det live för den bredare Marketplace-målgruppen.

> [!IMPORTANT]
> När du har tittat på ditt erbjudande i förhandsversionen måste du **välja Go live** för att publicera erbjudandet till allmänheten.

Ange din målgrupp för förhandsversionen med hjälp av ID-GUID för Azure-prenumeration, tillsammans med en valfri beskrivning för var och en. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt Azure-prenumerations-ID på sidan Prenumerationer i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till de här prenumerations-ID:erna definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras live. Om ditt erbjudande redan är live kan du definiera en målgrupp för förhandsversionen för att testa ändringar eller uppdateringar av ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Planera översikt.

## <a name="plan-overview"></a>Översikt över plan

På den här fliken kan du ange olika planalternativ inom samma erbjudande i Partnercenter. Planer (kallades tidigare SKU:er) kan skilja sig åt vad gäller vilka moln som är tillgängliga, till exempel globala moln, myndighetsmoln och den avbildning som planen refererar till. Om du vill visa ditt erbjudande på Marketplace måste du konfigurera minst en plan.

Du kan skapa upp till 100 planer för varje erbjudande: upp till 45 av dessa kan vara privata. Läs mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknadsplats.](../private-offers.md)

När du har skapat dina planer visas **följande på fliken Planera** översikt:

- Plannamn
- Prismodell
- Azure-regioner (globala eller myndighetsregioner)
- Aktuell publiceringsstatus
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i översikten över planen varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast:** Om planens status är ett utkast.
- **Stoppa säljplanen:** Om planens status publiceras live.

### <a name="create-new-plan"></a>Skapa en ny plan

Välj **Skapa ny plan.** Dialogrutan **Ny plan** visas.

I rutan **Plan-ID** skapar du ett unikt plan-ID för varje plan i det här erbjudandet. Det här ID:t visas för kunder i produktwebbadressen. Använd endast gemener och siffror, bindestreck eller understreck och högst 50 tecken.

I rutan **Plannamn** anger du ett namn för planen. Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas i ditt erbjudande. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda erbjudandenamnet **Windows Server** med planerna Windows **Server 2016** och **Windows Server 2019**.

> [!NOTE]
> Det går inte att ändra plan-ID:t när du har valt **Skapa**.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera konfiguration

På den här fliken kan du konfigurera vilka moln som planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="azure-regions"></a>Azure-regioner

Alla planer för IoT Edge-modulerbjudanden görs automatiskt tillgängliga i **Azure Global**.  Din plan kan användas av kunder i alla globala Azure-regioner som använder marknadsplatsen. Mer information finns i [Geografisk tillgänglighet och valutastöd.](../marketplace-geo-availability-currencies.md)

Välj alternativet [Azure Government](../../azure-government/documentation-government-welcome.md) så att lösningen visas här. Det här är ett myndighetsmoln med kontrollerad åtkomst för kunder från amerikanska federala, statliga och lokala eller stamstyrda myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare ansvarar du för alla efterlevnadskontroller, säkerhetsåtgärder och metodtips för den här moln communityn. Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA). Innan [du](../../azure-government/documentation-government-manage-marketplace-partners.md) publicerar Azure Government bör du testa och bekräfta lösningen inom det området eftersom resultatet kan vara annorlunda. Om du vill mellanse och testa din lösning begär du ett utvärderingskonto [från Microsoft Azure Government utvärderingsversion.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> När planen har publicerats och är tillgänglig i en viss region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om **Azure Government** har valts under **Azure-regioner**.

Azure Government hanterar data som omfattas av vissa myndighetsregler och krav. Till exempel FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att göra dina certifieringar medvetna om dessa program kan du tillhandahålla upp till 100 länkar som beskriver dina certifieringar. Dessa kan vara länkar till dina listor i programmet direkt eller till din egen webbplats. Dessa länkar är synliga för Azure Government kunder.

### <a name="plan-listing"></a>Lista över planer

På den här fliken visas specifik information för varje plan inom samma erbjudande.

### <a name="plan-name"></a>Plannamn

Det här är förfyllt med det namn som du gav planen när du skapade den. Du kan ändra det här namnet efter behov. Det kan vara upp till 50 tecken långt. Det här namnet visas som rubriken för den här planen i Azure Marketplace och Azure Portal. Den används som standardmodulnamn när planen är redo att användas.

### <a name="plan-summary"></a>Plansammanfattning

Ge en kort sammanfattning av din plan (inte erbjudandet). Den här sammanfattningen visas Azure Marketplace sökresultat och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Planbeskrivning

Beskriv vad som gör den här planen unik, samt skillnaderna mellan planer i ditt erbjudande. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas Azure Marketplace och i Azure Portal på sidan med erbjudandelistan. Det kan vara samma innehåll som du angav i plansammanfattningen och innehålla upp till 2 000 tecken.

Välj **Spara utkast när** du har slutfört dessa fält.

#### <a name="plan-examples"></a>Planexempel

Här är ett exempel på Azure Marketplace planinformation (angivna priser är endast exempel och är inte avsedda att återspegla faktiska kostnader):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Visar Azure Marketplace planinformation.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Erbjudandets namn
2. Plannamn
3. Planbeskrivning

<br>Här är ett exempel på Azure Portal planinformation (angivna priser är endast exempel och är inte avsedda att återspegla faktiska kostnader):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Illustrerar Azure Portal planinformation.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Erbjudandets namn
2. Plannamn
3. Planbeskrivning

### <a name="availability"></a>Tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunderna inte kan söka efter, bläddra eller köpa det på Marketplace markerar du kryssrutan Dölj **plan** på fliken Tillgänglighet.

Det här fältet används ofta när:

- Erbjudandet är avsett att endast användas indirekt när det refereras till ett annat program.
- Erbjudandet bör inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongsbaserade erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

Erbjudandetypen **IoT Edge modul** är en specifik typ av container som körs på en IoT Edge enhet. På fliken **Teknisk konfiguration** anger du referensinformation för containeravbildningsdatabasen i [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), tillsammans med konfigurationsinställningar som gör att kunderna enkelt kan använda modulen.

När erbjudandet har publicerats kopieras IoT Edge containeravbildningen till Azure Marketplace ett specifikt offentligt containerregister. Alla begäranden från Azure-användare om att använda din modul Azure Marketplace offentliga containerregistret, inte ditt privata containerregister.

Du kan rikta in dig på flera plattformar och tillhandahålla flera versioner av modulcontaineravbildningen med hjälp av taggar. Mer information om taggar och versionshantering finns i Förbereda IoT Edge [tekniska tillgångar](create-iot-edge-module-asset.md)för modulen .

### <a name="image-repository-details"></a>Information om avbildningslager

Du anger följande information på fliken Information om **avbildningsdatabasen.**

**Välj bildkälla:** Välj alternativet **Azure Container Registry** bildkälla.

**Azure-prenumerations-ID:** Ange det prenumerations-ID där resursanvändningen rapporteras och tjänster debiteras för Azure Container Registry som innehåller containeravbildningen. Du hittar det här ID:t [på sidan Prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal.

**Namn på Azure-resursgrupp:** Ange [resursgruppens](../../azure-resource-manager/management/manage-resource-groups-portal.md) namn som innehåller Azure Container Registry med containeravbildningen. Resursgruppen måste vara tillgänglig i prenumerations-ID:t (ovan). Du hittar namnet på sidan [Resursgrupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure Portal.

**Namn på Azure-containerregister:** Ange namnet på den [Azure Container Registry som](../../container-registry/container-registry-intro.md) innehåller containeravbildningen. Containerregistret måste finnas i den Azure-resursgrupp som du angav tidigare. Ange endast registernamnet, inte det fullständiga inloggningsservernamnet. Se till att utelämna **azurecr.io** från namnet. Du hittar registernamnet på sidan [Containerregister i](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Administratörens användarnamn för Azure Container Registry:** Ange [administratörens användarnamn](../../container-registry/container-registry-authentication.md#admin-account)) som är associerat med Azure Container Registry som har containeravbildningen. Användarnamnet och lösenordet krävs för att se till att ditt företag har åtkomst till registret. Om du vill hämta administratörens användarnamn och lösenord anger du **egenskapen för** administratör aktiverat till **Sant** med hjälp av Azure Command-Line Interface (CLI). Om du vill kan du **ange Aktivera** för **Administratörsanvändare** i Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Illustrerar dialogrutan Uppdatera containerregister.":::

#### <a name="call-out-description"></a>Beskrivning av anrop

1. Administratörsanvändare

<br>**Lösenord för Azure Container Registry:** Ange lösenordet för det administratörsnamn som är associerat med Azure Container Registry och har containeravbildningen. Användarnamnet och lösenordet krävs för att se till att ditt företag har åtkomst till registret. Du kan hämta lösenordet från Azure Portal genom att **gå till Container Registry** åtkomstnycklar eller med Azure CLI med hjälp av kommandot  >   [show.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Visar skärmen för åtkomstnyckel i Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Åtkomstnycklar
2. Användarnamn
3. Lösenord

**Namnet på lagringsplatsen i Azure Container Registry**. Ange namnet på den Azure Container Registry som innehåller avbildningen. Du anger namnet på lagringsplatsen när du push-pushar avbildningen till registret. Du hittar namnet på lagringsplatsen genom att gå [till Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **lagringsplatsen.** Mer information finns i [Visa lagringsplatsen för containerregister i Azure Portal](../../container-registry/container-registry-repositories.md). När namnet har angetts kan det inte ändras. Använd ett unikt namn för varje erbjudande i ditt konto.

> [!NOTE]
> Vi stöder inte krypterade data Azure Container Registry Edge-modulcertifiering. Azure Container Registry ska skapas utan kryptering aktiverat.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunder måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna fortsätta att ha en specifik version av avbildningen. Du kan göra detta genom att lägga till nya bildtaggar varje gång du uppdaterar avbildningen.

**Avbildningstagg**. Det här fältet måste innehålla **en senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Den måste också innehålla en versionstagg (till exempel början med xx.xx.xx, där xx är ett tal). Kunder bör använda [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som en manifesttagg refererar till måste också läggas till så att vi kan ladda upp dem. Alla manifesttaggar (utom den senaste taggen) måste börja med antingen X.Y- eller X.Y.Z– där X, Y och Z är heltal. Om taggen senaste till exempel pekar på 1.0.1-linux-x64, 1.0.1-linux-arm32 och 1.0.1-windows-arm32 måste dessa sex taggar läggas till i det här fältet. Mer information om taggar och versionshantering finns i [Förbereda IoT Edge tekniska tillgångar.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Standarddistributionsinställningar (valfritt)

Definiera de vanligaste inställningarna för att distribuera din IoT Edge modul. Optimera kunddistributioner genom att låta dem starta IoT Edge-modulen med de här standardinställningarna.

**Standardvägar**. Den IoT Edge Hub hanterar kommunikationen mellan moduler, IoT Hub och enheter. Du kan ange vägar för indata och utdata mellan moduler och IoT Hub, vilket ger dig flexibiliteten att skicka meddelanden dit de behöver gå utan att ytterligare tjänster behöver bearbeta meddelanden eller skriva ytterligare kod. Vägar konstrueras med hjälp av namn/värde-par. Du kan definiera upp till fem standardvägnamn, var och en på upp till 512 tecken.

Se till att använda rätt [vägsyntax](../../iot-edge/module-composition.md#declare-routes)) i vägvärdet (vanligtvis definierat som FROM/message/* INTO $upstream). Det innebär att alla meddelanden som skickas av någon modul går till din IoT Hub. Om du vill referera till modulen använder du dess standardmodulnamn, som är **erbjudandets namn,** utan blanksteg eller specialtecken. Om du vill referera till andra moduler som ännu inte är kända använder du <FROM_MODULE_NAME> konvention för att meddela dina kunder att de behöver uppdatera den här informationen. Mer information om IoT Edge finns i [Deklarera vägar](../../iot-edge/module-composition.md#declare-routes)).

Om modulen ContosoModule till exempel lyssnar efter indata på ContosoInput och utdata på ContosoOutput är det klokt att definiera följande två standardvägar:

- Namn #1: ToContosoModule
- Värde #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Namn #2: FromContosoModuleToCloud
- Värde #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Standardmodultvillingens önskade egenskaper**. En modultvilling är ett JSON-dokument i IoT Hub som lagrar tillståndsinformationen för en modulinstans, inklusive önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera modulkonfiguration eller villkor. Lösningens backend kan ange önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot ändringsmeddelanden i önskade egenskaper. Önskade egenskaper skapas med upp till fem namn/värde-par och varje standardvärde måste vara mindre än 512 tecken. Du kan definiera upp till fem önskade egenskaper för namn/värde-tvilling. Värdena för önskade tvillingegenskaper måste vara giltiga JSON, ej rymda, utan matriser med en maximal kapslad hierarki med fyra nivåer. I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) kan du lägga till en parameter som standardvärde. Mer information om önskade tvillingegenskaper finns i [Definiera eller uppdatera önskade egenskaper](../../iot-edge/module-composition.md#define-or-update-desired-properties)).

Om en modul till exempel stöder en dynamiskt konfigurerbar uppdateringshastighet med önskade tvillingegenskaper är det bra att definiera följande önskade egenskap för standardtvillingen:

- Namn #1: RefreshRate
- Värde #1: 60

**Standardmiljövariabler**. Miljövariabler ger kompletterande information till en modul som hjälper konfigurationsprocessen. Miljövariabler skapas med namn/värde-par. Varje standardmiljövariabelnamn och -värde måste vara färre än 512 tecken och du kan definiera upp till fem. När en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) kan du lägga till en parameter som standardvärde.

Om en modul till exempel kräver att användningsvillkoren godkänns innan den startas kan du definiera följande miljövariabel:

- Namn #1: ACCEPT_EULA
- Värde #1: Y

**Standardalternativ för att skapa containrar.** Alternativ för att skapa containrar styr skapandet av IoT Edge Docker-containern. IoT Edge docker-motorns API-alternativ för att skapa container. Se alla alternativ i Lista [containrar.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Fältet För att skapa alternativ måste vara giltigt JSON, ej rymt och innehålla färre än 512 tecken.

Om en modul till exempel kräver portbindning definierar du följande alternativ för att skapa:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in det för att granska och publicera.

I det övre högra hörnet i portalen väljer du **Granska och publicera**.

På granskningssidan kan du se publiceringsstatusen:

- Se slutförandestatus för varje avsnitt av erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har markerats som slutförda.
    - **Inte startad** – avsnittet har inte startats och måste slutföras.
    - **Ofullständig** – Avsnittet innehåller fel som måste åtgärdas eller som kräver att du anger mer information. Se avsnitten tidigare i det här dokumentet för vägledning.
    - **Slutför** – Avsnittet innehåller alla nödvändiga data och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka erbjudandet.
- Ange testinstruktioner till certifieringsteamet för att se till att ditt erbjudande testas korrekt. Ange även eventuella ytterligare anteckningar som är användbara för att förstå ditt erbjudande.

Om du vill skicka erbjudandet för publicering väljer du **Publicera.**

Vi skickar dig ett e-postmeddelande för att meddela dig när en förhandsversion av erbjudandet är tillgänglig för granskning och godkännande. Om du vill publicera ditt erbjudande offentligt går du till Partnercenter och väljer **Go-live**.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande på den kommersiella marknadsplatsen](update-existing-offer.md)
