---
title: Planera ett Azure Application erbjudande för den kommersiella marknads platsen
description: Lär dig hur du planerar ett nytt Azure-program för att lista eller sälja på Azure Marketplace, eller via ett CSP-program (Cloud Solution Provider) med hjälp av den kommersiella Marketplace-portalen i Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: f98f128e56810e3dd710ce454c8dc34b333690d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879300"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Planera ett Azure Application erbjudande för den kommersiella marknads platsen

I den här artikeln beskrivs de olika alternativen och kraven för att publicera ett Azure Application erbjudande till Microsofts kommersiella marknads platser.

## <a name="before-you-begin"></a>Innan du börjar

Design, utveckling och testning av Azure-programerbjudanden kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet. Teknik teamet bör ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/).
- Hur du [utformar och arkitekterar Azure-program](https://azure.microsoft.com/solutions/architecture/).
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking).
- Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Arbeta med [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Teknisk dokumentation och resurser

Granska följande resurser när du planerar ditt Azure Application-erbjudande för den kommersiella Marketplace.

- [Förstå Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)
- Snabbstarter:
    - [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/)
    - [Best Practices guide för Azure-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Publicera programdefinition](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Distribuera tjänstkatalogapp](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Självstudier:
    - [Skapa definitionsfiler](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Stickprov
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Hanterade programlösningar](../azure-resource-manager/managed-applications/sample-projects.md)

[Mallarna för video skapande lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) ger en omfattande introduktion till Azures program erbjudande typ:

- Vilka erbjudande typer är tillgängliga
- Det krävs tekniska till gångar
- Så här redigerar du en Azure Resource Manager mall
- Utveckla och testa användar gränssnittet för appen
- Så här publicerar du app-erbjudandet
- Programmets gransknings process

### <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera ditt Azure-program:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklings miljön:

- [Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) med följande fil namns tillägg:
    - Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Du kan granska de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan. Om du använder Visual Studio går du till [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Listalternativ

När ditt erbjudande har publicerats visas List alternativen för ditt erbjudande som en knapp i det övre vänstra hörnet på ditt erbjudandes List sida. Följande skärm bild visar till exempel sidan erbjudande på Azure Marketplace med knappen _Hämta nu_ . Om du har valt att erbjuda en testenhet visas även knappen _testa enhet_ .

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Visar en listsida på Azure Marketplace.":::

## <a name="test-drive"></a>Test enhet

Du kan välja att aktivera en testenhet för ditt Azure Applications erbjudande som gör det möjligt för kunder att prova ditt erbjudande innan du köper det. Mer information om test enheter finns i [Vad är en test enhet?](what-is-test-drive.md). Information om hur du konfigurerar olika typer av test enheter finns i [teknisk konfiguration för test enhet](test-drive-technical-configuration.md).

Du kan också läsa om [metod tipsen för test enheten](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) och hämta [översikten över test enheter](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (se till att blockeringen av popup-fönster är avstängd).

> [!NOTE]
> Information användaren bör Observera även om skimmingBecause alla Azure-program implementeras med hjälp av en Azure Resource Manager mall, är den enda typen av test enhet som är tillgänglig för en [Azure Application en Azure Resource Manager baserad test enhet](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Kund ledare

Du måste ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) för att samla in kund information. Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namn, ID och onlinebutik där de hittade ditt erbjudande, kommer att skickas till det CRM-system som du har konfigurerat. Den kommersiella Marketplace har stöd för en rad olika CRM-system, tillsammans med alternativet att använda en Azure-tabell eller konfigurera en HTTPS-slutpunkt med energi automatisering.

Du kan lägga till eller ändra en CRM-anslutning när som helst under eller efter att erbjudandet har skapats. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategorier och underkategorier

Du kan välja minst en och upp till två kategorier för att gruppera ditt erbjudande i lämpliga sökområden för handels platser. Du kan välja upp till två under Kategorier för varje primär och sekundär kategori. En fullständig lista över kategorier och under kategorier finns i [erbjudande om bästa praxis](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Juridiska kontrakt

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt som du kan använda för dina erbjudanden på den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

Om du väljer att använda standard kontraktet har du möjlighet att lägga till generella ändrings villkor och upp till 10 anpassade ändringar i standard kontraktet. Du kan också använda egna villkor i stället för standard kontraktet. Du kommer att hantera informationen på sidan **Egenskaper** . Detaljerad information finns i [standard kontrakt för Microsoft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> När du har publicerat ett erbjudande med hjälp av standard avtalet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Det är ett "eller"-scenario. Du erbjuder antingen din lösning enligt standard avtalet eller dina egna villkor. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

## <a name="offer-listing-details"></a>Information om erbjudande lista

När du skapar ett nytt Azure Application erbjudande i Partner Center, kommer du att ange text, bilder, valfria videor och annan information på sidan med erbjudande listan. Detta är den information som kunderna ser när de identifierar din erbjudande lista på Azure Marketplace, som du ser i följande exempel.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Kategorier
3. Support adress (länk)
4. Villkor för användning
5. Sekretess policy adress (länk)
6. Erbjudandets namn
7. Sammanfattning
8. Beskrivning
9. Skärm bilder/videor

Följande skärm bild visar hur information om erbjudandet visas i Azure Portal:

[![Visar hur det här erbjudandet visas i Azure Portal.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Rubrik
2. Beskrivning
3. Användbara länkar
4. Skärmbilder

> [!NOTE]
> Erbjudande för List innehåll måste inte vara på engelska om erbjudande beskrivningen börjar med frasen "det här programmet är endast tillgängligt på [språk som inte är engelska]".

För att hjälpa till att skapa ett erbjudande enklare kan du förbereda några av dessa objekt i förväg. Följande objekt krävs om inget annat anges.

- **Namn**: namnet visas som rubrik på din erbjudande lista på den kommersiella Marketplace. Namnet kan vara ett varumärke. Den får inte innehålla emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken.
- **Sammanfattning av Sök Resultat**: Beskriv syftet eller funktionen hos ditt erbjudande som en enda mening, i klartext utan rad brytningar, med 100 tecken eller mindre. Den här sammanfattningen används i Sök resultaten för de kommersiella Marketplace-listorna.
- **Kort beskrivning**: ger upp till 256 tecken med oformaterad text. Den här sammanfattningen visas på ditt erbjudandes informations sida.
- **Beskrivning**: den här beskrivningen visas i Översikt över Azure Marketplace-lista (er). Överväg att inkludera ett värde förslag, viktiga fördelar, avsedda användar Base, kategori-eller bransch associationer, inköps möjligheter för appar, kund behov eller smärta som erbjudande adresser, obligatoriska upplysningar och en länk till mer information.

    Den här text rutan innehåller text redigerings kontroller som du kan använda för att göra din beskrivning mer engagerande. Du kan också använda HTML-taggar för att formatera beskrivningen. Du kan ange upp till 3 000 tecken text i den här rutan, inklusive HTML-kod och blank steg. Ytterligare tips finns i [skriva en fantastisk app-Beskrivning](/windows/uwp/publish/write-a-great-app-description) och [HTML-taggar som stöds i de kommersiella erbjudandena för Marketplace-erbjudanden](supported-html-tags.md).

- **Sök Nyckelord** (valfritt): ange upp till tre Sök nyckelord som kunder kan använda för att hitta ditt erbjudande i onlinebutiken. För bästa resultat bör du också använda dessa nyckelord i beskrivningen. Du behöver inte inkludera erbjudandets **namn** och **Beskrivning**. Texten tas automatiskt med i sökningen.
- **Länk till sekretess policy**: URL: en för ditt företags sekretess policy. Du måste ange en giltig sekretess policy och du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter.
- **Användbara länkar** (valfritt): du kan ange länkar till olika resurser för användare av ditt erbjudande. Till exempel forum, FAQ och viktig information.
- **Kontakt information**: du måste ange följande kontakter från din organisation:
  - **Support kontakt**: Ange namn, telefon och e-postadress för Microsoft-partner som ska användas när dina kunder öppnar biljetter. Du måste även ta med URL: en för din support webbplats.
  - **Teknisk kontakt**: Ange namn, telefon och e-postadress som Microsoft ska använda direkt när det är problem med erbjudandet. Den här kontakt informationen visas inte i den kommersiella marknads platsen.
  - **Kontakta CSP-programmet** (valfritt): Ange namn, telefon och e-postadress om du väljer att använda Cloud Solution Provider (CSP) så att dessa partner kan kontakta dig med frågor. Du kan också inkludera en URL till ditt marknadsförings material.
- **Media – logo typer**: Ange en PNG-fil för logo typen för **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.
  - Stor (från 216 x 216 till 350 x 350 BPT, krävs)
  - Medel (90 x 90 BPT, valfritt)
  - Liten (48 x 48 BPT, valfritt)

  Dessa Logo typer används på olika platser i onlinebutiker:
  - Den lilla logo typen visas i Sök resultaten för Azure Marketplace.
  - Medie logo typen visas när du skapar en ny resurs i Microsoft Azure.
  - Den stora logo typen visas på sidan med din erbjudande lista i Azure Marketplace.

  Följ dessa rikt linjer för dina logo typer:

  - Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
  - Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger.
  - Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
  - Utseendet på logo typen bör vara platt och undvika toningar i logo typen eller bakgrunden. Placera inte text på logotypen – inte ens företagets eller varumärkets namn. Suddiga bilder gör att ditt bidrag avvisas.
  - Se till att logotypen inte har sträckts ut.

- **Media-skärm bilder** (valfritt): Vi rekommenderar att du lägger till skärm bilder som visar hur ditt erbjudande fungerar. Du kan lägga till upp till fem skärm dum par med följande krav, som visar hur ditt erbjudande fungerar:
  - 1280 x 720 pixlar
  - . png-fil
  - Måste innehålla en beskrivning
- **Media – videor** (valfritt): du kan lägga till upp till fem videor med följande krav som demonstrerar ditt erbjudande:
  - Name
  - URL: måste vara värd för YouTube eller Vimeo.
  - Miniatyr: 1280 x 720. png-fil

> [!NOTE]
> Ditt erbjudande måste uppfylla de allmänna [certifierings principerna för kommersiella marknads platser](/legal/marketplace/certification-policies#100-general.md) som ska publiceras på den kommersiella marknads platsen.

## <a name="preview-audience"></a>Förhandsgranska mål grupp

En förhands gransknings grupp kan komma åt ditt erbjudande innan det publiceras Live i onlinebutiker för att testa slut punkt till slut punkt innan du publicerar den Live.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat plan. En privat plan är en som du bara gör tillgänglig för en speciell mål grupp som du väljer. På så sätt kan du förhandla fram en anpassad plan med vissa kunder.

Du definierar förhands gransknings publiken med ID: n för Azure-prenumerationer, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder.

## <a name="technical-configuration"></a>Teknisk konfiguration

För hanterade program som genererar mätnings händelser med hjälp av [API: er för avläsning](partner-center-portal/marketplace-metering-service-apis.md)av program vara, måste du ange den identitet som din tjänst kommer att använda när du genererar Mät händelser.

Den här konfigurationen krävs om du vill använda [händelsen för batch-användning](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event). Om du vill skicka [användnings händelse](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)kan du också använda [instansens metadatatjänst](../active-directory/managed-identities-azure-resources/overview.md) för att hämta [token för JSON-webbtoken (JWT)](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

- **Azure Active Directory klient-ID** (krävs): inuti Azure Portal måste du [skapa en Azure Active Directory-app (AD)](../active-directory/develop/howto-create-service-principal-portal.md) så att vi kan verifiera anslutningen mellan våra två tjänster bakom en autentiserad kommunikation. Du hittar [klient-ID:](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) t för din Azure Active Directory (Azure AD)-app i [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) bladet i din Azure Active Directory. I kolumnen **visnings namn** väljer du appen. Leta sedan efter **Egenskaper** och sedan för **katalog (klient) ID** (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).
- **Azure Active Directory program-ID** (obligatoriskt): du behöver också ditt [program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) och en autentiseringsnyckel. Om du vill hitta ditt program-ID går du till bladet [Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Active Directory. I kolumnen **visnings namn** väljer du appen och letar sedan efter **program-ID** (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ). Om du vill hitta autentiseringsnyckel går du till **Inställningar** och väljer **nycklar**. Du måste ange en beskrivning och varaktighet och kommer sedan att tillhandahålla ett Number-värde.

> [!NOTE]
> Azure-program-ID: t associeras med ditt utgivar-ID och kan bara användas på nytt i det här utgivar kontot.

## <a name="additional-sales-opportunities"></a>Ytterligare försäljnings möjligheter

Du kan välja att välja mellan marknadsförings-och försäljnings kanaler som stöds av Microsoft. När du skapar erbjudandet i Partner Center visas två flikar i slutet av processen:

- Att **sälja via CSP**: Använd det här alternativet om du vill att CSP-partner (Microsoft Cloud solution providers) ska kunna sälja din lösning som en del av ett sammanslaget erbjudande. Mer information finns i [Cloud Solution Provider-programmet](./cloud-solution-providers.md) .
- **Samförsäljning med Microsoft: med** det här alternativet kan Microsofts försäljnings team överväga din IP-samkunds kvalificerade lösning vid utvärdering av kundernas behov. Detaljerad information om hur du förbereder erbjudandet för utvärdering finns i [alternativet samförsäljning på den kommersiella Marketplace](commercial-marketplace-co-sell.md). Information om krav för IP-samförsäljning finns i [krav för medförsäljnings status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Mer information om marknadsföring av ditt erbjudande via Microsoft CSP partner Channels finns i [moln lösnings leverantörer](cloud-solution-providers.md).

Mer information finns i [utveckla din moln verksamhet med Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Planer

Azure Application erbjudanden kräver minst en plan. En plan definierar lösningens omfattning och begränsningar samt tillhör ande prissättning, om tillämpligt. Du kan skapa flera planer för ditt erbjudande för att ge dina kunder olika tekniska och pris alternativ.

Allmän vägledning om planer, inklusive pris modeller och privata planer finns i [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md). I följande avsnitt beskrivs ytterligare information som är speciell för Azure Application planer.

### <a name="types-of-plans"></a>Typer av planer

Det finns två typer av Azure-programplaner: _lösnings mal len_ och _hanterade program_. Båda plan typerna stöder automatisering av distribution och konfiguration av en lösning utöver en enskild virtuell dator (VM). Du kan automatisera processen med att tillhandahålla flera resurser, inklusive virtuella datorer, nätverk och lagrings resurser för att tillhandahålla komplexa lösningar, till exempel IaaS-lösningar. Båda typerna av abonnemang kan använda många olika typer av Azure-resurser, inklusive men inte begränsat till virtuella datorer.

- **Lösnings mal len** planer är ett av de största sätten att publicera en lösning på den kommersiella marknads platsen. Lösningar för lösnings mal len är inte i ett särskilt lägen i den kommersiella marknads platsen, men de kan användas för att distribuera betalda VM-erbjudanden som debiteras via den kommersiella marknads platsen. Använd lösnings mal len plan typ när kunden ska hantera lösningen och transaktionerna debiteras genom en annan plan. Mer information om hur du skapar Solution-mallar finns i [Vad är Azure Resource Manager?](../azure-resource-manager/management/overview.md)
- Med **hanterade program** planer kan du enkelt skapa och leverera fullständigt hanterade, färdiga program för dina kunder. De har samma funktioner som lösnings mal len planer, med några viktiga skillnader:
    - Resurserna distribueras till en resurs grupp och hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. 
    - Som utgivare kan du ange kostnaden för kontinuerlig support för lösningen och transaktioner som stöds via den kommersiella Marketplace.
 
    Använd den hanterade program Plans typen när du eller din kund kräver att lösningen hanteras av en partner eller att du distribuerar en prenumerations-baserad lösning. Mer information om fördelarna och typerna av hanterade program finns i [Översikt över Azure Managed Applications](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Nästa steg

- Information om hur du planerar en lösnings mall finns i [Planera en lösnings mall för ett erbjudande för Azure-program](plan-azure-app-solution-template.md).
- Information om hur du planerar ett Azure-hanterat program finns i [planera ett Azure-hanterat program för ett erbjudande för Azure-program](plan-azure-app-managed-app.md).