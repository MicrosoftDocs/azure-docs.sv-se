---
title: Förbättra prestanda genom att komprimera filer i Azure frontend-standard/Premium (för hands version)
description: Lär dig hur du kan förbättra fil överförings hastigheten och öka sid inläsnings prestanda genom att komprimera filerna i Azures front dörr.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099673"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Förbättra prestanda genom att komprimera filer i Azure frontend-standard/Premium (för hands version)

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Fil komprimering är en effektiv metod för att förbättra fil överförings hastigheten och öka sid inläsnings prestanda. Komprimeringen minskar fil storleken innan den skickas av servern. Fil komprimering kan minska bandbredds kostnaderna och ge användarna en bättre upplevelse.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
Det finns två sätt att aktivera fil komprimering på:

- Aktivera komprimering på ursprungs servern. Azures frontend-dörr skickas längs de komprimerade filerna och levererar dem till klienter som begär dem.
- Aktivera komprimering direkt på POP-servrarna i Azures front dörr (*komprimering i farten*). I det här fallet komprimerar Azures front dörr filerna och skickar dem till slutanvändarna.

> [!IMPORTANT]
> Konfigurations ändringar i Azure-frontend tar upp till 10 minuter för att spridas över nätverket. Om du ställer in komprimering för första gången för CDN-slutpunkten bör du vänta 1-2 timmar innan du felsöker för att se till att komprimerings inställningarna har spridit till alla pop.

## <a name="enabling-compression"></a>Aktivera komprimering

> [!Note]
> I Azures front dörr är komprimeringen en del av **aktivering av cachelagring** i väg. När du **aktiverar cachelagring** kan du utnyttja komprimering i Azures front dörr.

Du kan aktivera komprimering på följande sätt:
* Under snabb registrering – när du aktiverar cachelagring kan du Aktivera komprimering.
* Under anpassad skapande – aktivera cachelagring och komprimering när du lägger till en väg. 
* I slut punkts hanterarens flöde.
* På sidan optimering.

### <a name="enable-compression-in-endpoint-manager"></a>Aktivera komprimering i slut punkts hanteraren

1. På sidan Azures standard-/Premium-profil går du till **slut punkts hanteraren** och väljer den slut punkt som du vill aktivera komprimering för.

1. Välj **Redigera slut punkt** och välj sedan den **väg** som du vill aktivera komprimering för. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Skärm bild av slut punkts hanterarens landnings sida." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Se till att **Aktivera cachelagring** är markerat och markera kryss rutan för **Aktivera komprimering**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Aktivera komprimering i slut punkts hanteraren.":::   

1. Välj **Uppdatera** för att spara konfigurationen.

### <a name="enable-compression-in-optimization"></a>Aktivera komprimering under optimering

1. På sidan för Azures front dörr standard/Premium går du till **optimeringar** under Inställningar. Expandera slut punkten om du vill se en lista över vägar. 

1. Markera de tre punkterna bredvid den **väg** som har komprimering *inaktiverat*. Välj sedan **konfigurera routning**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Skärm bild av Aktivera komprimering på optimerings sidan." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Se till att **Aktivera cachelagring** är markerat och markera kryss rutan för **Aktivera komprimering**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Skärm bild som visar aktivering av komprimering i slut punkts hanteraren."::: 

1. Klicka på **Uppdatera**.

## <a name="modify-compression-content-type"></a>Ändra komprimerings innehålls typ

Du kan ändra standard listan över MIME-typer på optimerings sidan.

1. På sidan för Azures front dörr standard/Premium går du till **optimeringar** under Inställningar. Välj sedan den **väg** som har komprimering *aktiverat*.

1. Markera de tre punkterna bredvid den **väg** som har komprimering *aktive rad*. Välj sedan **Visa komprimerade filtyper**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Skärm bild av optimerings sida." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Ta bort standard formaten eller Välj **Lägg** till för att lägga till nya innehålls typer.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Skärm bild av sidan Anpassa fil komprimering."::: 

1. Välj **Spara** för att uppdatera komprimerings konfigurationen.

## <a name="disabling-compression"></a>Inaktivera komprimering

Du kan inaktivera komprimering på följande sätt:
* Inaktivera komprimering i slut punkts hanterarens flöde.
* Inaktivera komprimering på optimerings sidan.

### <a name="disable-compression-in-endpoint-manager"></a>Inaktivera komprimering i slut punkts hanteraren

1. På sidan Azures standard-/Premium-profil går du till **slut punkts hanteraren** under Inställningar. Välj den slut punkt som du vill inaktivera komprimeringen för.

1. Välj **Redigera slut punkt** och välj sedan den **väg** som du vill inaktivera komprimeringen för. Avmarkera kryss rutan **Aktivera komprimering** .

1. Välj **Uppdatera** för att spara konfigurationen.

### <a name="disable-compression-in-optimizations"></a>Inaktivera komprimering i optimeringar

1. På sidan för Azures front dörr standard/Premium går du till **optimeringar** under Inställningar. Välj sedan den **väg** som har komprimering *aktiverat*.

1. Markera de tre punkterna bredvid den **väg** där komprimering *är aktiverat* och välj sedan *konfigurera routning*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Skärm bild av inaktivera komprimering på optimerings sidan."::: 

1. Avmarkera kryss rutan **Aktivera komprimering** .

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Skärm bild av sidan uppdatera väg för att inaktivera komprimering."::: 

1. Välj **Uppdatera** för att spara konfigurationen.

## <a name="compression-rules"></a>Komprimerings regler

I Azures front dörr komprimeras endast kvalificerade filer. För att vara kvalificerad för komprimering måste en fil:
* Vara av en MIME-typ 
* Vara större än 1 KB
* Vara mindre än 8 MB

Dessa profiler stöder följande komprimerings kodningar:
* gzip (GNU zip)
* brotli 

Om begäran stöder fler än en komprimerings typ prioriteras brotli-komprimeringen.

När en begäran för en till gång anger gzip-komprimering och begäran resulterar i en cache missar, gör Azure-frontend gzip-komprimering av till gången direkt på POP-servern. Efteråt hanteras den komprimerade filen från cachen.

Om ursprunget använder Chunked Transfer Encoding (common Table EXPRESSIONS) för att skicka komprimerade data till Azures frontend-POP, stöds inte svars storlekar som är större än 8 MB. 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar din första [regel uppsättning](how-to-configure-rule-set.md)
- Läs mer om [matchnings villkor för regel uppsättning](concept-rule-set-match-conditions.md)
- Läs mer om [regel uppsättningen för Azures frontend-dörr](concept-rule-set.md)
