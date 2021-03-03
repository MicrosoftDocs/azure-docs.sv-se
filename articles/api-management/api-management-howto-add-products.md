---
title: Självstudie – Skapa och publicera en produkt i Azure API Management
description: 'I den här självstudien skapar du och publicerar en produkt i Azure API Management. När den har publicerats kan utvecklare börja använda produktens API: er.'
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "100545164"
---
# <a name="tutorial-create-and-publish-a-product"></a>Självstudie: skapa och publicera en produkt  

I Azure API Management innehåller en [*produkt*](api-management-terminology.md#term-definitions) en eller flera API: er samt användnings kvot och användnings villkor. När en produkt har publicerats kan utvecklarna prenumerera på produkten och börja använda produktens API: er.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management produkter i portalen":::


## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Skapa och publicera en produkt

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på Azure Portal och navigera till API Management-instansen.
1. I det vänstra navigerings fältet väljer du **produkter**  >  **+ Lägg till**.
1.  I fönstret **Lägg till produkt** anger du de värden som beskrivs i följande tabell för att skapa din produkt.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Lägg till produkt i portalen":::

    | Namn                     | Beskrivning                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Visningsnamn             | Namnet som du vill att det ska visas i [Developer-portalen](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Beskrivning              | Ange information om produkten, till exempel dess syfte, de API: er som ger åtkomst till och annan information.                                                                                                                                               |
    | Stat                    | Välj **publicerad** om du vill publicera produkten. Produkten måste vara publicerad innan API:er i en produkt kan anropa den. Som standard är nya produkter opublicerade och visas endast i gruppen  **Administratörer** .                                                                                      |
    | Prenumeration krävs    | Välj om en användare måste prenumerera för att använda produkten.                                                                                                                                                                                                                                   |
    | Godkännande krävs        | Välj om du vill att en administratör ska granska och godkänna eller avvisa prenumerations försök till den här produkten. Om du inte väljer det här godkänns prenumerations försök automatiskt.                                                                                                                         |
    | Antal tillåtna prenumerationer | Du kan också begränsa antalet flera samtidiga prenumerationer.                                                                                                                                                                                                                                |
    | Juridiska villkor              | Du kan inkludera användningsvillkor för produkten som prenumeranter måste godkänna för att kunna använda produkten.                                                                                                                                                                                                             |
    | API:er                     | Välj en eller flera API: er. Du kan också lägga till API: er när du har skapat produkten. Mer information finns i [lägga till API: er till en produkt](#add-apis-to-a-product) senare i den här artikeln. |

3. Välj **skapa** för att skapa den nya produkten.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Skapa en produkt genom att köra kommandot [AZ APIM Product Create](/cli/azure/apim/product#az_apim_product_create) :

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Du kan ange olika värden för din produkt:

   | Parameter | Beskrivning |
   |-----------|-------------|
   | `--product-name` | Namnet som du vill att det ska visas i [Developer-portalen](api-management-howto-developer-portal.md). |
   | `--description`  | Ange information om produkten, till exempel dess syfte, de API: er som ger åtkomst till och annan information. |
   | `--state`        | Välj **publicerad** om du vill publicera produkten. Produkten måste vara publicerad innan API:er i en produkt kan anropa den. Som standard är nya produkter opublicerade och visas endast i gruppen  **Administratörer** . |
   | `--subscription-required` | Välj om en användare måste prenumerera för att använda produkten. |
   | `--approval-required` | Välj om du vill att en administratör ska granska och godkänna eller avvisa prenumerations försök till den här produkten. Om du inte väljer det här godkänns prenumerations försök automatiskt. |
   | `--subscriptions-limit` | Du kan också begränsa antalet flera samtidiga prenumerationer.|
   | `--legal-terms`         | Du kan inkludera användningsvillkor för produkten som prenumeranter måste godkänna för att kunna använda produkten. |

Om du vill se dina aktuella produkter använder du kommandot [AZ APIM Product List](/cli/azure/apim/product#az_apim_product_list) :

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Du kan ta bort en produkt genom att använda kommandot [AZ APIM Product Delete](/cli/azure/apim/product#az_apim_product_delete) :

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Lägga till fler konfigurationer

Fortsätt att konfigurera produkten när du har sparat den. I API Management-instansen väljer du produkten i fönstret **produkter** . Lägg till eller uppdatera:

|Objekt   |Beskrivning  |
|---------|---------|
|Inställningar     |    Produktens metadata och tillstånd     |
|API:er     |  API: er kopplade till produkten       |
|[Principer](api-management-howto-policies.md)     |  Principer som tillämpas på produkt-API: er      |
|Åtkomstkontroll     |  Produkt synlighet för utvecklare eller gäster       |
|[Prenumerationer](api-management-subscriptions.md)    |    Produkt prenumeranter     |

## <a name="add-apis-to-a-product"></a>Lägga till API:er till en produkt

Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. När produkten skapas kan du lägga till en eller flera befintliga API: er. Du kan också lägga till API: er till produkten senare, antingen från sidan produkt **Inställningar** eller när du skapar ett API.

Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

### <a name="add-an-api-to-an-existing-product"></a>Lägga till en API till en befintlig produkt

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I det vänstra navigerings fönstret på API Management-instansen väljer du **produkter**.
1. Välj en produkt och välj sedan **API: er**.
1. Välj **+ Lägg till**.
1. Välj en eller flera API: er och **Välj** sedan.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Lägg till API i befintlig produkt":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du vill se dina hanterade API: er använder du kommandot [AZ APIM API List](/cli/azure/apim/api#az_apim_api_list) :

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Om du vill lägga till ett API för produkten kör du kommandot [AZ APIM Product API Add](/cli/azure/apim/product/api#az_apim_product_api_add) :

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Verifiera tillägget med kommandot [AZ APIM Product API List](/cli/azure/apim/product/api#az_apim_product_api_list) :

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Du kan ta bort ett API från en produkt genom att använda kommandot [AZ APIM Product API Delete](/cli/azure/apim/product/api#az_apim_product_api_delete) :

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Du kan skapa eller uppdatera en användares prenumeration på en produkt med anpassade prenumerations nycklar via ett [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) -eller PowerShell-kommando.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa och publicera en produkt
> * Lägga till ett API till produkten

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Skapa en tom API och testa API-svaren](mock-api-responses.md)
