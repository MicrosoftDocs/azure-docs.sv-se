---
title: Självstudie – Publicera versioner av ditt API med Azure API Management
description: Följ stegen i den här självstudien för att lära dig hur du publicerar flera API-versioner i API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: 5930979258372e4269c4d453ffbc5e0d46258088
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483622"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Självstudie: Publicera flera versioner av ditt API 

Det finns tillfällen när det är opraktiskt att alla anropare till ditt API använder exakt samma version. När anropare vill uppgradera till en senare version vill de ha en metod som är lätt att förstå. Som du ser i den här självstudien är det möjligt att tillhandahålla *flera versioner* i Azure API Management. 

Bakgrundsinformation finns i [Versioner & revisioner.](https://azure.microsoft.com/blog/versions-revisions/)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version till ett befintligt API
> * Välja en version av ett schema
> * Lägga till versionen till en produkt
> * Gå till utvecklarportalen för att se versionen

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Version som visas i Azure Portal":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: Skapa [en Azure API Management instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-version"></a>Lägga till en ny version

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Välj **API:er**.
1. Välj **Demo Conference API** från listan över API. 
1. Välj snabbmenyn (**...**) bredvid **Demo Conference API**.
1. Välj **Lägg till version**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API-snabbmeny – lägg till version":::


> [!TIP]
> Versioner kan också aktiveras när du skapar ett nytt API. På skärmen **Lägg till API** väljer du Version this **API?**.

## <a name="choose-a-versioning-scheme"></a>Välja ett versionsschema

I Azure API Management du hur anropare anger API-versionen genom att välja ett *versionsschema:* **sökväg, rubrik** eller **frågesträng**. I följande exempel *används sökvägen* som versionsschema.

Ange värdena från följande tabell. Välj sedan **Skapa** för att skapa din version.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Fönstret Lägg till version":::



|Inställning   |Värde  |Beskrivning  |
|---------|---------|---------|
|**Namn**     |  *demo-conference-api-v1*       |  Unikt namn i din API Management instans.<br/><br/>Eftersom en version i själva verket är ett nytt API som baseras på en [API-revision](api-management-get-started-revise-api.md)är den här inställningen det nya API:ets namn.   |
|**Versionsschema**     |  **Sökväg**       |  Hur anropare anger API-versionen.     |
|**Versionsidentifierare**     |  *v1*       |  Schemaspecifik indikator för versionen. För **Sökväg**, suffixet för API:ets URL-sökväg. <br/><br/> Om **Rubrik** eller **Frågesträng** har valts anger du ytterligare ett värde: namnet på rubriken eller frågesträngsparametern.<br/><br/> Ett användningsexempel visas.        |
|**Produkter**     |  **Obegränsat**       |  Du kan också välja en eller flera produkter som API-versionen är associerad med. Om du vill publicera API:t måste du associera det med en produkt. Du kan också [lägga till versionen i en produkt](#add-the-version-to-a-product) senare.      |

När du har skapat versionen visas den under **Demo Conference API** i API-listan. Nu visas två API:er: **Ursprungliga** och **v1.**

![Versioner som listas under API i Azure Portal](media/api-management-getstarted-publish-versions/version-list.png)

Nu kan du redigera och konfigurera **v1 som** ett API som är separat från **ursprunglig .** Ändringar i de olika versionerna påverkar inte varandra.

> [!Note]
> Om du lägger till en version i ett API som inte är en version **skapas** även en ursprunglig version automatiskt. Den här versionen svarar på standard-URL:en. Genom att skapa en ursprunglig version säkerställer du att alla befintliga anropare inte bryts av processen med att lägga till en version. Om du skapar ett nytt API med versioner aktiverade i början skapas ingen ursprunglig version.

## <a name="add-the-version-to-a-product"></a>Lägga till versionen till en produkt

För att en anropare ska kunna se den nya versionen, måste den läggas till en *produkt*. Om du inte redan har lagt till versionen i en produkt kan du lägga till den i en produkt när som helst.

Om du till exempel vill lägga till versionen i **produkten Obegränsat:**
1. I Azure Portal navigerar du till din API Management instans.
1. Välj **Products**  >  **Unlimited**  >  **API:er**  >  **+ Lägg till**.
1. Välj **Demo Conference API**, version **v1**.
1. Klicka på **Välj**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Lägg till version till produkt":::

## <a name="use-version-sets"></a>Använda versionsuppsättningar

När du skapar flera versioner skapar Azure Portal en *versionsuppsättning*, som representerar en uppsättning versioner för ett enda logiskt API. Välj namnet på ett API som har flera versioner. Den Azure Portal visar **versionsuppsättningen**. Du kan anpassa namn **och** **beskrivning för** en virtuell uppsättning.

Du kan interagera direkt med versionsuppsättningar med hjälp av Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill se alla versionsuppsättningar kör du [kommandot az apim api versionset list:](/cli/azure/apim/api/versionset#az_apim_api_versionset_list)

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

När Azure Portal skapar en version åt dig tilldelar den ett alfanumeriskt namn som visas i **kolumnen** Namn i listan. Använd det här namnet i andra Azure CLI-kommandon.

Om du vill se information om en versionsuppsättning kör du [kommandot az apim api versionset show:](/cli/azure/apim/api/versionset#az_apim_api_versionset_show)

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Mer information om versionsuppsättningar finns i [Versioner i Azure API Management](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Gå till utvecklarportalen för att se versionen

Om du har provat [utvecklarportalen kan](api-management-howto-developer-portal-customize.md)du se API-versioner där.

1. Välj **Utvecklarportal** i den översta menyn.
2. Välj **API:er** och sedan **Demo Conference API**.
3. Du bör se en listrutan med flera versioner bredvid API-namnet.
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
