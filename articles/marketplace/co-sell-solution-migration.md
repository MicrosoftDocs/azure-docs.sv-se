---
title: Migrera samförsäljnings lösningar från OCP GTM till Partner Center för Microsoft AppSource
description: Lär dig hur du migrerar samförsäljnings lösningar från OCP GTM till Partner Center för Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593415"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migrering av samförsäljnings lösningar från OCP GTM till den kommersiella marknaden

Microsoft flyttar publicerings upplevelsen. Den [kommersiella marknads platsen](overview.md) tillhandahåller publicering till samförsäljning via Microsofts tre kanaler genom att centralisera erbjudanden för skapande och hantering i Partner Center, där du redan hanterar din relation med Microsoft.

Som en Microsoft-partner som har registrerats på den kommersiella marknads platsen kan du:

- Publicera dina erbjudanden centralt och samsälja över Microsofts direkta kund-, partner-och försäljnings kanaler.
- Se till att dina erbjudanden finns i rätt onlinebutik –[Microsoft AppSource](https://appsource.microsoft.com) eller [Azure Marketplace](https://azure.microsoft.com)– för att uppnå miljon tals moln kunder som överensstämmer med ditt erbjudandes funktioner.
- Gör din egen publicerings upplevelse till samförsäljning med de erbjudanden som passar dina affärs mål.
- Justera din erbjudande publicering i Partner Center, där du redan hanterar dina Microsoft-relationer och samförsäljnings möjligheter.
- Lås upp [marknads förmåner](partner-center-portal/marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Krav för att fortsätta samförsäljning med Microsoft

Se till att du har ett aktivt Microsoft Partner Network medlemskap och har registrerats på den kommersiella Marketplace i Partner Center.

- Delta i Microsoft Partner Network [kostnads fritt](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership). Som partner har du till gång till exklusiva resurser, program, verktyg och anslutningar för att växa din verksamhet.
- Om du inte har ett konto på kommersiell marknads plats kan du [Registrera dig nu](partner-center-portal/create-account.md) för att fortsätta att sälja med Microsoft och få till gång till den fullständiga publicerings upplevelsen.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Publicera uppdateringar för att uppnå medförsäljnings klar status

För att din lösning ska kunna identifieras av Microsofts säljare och partner måste den uppfylla de [färdiga kraven i Co-försäljningen](marketplace-co-sell.md). För att en Microsoft-säljare ska kunna motiverade måste din lösning uppfylla de incitament som uppfyller [kraven](marketplace-co-sell.md). Slutför de här kraven på fliken medförsäljning i Partner Center (se [den här avbildningen](#cosell-tab) senare i den här artikeln).

> [!NOTE]
> På kommersiella marknads platser kallas dina lösningar för "erbjudanden" under publicerings upplevelsen.

När du har registrerat dig på den kommersiella Marketplace förbereder du att migrera dina lösningar från OCP GTM.

Följ de här stegen innan du importerar dina lösningar från OCP GTM:

1. Gå till företagets [Publisher-lista](https://partner.microsoft.com/dashboard/account/v3/publishers/list). Den innehåller kontots ägare, hanterare och utvecklare som har publicerings åtkomst. Lär dig mer om [användar roller i Partner Center](./partner-center-portal/manage-account.md#define-user-roles-and-permissions).
2. Be en av de listade kontakterna att [lägga till användare](https://partner.microsoft.com/dashboard/account/usermanagement) till den kommersiella marknads platsen som *chefer* eller *utvecklare*, eftersom endast dessa roller kan redigera och publicera lösningar.
3. Samar beta med dina utvecklare för att flytta dina lösningar från ditt OCP GTM-konto till den kommersiella marknads platsen.
4. Bestäm vilket av följande som du vill göra:
    1. Slå ihop den här lösningen med ett liknande erbjudande i den kommersiella marknads platsen.
    1. Migrera den här lösningen från OCP GTM till den kommersiella marknads platsen.
    1. Ta bort den här lösningen.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Migrera dina lösningar från OCP GTM

1. Påbörja migreringen [här](https://partner.microsoft.com/solutions/migration#).
2. Välj sidan **Översikt** och **Klicka här för att komma igång**.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Översikts sidan för partner Center, fliken Översikt.":::

3. Börja migrera genom att välja fliken **lösningar** , som visar alla lösningar som är kopplade till dina MPN-ID: n.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Översikts sidan för partner Center, fliken lösningar.":::

    > [!NOTE]
    > Den här fliken noterar om det inte finns några lösningar som väntar på att migreras till den kommersiella marknads platsen. För att fortsätta att sälja med Microsoft, se till att de migrerade lösningarna publiceras på den kommersiella marknads platsen.

    Läs mer om lösnings statusen genom att granska verktygs tipsen. Alla lösningar som väntar på åtgärd visas under **åtgärd**.<a name="beginmigration"></a>

4. Välj **Starta migrering** (se bilden ovan) för den lösning som du vill migrera och välj sedan ett av följande alternativ:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="De tre alternativen för migrering.":::

### <a name="merge-solution-with-a-similar-offer"></a>Sammanfoga lösning med ett liknande erbjudande

Välj det här alternativet om lösningen redan har publicerats på den kommersiella marknaden och de två ska slås samman i ett enda erbjudande. Detta undviker att skapa dubbletter av erbjudanden.

1. Identifiera det befintliga erbjudandet.
    1. Välj **Jag vill slå samman lösningen med ett liknande erbjudande på den kommersiella Marketplace** (se **åtgärd krävs** avbildning [ovan](#beginmigration)).
    1. På fliken **åtgärd 1** visas Live Marketplace för kommersiella marknads platser som din OCP GTM-lösning kan associeras med. Välj Live-erbjudandet i listan om du har ett. Om det inte finns någon lista över erbjudanden att välja bland, anger du adressen till kunden (URL) från Microsoft AppSource eller Azure Marketplace.
        [![Fliken åtgärd 1 i sammanslagnings processen.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Välj **Fortsätt**.
1. Begär sammanfogningen.
    1. Fliken **åtgärd 2** visar riktningar för att begära sammanslagningen av din OCP GTM-lösning med den som du har identifierat. Om du vill begära sammanslagningen väljer du **spara & kontakta supporten** så öppnas sidan partner support i en webbläsare.
    1. Välj **Ange problem information** och ange följande: [ ![ fliken åtgärd 2 i sammanslagnings processen.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Välj **Skicka**. Partner support teamet kommer att kontakta dig inom två arbets dagar.
    1. Partner supporten kommer att samar beta med dig för att säkerställa en lyckad sammanslagning av det här erbjudandet så att det publiceras som ett Live-erbjudande.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Migrera den här lösningen från OCP GTM

Välj det här alternativet om du kan ha en OCP GTM-lösning som ännu inte har ett motsvarande erbjudande publicerat på den kommersiella marknads platsen. Du måste publicera den här lösningen på den kommersiella marknads platsen för att kunna fortsätta att sälja med Microsoft och genom att migrera den här lösningen sparar du tid genom att behålla informationen och material strukturen från OCP GTM. Det här alternativet kräver att du väljer en erbjudande typ.

1. Välj **Jag vill migrera den här lösningen från OCP GTM till den kommersiella marknads platsen** (se **åtgärd krävs** avbildning [ovan](#beginmigration)) och **Fortsätt** sedan.
1. På fliken **åtgärd 1** väljer du [erbjudande typen](publisher-guide-by-offer-type.md)och **fortsätter** sedan.

    [![Fliken åtgärd 1 i flyttnings processen.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. På fliken **åtgärd 2** väljer du [utgivar profilen](partner-center-portal/create-account.md) i listan som tillhandahålls. Om du inte har ett utgivar konto skapar du ett i [partner Center](https://partner.microsoft.com/solutions/migration)och väljer det här.

    [![Fliken åtgärd 2 i flyttnings processen.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. Välj **skapa ett utkast** för att migrera din lösning till den kommersiella marknads platsen som ett utkast. Den kommer inte att vara Live än.
1. Fortsätt publicerings processen i Partner Center. Information om hur du publicerar i Partner Center finns i [göra ditt erbjudande Live på den kommersiella Marketplace](#make-your-offer-live-in-the-marketplace) nedan.

### <a name="discard-this-solution"></a>Ta bort den här lösningen

Välj det här alternativet när en lösning i OCP GTM-lösningar inte längre är relevant. Du uppmanas att bekräfta ignoreraen och du kan också ångra det senare.

1. Välj **Jag vill ignorera den här lösningen** (se **åtgärd krävs** avbildning [ovan](#beginmigration)) och **Fortsätt** sedan.
2. Välj **Ignorera**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Bekräfta ignorera.":::

3. Om du vill ångra dina borttagningar väljer du **Ångra ignorera**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="Länken ångra borttagning.":::

4. Om du behöver ytterligare hjälp kan du välja fliken **få hjälp** att kontakta support teamet för partner.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="Support länken på fliken Hämta hjälp.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Gör ditt erbjudande tillgängligt i Marketplace

Om du har valt att migrera ditt erbjudande till den kommersiella marknads platsen kommer det att komma som ett utkast. Du måste fortfarande publicera ditt erbjudande för att göra det live på den kommersiella marknads platsen. Detta behåller sin försäljnings-, stimulans-och hänvisnings pipeline.

Detaljerade anvisningar om vilka uppgifter du måste ange innan du kan publicera ditt erbjudande finns i lämplig [publicerings guide](publisher-guide-by-offer-type.md). För en sammanfattning läser du nedan.

1. På sidan **Översikt** i Partner Center väljer du namnet på utkast erbjudandet.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="Sidan erbjudande översikt i Partner Center.":::

<a name="cosell-tab"></a>

2. Slutför den information som krävs på varje flik. Om du vill kan du slutföra åter försäljning **via CSP** -sidan (i den vänstra navigerings menyn nedan) för att gå med i programmet för Cloud Solution Provider (CSP). I **Lär dig mer** om länkar och knapp beskrivningar får du information om kraven och detaljerna.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="Fliken erbjudande översikt konfigureras i Partner Center.":::

3. Några av Microsofts säljar information kopierades från OCP GTM-lösningen. Slutför den återstående information som krävs på fliken **medförsäljning med Microsoft** för att göra ditt erbjudande i Co-försäljningen klart. När du är klar väljer du **Granska och publicera**. Mer information finns i avsnittet [Konfigurera samförsäljning för ett erbjudande för handels platser](commercial-marketplace-co-sell.md).

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="Fliken erbjud Co-försäljning i Partner Center.":::

4. När du har granskat all information som skickats väljer du **publicera** för att skicka utkastet för certifierings granskning.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="Fliken Granska och knappen publicera i Partner Center.":::

5. Spåra statusen för ditt bidrag på fliken **Översikt** .

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="Statusfältet för publicering av erbjudandet på fliken Översikt i Partner Center.":::

6. Vi meddelar dig när vår certifierings granskning är klar. Om vi tillhandahåller åtgärdad feedback kan du adressera den och sedan välja **publicera** för att initiera en certifiering.
7. När ditt erbjudande har klarat certifieringen kan du förhandsgranska erbjudandet med den angivna länken och göra eventuella slutliga justeringar som du vill ha. När du är klar väljer du **Go Live** (se knappen ovan) för att publicera ditt erbjudande till relevanta kommersiella Marketplace-butik. När du är Live behåller ditt erbjudande sin status för samförsäljning från den ursprungliga OCP GTM-lösningen.

## <a name="next-steps"></a>Nästa steg

- [Sälja via CSP-partner](cloud-solution-providers.md)
- [Konfigurera samförsäljning för ett erbjudande för handels platser](commercial-marketplace-co-sell.md)
- Visa dessa [vanliga frågor och svar](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
