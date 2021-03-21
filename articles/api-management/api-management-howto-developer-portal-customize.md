---
title: Självstudie – åtkomst till och anpassning av Developer-portalen – Azure API Management | Microsoft Docs
description: 'I den här kursen får du lära dig hur du anpassar API Management Developer-portalen, en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er.'
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012995"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Självstudie: åtkomst och anpassning av Developer-portalen

*Developer-portalen* är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er. Det är där API-konsumenter kan identifiera dina API: er, lära sig hur de används och begära åtkomst.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Öppna den hanterade versionen av Developer-portalen
> * Navigera i det administrativa gränssnittet
> * Anpassa innehållet
> * Publicera ändringarna
> * Visa den publicerade portalen

Du hittar mer information om Developer-portalen i [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management Developer-portalen – administratörs läge" border="false":::

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Importera och publicera en Azure API Management-instans. Mer information finns i [Importera och publicera](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Få åtkomst till portalen som administratör

Följ stegen nedan för att få åtkomst till den hanterade versionen av portalen.

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Välj **Developer Portal** -knappen i det övre navigerings fältet. En ny flik i webbläsaren med en administrativ version av portalen öppnas.

## <a name="understand-the-portals-administrative-interface"></a>Förstå portalens administrativa gränssnitt

### <a name="default-content"></a>Standard innehåll 

Om du ansluter till portalen för första gången, tillhandahålls standard innehållet automatiskt i bakgrunden. Standard innehållet har utformats för att presentera portalens funktioner och minimera anpassningarna som behövs för att anpassa portalen. Du kan läsa mer om vad som ingår i Portal innehållet i [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Visuell redigerare

Du kan anpassa portalens innehåll med den visuella redigeraren. 
* I meny avsnitten till vänster kan du skapa eller ändra sidor, media, layouter, menyer, format eller webbplats inställningar. 
* Med meny alternativen längst ned kan du växla mellan visnings punkter (till exempel mobil eller stationär), Visa elementen i portalen som är synliga för autentiserade eller anonyma användare eller Spara eller ångra åtgärder.
* Lägg till rader till en sida genom att klicka på en blå ikon med ett plus tecken. 
* Widgetar (till exempel text, bilder eller API-listor) kan läggas till genom att trycka på en grå ikon med ett plus tecken.
* Arrangera om objekt på en sida med dra och släpp-interaktionen. 

### <a name="layouts-and-pages"></a>Layouter och sidor

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Sidor och layouter" border="false":::

Layouter definierar hur sidor visas. I standard innehållet finns det till exempel två layouter: en gäller för start sidan och den andra till alla återstående sidor.

En layout tillämpas på en sida genom att matcha dess URL-mall till sidans URL. Till exempel kommer en layout med en URL-mall att `/wiki/*` tillämpas på varje sida med `/wiki/` segmentet i URL: en: `/wiki/getting-started` , `/wiki/styles` osv.

I den föregående bilden markeras innehåll som tillhör layouten i blått, medan sidan markeras med rött. Meny avsnitten markeras.

### <a name="styling-guide"></a>Hand bok

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Hand bok" border="false":::

Hand boken är en panel som skapats med designers i åtanke. Det gör det möjligt för att visa och formatera alla visuella element i portalen. Formatet är hierarkiskt-många element ärver egenskaper från andra element. Till exempel använder knapp element färger för text och bakgrund. Om du vill ändra färg på en knapp måste du ändra den ursprungliga varianten av färg.

Om du vill redigera en variant markerar du den och väljer Penn ikonen som visas överst. När du har gjort ändringarna i popup-fönstret stänger du det.

### <a name="save-button"></a>Knappen Spara

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Knappen Spara" border="false":::

När du gör en ändring i portalen måste du spara den manuellt genom att välja knappen **Spara** i menyn längst ned eller trycka på [Ctrl] + [S]. När du sparar ändringarna överförs det ändrade innehållet automatiskt till din API Management-tjänst.

## <a name="customize-the-portals-content"></a>Anpassa portalens innehåll

Innan du gör din portal tillgänglig för besökarna bör du anpassa det automatiskt genererade innehållet. Rekommenderade ändringar omfattar layouter, format och innehållet på Start sidan.

> [!NOTE]
> På grund av integrations överväganden kan följande sidor inte tas bort eller flyttas under en annan URL:,,,,,,,,, `/404` `/500` `/captcha` `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` , `/signup` .

### <a name="home-page"></a>Startsida

Standard **Start** sidan är fylld med plats hållarens innehåll. Du kan antingen ta bort hela avsnitt som innehåller det här innehållet eller behålla strukturen och justera elementen ett i taget. Ersätt den genererade texten och bilderna med dina egna och se till att länkarna pekar på önskade platser.

### <a name="layouts"></a>Former

Ersätt den automatiskt genererade logo typen i navigerings fältet med din egen bild.

### <a name="styling"></a>Formatera

Även om du inte behöver justera några format kan du överväga att justera vissa element. Ändra till exempel den primära färgen så att den matchar ditt märkes färg.

### <a name="customization-example"></a>Exempel på anpassning

I följande video visar vi hur du redigerar innehållet i portalen, anpassar webbplatsens utseende och publicerar ändringarna.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publicera portalen

Om du vill göra din portal och dess senaste ändringar tillgängliga för besökare måste du *publicera* den. Du kan publicera portalen i portalens administrativa gränssnitt eller från Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publicera från det administrativa gränssnittet

1. Se till att du har sparat ändringarna genom att välja ikonen **Spara** .
1. I avsnittet **åtgärder** på menyn väljer du **Publicera webbplats** . Den här åtgärden kan ta några minuter.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publicera Portal" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publicera från Azure Portal

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. På den vänstra menyn, under **Developer-portalen**, väljer du **Portal översikt**.
1. I fönstret **Portal översikt** väljer du **publicera**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publicera portal från Azure Portal":::

> [!NOTE]
> Portalen måste publiceras igen efter API Management tjänst konfigurations ändringar. Publicera till exempel portalen igen när du har tilldelat en anpassad domän, uppdaterat identitets leverantörer, ange delegering eller ange inloggnings-och produkt villkor.


## <a name="visit-the-published-portal"></a>Besök den publicerade portalen

När du har publicerat portalen kan du använda den på samma URL som administrations panelen, till exempel `https://contoso-api.developer.azure-api.net` . Visa den i en separat webbläsarsession (med Incognito eller privat webbläsare) som extern besökare.

## <a name="apply-the-cors-policy-on-apis"></a>Använd CORS-principen för API: er

Om du vill låta besökare i portalen testa API: erna via den inbyggda interaktiva konsolen aktiverar du CORS (resurs delning mellan ursprung) i dina API: er. Mer information finns i [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Nästa steg

Läs mer om Developer-portalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Migrera till den nya Developer-portalen](developer-portal-deprecated-migration.md) från den föråldrade gamla portalen.