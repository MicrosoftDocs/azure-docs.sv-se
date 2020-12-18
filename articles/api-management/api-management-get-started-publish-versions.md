---
title: Självstudie – publicera versioner av ditt API med hjälp av Azure API Management
description: Följ stegen i den här självstudien för att lära dig hur du publicerar flera API-versioner i API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 4a107b4cc0dbf0b0845211ca64691fb0e792a47c
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679086"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Självstudie: publicera flera versioner av ditt API 

Det finns tillfällen när det är opraktiskt att ha alla anropare till din API-användning exakt samma version. När anropare vill uppgradera till en senare version vill de ha en metod som är lätt att förstå. Som du ser i den här självstudien är det möjligt att tillhandahålla flera *versioner* i Azure API Management. 

För bakgrunden, se [versioner & revisioner](https://azure.microsoft.com/blog/versions-revisions/).

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Lägga till en ny version till ett befintligt API
> * Välja en version av ett schema
> * Lägga till versionen till en produkt
> * Gå till utvecklarportalen för att se versionen

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Version som visas i Azure Portal":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-version"></a>Lägga till en ny version

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Välj **API:er**.
1. Välj **Demo Conference API** från listan över API. 
1. Välj snabb menyn (**...**) bredvid **demo konferens-API**.
1. Välj **Lägg till version**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Snabb meny för API – Lägg till version":::


> [!TIP]
> Versioner kan också aktive ras när du skapar ett nytt API. På skärmen **Lägg till API** väljer du **version denna API?**.

## <a name="choose-a-versioning-scheme"></a>Välja ett versionsschema

I Azure API Management väljer du hur anropare anger API-versionen genom att välja ett *versions schema*: **sökväg, sidhuvud** eller **frågesträng**. I följande exempel används *Path* som versions schema.

Ange värdena från följande tabell. Välj **skapa** för att skapa din version.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Fönstret Lägg till version":::



|Inställningen   |Värde  |Beskrivning  |
|---------|---------|---------|
|**Namn**     |  *demo – konferens-API-v1*       |  Unikt namn i API Management-instansen.<br/><br/>Eftersom en version i själva verket är ett nytt API som baseras på en API- [revision](api-management-get-started-revise-api.md)är den här inställningen det nya API: ns namn.   |
|**Versions schema**     |  **Sökväg**       |  Hur anropare anger API-versionen.     |
|**Version identifierare**     |  *v1*       |  Schema-speciell indikator för versionen. För **sökväg**, suffixet för API-URL-sökvägen. <br/><br/> Om du har valt **sidhuvud** eller **frågesträng** anger du ett ytterligare värde: namnet på sidhuvudet eller frågesträngen.<br/><br/> Ett användnings exempel visas.        |
|**Produkter**     |  **Obegränsat**       |  Du kan också välja en eller flera produkter som API-versionen är associerad med. Om du vill publicera API:t måste du associera det med en produkt. Du kan också [lägga till versionen till en produkt](#add-the-version-to-a-product) senare.      |

När du har skapat versionen visas den nu under **demo konferens-API** i API-listan. Nu visas två API: er: **original** och **v1**.

![Versioner som listas under API i Azure Portal](media/api-management-getstarted-publish-versions/version-list.png)

Nu kan du redigera och konfigurera **v1** som ett API som är skilt från **originalet**. Ändringar i de olika versionerna påverkar inte varandra.

> [!Note]
> Om du lägger till en version till en icke-versions-API skapas även ett **original** automatiskt. Den här versionen svarar på standard-URL: en. Om du skapar en ursprunglig version ser du till att alla befintliga anropare inte bryts genom processen för att lägga till en version. Om du skapar ett nytt API med aktiverade versioner i början skapas inte en ursprunglig.

## <a name="add-the-version-to-a-product"></a>Lägga till versionen till en produkt

För att en anropare ska kunna se den nya versionen, måste den läggas till en *produkt*. Om du inte redan har lagt till versionen i en produkt kan du när som helst lägga till den i en produkt.

Till exempel för att lägga till versionen till den **obegränsade** produkten:
1. I Azure Portal navigerar du till API Management-instansen.
1. Välj **produkter**  >  **obegränsat antal**  >  **API: er**  >  **+ Lägg till**.
1. Välj **demo konferens-API**, version **v1**.
1. Klicka på **Välj**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Lägg till version i produkt":::

## <a name="browse-the-developer-portal-to-see-the-version"></a>Gå till utvecklarportalen för att se versionen

Om du har provat [Developer-portalen](api-management-howto-developer-portal-customize.md)kan du se API-versioner där.

1. Välj **Utvecklarportal** i den översta menyn.
2. Välj **API:er** och sedan **Demo Conference API**.
3. Du bör se en listruta med flera versioner bredvid API-namnet.
4. Välj **v1**.
5. Observera **Fråge-URL** för den första åtgärden i listan. Det visar att URL-sökvägen för API:et innehåller **v1**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till en ny version till ett befintligt API
> * Välja en version av ett schema 
> * Lägga till versionen till en produkt
> * Gå till utvecklarportalen för att se versionen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Anpassa stilen på utvecklarportalens sidor](api-management-howto-developer-portal-customize.md)
