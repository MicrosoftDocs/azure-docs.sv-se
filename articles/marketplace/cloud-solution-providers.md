---
title: Cloud Solution Provider – Microsoft Commercial Marketplace
description: Lär dig hur du säljer dina erbjudanden via program partner kanalen för Microsoft Cloud Solution Provider (CSP) på den kommersiella Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: c906d37a01f0fca2d4114e8ba07078fc46eec88b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131181"
---
# <a name="cloud-solution-provider-program"></a>CSP-program

Den här artikeln förklarar hur du konfigurerar ditt erbjudande som ska vara tillgängligt för CSP-programmet (Cloud Solution Provider). Förutom att publicera dina erbjudanden via [onlinebutiker för kommersiella platser](overview.md#commercial-marketplace-online-stores), kan du också sälja via CSP-programmet för att komma åt miljon tals kvalificerade Microsoft-kunder som programmet hanterar.

Du kan konfigurera nya eller befintliga erbjudanden för tillgänglighet i CSP-programmet på ett valbart sätt, vilket gör det möjligt för CSP-partner att sälja dina produkter och skapa sammanställda lösningar för kunder.

Utgivare ansvarar för att tillhandahålla stöd för att ge support för att ge slutanvändarna möjlighet att ge support för partner i CSP-programmet och/eller kunder att kontakta dig. Det är en bra idé att tillhandahålla partner i CSP-programmet med användar dokumentation, utbildning och tjänst hälso-och avbrotts meddelanden (i förekommande fall) så att partner i CSP-programmet är utrustade för att hantera support förfrågningar på nivå 1 från kunder.  

Följande erbjudanden är berättigade att välja att säljas av partner i CSP-programmet:

- SaaS-erbjudanden (Software-as-a-Service)
- Virtual Machines (VM)
- Lösningsmallar
- Hanterade program

> [!NOTE]
> Behållare och ta med din egen licens (BYOL) VM-planer används som standard för att säljas av partner i CSP-programmet.

## <a name="how-to-configure-an-offer"></a>Så här konfigurerar du ett erbjudande

Konfigurera alternativet för att välja CSP-program när du skapar erbjudandet i Partner Center. [Lär dig mer om den ändrade utgivarens upplevelse](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Deltagande i Partner Center

Opt-in-upplevelsen finns under Audience-modulen CSP-åter försäljare:

![CSP åter försäljarens publik](media/marketplace-publishers-guide/csp-reseller-audience.png)

Välj mellan tre alternativ:

1. Alla partner i CSP-programmet.
2. Vissa partner i CSP-programmet som jag väljer.
3. Inga partner i CSP-programmet.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Alternativ 1: alla partner i CSP-programmet

![Alla partner i CSP-programmet](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Genom att välja det här alternativet är alla partner i CSP-programmet berättigade till att sälja erbjudandet till sina kunder.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Alternativ 2: vissa partner i CSP-programmet som jag väljer

![Vissa partner i CSP-programmet som jag väljer](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Genom att välja det här alternativet godkänner du vilka partner i CSP-programmet som är berättigade att sälja erbjudandet.

Om du vill auktorisera partner väljer du **Välj CSP-partner** och en meny visas där du kan söka efter partner namn eller CSP Azure Active Directory (Azure AD) klient-ID.

![Välj CSP-menyn](media/marketplace-publishers-guide/csp-pop-up-module.png)

Du kan använda Sök filter, till exempel **land**, **kompetens** eller **kunskap**.

![Lands-, region-, kompetens-och kunskaps filter för partner sökning](media/marketplace-publishers-guide/csp-add-resellers.png)

När du har valt listan över partners väljer du **Lägg till**.

![Exempel lista över behöriga partner i CSP-programmet](media/marketplace-publishers-guide/csp-add-resellers-details.png)

En tabell som visar listan över partner som du har valt visas på mål sidan för CSP-åter försäljaren.

![Tabell med lista över partner på sidan för CSP-åter försäljaren](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Välj **Spara utkast** för att registrera dina ändringar.

Om erbjudandet är avpublicerat måste du publicera ditt erbjudande för att göra det tillgängligt för dina valda partner.

>[!NOTE]
>Om du godkänner en partner i CSP-programmet i en viss region kan de sälja erbjudandet till alla kunder som tillhör den aktuella regionen. För ytterligare information om hur CSP-erbjudanden klassificeras under regioner, se [Cloud Solution Provider program regionala marknader och valutor](/partner-center/regional-authorization-overview).

Om du uppdaterar CSP-listan för ett redan publicerat erbjudande lägger du till ytterligare partner och väljer **Synkronisera CSP-mål**.

Om du har ett erbjudande som redan har en lista över behöriga partner och du vill använda samma lista för ett annat erbjudande, använder du **import/export**. Gå till det erbjudande som innehåller CSP-listan och välj **Exportera kryptografiproviders**. Funktionen utvecklar en. csv-fil som kan importeras till ett annat erbjudande.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Alternativ 3: inga partner i CSP-programmet

![Inga partner i CSP-programmet](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Genom att välja det här alternativet är du väljer ditt erbjudande från CSP-programmet. Du kan ändra det här alternativet när som helst.

## <a name="deauthorize-partners-in-the-csp-program"></a>Deauktorisera partner i CSP-programmet

Om du har auktoriserat en partner i CSP-programmet och den partnern redan har sålt produkten till sina kunder, kommer du inte att kunna godkänna den partnern.

Om en partner i CSP-programmet inte har sålt produkten till kunderna och du vill ta bort CSP: n när ditt erbjudande har publicerats, använder du följande anvisningar:

1. Gå till [sidan supportbegäran](https://aka.ms/marketplacepublishersupport). De första menyerna i listan fylls i automatiskt.

   > [!NOTE]
   > Ändra inte markeringarna i list rutan i förväg.

2. För **Välj produkt version väljer du hantering av** **Live-erbjudandet**.
3. För **Välj en kategori som bäst beskriver problemet** väljer du den kategori som refererar till ditt erbjudande.
4. För **Välj ett problem som bäst beskriver problemet väljer du** **uppdatera befintligt erbjudande**.
5. Välj **Nästa** för att komma till **sidan ärende information** och ange mer information om problemet.
6. Använd **deauktorisera CSP** som ärende rubrik och fyll i resten av de nödvändiga avsnitten.

## <a name="navigate-between-options"></a>Navigera mellan alternativ

Använd det här avsnittet för att navigera mellan de tre alternativen för CSP-åter försäljare.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigera från alternativ ett: alla partner i CSP-programmet

Om erbjudandet för närvarande är **alternativ 1: någon partner i CSP-programmet** och du vill navigera till något av de andra två alternativen, använder du följande instruktioner för att skapa en begäran:

1. Gå till [sidan supportbegäran](https://aka.ms/marketplacepublishersupport). De första menyerna i listan fylls i automatiskt.

   > [!NOTE]
   > Ändra inte markeringarna i list rutan i förväg.

2. För **Välj produkt version väljer du hantering av** **Live-erbjudandet**.
3. För **Välj en kategori som bäst beskriver problemet** väljer du den kategori som refererar till ditt erbjudande.
4. För **Välj ett problem som bäst beskriver problemet väljer du** **uppdatera befintligt erbjudande**.
5. Välj **Nästa** för att komma till **sidan ärende information** och ange mer information om problemet.
6. Använd **deauktorisera CSP** som ärende rubrik och fyll i resten av de nödvändiga avsnitten.

> [!NOTE]
> Om du försöker navigera till alternativ två och en partner i CSP-programmet redan har sålt erbjudandet till sina kunder, är den partnern som standard redan i listan över behöriga partner.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigera från alternativ två: vissa partner i CSP-programmet som jag väljer

Om erbjudandet för närvarande är **Alternativ 2: vissa partner i CSP-programmet som jag väljer** och du vill navigera till **alternativ ett: alla partner i CSP-programmet** använder följande instruktioner för att skapa en begäran:

1. Gå till [sidan supportbegäran](https://aka.ms/marketplacepublishersupport). De första menyerna i listan fylls i automatiskt.

   > [!NOTE]
   > Ändra inte markeringarna i list rutan i förväg.

2. För **Välj produkt version väljer du hantering av** **Live-erbjudandet**.
3. För **Välj en kategori som bäst beskriver problemet** väljer du den kategori som refererar till ditt erbjudande.
4. För **Välj ett problem som bäst beskriver problemet väljer du** **uppdatera befintligt erbjudande**.
5. Välj **Nästa** för att komma till **sidan ärende information** och ange mer information om problemet.
6. Använd **deauktorisera CSP** som ärende rubrik och fyll i resten av de nödvändiga avsnitten.

 Om erbjudandet för närvarande är **Alternativ 2: särskilda partner i CSP-programmet som jag väljer** och du vill navigera till **Alternativ 3: inga partner i CSP-programmet**. du kommer bara att kunna navigera till det alternativet om de partner i CSP-programmet som du tidigare har godkänt inte har sålt ditt erbjudande till slutanvändare. Använd följande instruktioner för att skapa en begäran:

1. Gå till [sidan supportbegäran](https://aka.ms/marketplacepublishersupport). De första menyerna i listan fylls i automatiskt.

   > [!NOTE]
   > Ändra inte markeringarna i list rutan i förväg.

2. För **Välj produkt version väljer du hantering av** **Live-erbjudandet**.
3. För **Välj en kategori som bäst beskriver problemet** väljer du den kategori som refererar till ditt erbjudande.
4. För **Välj ett problem som bäst beskriver problemet väljer du** **uppdatera befintligt erbjudande**.
5. Välj **Nästa** för att komma till **sidan ärende information** och ange mer information om problemet.
6. Använd **deauktorisera CSP** som ärende rubrik och fyll i resten av de nödvändiga avsnitten.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigera från alternativ 3: inga partner i CSP-programmet

Om erbjudandet för närvarande är **Alternativ 3: inga partner i CSP-programmet**, kan du navigera till något av de andra två alternativen när som helst.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Dela försäljnings-och support material med partner i CSP-programmet

För att hjälpa partner i Cloud Solution Provider-programmet att representera ditt erbjudande och engagera med din organisation måste du skicka in försäljnings-och support material som kommer att vara tillgängliga för åter försäljarna. De här resurserna är inte tillgängliga för kunder i onlinebutiker.

### <a name="partner-center-csp-channel"></a>Partner Center CSP-kanal

Om du har valt CSP-kanalen i Partner Center måste utgivare ange en URL som är värd för relevant marknadsförings material och kanal kontakt information under modulen för erbjudande listan.

![Information om CSP-information för partner Center](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Go-to-Marketing-tjänster](https://partner.microsoft.com/reach-customers/gtm).
- Logga in på [partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) för att skapa och konfigurera ditt erbjudande.