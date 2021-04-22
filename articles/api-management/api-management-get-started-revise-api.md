---
title: Självstudie – Använda revisioner i API Management för att göra icke-api-ändringar på ett säkert sätt
titleSuffix: Azure API Management
description: Följ stegen i den här självstudien för att lära dig hur du gör ändringar som är bakåtkompatibla i API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6ab3f2e4e2164611820813a5fdb04e2b27df1ff2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877038"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Självstudie: Använda revisioner för att göra icke-api-ändringar på ett säkert sätt
När ditt API är klart och börjar användas av utvecklare, måste du så småningom göra ändringar för det API:et och samtidigt se till att du inte stör anropen till API:et. Det är också bra att informera utvecklarna om de ändringar du gjort. 

I Azure API Management du *revisioner för att* göra icke-brytnings-API-ändringar så att du kan modellera och testa ändringar på ett säkert sätt. När du är klar kan du göra en revision aktuell och ersätta ditt aktuella API. 

Bakgrundsinformation finns i [Versioner & och](https://azure.microsoft.com/blog/versions-revisions/) [API-versionshantering med Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="API-revisioner i Azure Portal":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: Skapa [en Azure API Management instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Lägga till en ny version

1. Logga in på [Azure Portal](https://portal.azure.com)och gå till din API Management instans.
1. Välj **API:er**.
2. Välj **Demo Conference API (Demokonferens-API)** i API-listan (eller ett annat API som du vill lägga till revisioner i).
3. Välj fliken **Revisioner.**
4. Välj **+ Lägg till revision.**

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Lägga till API-granskning":::

    > [!TIP]
    > Du kan också välja **Lägg till revision** på snabbmenyn (**...**) för API:et.

5. Ange en beskrivning för din nya revision så att du kommer ihåg vad den ska användas för.
6. Välj **Skapa**,
7. Nu skapas en ny version.

    > [!NOTE]
    > Det ursprungliga API:et ligger kvar i **Revision 1**. Det är den här revisionen dina användare fortsätter att anropa tills du väljer att aktualisera en annan revision aktuella.

## <a name="make-non-breaking-changes-to-your-revision"></a>Göra bakåtkompatibla ändringar till din revision

1. Välj **Demo Conference API** från listan över API.
1. Överst på skärmen väljer du fliken **Design**.
1. Observera att **revisionsväljaren** (direkt ovanför designfliken) **Revision 2** som aktuellt val.

    > [!TIP]
    > Använd revisionsväljaren för att växla mellan de revisioner du vill arbeta med.
1. Välj **+ Lägg till åtgärd**.
1. Ange att den nya åtgärden ska vara **POST**, och ange Name (namn), Display Name (visningsnamn) och URL för åtgärden som **test**.
1. **Spara** den nya åtgärden.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Ändra granskning":::
1. Nu har du gjort en ändring i **Revision 2**. Använd **revisionsväljaren längst** upp på sidan för att växla tillbaka till **Revision 1.**
1. Observera att den nya åtgärden inte syns i **Revision 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Aktualisera dina revisioner och lägga till en ändringsloggpost

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj fliken **Revisioner** från menyn upptill på sidan.
1. Öppna snabbmenyn (**...**) för **revision 2**.
1. Välj **Gör aktuell.**
1. Markera kryssrutan **Post to Public Change log for this API** (Publicera till offentlig ändringslogg för det här API:et) om du vill publicera anteckningar om den här ändringen. Ange en beskrivning för din ändring som utvecklare ser, till exempel: **Testa revisioner. En ny teståtgärd har lagts till.**
1. Nu är **Revision 2** aktuell.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Revision-menyn i fönstret Revisioner":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här börjar du använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Använd den här proceduren för att skapa och uppdatera en version.

1. Kör kommandot [az apim api list för](/cli/azure/apim/api#az_apim_api_list) att se dina API-ID:n:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   Det API-ID som ska användas i nästa kommando är `Name` värdet. API-revisionen finns i `ApiRevision` kolumnen .

1. Kör kommandot [az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create) för att skapa versionen med en version:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Den revision som du släpper blir den aktuella revisionen.

1. Om du vill se dina versioner använder du [kommandot az apim api release list:](/cli/azure/apim/api/release#az_apim_api_release_list)

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Anteckningarna som du anger visas i ändringsloggen. Du kan se dem i utdata från föregående kommando.

1. När du skapar en version är `--notes` parametern valfri. Du kan lägga till eller ändra anteckningarna senare med hjälp av [kommandot az apim api release](/cli/azure/apim/api/release#az_apim_api_release_update) update:

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Använd värdet i kolumnen `Name` för publicerings-ID:t.

Du kan ta bort alla versioner genom att köra [kommandot az apim api release delete:](/cli/azure/apim/api/release#az_apim_api_release_delete)

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

Om du har provat [utvecklarportalen kan](api-management-howto-developer-portal-customize.md)du granska API-ändringarna och ändringsloggen där.

1. I Azure Portal väljer du **API:er.**
1. Välj **Utvecklarportalen** på den översta menyn.
1. I utvecklarportalen väljer du **API:er** och sedan **Demo Conference API**.
1. Lägg märke till att din nya **teståtgärd** nu är tillgänglig.
1. Välj **Ändringslogg** nära API-namnet.
1. Observera att din post i ändringsloggen visas i den här listan.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till en ny version
> * Göra bakåtkompatibla ändringar till din revision
> * Aktualisera dina revisioner och lägga till en ändringsloggpost
> * Gå till utvecklarportalen för att se dina ändringar och ändringsloggen

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Publicera flera versioner av ditt API](api-management-get-started-publish-versions.md)
