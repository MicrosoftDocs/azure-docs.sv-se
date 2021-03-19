---
title: Snabb start – skapa en Azure API Management-instans
description: Skapa en ny Azure API Management Service-instans med hjälp av Azure Portal.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90708214"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Snabb start: skapa en ny instans av Azure API Management-tjänsten med hjälp av Azure Portal

Azure API Management (APIM) hjälper organisationer att publicera API:er till externa partner och interna utvecklare så att de kan få ut maximalt av sina data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga Server dels tjänster var som helst. Mer information finns i [Översikt](api-management-key-concepts.md).

I den här snabbstarten beskrivs stegen för att skapa en ny API Management-instans med Azure-portalen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management instans":::

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-new-service"></a>Skapa en ny tjänst

1. I menyn i Azure-portalen väljer du **Skapa en resurs**. Du kan också välja **skapa en resurs** på Azures **Start** sida. 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Välj Skapa en resurs":::

   
1. På sidan **ny** väljer du **integration**  >  **API Management**.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Ny Azure API Management-instans":::
   
1. På sidan **API Management tjänst** anger du inställningar.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Ny instans":::
   
   | Inställning                 | Beskrivning   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Namn**                | Ett unikt namn för din API Management-tjänst. Namnet kan inte ändras senare. Tjänst namnet avser både tjänsten och motsvarande Azure-resurs. <br/> Tjänst namnet används för att generera ett standard domän namn: *\<name\> . Azure-API.net.* Om du vill använda ett anpassat domännamn läser du [Konfigurera ett anpassat domännamn](configure-custom-domain.md). |
   | **Prenumeration**          | Den prenumeration som den här nya tjänstinstansen kommer att skapas för.   |
   | **Resursgrupp**      |  Välj en ny eller befintlig resurs grupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. |
   | **Plats**          | Välj en geografisk region nära dig från tillgängliga API Management tjänst platser. | 
   | **Organisationsnamn**   | Namnet på din organisation. Namnet används på ett antal platser, däribland titeln på utvecklarportalen och avsändaren av e-postmeddelanden. |                                                         
   | **E-postadress för administratör** | E-postadressen som alla meddelanden från **API Management** ska skickas till.   |  
   | **Prisnivå**        | Välj nivå för **utvecklare** för att utvärdera tjänsten. Den här nivån är inte för produktions användning. Mer information om att skala API Management-nivåerna finns i avsnittet [Uppgradera och skala](upgrade-and-scale.md). |

3. Välj **Skapa**.

    > [!TIP]
    > Det kan ta mellan 30 och 40 minuter att skapa och aktivera en API Management tjänst på den här nivån. Om du väljer **Fäst vid instrumentpanelen** blir det enklare att hitta en nyligen skapad tjänst.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Granska egenskaperna för din tjänst på **översikts** sidan.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="API Management instans":::

När din API Management tjänst instans är online är du redo att använda den. Börja med självstudien för att [Importera och publicera ditt första API](import-and-publish.md).

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan följande steg användas för att ta bort resursgruppen och alla relaterade resurser:

1. I Azure Portal söker du efter och väljer **resurs grupper**. Du kan också välja **resurs grupper** på **Start** sidan. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Navigering för resurs grupp":::

1. På sidan **resurs grupper** väljer du din resurs grupp.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Välj resursgrupp":::

1. På sidan resurs grupp väljer du **ta bort resurs grupp**. 
   
1. Skriv namnet på resurs gruppen och välj sedan **ta bort**.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Ta bort resursgrupp":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera och publicera ditt första API](import-and-publish.md)
