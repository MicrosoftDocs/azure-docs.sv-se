---
title: Så här lägger du till en anpassad domän i din Azure Front Door Standard/Premium SKU-konfiguration
description: I den här självstudien lär du dig att registrera en anpassad domän för att Azure Front Door Standard/Premium SKU.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387929"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Skapa en anpassad domän på Azure Front Door Standard/Premium SKU (förhandsversion) med hjälp av Azure Portal

> [!Note]
> Den här dokumentationen är Azure Front Door Standard/Premium (förhandsversion). Letar du efter information om Azure Front Door? Visa [här](../front-door-overview.md).

När du använder Azure Front Door Standard/Premium för programleverans krävs en anpassad domän om du vill att ditt eget domännamn ska vara synligt i dina slutanvändares begäranden. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.

När du har Azure Front Door en Standard/Premium-profil har standardvärden för frontend en underdomän azurefd.net. Den här underdomänen inkluderas i URL:en när Azure Front Door Standard/Premium levererar innehåll från din backend som standard. Till exempel `https://contoso-frontend.azurefd.net/activeusers.htm`. Av bekvämlighetsskäl tillhandahåller Azure Front Door möjligheten att associera en anpassad domän med standardvärden. Med det här alternativet kan du leverera ditt innehåll med en anpassad domän i url:en i stället för ett Azure Front Door Standard-/Premium-ägda domännamn. Till exempel " https://www.contoso.com/photo.png ".

> [!IMPORTANT]
> Azure Front Door Standard/Premium (förhandsversion) är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [**Kompletterande användningsvillkor för förhandsversioner Microsoft Azure förhandsversioner.**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Förutsättningar
* Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [Snabbstart: Skapa en Front Door Standard/Premium.](create-front-door-portal.md)

* Om du inte redan har en anpassad domän måste du först köpa en med en domänleverantör. Se exempelvis [Köpa ett anpassat domännamn](../../app-service/manage-custom-dns-buy-domain.md).

* Om du använder Azure som värd för [dina DNS-domäner](../../dns/dns-overview.md)måste du delegera domänleverantörens DNS (Domain Name System) till en Azure DNS. Mer information finns i [Delegera en domän till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Om du använder en domänprovider för att hantera DNS-domänen måste du i annat fall verifiera domänen manuellt genom att ange de DNS TXT-poster som efterfrågas.

## <a name="add-a-new-custom-domain"></a>Lägga till en ny anpassad domän

> [!NOTE]
> I offentlig förhandsversion stöds inte användning Azure DNS för att skapa Apex-domäner på Azure Front Door Standard/Premium. Det finns andra DNS-leverantörer som stöder CNAME-utplattning eller DNS-ning som gör att APEX-domäner kan användas för Azure Front Door Standard/Premium.

En anpassad domän hanteras av avsnittet Domäner i portalen. En anpassad domän kan skapas och verifieras före associationen till en slutpunkt. En anpassad domän och dess underdomäner kan bara associeras med en enda slutpunkt i taget. Du kan dock använda olika underdomäner från samma anpassade domän för olika ytterdörrar. Du kan också mappa anpassade domäner med olika underdomäner till samma Front Door slutpunkt.

1. Under Inställningar för Azure Front Door profil väljer du *Domäner*  och sedan knappen **Lägg till en** domän.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Skärmbild av knappen Lägg till domän på landningssidan för domänen.":::

1. Sidan **Lägg till en** domän visas där du kan ange information om den anpassade domänen. Du kan välja Azure-hanterad DNS, vilket rekommenderas eller så kan du välja att använda din egen DNS-provider. Om du väljer Azure-hanterad DNS väljer du en befintlig DNS-zon och väljer sedan en anpassad underdomän eller skapar en ny. Om du använder en annan DNS-provider anger du det anpassade domännamnet manuellt. Välj **Lägg till** för att lägga till din anpassade domän.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Skärmbild av sidan Lägg till en domän.":::

    En ny anpassad domän skapas med valideringstillståndet **Skicka**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Skärmbild av sändning av domänvalideringstillstånd.":::

    Vänta tills valideringstillståndet ändras **till Väntar.** Den här åtgärden kan ta några minuter.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Skärmbild av väntande domänverifieringstillstånd.":::

1. Välj **väntande** valideringstillstånd. En ny sida visas med den DNS TXT-postinformation som behövs för att verifiera den anpassade domänen. TXT-posten har formen `_dnsauth.<your_subdomain>` . Om du använder en Azure DNS zon väljer  du knappen Lägg till så skapas en ny TXT-post med det visade postvärdet i Azure DNS zonen. Om du använder en annan DNS-provider skapar du manuellt en ny TXT-post med namn med `dnsauth.<your_subdomain>` postvärdet som visas på sidan.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Skärmbild av sidan Verifiera anpassad domän.":::

1. Välj uppdateringsstatus. När domänen har verifierats med DNS TXT-posten ändras valideringsstatusen till **verifierad**. Det kan ta några minuter att verifiera den här åtgärden.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Skärmbild av verifierad anpassad domän.":::

1. Stäng sidan för att återgå till landningssidan för listan över anpassade domäner. Etableringstillståndet för den anpassade domänen bör ändras till **Etablerat** och valideringstillståndet bör ändras till **Godkänd.**

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Skärmbild av status för etablerade och godkända.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associera den anpassade domänen med din Front Door slutpunkt

När du har verifierat din anpassade domän kan du lägga till den i din Azure Front Door Standard/Premium-slutpunkten.

1. När den anpassade domänen har verifierats kan du associera den med en befintlig Azure Front Door Standard/Premium-slutpunkt och väg. Välj länken Endpoint Association (Slutpunktsassociaty) för **att öppna sidan Associate endpoint and routes (Associera slutpunkt och** vägar).  Välj en slutpunkt och vägar som du vill associera med. Välj sedan **Associera.** Stäng sidan när associera-åtgärden har slutförts.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Skärmbild av sidan associera slutpunkt och vägar.":::

    Statusen för slutpunktsassociatyten bör ändras så att den återspeglar den slutpunkt som den anpassade domänen för närvarande är associerad med. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Skärmbild av länken för slutpunktsassociatyer.":::

1. Välj länken DNS-tillstånd.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Skärmbild av länken DNS-tillstånd.":::

1. Sidan **Lägg till eller uppdatera CNAME-posten** visas och visar den CNAME-postinformation som måste anges innan trafiken kan börja flöda. Om du använder Azure DNS zoner kan du skapa CNAME-posterna genom att välja **knappen** Lägg till på sidan. Om du använder en annan DNS-provider måste du manuellt ange CNAME-postnamnet och värdet som visas på sidan.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Skärmbild av att lägga till eller uppdatera CNAME-posten.":::

1. När CNAME-posten har skapats och den anpassade domänen är associerad Azure Front Door slutpunkten slutförs börjar trafikflödet flöda.

    > [!NOTE]
    > Om HTTPS är aktiverat kan det ta några minuter att etablera och sprida certifikat eftersom spridningen görs till alla gränsplatser. 

## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har verifierat och associerat den anpassade domänen kontrollerar du att den anpassade domänen är korrekt refererad till slutpunkten.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Skärmbild av verifierad och associerad anpassad domän.":::

Kontrollera slutligen att ditt programinnehåll blir betjänat med hjälp av en webbläsare.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du aktiverar HTTPS för din anpassade domän fortsätter du till nästa självstudie.

> [!div class="nextstepaction"]
> [Aktivera HTTPS för en anpassad domän]()
