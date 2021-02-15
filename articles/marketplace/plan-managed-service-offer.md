---
title: Planera ett hanterat tjänst erbjudande för Microsofts kommersiella marknads plats
description: Planera ett nytt hanterat tjänst erbjudande för Azure Marketplace med hjälp av programmet för kommersiella marknads platser i Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371948"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Planera ett hanterat tjänst erbjudande för Microsofts kommersiella marknads plats

I den här artikeln beskrivs kraven för att publicera ett hanterat tjänst erbjudande på Microsofts kommersiella marknads plats via partner Center.

Hanterade tjänster är Azure Marketplace-erbjudanden som möjliggör mellan-klienter och hantering av flera innehavare med Azure Lighthouse. Mer information finns i [Vad är Azure Lighthouse?](../lighthouse/overview.md) När en kund köper ett hanterat tjänst erbjudande kan de delegera en eller flera prenumerationer eller resurs grupper. Du kan sedan arbeta med dessa resurser med hjälp av [Azures delegerade resurs hanterings](../lighthouse/concepts/azure-delegated-resource-management.md) funktioner i Azure Lighthouse.

## <a name="eligibility-requirements"></a>Behörighetskrav

Om du vill publicera ett hanterat tjänst erbjudande måste du ha fått Microsoft-kompetensen guld eller silver i moln plattformen. Den här kompetensen visar dina expert kunskaper för kunderna. Mer information finns i [Microsoft Partner Network kompetenser](https://partner.microsoft.com/membership/competencies).

Erbjudanden måste uppfylla alla tillämpliga [certifierings principer för kommersiella marknads platser](/legal/marketplace/certification-policies) som ska publiceras på Azure Marketplace.

## <a name="customer-leads"></a>Kund ledare

Du måste ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) för att samla in kund information. Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namn, ID och onlinebutik där de hittade ditt erbjudande, kommer att skickas till det CRM-system som du har konfigurerat. Den kommersiella Marketplace har stöd för olika typer av CRM-system, tillsammans med alternativet att använda en Azure-tabell eller konfigurera en HTTPS-slutpunkt med energi automatisering.

Du kan lägga till eller ändra en CRM-anslutning när som helst under eller efter att erbjudandet har skapats. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Juridiska kontrakt

På sidan Egenskaper i Partner Center uppmanas du att ange **villkor** för användning av ditt erbjudande. Du kan antingen ange dina villkor direkt i Partner Center eller ange URL: en där de kan hittas. Kunderna måste acceptera dessa villkor innan de kan köpa ditt erbjudande.

## <a name="offer-listing-details"></a>Information om erbjudande lista

När du skapar ett hanterat tjänst erbjudande i Partner Center, anger du text, bilder, dokument och annan information om erbjudandet. Detta är vad kunderna ser när de identifierar ditt erbjudande på Azure Marketplace. Se följande exempel:

:::image type="content" source="media/example-managed-service.png" alt-text="Visar hur ett hanterat tjänst erbjudande visas på Azure Marketplace.":::

**Beskrivningar av anrop**

1. Logotyp
1. Namn
1. Kort beskrivning
1. Kategorier
1. Juridiska avtal och sekretess policy
1. Beskrivning
1. Skärm bilder/videor
1. Användbara länkar

Här är ett exempel på hur erbjudande listan visas i Azure Portal:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal.":::

**Beskrivningar av anrop**

1. Namn
2. Beskrivning
3. Användbara länkar
4. Skärm bilder/videor

> [!NOTE]
> Om ditt erbjudande är på ett annat språk än engelska kan erbjudande listan finnas på det språket, men beskrivningen måste börja eller sluta med den engelska frasen "den här tjänsten är tillgänglig på ett &lt; språk för ditt erbjudande innehåll>". Du kan också tillhandahålla stöd dokument på ett språk som skiljer sig från det som används i listan med erbjudande information.

För att hjälpa till att skapa ett erbjudande enklare kan du förbereda några av dessa objekt i förväg. Följande objekt krävs om inget annat anges.

**Namn**: detta visas som rubrik för din erbjudande lista på den kommersiella marknads platsen. Namnet kan vara ett varumärke. Den får inte innehålla emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken.

**Sammanfattning av Sök Resultat**: Beskriv syftet eller målet med ditt erbjudande på högst 100 tecken. Den här sammanfattningen används i Sök resultaten på den kommersiella Marketplace-listan. Den bör inte vara identisk med rubriken. Överväg att ta med dina främsta SEO-Nyckelord.

**Kort beskrivning**: Ange en kort beskrivning av ditt erbjudande (upp till 256 tecken). Den visas i din erbjudande lista i Azure Portal.

**Beskrivning**: Beskriv ditt erbjudande på 3 000 tecken eller mindre. Den här beskrivningen visas i den kommersiella Marketplace-listan. Överväg bland annat ett värde förslag, viktiga fördelar, kategori-eller bransch associationer och alla nödvändiga upplysningar.

Här följer några tips om hur du skriver beskrivningen:

* Beskriv tydligt värdet för ditt erbjudande i de första meningarna, inklusive:
    * Den typ av användare som har nytta av erbjudandet.
    * Vad kunden behöver eller problem med erbjudandet.
* Kom ihåg att de första meningarna kan visas i Sök resultaten.
* Använd branschspecifika vokabulär.

Du kan använda HTML-taggar för att formatera beskrivningen. Information om HTML-formatering finns [i HTML-taggar som stöds i de kommersiella erbjudandena för Marketplace-erbjudanden](./supported-html-tags.md).

**Länk till sekretess policy**: Ange en URL till sekretess policyn som finns på din webbplats. Du ansvarar för att se till att ditt erbjudande följer sekretess lagar och föreskrifter, och för att tillhandahålla en giltig sekretess policy.

**Användbara länkar** (valfritt): Ladda upp kompletterande online-dokument om ditt erbjudande.

**Kontakt information**: Ange namn, e-postadress och telefonnummer för två personer i företaget (du kan vara en av dem): en support kontakt och en teknisk kontakt. Vi använder den här informationen för att kommunicera med dig om ditt erbjudande. Den här informationen visas inte för kunder, men kan tillhandahållas till leverantörer av moln lösningar (CSP)

**Support-URL: er** (valfritt): om du har support webbplatser för globala Azure-kunder och/eller Azure Government kunder, anger du dessa URL: er.

**Marketplace-Media – logo typer**: Ange en PNG-fil för ditt erbjudande med stor storlek. Partner Center använder det för att skapa medel stora och små logo typer. Du kan också ersätta dessa Logo typer med en annan avbildning senare.

* Den stora logo typen (från 216 x 216 till 350 x 350 BPT) visas på din erbjudande lista på Azure Marketplace.
* Medie logo typen (90 x 90 BPT) visas när en ny resurs skapas.
* Den lilla logo typen (48 x 48 BPT) används i Sök resultaten för Azure Marketplace.

Följ dessa rikt linjer för dina logo typer:

* Se till att bilden inte sträcks ut.
* Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
* De Azure Portal färgerna är vita och svarta. Använd inte dessa som bakgrund för din logo typ. Vi rekommenderar enkla primära färger som gör din logo typ framträdande.
* Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
* Utseendet på logo typen bör vara platt. Undvik övertoningar. Placera inte text på logotypen – inte ens företagets eller varumärkets namn.

**Marketplace-Media – skärm bilder** (valfritt): Lägg till upp till fem bilder som visar hur ditt erbjudande fungerar. Alla bilder måste vara 1280 x 720 bild punkter i storlek och i. PNG-format.

**Marketplace-Media – videor** (valfritt): Ladda upp upp till fem videor som demonstrerar ditt erbjudande. Videorna måste finnas på YouTube eller Vimeo och ha en miniatyr bild (1280 x 720 PNG-fil).

## <a name="preview-audience"></a>Förhandsgranska mål grupp

En förhands gransknings grupp kan komma åt ditt erbjudande innan det publiceras på Azure Marketplace för att testa det. På sidan för **hands versions mål** i Partner Center kan du definiera en begränsad förhands gransknings mål grupp.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat plan. En privat plan är en som du bara gör tillgänglig för en speciell mål grupp som du väljer. På så sätt kan du förhandla fram en anpassad plan med vissa kunder.

Du kan skicka inbjudningar till Microsoft-konto (MSA) eller Azure Active Directory (Azure AD) e-postadresser. Lägg till upp till 10 e-postadresser manuellt eller importera upp till 20 med en. csv-fil. Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

## <a name="plans-and-pricing"></a>Planer och priser

Hanterade tjänst erbjudanden kräver minst en plan. En plan definierar lösningens omfattning, gränser och tillhör ande prissättning, om tillämpligt. Du kan skapa flera planer för ditt erbjudande för att ge dina kunder olika tekniska och pris alternativ. Allmän vägledning om planer, inklusive privata planer, finns i [planer och priser för kommersiella Marketplace-erbjudanden](plans-pricing.md).

Hanterade tjänster har endast stöd för en pris modell: **Bring Your Own License (BYOL)**. Det innebär att du debiteras kunderna direkt, och Microsoft debiterar inte några avgifter.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett erbjudande för hanterad tjänst](./create-managed-service-offer.md)
* [Metodtips för erbjudandelistor](./gtm-offer-listing-best-practices.md)