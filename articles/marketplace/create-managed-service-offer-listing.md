---
title: Så här konfigurerar du den hanterade tjänstens erbjudande om registrering i Microsoft Partner Center
description: Lär dig hur du konfigurerar den hanterade tjänstens erbjudande lista över information på Azure Marketplace med partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 5ab831ae6ab761804fc7a4665000a13ab61acadc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918444"
---
# <a name="how-to-configure-your-managed-service-offer-listing-details"></a>Så här konfigurerar du den hanterade tjänstens erbjudande List information

Den information som du anger på sidan för **erbjudande listan** i Partner Center visas på Azure Marketplace. Detta inkluderar namnet på erbjudandet, beskrivningen, mediet och andra marknadsförings till gångar.

> [!NOTE]
> Om ditt erbjudande är på ett annat språk än engelska kan erbjudande listan finnas på det språket, men beskrivningen måste börja eller sluta med den engelska frasen "den här tjänsten är tillgänglig på ett &lt; språk för ditt erbjudande innehåll>". Du kan också tillhandahålla stöd dokument på ett språk som skiljer sig från det som används i listan med erbjudande information.

Ange den information som beskrivs nedan på sidan **erbjudande lista** i Partner Center. Om du vill veta mer om registrerings informationen för ditt hanterade tjänst erbjudande kan du gå igenom [planera ett hanterat tjänst erbjudande](./plan-managed-service-offer.md).

## <a name="marketplace-details"></a>Information om Marketplace

1. Rutan **namn** fylls i i förväg med det namn som du angav tidigare i dialog rutan Nytt erbjudande, men du kan ändra det när du vill. Namnet visas som rubrik på din erbjudande lista i onlinebutiken.
2. Beskriv syftet eller målet med ditt erbjudande på högst 100 tecken i rutan **Sammanfattning av Sök Resultat** .
3. I fältet **kort beskrivning** anger du en kort beskrivning av ditt erbjudande (upp till 256 tecken). Den visas i din erbjudande lista i Azure Portal.
4. Beskriv det hanterade tjänst erbjudandet i fältet **Beskrivning** . Du kan ange upp till 2 000 tecken text i den här rutan, inklusive HTML-taggar och blank steg. Information om HTML-formatering finns [i HTML-taggar som stöds i erbjudandena beskrivningar](./supported-html-tags.md).
5. I **länk rutan sekretess policy** anger du en länk (från och med https) till din organisations sekretess policy. Du är ansvarig för att se till att ditt erbjudande följer sekretess lagar och föreskrifter, och för att tillhandahålla en giltig sekretess policy.

## <a name="useful-links"></a>Användbara länkar

Du har möjlighet att tillhandahålla kompletterande online-dokument om din lösning:

1. Välj **Lägg till en länk**.
2. Ange ett namn och en webb adress (från och med https) för varje dokument.

## <a name="contact-information"></a>Kontaktinformation

Ange namn, e-postadress och telefonnummer för två personer i företaget (du kan vara en av dem): en support kontakt och en teknisk kontakt. Vi använder den här informationen för att kommunicera med dig om ditt erbjudande. Den här informationen visas inte för kunder, men kan tillhandahållas av CSP-partner (Cloud Solution Provider).

## <a name="support-urls"></a>Support-URL: er

Om du har support webbplatser för globala Azure-kunder och/eller Azure Government kunder, anger du URL: en från https.

## <a name="marketplace-media"></a>Marketplace-Media

> [!NOTE]
> Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som `https://upload.xboxlive.com` används av Partner Center.

### <a name="add-logos"></a>Lägg till logo typer

Under **logo typer** laddar du upp en **stor** logo typ i. PNG-format mellan 216 x 216 och 350 x 350 bild punkter. Partner Center skapar automatiskt **medel** och **liten** logo typer, som du kan ersätta senare.

* Den stora logo typen (från 216 x 216 till 350 x 350 BPT) visas på din erbjudande lista på Azure Marketplace.
* Medie logo typen (90 x 90 BPT) visas när en ny resurs skapas.
* Den lilla logo typen (48 x 48 BPT) används i Sök resultaten för Azure Marketplace.

### <a name="add-screenshots-optional"></a>Lägg till skärm bilder (valfritt)

Lägg till upp till fem bilder som demonstrerar ditt erbjudande. Alla bilder måste vara 1280 x 720 bild punkter i storlek och i. PNG-format.

1. Dra och släpp din PNG-fil under **skärm bilder** i rutan **skärm bild** .
2. Välj **Lägg till bild rubrik**.
3. I dialog rutan som visas anger du en beskrivning.
4. Upprepa steg 1 till 3 för att lägga till ytterligare skärm bilder.

### <a name="add-videos-optional"></a>Lägg till videor (valfritt)

Du kan lägga till länkar till YouTube eller Vimeo-videor som demonstrerar ditt erbjudande. Dessa videor visas för kunder tillsammans med ditt erbjudande. Du måste ange en miniatyr bild av videon, storlek 1280 x 720 pixlar och i. PNG-format. Lägg till högst fem videor per erbjudande.

1. Under **videor** väljer du **länken Lägg till video**.
2. I rutorna som visas anger du namn och länk för videon.
3. Dra och släpp en PNG-fil (1280 x 720 pixlar) till den grå **miniatyr** rutan.
4. Upprepa steg 1 till 3 för att lägga till en annan video.

Välj **Spara utkast** innan du fortsätter till nästa flik: **Förhandsgranska mål grupp**.

## <a name="next-steps"></a>Nästa steg

* [Lägg till en målgrupp för förhandsversionen](create-managed-service-offer-preview.md)
