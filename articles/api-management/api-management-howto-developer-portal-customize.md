---
title: Självstudie – Få åtkomst till och anpassa utvecklarportalen – Azure API Management | Microsoft Docs
description: Följ den här självstudien om du vill lära dig hur du anpassar API Management-utvecklarportalen, en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API:er.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 7c341dee3106530715248355da4412b97ed30980
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739628"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Självstudie: Få åtkomst till och anpassa utvecklarportalen

*Utvecklarportalen* är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API:er. Det är där API-konsumenter kan identifiera dina API:er, lära sig hur de används och begära åtkomst.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Få åtkomst till den hanterade versionen av utvecklarportalen
> * Navigera i det administrativa gränssnittet
> * Anpassa innehållet
> * Publicera ändringarna
> * Visa den publicerade portalen

Du hittar mer information på utvecklarportalen i Översikt över [Azure API Management utvecklarportalen.](api-management-howto-developer-portal.md)

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management utvecklarportalen – administratörsläge" border="false":::

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Importera och publicera en Azure API Management instans. Mer information finns i [Importera och publicera](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Få åtkomst till portalen som administratör

Följ stegen nedan för att få åtkomst till den hanterade versionen av portalen.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Välj knappen **Utvecklarportal** i det övre navigeringsfältet. En ny webbläsarflik med en administrativ version av portalen öppnas.


## <a name="developer-portal-architectural-concepts"></a>Arkitekturbegrepp för utvecklarportalen

Portalkomponenterna kan delas in logiskt i två kategorier: *kod* och *innehåll*.

### <a name="code"></a>Kod

Koden finns på GitHub API Management portalen [och](https://github.com/Azure/api-management-developer-portal) innehåller:

- **Widgetar** – representerar visuella element och kombinerar HTML, JavaScript, format förmåga, inställningar och innehållsmappning. Exempel är en bild, ett textstycke, ett formulär, en lista över API:er osv.
- **Formatdefinitioner** – ange hur widgetar kan formateras
- **Motor** – som genererar statiska webbsidor från portalinnehåll och skrivs i JavaScript
- **Visuell redigerare** – möjliggör anpassning och redigering i webbläsaren

### <a name="content"></a>Innehåll

Innehållet är indelat i två underkategorier: *portalinnehåll* *och API Management innehåll*.

*Portalinnehållet* är specifikt för portalen och innehåller:

- **Sidor** – till exempel landningssida, API-självstudier, blogginlägg
- **Media** – bilder, animeringar och annat filbaserat innehåll
- **Layouter** – mallar som matchas mot en URL och definierar hur sidor visas
- **Format** – värden för formatdefinitioner, till exempel teckensnitt, färger, kantlinjer
- **Inställningar** – konfigurationer somicon, webbplatsmetadata

    Portalinnehåll, förutom media, uttrycks som JSON-dokument.

*API Management innehåller entiteter* som API:er, åtgärder, produkter och prenumerationer.
## <a name="understand-the-portals-administrative-interface"></a>Förstå portalens administrativa gränssnitt

### <a name="default-content"></a>Standardinnehåll 

Om du använder portalen för första gången etableras standardinnehållet automatiskt i bakgrunden. Standardinnehåll har utformats för att demonstrera portalens funktioner och minimera de anpassningar som behövs för att anpassa portalen. Du kan lära dig mer om vad som ingår i portalinnehållet i [Översikt över Azure API Management utvecklarportalen.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Visuellt redigeringsprogram

Du kan anpassa innehållet i portalen med den visuella redigeraren. 
* Med menyavsnitten till vänster kan du skapa eller ändra sidor, media, layouter, menyer, format eller webbplatsinställningar. 
* Med menyalternativen längst ned kan du växla mellan visningsområdet (till exempel mobil eller stationär dator), visa de element i portalen som är synliga för autentiserade eller anonyma användare eller spara eller ångra åtgärder.
* Lägg till rader på en sida genom att klicka på en blå ikon med ett plustecken. 
* Widgetar (till exempel text, bilder eller API:er) kan läggas till genom att trycka på en grå ikon med ett plustecken.
* Ordna om objekt på en sida med dra och släpp-interaktionen. 

### <a name="layouts-and-pages"></a>Layouter och sidor

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Sidor och layouter" border="false":::

Layouter definierar hur sidor visas. I standardinnehållet finns det till exempel två layouter: en gäller för startsidan och den andra för alla återstående sidor.

En layout tillämpas på en sida genom att matcha dess URL-mall med sidans URL. Till exempel tillämpas en layout med en URL-mall av på varje `/wiki/*` sida med segmentet i `/wiki/` URL:en: `/wiki/getting-started` , `/wiki/styles` osv.

I föregående bild markeras innehåll som hör till layouten i blått, medan sidan är markerad i rött. Menyavsnitten är markerade.

### <a name="styling-guide"></a>Formatguide

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Formatguide" border="false":::

Formatguiden är en panel som skapats med designers i åtanke. Det gör det möjligt att övervaka och formatera alla visuella element i portalen. Formateringen är hierarkisk – många element ärver egenskaper från andra element. Knappelement använder till exempel färger för text och bakgrund. Om du vill ändra en knapps färg måste du ändra den ursprungliga färgvarianten.

Om du vill redigera en variant markerar du den och väljer pennikonen som visas ovanpå den. När du har gjort ändringarna i popup-fönstret stänger du det.

### <a name="save-button"></a>Knappen Spara

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Knappen Spara" border="false":::

När du gör en ändring i portalen måste du  spara den manuellt genom att välja knappen Spara i menyn längst ned eller trycka på [Ctrl]+[S]. När du sparar ändringarna överförs det ändrade innehållet automatiskt till din API Management tjänst.

## <a name="customize-the-portals-content"></a>Anpassa portalens innehåll

Innan du gör portalen tillgänglig för besökare bör du anpassa det automatiskt genererade innehållet. Rekommenderade ändringar omfattar layouter, format och innehållet på startsidan.

> [!NOTE]
> På grund av integrationsöverväganden kan följande sidor inte tas bort eller flyttas under en annan URL: `/404` , , , , , , , , , `/500` , , `/captcha` , , , `/change-password` , `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` `/signup` .

### <a name="home-page"></a>Startsida

Standardsidan **Start** är fylld med platshållarinnehåll. Du kan antingen ta bort hela avsnitt som innehåller det här innehållet eller behålla strukturen och justera elementen en i varje. Ersätt den genererade texten och bilderna med din egen och se till att länkarna pekar på önskade platser.

### <a name="layouts"></a>Layouter

Ersätt den automatiskt genererade logotypen i navigeringsfältet med din egen bild.

### <a name="styling"></a>Styling

Även om du inte behöver justera några format kan du överväga att justera vissa element. Du kan till exempel ändra den primära färgen så att den matchar ditt varumärkes färg.

### <a name="customization-example"></a>Anpassningsexempel

I följande video visar vi hur du redigerar innehållet i portalen, anpassar webbplatsens utseende och publicerar ändringarna.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publicera portalen

Om du vill göra din portal och dess senaste ändringar tillgängliga för besökare, måste *du publicera* den. Du kan publicera portalen i portalens administrativa gränssnitt eller från Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publicera från det administrativa gränssnittet

1. Se till att du har sparat ändringarna genom att välja **ikonen** Spara.
1. I avsnittet **Åtgärder** på menyn väljer du **Publicera webbplats.** Den här åtgärden kan ta några minuter.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publicera portal" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publicera från Azure Portal

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. I den vänstra menyn, under **Utvecklarportalen,** väljer du **Portalöversikt.**
1. I **portalens översiktsfönster** väljer du **Publicera.**

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publicera portalen från Azure Portal":::

> [!NOTE]
> Portalen måste publiceras igen efter att API Management av tjänstkonfigurationen. Du kan till exempel publicera om portalen när du har tilldelar en anpassad domän, uppdatera identitetsproviders, ange delegering eller ange inloggnings- och produktvillkor.


## <a name="visit-the-published-portal"></a>Besök den publicerade portalen

När du har publicerat portalen kan du komma åt den på samma URL som den administrativa panelen, till exempel `https://contoso-api.developer.azure-api.net` . Visa den i en separat webbläsarsession (med inkognitoläge eller privat surfläge) som en extern besökare.

## <a name="apply-the-cors-policy-on-apis"></a>Tillämpa CORS-principen på API:er

Om du vill att portalbesökarna ska kunna testa API:erna via den inbyggda interaktiva konsolen aktiverar du CORS (resursdelning mellan ursprung) på dina API:er. Mer information finns i Vanliga [frågor och svar om Azure API Management-utvecklarportalen.](developer-portal-faq.md#cors)

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Migrera till den nya utvecklarportalen](developer-portal-deprecated-migration.md) från den inaktuella äldre portalen.