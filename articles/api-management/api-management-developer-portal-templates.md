---
title: Anpassa API Management Developer-portalen med hjälp av mallar
titleSuffix: Azure API Management
description: Lär dig hur du anpassar Azure API Management Developer-portalen med hjälp av mallar.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092893"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Anpassa Azure API Management Developer-portalen med hjälp av mallar

Det finns tre grundläggande metoder för att anpassa utvecklarportalen i Azure API Management:

* [Redigera innehållet på statiska sidor och sidlayoutelement][modify-content-layout]
* [Uppdatera formaten som används för sidelement i utvecklingsportalen][customize-styles]
* [Ändra mallarna som används för sidor som genereras av portalen][portal-templates] (förklaras i den här hand boken)

Mallar används för att anpassa innehållet i systemgenererade utvecklings Portal sidor (till exempel API-dokument, produkter, användarautentisering osv.). Genom att använda [DotLiquid](http://dotliquidmarkup.org/) -syntax och en angiven uppsättning lokaliserade sträng resurser, ikoner och sid kontroller har du stor flexibilitet att konfigurera innehållet på sidorna när du ser anpassa.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Översikt över utvecklare Portal mallar

Redigera mallar görs från **Developer-portalen** när du är inloggad som administratör. Börja med att öppna Azure Portal och klicka på **Developer Portal** i tjänst verktygsfältet i API Management-instansen.

Du kommer åt Developer Portal-mallarna genom att klicka på anpassnings ikonen till vänster för att Visa anpassnings menyn och klicka på **mallar**.

![Skärm bild som visar anpassnings ikonen på menyn anpassa.][api-management-customize-menu]

I listan mallar visas flera kategorier av mallar som täcker de olika sidorna i Developer-portalen. Varje mall är annorlunda, men stegen för att redigera dem och publicera ändringarna är desamma. Om du vill redigera en mall klickar du på namnet på mallen.

![Mallar för utvecklare][api-management-templates-menu]

Genom att klicka på en mall går du till sidan för utvecklings portalen som är anpassningsbar med mallen. I det här exemplet visas **produkt list** mal len. Mallen för **produkt listan** styr området på skärmen som anges av den röda rektangeln.

![Mall för produkt lista][api-management-developer-portal-templates-overview]

Vissa mallar, t. ex. mallar för **användar profiler** , anpassar olika delar av samma sida.

![Mallar för användar profiler][api-management-user-profile-templates]

Redigeraren för varje utvecklare Portal mall har två avsnitt som visas längst ned på sidan. Den vänstra sidan visar redigerings fönstret för mallen och den högra sidan visar mallens data modell.

Redigerings fönstret för mallar innehåller den markering som styr utseendet och beteendet för motsvarande sida i Developer-portalen. Markeringen i mallen använder [DotLiquid](http://dotliquidmarkup.org/) -syntaxen. Ett populärt redigerings program för DotLiquid är [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Alla ändringar som görs i mallen under redigering visas i real tid i webbläsaren, men de visas inte för dina kunder förrän du [sparar](#to-save-a-template) och [publicerar](#to-publish-a-template) mallen.

![Mallkod][api-management-template]

Fönstret **mall data** innehåller en guide till data modellen för de entiteter som är tillgängliga för användning i en viss mall. Den innehåller den här guiden genom att visa de real tids data som för närvarande visas i Developer-portalen. Du kan expandera fönstret mallar genom att klicka på rektangeln i det övre högra hörnet i fönstret **mallens data** .

![Mallens data modell][api-management-template-data]

I föregående exempel finns det två produkter som visas i Developer-portalen som hämtades från de data som visas i fönstret **mall data** , som du ser i följande exempel:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

Markeringen i **produkt list** mal len bearbetar data för att tillhandahålla önskade utdata genom att gå igenom insamling av produkter för att visa information och en länk till varje enskild produkt. Observera `<search-control>` `<page-control>` elementen och i markeringen. Dessa styr visningen av Sök-och sid kontroller på sidan. `ProductsStrings|PageTitleProducts` är en lokaliserad sträng referens som innehåller `h2` sidans sidhuvud text. En lista över sträng resurser, sid kontroller och ikoner som är tillgängliga för användning i mallar för utvecklare finns i [API Management Developer Portal templates Reference](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Så här sparar du en mall
Om du vill spara en mall klickar du på Spara i redigeraren för mallar.

![Spara mallen][api-management-save-template]

Sparade ändringar är inte aktiva i Developer-portalen förrän de har publicerats.

## <a name="to-publish-a-template"></a>Publicera en mall
Sparade mallar kan publiceras antingen individuellt eller tillsammans. Om du vill publicera en enskild mall klickar du på Publicera i redigeraren för mallar.

![Publicera mall][api-management-publish-template]

Bekräfta och gör mallen Live på Developer-portalen genom att klicka på **Ja** .

![Skärm bild som visar var du väljer Ja för att göra mallen Live.][api-management-publish-template-confirm]

Publicera alla för tillfället opublicerade versioner genom att klicka på **publicera** i listan mallar. Opublicerade mallar anges med en asterisk som följer mallnamnet. I det här exemplet publiceras **produkt listan** och **produkt** mallarna.

![Publicera mallar][api-management-publish-templates]

Klicka på **publicera anpassningar** för att bekräfta.

![Bekräfta publicering][api-management-publish-customizations]

Nyligen publicerade mallar träder i kraft direkt i Developer-portalen.

## <a name="to-revert-a-template-to-the-previous-version"></a>Återställa en mall till den tidigare versionen
Om du vill återställa en mall till den tidigare publicerade versionen klickar du på Återställ i redigeraren för mallar.

![Skärm bild som visar den ikon du använder för att återställa en mall.][api-management-revert-template]

Bekräfta genom att klicka på **Ja**.

![Skärm bild som visar var du väljer Ja för att bekräfta ändringarna.][api-management-revert-template-confirm]

Den tidigare publicerade versionen av en mall är Live i Developer-portalen när återställnings åtgärden har slutförts.

## <a name="to-restore-a-template-to-the-default-version"></a>Återställa en mall till standard versionen
Att återställa mallar till standard versionen är en två stegs process. Först måste mallarna återställas och de återställda versionerna måste publiceras.

Om du vill återställa en enskild mall till standard versionen klickar du på Återställ i redigeraren för mallar.

![Återställ mall][api-management-reset-template]

Bekräfta genom att klicka på **Ja**.

![Bekräfta][api-management-reset-template-confirm]

Om du vill återställa alla mallar till sina standard versioner klickar du på **Återställ standardmallar** i mal listan.

![Återställa mallar][api-management-restore-templates]

De återställda mallarna måste sedan publiceras separat eller samtidigt genom att följa stegen i [för att publicera en mall](#to-publish-a-template).

## <a name="next-steps"></a>Nästa steg
Referens information för mallar för utvecklare, sträng resurser, ikoner och sid kontroller finns i [API Management Developer Portal templates Reference](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
