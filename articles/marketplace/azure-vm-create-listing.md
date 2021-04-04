---
title: Konfigurera List information om erbjudanden för virtuella datorer på Azure Marketplace
description: Lär dig hur du konfigurerar den virtuella datorns erbjudande om registrering på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 7030ffeb49c0a3919894f6dcf81e7252777267c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629605"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Så här konfigurerar du List information om erbjudanden för virtuella datorer

På sidan **erbjudande lista** (Välj från vänster-navigerings-menyn i Partner Center) definierar du erbjudande information som erbjudande namn, beskrivning, länkar och kontakter.

> [!NOTE]
> Ditt erbjudande som visar innehåll, till exempel beskrivning, dokument, skärm bilder och användnings villkor, behöver inte vara på engelska, så länge som erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt i \<non-English language> ". Du kan också ange en webb adress för att länka till en webbplats som erbjuder innehåll på ett annat språk än det som används i list innehållet för erbjudandet.

## <a name="marketplace-details"></a>Information om Marketplace

### <a name="name"></a>Name

Namnet som du anger här visas för kunder som titel på din erbjudande lista. Det här fältet fylls i automatiskt med det namn som du angav i rutan för **erbjudet alias** när du skapade erbjudandet. Namnet:

- Kan innehålla varumärkes-och Copyright-symboler.
- Måste vara 50 tecken eller mindre.
- Det går inte att inkludera emojis.

### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av ditt erbjudande för visning i Azure Marketplace Sök resultat. Det kan innehålla upp till 100 tecken.

### <a name="short-description"></a>Kort beskrivning

Ange en längre Beskrivning av ditt erbjudande för visning i Azure Marketplace Sök resultat. Det kan innehålla upp till 256 tecken.

### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Använd HTML-taggar för att formatera beskrivningen så att den blir mer intressant. En lista över tillåtna taggar finns i [HTML-taggar som stöds](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange webb adressen (URL) för din organisations sekretess policy. Se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du måste också publicera en giltig sekretess policy på din webbplats.

## <a name="useful-links"></a>Användbara länkar

Ge kompletterande online-dokument om ditt erbjudande. Om du vill lägga till en länk väljer du **Lägg till en länk** och fyller i följande fält:

- **Namn**: kunder kommer att se namnet på informations sidan.
- **Länk**: Ange en webb adress som kunder kan använda för att visa ditt online-dokument.

## <a name="customer-support-links"></a>Kund support länkar

Ange support webbplatsen där kunderna kan kontakta support teamet.

- Azure Global support webbplats
- Azure Government support webbplats

## <a name="partner-support-contact"></a>Support kontakt för partner

Ange **namn**, **e-postadress** och **telefonnummer** för Microsoft-partner som ska användas när kunderna öppnar ett support ärende. Den här informationen visas inte på Azure Marketplace.

## <a name="engineering-contact"></a>Teknisk kontakt

Ange **namn**, **e-postadress** och **telefonnummer** för Microsoft som ska användas när det finns problem med erbjudandet, inklusive problem med certifieringen. Den här informationen visas inte på Azure Marketplace.

## <a name="cloud-solution-provider-program-contact"></a>Program kontakt för Cloud Solution Provider

Ange **namn**, **e-postadress** och **telefon** för den person som hanterar ditt CSP-program (om tillämpligt). Den här informationen visas inte på Azure Marketplace. För **CSP-programmet marknadsförings material** anger du webb adressen där partner i det här programmet kan visa ditt marknadsförings material.

## <a name="marketplace-media"></a>Marketplace-Media

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder gör att ditt bidrag avvisas.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att det lokala nätverket inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

### <a name="logos"></a>Typerna

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Medel** (90 x 90 BPT, valfritt)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Varje skärm bild måste vara 1280 x 720 bild punkter i storlek och i PNG-format. Varje skärm bild måste innehålla en beskrivning.

### <a name="videos"></a>Video

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Videor bör finnas på en extern video tjänst. Ange varje video namn, webb adress och en miniatyr bild av videon med 1280 x 720 pixlar.

Mer information om resurser för Marketplace finns i [metod tips för Marketplace-erbjudanden](gtm-offer-listing-best-practices.md).

Välj **Spara utkast** innan du fortsätter till nästa flik på vänster-navigerings-menyn, **förhands gransknings mål**.

## <a name="next-steps"></a>Nästa steg

- [Skapa en förhands gransknings publik](azure-vm-create-preview.md)
