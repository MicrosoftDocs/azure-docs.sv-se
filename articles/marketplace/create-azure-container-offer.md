---
title: Skapa ett Azure-containererbjudande – Azure Marketplace
description: Lär dig hur du skapar och publicerar ett containererbjudande till Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: dc7a81f1646fc9f51a4e0bcaf37ef61ca669414e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780523"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Skapa ett Azure Container-erbjudande på Azure Marketplace

Den här artikeln beskriver hur du skapar och publicerar ett containererbjudande för Azure Marketplace. Innan du börjar [skapar du ett Commercial Marketplace-konto i Partnercenter](create-account.md) om du inte har gjort det ännu. Kontrollera att ditt konto har registrerats i programmet för den kommersiella marknadsplatsen.

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [Partner Center](https://partner.microsoft.com/dashboard/home).

2. I den vänstra navigeringsmenyn väljer du **Commercial Marketplace** Overview (Översikt över Kommersiell  >  **marknadsplats).**

3. På sidan Översikt väljer du **+ Nytt erbjudande Azure**  >  **Container**.

   ![Visar den vänstra navigeringsmenyn.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
> När ett erbjudande har publicerats visas ändringar som görs i det i Partnercenter endast i onlinebutiker efter att erbjudandet har publicerats på nytt. Se till att du alltid publicerar på nytt när du har gjort ändringar.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID.** Detta är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och understreck, men inga blanksteg och är begränsat till 50 tecken. Om du till exempel anger **test-offer-1** blir erbjudandets webbadress `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID kan inte ändras när du har valt **Skapa.**

Ange ett **erbjudandealias**. Det här är det namn som används för erbjudandet i Partnercenter.

- Det här namnet används inte på Marketplace och skiljer sig från erbjudandets namn och andra värden som visas för kunder.
- Detta kan inte ändras när du har valt **Skapa**.

Välj **Skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-overview"></a>Översikt över erbjudande

På **sidan Översikt över** erbjudande visas en visuell representation av de steg som krävs för att publicera erbjudandet (både slutförda och kommande) och hur lång tid varje steg bör ta att slutföra.

Den här sidan visar olika länkar baserat på erbjudandets aktuella status. Exempel:

- Om erbjudandet är ett utkast – Ta bort utkasterbjudande
- Om erbjudandet är live – [sluta sälja erbjudandet](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsversion – [Go-live](review-publish-offer.md#previewing-and-approving-your-offer)
- Om du inte har slutfört utgivarut logga ut – [Avbryt publicering.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Konfiguration av erbjudande

Följ dessa steg för att konfigurera ditt erbjudande.

### <a name="customer-leads--optional"></a>Kund leads – valfritt

När du publicerar ditt erbjudande på den kommersiella marknadsplatsen med Partnercenter kan du ansluta det till ditt CRM-system (Customer Relationship Management). På så sätt kan du ta emot kundkontaktinformation så snart någon uttrycker intresse för eller använder din produkt.

1. **Välj ett leadmål där du vill att vi ska skicka kund-leads**. Partner Center stöder följande CRM-system:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Om ditt CRM-system inte visas ovan använder du [Azure Table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) eller [Https-slutpunkten](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) för att lagra kund leaddata och exporterar sedan data till ditt CRM-system.

2. Anslut ditt erbjudande till leadmålet när du publicerar i Partnercenter.
3. Bekräfta att anslutningen till leadmålet är korrekt konfigurerad. När du har publicerat den i Partnercenter verifierar vi anslutningen och skickar en test lead till dig. När du förhandsgranskar erbjudandet innan det blir live kan du även testa din leadanslutning genom att försöka köpa erbjudandet själv i förhandsversionsmiljön.
4. Kontrollera att anslutningen till leadmålet förblir uppdaterad så att du inte förlorar några leads.

Här är några ytterligare leadhanteringsresurser:

- [Kundleads från ditt erbjudande på den kommersiella marknadsplatsen](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Vanliga frågor om leadhantering](lead-management-faq.md#common-questions-about-lead-management)
- [Felsöka konfigurationsfel för lead](lead-management-faq.md#publishing-config-errors)
- [Leadhantering – översikt](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Kontrollera att popup-blockeraren är avstängd).

Välj **Spara utkast innan** du fortsätter.

### <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier som används för att gruppera ditt erbjudande på marknadsplatsen och de juridiska kontrakt som stöder ditt erbjudande.

#### <a name="category"></a>Kategori

Välj kategorier och underkategorier för att placera ditt erbjudande i lämpliga sökområden på Marketplace. Se till att beskriva hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två underkategorier för varje primär och/eller sekundär kategori. Om ingen underkategori gäller för ditt erbjudande väljer du **Ej tillämpligt.**

Se den fullständiga listan över kategorier och underkategorier i [Metodtips för erbjudandelistor.](gtm-offer-listing-best-practices.md) Containrar visas alltid under **Containrar** och sedan kategorin **Containeravbildningar.**

#### <a name="legal"></a>Juridisk information

Du måste ange allmänna villkor för erbjudandet. Det finns två alternativ:

- Använd Standardavtal microsofts kommersiella marknadsplats.
- Ange dina egna villkor.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardavtal för Microsofts kommersiella marknadsplats

Vi erbjuder en Standardavtal mall för att underlätta transaktioner på den kommersiella marknadsplatsen. Du kan välja att erbjuda din lösning under Standardavtal, som kunderna bara behöver kontrollera och godkänna en gång. Det här är ett bra alternativ om du inte vill skapa anpassade villkor.

Mer information om Standardavtal finns i [Standardavtal för Microsofts kommersiella marknadsplats.](standard-contract.md) Du kan också ladda [ned Standardavtal](https://go.microsoft.com/fwlink/?linkid=2041178) PDF-filen (kontrollera att blockeringen av popup-fönster är inaktiverad).

Om du vill Standardavtal du alternativet **Använd Standardavtal för Microsofts kommersiella marknadsplats](.. /standard-contract.md)

> [!NOTE]
> När du har publicerat ett erbjudande med standardavtalet för Microsofts kommersiella marknadsplats kan du inte använda dina egna anpassade villkor. Du kan antingen erbjuda Standardavtal under Standardavtal eller enligt dina egna villkor.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Visar kryssrutan Använda Standardavtal för Microsofts kommersiella marknadsplats.":::

##### <a name="your-own-terms-and-conditions"></a>Dina egna villkor

Ange dina egna anpassade villkor genom att ange dem i **rutan Allmänna** villkor. Du kan ange ett obegränsat antal tecken med text i den här rutan. Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Erbjudandelista.

## <a name="offer-listing"></a>Erbjudandelista

På den här sidan kan du definiera erbjudandeinformationen som visas på den kommersiella marknadsplatsen. Detta inkluderar erbjudandets namn, beskrivning och bilder.

> [!NOTE]
> Erbjudandeinformation behöver inte vara på engelska om erbjudandebeskrivningen börjar med frasen "Det här programmet är endast tillgängligt på [icke-engelska språk]." Det är också okej att tillhandahålla en användbar länk för att erbjuda innehåll på ett språk som skiljer sig från det som används i informationen om erbjudandelistan.

### <a name="name"></a>Name

Namnet som du anger här visas som rubrik för ditt erbjudande. Det här fältet fylls i förväg med den text som du angav i **rutan Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara varumärken (och du kan inkludera varumärken och upphovsrättssymboler).
- Får inte vara längre än 50 tecken.
- Kan inte innehålla emojis.

### <a name="search-results-summary"></a>Sammanfattning av sökresultat

En kort beskrivning av ditt erbjudande. Det kan vara upp till 100 tecken långt och används i Sökresultat på Marketplace.

### <a name="long-summary"></a>Lång sammanfattning

En mer detaljerad beskrivning av ditt erbjudande. Det kan vara upp till 256 tecken långt och används i Sökresultat på Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Länk till sekretesspolicy

Ange webbadressen till din organisations sekretesspolicy. Du är ansvarig för att se till att ditt erbjudande uppfyller sekretesslagar och sekretessregler. Du ansvarar också för att publicera en giltig sekretesspolicy på din webbplats.

#### <a name="useful-links"></a>Användbara länkar

Ange kompletterande onlinedokument om ditt erbjudande. Du kan lägga till upp till 25 länkar. Om du vill lägga till en länk **väljer du + Lägg till en** länk och fyller sedan i följande fält:

- **Rubrik** – Kunder ser detta på informationssidan för ditt erbjudande.
- **Länk (URL) –** Ange en länk för kunder för att visa ditt onlinedokument. Länken måste börja med http:// eller https://.

### <a name="contact-information"></a>Kontaktinformation

Du måste ange namn, e-postadress och telefonnummer för en **supportkontakt och** en **teknisk kontakt**. Den här informationen visas inte för kunder, men den är tillgänglig för Microsoft. Den kan också tillhandahållas till Molnlösningsleverantör (CSP)-partner.

- Supportkontakt (krävs): För allmänna supportfrågor.
- Teknisk kontakt (krävs): För tekniska frågor och certifieringsproblem.
- CSP-programkontakt (valfritt): För återförsäljarfrågor som rör CSP-programmet.

I **avsnittet Supportkontakt** anger du **supportwebbplatsen** där partner kan hitta support för ditt erbjudande baserat på om erbjudandet är tillgängligt i globala Azure, Azure Government eller både och.

I **kontaktavsnittet för CSP-programmet** anger du länken ( Marknadsföringsmaterial för **CSP-program**) där CSP-partner kan hitta marknadsföringsmaterial för ditt erbjudande.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marketplace-listor

Mer information om hur du skapar erbjudandelistor finns i [Metodtips för erbjudandelistor](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-avbildningar

Ange logotyper och bilder som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder avvisas.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar den https://upload.xboxlive.com tjänst som används av Partnercenter.

#### <a name="store-logos"></a>Butikslogotyp

Ange en PNG-fil för **logotypen Stor** storlek. Partner center använder detta för att skapa en **liten** och en **medelstor** logotyp. Du kan också ersätta dem med olika bilder senare.

- **Stor** (från 216 x 216 till 350 x 350 px, krävs)
- **Medel** (90 x 90 px, valfritt)
- **Liten** (48 x 48 px, valfritt)

Dessa logotyper används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Skärmbilder (valfritt)

Lägg till upp till fem skärmbilder som visar hur ditt erbjudande fungerar. Var och en måste vara 1280 x 720 bildpunkter i storlek och i PNG-format.

#### <a name="videos-optional"></a>Videor (valfritt)

Lägg till upp till fem videor som visar ditt erbjudande. Ange videons namn, dess webbadress och en PNG-miniatyrbild av videon med storleken 1 280 x 720 bildpunkter.

#### <a name="offer-examples"></a>Exempel på erbjudanden

I följande exempel visas hur erbjudandelistefälten visas på olika platser i erbjudandet.

Då visas sidan **erbjudandelista** i Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustrerar sidan med erbjudandelistan i Azure Marketplace." :::

Detta visar sökresultat i Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustrerar sökresultaten i Azure Marketplace.":::

Då visas sidan **erbjudandelista** i Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustrerar sidan med erbjudandelistan i Azure Portal.":::

Detta visar sökresultat i Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustrerar sökresultaten i Azure Portal.":::

## <a name="preview"></a>Förhandsgranskning

På fliken Förhandsgranska kan du välja en begränsad målgrupp för **förhandsversionen för** att verifiera ditt erbjudande innan du publicerar det live.

> [!IMPORTANT]
> När du har tittat på **ditt erbjudande** i förhandsversionen måste du välja Go live (Gå **live)** för att publicera ditt erbjudande till allmänheten.

Ange din målgrupp för förhandsversionen med hjälp av ID-GUID för Azure-prenumeration, tillsammans med en valfri beskrivning för var och en. Inget av dessa fält kan ses av kunder.

> [!NOTE]
> Du hittar ditt Azure-prenumerations-ID på sidan Prenumerationer i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till de här prenumerations-ID:erna bestämmer du vem som kan förhandsgranska ditt erbjudande innan det publiceras live. Om ditt erbjudande redan är live kan du välja en målgrupp för förhandsversionen för att testa ändringar eller uppdateringar av ditt erbjudande.

Välj **Spara utkast innan** du fortsätter.

## <a name="plan-overview"></a>Översikt över plan

På den här fliken kan du ange olika planalternativ inom samma erbjudande. Planer (kallades tidigare SKU:er) kan skilja sig åt vad gäller vilka moln som är tillgängliga, till exempel globala moln, myndighetsmoln och den avbildning som planen refererar till. Om du vill visa ditt erbjudande på den kommersiella marknadsplatsen måste du konfigurera minst en plan.

Du kan skapa upp till 100 planer för varje erbjudande: upp till 45 av dessa kan vara privata. Läs mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknadsplats.](private-offers.md)

När du har skapat dina planer visas **följande på fliken Planera** översikt:

- Plannamn
- Prismodell
- Azure-regioner (globala eller myndighetsregioner)
- Aktuell publiceringsstatus
- Alla tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i översikten över planen varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast** – Om planens status är ett utkast.
- **Stoppa säljplan** – Om planens status publiceras live.

### <a name="create-new-plan"></a>Skapa ny plan

Välj **Skapa ny plan.** Dialogrutan **Ny plan** visas.

I rutan **Plan-ID** skapar du ett unikt plan-ID för varje plan i det här erbjudandet. Det här ID:t visas för kunderna i produktens webbadress. Använd endast gemener och siffror, bindestreck eller understreck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID:t när du har valt **Skapa**.

I rutan **Plannamn** anger du ett namn för planen. Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas i ditt erbjudande. Skapa ett unikt namn för varje plan i det här erbjudandet. Du kan till exempel använda erbjudandenamnet **Windows Server** med planerna Windows **Server 2016** och **Windows Server 2019**.

### <a name="plan-setup"></a>Planera konfiguration

På den här fliken kan du välja vilka moln planen är tillgänglig i. Dina svar på den här fliken påverkar vilka fält som visas på andra flikar.

#### <a name="azure-regions"></a>Azure-regioner

Alla planer för Azure Container-erbjudanden görs automatiskt tillgängliga i **Azure Global**.  Din plan kan användas av kunder i alla globala Azure-regioner som använder den kommersiella marknadsplatsen. Mer information finns i [Geografisk tillgänglighet och valutastöd.](marketplace-geo-availability-currencies.md)

Välj alternativet [Azure Government](../azure-government/documentation-government-welcome.md) att få lösningen att visas här. Det här är ett myndighetsmoln med kontrollerad åtkomst för kunder från amerikanska federala, statliga och lokala myndigheter eller myndigheter, samt partner som är berättigade att betjäna dem. Som utgivare ansvarar du för alla efterlevnadskontroller, säkerhetsåtgärder och bästa praxis för den här moln communityn. Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA). Innan [du](../azure-government/documentation-government-manage-marketplace-partners.md) publicerar Azure Government bör du testa och bekräfta lösningen inom det området eftersom resultatet kan vara annorlunda. Om du vill skapa och testa din lösning begär du ett utvärderingskonto [från Microsoft Azure Government utvärderingsversion.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> När planen har publicerats och är tillgänglig i en viss region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet kan bara visas om **Azure Government** har valts under **Azure-regioner**.

Azure Government hanterar data som omfattas av vissa myndighetsregler och krav. Till exempel FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS.

Om du vill visa dina certifieringar för dessa program kan du ange upp till 100 länkar som beskriver dem. Dessa kan vara länkar till dina listor i programmet direkt eller till din egen webbplats. Dessa länkar är synliga för Azure Government kunder.

### <a name="plan-listing"></a>Lista över planer

På den här fliken visas specifik information för varje plan i det aktuella erbjudandet.

### <a name="plan-name"></a>Plannamn

Det här är förfyllt med det namn som du gav planen när du skapade den. Du kan ändra det här namnet efter behov. Det kan vara upp till 50 tecken långt. Det här namnet visas som rubriken för den här planen i Azure Marketplace och Azure Portal. Den används som standardmodulnamn när planen är redo att användas.

### <a name="plan-summary"></a>Översikt över plan

En kort sammanfattning av din programvaruplan (inte erbjudandet). Den här sammanfattningen visas Azure Marketplace sökresultat och kan innehålla upp till 100 tecken.

### <a name="plan-description"></a>Planbeskrivning

Beskriv vad som gör den här programvaruplanen unik, samt skillnaderna mellan planer i ditt erbjudande. Beskriv inte erbjudandet, bara planen. Den här beskrivningen visas i Azure Marketplace och i Azure Portal på **sidan Med** erbjudande. Det kan vara samma innehåll som du angav i plansammanfattningen och innehålla upp till 2 000 tecken.

Välj **Spara när** du har slutfört de här fälten.

#### <a name="plan-examples"></a>Planexempel

I följande exempel visas hur fälten för planlistan visas i olika vyer.

Det här är fälten i Azure Marketplace när du visar planinformation:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustrerar de fält som visas när du visar planinformation i Azure Marketplace.":::

Det här är planinformation om Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Visar planinformation om Azure Portal.":::

### <a name="plan-availability"></a>Planera tillgänglighet

Om du vill dölja ditt publicerade erbjudande så att kunderna inte kan söka efter, bläddra eller köpa det på Marketplace markerar du kryssrutan Dölj **plan** på **fliken** Tillgänglighet.

Det här fältet används när:

- Erbjudandet är avsett att användas indirekt när det refereras via ett annat program.
- Erbjudandet bör inte köpas individuellt.
- Planen användes för inledande testning och är inte längre relevant.
- Planen användes för tillfälliga eller säsongsbaserade erbjudanden och bör inte längre erbjudas.

## <a name="technical-configuration"></a>Teknisk konfiguration

Containeravbildningar måste finnas i en privat [Azure Container Registry](https://azure.microsoft.com/services/container-registry/). På fliken **Teknisk konfiguration anger** du referensinformation för lagringsplatsen för containeravbildningen i Azure Container Registry.

När erbjudandet har publicerats kopieras containeravbildningen till Azure Marketplace ett specifikt offentligt containerregister. Alla begäranden om att använda containeravbildningen betjänas från Azure Marketplace offentliga containerregistret, inte ditt privata. Mer information finns i Förbereda [tekniska tillgångar för azure-containrar.](create-azure-container-technical-assets.md)

### <a name="image-repository-details"></a>Information om avbildningslager

Ange följande information på fliken Information **om avbildningsdatabasen.**

**Azure-prenumerations-ID** – Ange det prenumerations-ID där användningen rapporteras och tjänster debiteras för Azure Container Registry som innehåller containeravbildningen. Du hittar det här ID:t [på sidan Prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure Portal.

**Namn på Azure-resursgrupp** – Ange [resursgruppens](../azure-resource-manager/management/manage-resource-groups-portal.md) namn som innehåller Azure Container Registry med containeravbildningen. Resursgruppen måste vara tillgänglig i prenumerations-ID:t (ovan). Du hittar namnet på sidan [Resursgrupper](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) i Azure Portal.

**Azure Container Registry namn** – Ange namnet på den [Azure Container Registry som](../container-registry/container-registry-intro.md) innehåller containeravbildningen. Containerregistret måste finnas i den Azure-resursgrupp som du angav tidigare. Inkludera endast registernamnet, inte det fullständiga inloggningsservernamnet. Se till att utelämna **azurecr.io** från namnet. Du hittar registernamnet på sidan [Containerregister i](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) Azure Portal.

**Administratörsnamn för den Azure Container Registry –** Ange administratörens [användarnamn](../container-registry/container-registry-authentication.md#admin-account)) som är länkat till Azure Container Registry som har containeravbildningen. Användarnamnet och lösenordet krävs för att se till att ditt företag har åtkomst till registret. Om du vill hämta administratörens användarnamn och lösenord anger du **egenskapen för** administratör aktiverat till **Sant** med hjälp av Azure Command-Line Interface (CLI). Om du vill kan du **ställa in Administratörsanvändare** **på Aktivera** i Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Illustrerar dialogrutan Uppdatera containerregister.":::

**Lösenord för Azure Container Registry** – Ange lösenordet för det administratörsnamn som är associerat med Azure Container Registry och har containeravbildningen. Användarnamnet och lösenordet krävs för att se till att ditt företag har åtkomst till registret. Du kan hämta lösenordet från Azure Portal genom att **gå Container Registry**  >  **åtkomstnycklar** eller med Azure CLI med hjälp av [kommandot visa](/cli/azure/acr/credential#az_acr_credential_show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Visar menyn Åtkomstnyckel.":::

**Namnet på lagringsplatsen i Azure Container Registry**. Ange namnet på den Azure Container Registry som innehåller avbildningen. Inkludera namnet på lagringsplatsen när du push-pushar avbildningen till registret. Du hittar namnet på lagringsplatsen genom att gå [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **lagringsplatsen.** Mer information finns i [Visa containerregisterdatabaser i Azure Portal](../container-registry/container-registry-repositories.md).

> [!NOTE]
> När namnet har angetts kan det inte ändras. Använd ett unikt namn för varje erbjudande i ditt konto.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Bildtaggar för nya versioner av ditt erbjudande

Kunderna måste kunna hämta uppdateringar automatiskt från Azure Marketplace när du publicerar en uppdatering. Om de inte vill uppdatera måste de kunna fortsätta att ha en specifik version av avbildningen. Du kan göra detta genom att lägga till nya bildtaggar varje gång du uppdaterar avbildningen.

### <a name="image-tag"></a>Bildtagg

Det här fältet måste innehålla **en senaste** tagg som pekar på den senaste versionen av avbildningen på alla plattformar som stöds. Den måste också innehålla en versionstagg (till exempel början med xx.xx.xx, där xx är ett tal). Kunder bör använda [manifesttaggar](https://github.com/estesp/manifest-tool) för att rikta in sig på flera plattformar. Alla taggar som en manifesttagg refererar till måste också läggas till så att vi kan ladda upp dem.

Alla manifesttaggar (utom den senaste taggen) måste börja med antingen X.Y eller **-** X.Y.Z– där X, Y och Z är heltal. Om taggen  senaste till exempel pekar på 1.0.1-linux-x64, 1.0.1-linux-arm32 och 1.0.1-windows-arm32 måste dessa sex taggar läggas till i det här fältet. Mer information finns i Förbereda [tekniska tillgångar för azure-containrar.](create-azure-container-technical-assets.md)

> [!NOTE]
> Kom ihåg att lägga till en testtagg i bilden så att du kan identifiera bilden under testningen.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in det för att granska och publicera.

I det övre högra hörnet i portalen väljer du **Granska och** **publicera**.

På granskningssidan kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet. Du kan inte publicera förrän alla avsnitt i erbjudandet har markerats som slutförda.
  - **Inte startad** – Har inte startats och måste slutföras.
  - **Ofullständig** – Innehåller fel som måste åtgärdas eller kräver att du anger mer information. Se avsnitten tidigare i det här dokumentet för hjälp.
  - **Complete** – Innehåller alla nödvändiga data utan fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka erbjudandet.
- Ange testinstruktioner till certifieringsteamet för att se till att ditt erbjudande testas korrekt. Ange även eventuella ytterligare anteckningar som är användbara för att förstå ditt erbjudande.

Om du vill skicka erbjudandet för publicering väljer du **Publicera.**

Vi skickar dig ett e-postmeddelande för att meddela dig när en förhandsversion av erbjudandet är tillgänglig för granskning och godkännande.

Om du vill publicera ditt erbjudande offentligt går du till Partnercenter och väljer **Go-live**.

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande på den kommersiella marknadsplatsen](./partner-center-portal/update-existing-offer.md)