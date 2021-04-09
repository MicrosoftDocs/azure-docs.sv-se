---
title: Så här konfigurerar du din Azure Application lista över erbjudanden om erbjudanden
description: Lär dig hur du konfigurerar registrerings information för ditt Azure Application-erbjudande i Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 91b6d432042aa37a1b929f61e9742f814fb9dc40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370495"
---
# <a name="how-to-configure-your-azure-application-offer-listing-details"></a>Så här konfigurerar du din Azure Application lista över erbjudanden om erbjudanden

Den information som du anger på sidan för **erbjudande listan** för ditt Azure Application-erbjudande visas i Microsoft-onlinebutiker online. Här ingår beskrivningar av ditt erbjudande, skärm bilder och dina marknadsförings resurser. Om du vill se vad det ser ut, se [erbjudande lista](plan-azure-application-offer.md#offer-listing-details).

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder och användnings villkor) behöver inte vara på engelska om erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [icke-engelskt språk]". Det är också acceptabelt att tillhandahålla en _användbar länk_ -URL för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

## <a name="marketplace-details"></a>Information om Marketplace

På sidan **erbjudande lista** , under **Marketplace-information**, utför du följande steg. Om du vill veta mer om dessa uppgifter som krävs, se [erbjudande lista](plan-azure-application-offer.md#offer-listing-details).

1. Rutan **namn** är förifylld med det namn som du angav tidigare i dialog rutan **nytt erbjudande** . Du kan ändra namnet när som helst. Det namn som du anger här visas för kunder som rubrik på din erbjudande lista.
1. Ange upp till 100 tecken text i rutan **Sammanfattning av Sök Resultat** . Den här sammanfattningen används i Sök resultaten för Marketplace-listan.
1. I rutan **kort beskrivning** anger du upp till 256 tecken med oformaterad text. Den här sammanfattningen visas på ditt erbjudandes informations sida.
1. I rutan **Beskrivning** anger du en beskrivning för ditt erbjudande. Den här text rutan innehåller text redigerings kontroller som du kan använda för att göra din beskrivning mer engagerande. Du kan också använda HTML-taggar för att formatera beskrivningen. Du kan ange upp till 3 000 tecken text i den här rutan, inklusive HTML-kod och blank steg. Information om HTML-formatering finns [i HTML-taggar som stöds i de kommersiella erbjudandena för Marketplace-erbjudanden](supported-html-tags.md).
1. Valfritt I rutorna **Sök Nyckelord** anger du upp till tre Sök nyckelord som kunder kan använda för att hitta ditt erbjudande i den kommersiella Marketplace. Du behöver inte inkludera erbjudandets **namn** och **Beskrivning** eftersom den texten automatiskt ingår i sökningen.
1. I **länk rutan sekretess policy** anger du en länk (från och med https) till din organisations sekretess policy. Du är ansvarig för att se till att din app uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

## <a name="add-supplemental-links-optional"></a>Lägg till kompletterande länkar (valfritt)

Slutför de här stegen för att lägga till länkar till kompletterande onlinedokumentation.

1. Om du vill lägga till valfria kompletterande online-dokument om din app eller relaterade tjänster väljer du **Lägg till en länk** under **användbara länkar**.
1. I de fält som visas anger du en rubrik (upp till 255 tecken) och länken (från och med `https://` ) till online-dokumentet.
1. Upprepa steg 1 till 2 om du vill ange en annan länk.

## <a name="enter-your-contact-information"></a>Ange din kontakt information

Ange information för följande kontakter under **kontakt information**:

- **Support kontakt** (krävs) – för allmänna supportfrågor.
- Teknisk **kontakt** (krävs) – för tekniska frågor. Vi kommer att använda den här informationen för att kontakta dig när det finns problem med erbjudandet, inklusive certifierings problem.
- **Kontakta CSP-programmet** (valfritt) – för support-och affärs problem. Den här informationen visas bara för CSP-partner.

För varje kontakt anger du ett namn, telefonnummer och e-postadress (de visas inte offentligt). Det krävs en support-URL för support kontakten (detta kommer att visas offentligt).

1. I rutan **support kontakt** anger du ett namn, en e-postadress, telefonnummer och webb adressen till din support sida.
1. Ange ett namn, en e-postadress och telefonnummer i rutan **teknisk kontakt** .
1. Valfritt I rutan **kontakt i CSP-programmet** anger du ett namn, en e-postadress och ett telefonnummer.
1. Om du vill utöka erbjudandet till program varan för [Cloud Solution Provider (CSP)](cloud-solution-providers.md), i rutan **marknadsförings material för CSP** , anger du en länk till marknadsförings materialet.

    > [!NOTE]
    > CSP-programmet utökar erbjudandet till ett bredare utbud av kvalificerade kunder genom att göra det möjligt för CSP-partner att paketera, marknadsföra och sälja ditt erbjudande. Dessa åter försäljare behöver åtkomst till material för marknadsföring av ditt erbjudande. Mer information finns i [Go-to-Marketing med Microsoft](https://partner.microsoft.com/reach-customers/gtm).

## <a name="add-marketplace-media"></a>Lägg till Marketplace-Media

### <a name="store-logos"></a>Butiks logo typer

Under **logo typer** överför du en **stor** logo typ i PNG-format mellan 216 x 216 och 350 x 350 bild punkter. Partner Center skapar automatiskt **små** (48 x 48) och **medium** (90 x 90) logo typer, som du kan ersätta senare om du vill.

Alla tre logo typernas storlekar används på olika platser i onlinebutiker.

- Den stora logo typen visas på sidan med din erbjudande lista i Azure Marketplace.
- Medie logo typen visas när du skapar en ny resurs i Microsoft Azure.
- Den lilla logo typen visas i Sök resultaten för Azure Marketplace.

### <a name="add-screenshots-optional"></a>Lägg till skärm bilder (valfritt)

Lägg till upp till fem skärm bilder som demonstrerar ditt erbjudande. Alla bilder måste vara 1280 x 720 bild punkter i storlek och i. PNG-format.

1. Dra och släpp i **skärm bilder**. PNG-fil till **skärm bild** rutan.
1. Klicka på ikonen Redigera bredvid **Lägg till bild text**.
1. I dialog rutan som visas anger du en beskrivning och klickar på **OK**.
1. Upprepa steg 1 till 3 för att lägga till ytterligare skärm bilder.

### <a name="add-videos-optional"></a>Lägg till videor (valfritt)

Du kan lägga till länkar till YouTube eller Vimeo-videor som demonstrerar ditt erbjudande. Dessa videor visas för kunder tillsammans med ditt erbjudande. Du måste ange en miniatyr bild av videon, ändra storlek till 1280 x 720 bild punkter i storlek och i. PNG-format. Du kan lägga till högst fyra videor per erbjudande.

1. Under **videor** väljer du länken **Lägg till video** .
1. I rutorna som visas anger du namn och länk för videon.
1. Dra och släpp en. PNG-fil (1280 x 720 pixlar) till den grå **miniatyr** rutan.
1. Upprepa steg 1 till 3 för att lägga till en annan video.

> [!TIP]
> Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

Välj **Spara utkast** innan du fortsätter till nästa flik: förhandsgranska mål grupp.

## <a name="next-steps"></a>Nästa steg

- [Så här lägger du till en förhands gransknings mål grupp för ditt Azure Application erbjudande](create-new-azure-apps-offer-preview.md)
