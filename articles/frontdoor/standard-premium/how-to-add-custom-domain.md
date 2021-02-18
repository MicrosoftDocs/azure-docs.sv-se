---
title: Så här lägger du till en anpassad domän i din Azure frontend-konfiguration för standard/Premium-SKU
description: I den här självstudien får du lära dig hur du integrerar en anpassad domän i Azures SKU för standard/Premium.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100456"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Skapa en anpassad domän i Azure frontend standard/Premium SKU (för hands version) med hjälp av Azure Portal

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

När du använder Azures frontend-standard/Premium för program leverans krävs en anpassad domän om du vill att ditt eget domän namn ska visas i dina slut användar förfrågningar. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.

När du har skapat en standard-/Premium-profil för Azure-dörren har standard klient dels värden en under domän till azurefd.net. Den här under domänen tas med i URL: en när Azure frontend-dörren standard/Premium levererar innehåll från din server del som standard. Till exempel `https://contoso-frontend.azurefd.net/activeusers.htm`. Av bekvämlighetsskäl tillhandahåller Azure Front Door möjligheten att associera en anpassad domän med standardvärden. Med det här alternativet levererar du ditt innehåll med en anpassad domän i din URL i stället för ett domän namn som ägs av Azure front dörr standard/Premium. Till exempel https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i kompletterande användnings [**villkor för Microsoft Azure för hands**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="prerequisites"></a>Förutsättningar
* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [snabb start: skapa en front dörr standard/Premium](create-front-door-portal.md).

* Om du inte redan har en anpassad domän måste du först köpa en med en domän leverantör. Se exempelvis [Köpa ett anpassat domännamn](../../app-service/manage-custom-dns-buy-domain.md).

* Om du använder Azure som värd för dina [DNS-domäner](../../dns/dns-overview.md)måste du delegera DOMÄNens DNS-domännamn (Domain Name System) till en Azure DNS. Mer information finns i [delegera en domän till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Annars, om du använder en domän leverantör för att hantera DNS-domänen, måste du verifiera domänen manuellt genom att ange de begärda DNS-TXT-posterna.

## <a name="add-a-new-custom-domain"></a>Lägg till en ny anpassad domän

Avsnittet för en anpassad domän hanteras av domäner i portalen. En anpassad domän kan skapas och verifieras före en koppling till en slut punkt. En anpassad domän och dess under domäner kan bara associeras med en enda slut punkt i taget. Du kan dock använda olika under domäner från samma anpassade domän för olika front dörrar. Du kan också mappa anpassade domäner med olika under domäner till samma front dörrs slut punkt.

1. Under Inställningar för din Azure-profil för front dörr väljer du *domäner*  och sedan knappen **Lägg till en domän** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Skärm bild av knappen Lägg till domän på domän landnings sidan.":::

1. Sidan **Lägg till en domän** visas där du kan ange information om den anpassade domänen. Du kan välja Azure-hanterad DNS, vilket rekommenderas, eller så kan du välja att använda en egen DNS-Provider. Om du väljer Azure-hanterad DNS väljer du en befintlig DNS-zon och väljer sedan en anpassad under domän eller skapar en ny. Om du använder en annan DNS-Provider anger du det anpassade domän namnet manuellt. Välj **Lägg till** för att lägga till din anpassade domän.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Skärm bild av sidan Lägg till en domän.":::

    En ny anpassad domän skapas med validerings tillstånd för att **Skicka** in.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Skärm bild av det går inte att skicka in domän validerings tillstånd.":::

    Vänta tills verifierings läget ändras till **väntar**. Den här åtgärden kan ta några minuter.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Skärm bild av domän validerings tillstånd väntar.":::

1. Välj validerings tillstånd **väntar** . En ny sida visas med DNS TXT-postinformation som krävs för att verifiera den anpassade domänen. TXT-posten är i form av `_dnsauth.<your_subdomain>` . Om du använder Azure DNS-baserad zon väljer du knappen **Lägg till** och en ny TXT-post med värdet Visa post skapas i zonen Azure DNS. Om du använder en annan DNS-Provider skapar du manuellt en ny TXT-post `dnsauth.<your_subdomain>` med namnet med post svärdet som visas på sidan.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Skärm bild av sidan Verifiera en anpassad domän.":::

1. Välj uppdaterings status. När domänen har verifierats med DNS TXT-posten ändras verifierings statusen till **verifierad**. Den här åtgärden kan ta några minuter att validera.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Skärm bild av anpassad domän verifierad.":::

1. Stäng sidan för att återgå till den anpassade listan med landnings sidor för anpassade domäner. Etablerings statusen för den anpassade domänen ska ändras till **etablerad** och validerings status bör ändras till **godkänd**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Skärm bild av statusen etablerad och godkänd.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Koppla den anpassade domänen till din front dörrs slut punkt

När du har verifierat din anpassade domän kan du lägga till den i din Azure frontend-standard/Premium-slutpunkt.

1. När den anpassade domänen har verifierats kan du associera den med en befintlig Azure frontend-standard/Premium-slutpunkt och-väg. Välj länken för **slut punkts associering** för att öppna sidan **koppla slut punkt och vägar** . Välj en slut punkt och de vägar som du vill associera med. Välj sedan **associera**. Stäng sidan när associerings åtgärden har slutförts.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Skärm bild av sidan koppla slut punkt och vägar.":::

    Status för slut punkts associationen bör ändras för att avspegla den slut punkt som den anpassade domänen för närvarande är associerad med. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Skärm bild av länk till slut punkts Association.":::

1. Välj länken DNS-tillstånd.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Skärm bild av länk till DNS-tillstånd.":::

1. Sidan **Lägg till eller uppdatera CNAME-post** visas och visar CNAME-postinformation som måste anges innan trafik kan börja flöda. Om du använder Azure DNS värdbaserade zoner kan du skapa CNAME-posterna genom att välja knappen **Lägg till** på sidan. Om du använder en annan DNS-Provider måste du manuellt ange CNAME-postens namn och värde som det visas på sidan.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Skärm bild av posten för att lägga till eller uppdatera CNAME.":::

1. När CNAME-posten har skapats och den anpassade domänen är kopplad till Azures frontend-slutpunkt slutförd börjar trafikflödet att flöda.

    > [!NOTE]
    > Om HTTPS är aktiverat kan certifikat etablering och-spridning ta några minuter eftersom spridningen görs till alla gräns platser. 

## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har verifierat och associerat den anpassade domänen kontrollerar du att den anpassade domänen är korrekt refererad till din slut punkt.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Skärm bild av verifierad och associerad anpassad domän.":::

Sedan kan du kontrol lera att ditt program innehåll kommer att hanteras med hjälp av en webbläsare.

## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie om du vill lära dig hur du aktiverar HTTPS för din anpassade domän.

> [!div class="nextstepaction"]
> [Aktivera HTTPS för en anpassad domän]()
