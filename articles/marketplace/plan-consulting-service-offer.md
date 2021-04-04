---
title: Planera ett konsult tjänst erbjudande för Microsofts kommersiella marknads plats
description: Planera ett nytt konsult tjänst erbjudande för Microsoft AppSource eller Azure Marketplace med hjälp av programmet för kommersiella marknads platser i Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 10c06f5b36de880302f17dc17c1d47d5209dd902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588479"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>Planera ett konsult tjänst erbjudande i den kommersiella marknads platsen

I den här artikeln beskrivs de olika alternativen och kraven för att publicera konsult tjänst erbjudanden till Microsofts kommersiella marknads platser. Läs den här artikeln för att förbereda ditt erbjudande om publicering med partner Center.

## <a name="whats-a-consulting-service"></a>Vad är en konsult tjänst?

Konsult tjänster är virtuella eller kund engagemang som stöder och utökar kundernas användning av Microsoft-produkter. Med en konsult tjänst kan kunderna utvärdera, utvärdera och distribuera lösningar som ger ytterligare deras affärs mål. Du väljer omfattning, varaktighet och pris struktur (fast pris eller kostnads fri) för din tjänst.

Konsult tjänst erbjudanden publiceras som **kontakta mig** -listor. Det innebär att kunder kommer att kontakta dig direkt från den information som du anger i din lista. Microsoft är värd för listan på den kommersiella marknads platsen och hanterar alla interaktioner med kunder. Du ansvarar för att uppfylla service, fakturering, fakturering och insamling från kunden.

## <a name="primary-products-and-online-stores"></a>Primära produkter och onlinebutiker

Varje konsult tjänst måste fokusera på någon av dessa Microsoft-produkter, som kallas **primär produkt** för erbjudandet:

:::row:::
    :::column:::
        Azure

        Dynamics 365 Business Central

        Dynamics 365-handel

        Dynamics 365 Customer Insights

        Dynamics 365-kundtjänster

        Dynamics 365-fälttjänster

        Dynamics 365-ekonomi

        Dynamics 365-marknadsföring
    :::column-end:::
    :::column:::
        Dynamics 365-automatisering av Project service

        Dynamics 365-försäljning

        Dynamics 365-hantering av leverans kedjan

        Microsoft 365

        Power Apps

        Power Automate

        Power BI

        Power Virtual Agents
    :::column-end:::
:::row-end:::

Den primära produkt du väljer i Partner Center avgör om ditt erbjudande ska publiceras till Microsoft AppSource eller Azure Marketplace.

* Om den primära produkten av konsult tjänsten är Azure visas erbjudandet på Azure Marketplace.
* Om den primära produkten inte är Azure, visas erbjudandet på AppSource.

Mer information om skillnaderna mellan AppSource och Azure Marketplace finns i onlinebutiker för [kommersiella Marketplace](./overview.md#commercial-marketplace-online-stores).

## <a name="eligibility-requirements"></a>Behörighetskrav

För att demonstrera kundernas expertis i ett fält måste du uppfylla ett antal krav innan du publicerar ett konsult tjänst erbjudande. Kraven beror på produkten med ditt erbjudandes kärna. En fullständig lista över krav krav för varje primär produkt finns i [certifierings principerna för konsult tjänster](/legal/marketplace/certification-policies#800-consulting-services).

> [!NOTE]
> För vissa primära produkter måste du ha en Microsoft-kompetens på guld eller silver i ditt lösnings områden. Mer information finns i [Microsoft Partner Network kompetenser](https://partner.microsoft.com/membership/competencies).

## <a name="service-type-and-duration"></a>Tjänste typ och varaktighet

Den kommersiella Marketplace har stöd för fem typer av konsult tjänster:

* **Utvärdering**: en utvärdering av en kunds miljö för att fastställa tillämpligheten för en lösning och beräkna kostnaden och tids linjen för implementeringen.
* **Kort**: en introduktion till en lösning eller en tjänst med hjälp av ramverk, demonstrationer och kund exempel.
* **Implementering**: en fullständig installation som resulterar i en helt fungerande lösning.
* **Koncept bevis**: en begränsad omfattning för att avgöra om en lösning uppfyller kundens krav.
* **Workshop**: ett interaktivt engagemang på kundens plats. Det kan omfatta utbildning, kortfattade, utvärderingar eller demonstrationer som bygger på kundens data eller miljö.

Tjänsten bör ha en fast och fördefinierad varaktighet på upp till 10 veckor. Tjänste tiden måste göras explicit i erbjudande listan.

## <a name="customer-leads"></a>Kund ledare

Du måste ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) för att samla in kund information. Kunden uppmanas att ange behörighet för att dela sin information. Dessa kund uppgifter, tillsammans med erbjudande namn, ID och onlinebutik där de hittade ditt erbjudande, kommer att skickas till det CRM-system som du har konfigurerat. Den kommersiella Marketplace har stöd för olika typer av CRM-system, tillsammans med alternativet att använda en Azure-tabell eller konfigurera en HTTPS-slutpunkt med energi automatisering.

Du kan lägga till eller ändra en CRM-anslutning när som helst under eller efter att erbjudandet har skapats. Mer detaljerad information finns i [kund leads från ditt kommersiella Marketplace-erbjudande](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="offer-listing-details"></a>Information om erbjudande lista

När du skapar ett konsult tjänst erbjudande i Partner Center, anger du text, bilder, dokument och annan information om erbjudandet. Detta är vad kunderna ser när de identifierar ditt erbjudande i AppSource eller Azure Marketplace. Se följande exempel:

![Visar hur ett konsult tjänst erbjudande visas i onlinebutiker](./media/example-consulting-service.png)

**Beskrivningar av anrop**

1. Logotyp
2. Typ av tjänst
3. Kompetenser
4. Lösnings områden (Azure Marketplace)/produkter (AppSource)
5. Branscher
6. Land/region
7. Erbjudandets namn
8. Sammanfattning av Sök Resultat
9. Beskrivning
10. Skärm bilder/videor
11. Stöd dokument

> [!NOTE]
> Om du levererar din tjänst på ett annat språk än engelska kan erbjudande listan finnas på det språket, men beskrivningen måste börja eller sluta med den engelska frasen "den här tjänsten är tillgänglig på ett &lt; språk för ditt erbjudande innehåll>". Du kan också tillhandahålla stöd dokument på ett språk som skiljer sig från det som används i listan med erbjudande information.

För att hjälpa till att skapa ett erbjudande enklare kan du förbereda några av dessa objekt i förväg. Följande objekt krävs om inget annat anges.

**Namn**: namnet visas som rubrik på din erbjudande lista på den kommersiella Marketplace. Namnet kan vara ett varumärke. Den får inte innehålla emojis (om de inte är varumärken och Copyright-symboler) och måste vara begränsade till 50 tecken. Namnet måste innehålla erbjudandets varaktighet och service typ för att maximera sökmotorns optimering (SEO). Det format som krävs är *Namn: varaktighet och typ*. Ta inte med ditt företags namn om det inte också är produkt namnet. Här är några exempel:

|Säg inte |Säg  |
|---|---|
|Komma igång med Azure IoT i tillverkning |IoT för produktion: 2 dagars utvärdering |
|Workshop på smarta popups |Smarta popup-användare: 1 – veckas workshop |
|SQL Server migrering av PoC enligt contoso |SQL-migrering: 3 – veckor proof of Concept |
| | |

**Sammanfattning av Sök Resultat**: Beskriv syftet eller målet med ditt erbjudande på högst 200 tecken. Den här sammanfattningen används i Sök resultaten på den kommersiella Marketplace-listan. Den bör inte vara identisk med rubriken. Överväg att ta med dina främsta SEO-Nyckelord.

**Beskrivning**: den här beskrivningen kommer att visas i den kommersiella Marketplace-listan. Överväg att inkludera ett värde förslag, viktiga fördelar, avsedd användar bas och eventuella kategori-eller bransch associationer.

När du skriver beskrivningen följer du dessa kriterier enligt tjänst typen:

|Typ av tjänst |Beskrivnings krav |
|---|---|
|Utvärdering |Inkludera en detaljerad agenda för utvärderingar som är längre än en dag och tydliggör vilken slut produkt som kunden kan förväntar sig. |
|Genom gång |Förklara vilken slut produkt som kunden kan förväntar sig.|
|Implementering |Lägg till en detaljerad agenda för implementeringar som är längre än en dag och Beskriv vilka tekniska förändringar, tekniska artefakter eller andra artefakter som en kund kan förväntar sig som ett resultat av ärendet. |
|Konceptbevis |Beskriv vilka tekniska förändringar, tekniska artefakter eller andra artefakter som en kund kan förväntar sig som ett resultat av ärendet. |
|Workshop |Ta med en detaljerad dag-, vecko-eller månads Agenda beroende på ditt erbjudandes varaktighet. Tydliggör inlärnings målen eller andra slut produkter i din workshop. |
| | |

Här följer några tips om hur du skriver beskrivningen:

* Beskriv tydligt värdet för ditt erbjudande i de första meningarna, inklusive:
    * Den typ av användare som har nytta av erbjudandet.
    * Kund behov eller problem som erbjuder erbjudandet.
* Kom ihåg att de första meningarna kan visas i Sök resultaten.
* Om priset för ditt erbjudande är uppskattat förklarar du vilka variabler som kommer att fastställa det slutliga priset.
* Använd branschspecifika vokabulär.

Du kan använda HTML-taggar för att formatera beskrivningen. Du kan ange upp till 2 000 tecken text i den här rutan, inklusive HTML-taggar och blank steg. Information om HTML-formatering finns [i HTML-taggar som stöds i de kommersiella erbjudandena för Marketplace-erbjudanden](./supported-html-tags.md).

**Sök Nyckelord** (valfritt): ange upp till tre Sök nyckelord som kunder kan använda för att hitta ditt erbjudande i onlinebutiker. Du behöver inte inkludera erbjudandets **namn** och **Beskrivning**.

**Varaktighet**: ditt konsult tjänst erbjudande måste ha en fördefinierad varaktighet på upp till 10 veckor.

**Kontakt information**: i Partner Center uppmanas du att ange namn, e-postadress och telefonnummer för två personer i företaget (du kan vara en av de två kontakterna). Vi använder den här informationen för att kommunicera med dig om ditt erbjudande. Den här informationen visas inte för kunder, men kan tillhandahållas av CSP-partner (Cloud Solution Provider).

**Stöd dokument**: överför minst ett och upp till tre kund motstående PDF-dokument som innehåller information om ditt erbjudande. De kan till exempel vara fakta blad eller broschyrer.

**Marketplace-Media – logo typer**: Ange en PNG-fil för stor logo typ. Partner Center kommer att använda den för att skapa en liten logo typ. Du kan välja att ersätta den lilla logo typen med en annan avbildning senare.

* Stor (från 216 x 216 till 350 x 350 BPT, krävs)
* Liten (48 x 48 BPT, valfritt)

Den stora logo typen visas på sidan med din erbjudande lista i Azure Marketplace eller AppSource. Den lilla logo typen visas i Sök resultaten för Azure Marketplace eller på sidan för AppSource huvud sida och Sök resultat.

Följ dessa rikt linjer för dina logo typer:

* Se till att bilden inte är suddig eller utsträckt.
* Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
* Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.
* Undvik övertoningar i logo typen eller bakgrunden. Placera inte text på logotypen – inte ens företagets eller varumärkets namn.

**Marketplace-Media – skärm bilder**: Lägg till minst en och upp till fem bilder som demonstrerar ditt erbjudande. Alla bilder måste vara 1280 x 720 bild punkter i storlek och i. PNG-format.

**Marketplace-Media – videor** (valfritt): du kan lägga till upp till fyra videor som demonstrerar ditt erbjudande. Videorna måste finnas på YouTube eller Vimeo och ha en miniatyr bild (1280 x 720 PNG-fil).

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Ditt konsult tjänst erbjudande kan göras tillgängligt i ett eller flera länder eller regioner. I Partner Center kan du bestämma priset för varje marknad du väljer. En fullständig lista över marknader och valutor som stöds finns i [geografisk tillgänglighet och valuta support för den kommersiella marknads platsen](./marketplace-geo-availability-currencies.md).


## <a name="next-steps"></a>Nästa steg

* [Skapa ett konsult tjänst erbjudande i den kommersiella marknads platsen](./create-consulting-service-offer.md)
* [Metodtips för erbjudandelistor](./gtm-offer-listing-best-practices.md)