---
title: 'Självstudie – felsöka API: er i Azure API Management med hjälp av spårning av förfrågningar'
description: Följ stegen i den här självstudien för att aktivera spårning och inspektion av bearbetnings steg för begäran i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542322"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>Självstudie: Felsöka API: er med hjälp av spårning av förfrågningar

I den här självstudien beskrivs hur du inspekterar (spårar) bearbetning av förfrågningar i Azure API Management för att hjälpa dig att felsöka och felsöka ditt API. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Spåra ett exempel anrop
> * Granska bearbetnings steg för begäran

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API Inspector":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

## <a name="verify-allow-tracing-setting"></a>Verifiera Tillåt spårnings inställning 

Inställningen **Tillåt spårning** för den prenumeration som används för ditt API måste vara aktive rad. Om du använder den inbyggda prenumerationen All-Access är den aktive rad som standard. Om du vill verifiera i portalen navigerar du till din API Management instans och väljer **prenumerationer**.

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="Tillåt spårning för prenumeration":::

## <a name="trace-a-call"></a>Spåra ett anrop

1. Logga in på [Azure Portal](https://portal.azure.com)och navigera till API Management-instansen.
1. Välj **API:er**.
1. Välj  **demo konferens-API** från din API-lista.
1. Välj fliken **Test**.
1. Välj åtgärden **GetSpeakers**.
1. Bekräfta att rubriken HTTP-begäran innehåller **OCP-APIM-trace: true** och ett giltigt värde för **OCP-APIM-Subscription-Key**. Om den inte är det väljer du **+ Lägg till rubrik** för att lägga till rubriken.
1. Välj **Skicka** för att göra ett API-anrop.

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="Konfigurera API-spårning":::

> [!TIP]
> Om **OCP-APIM-Subscription-Key** inte fylls i automatiskt i HTTP-förfrågan kan du hämta den i portalen. Välj **prenumerationer** och öppna snabb menyn (**...**) för din suscription. Välj **Visa/Dölj nycklar**. Du kan också återskapa nycklar om det behövs. Lägg sedan till en nyckel i rubriken.

## <a name="review-trace-information"></a>Granska spårnings information

1. När anropet är klart går du till fliken **spåra** i **http-svaret**.
1. Välj någon av följande länkar för att gå till detaljerad spårnings information: **inkommande**, **backend**, **utgående**.

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="Granska svars spårning":::

    * **Inkommande** – visar den ursprungliga begäran API Management tas emot från anroparen och de principer som tillämpas på begäran. Om du till exempel har lagt till principer i [Självstudier: transformera och skydda ditt API](transform-api.md)visas de här.

    * **Backend** – visar de begär anden som API Management skickas till API-Dataservern och svaret den fick.

    * **Utgående** – visar de principer som tillämpas på svaret innan du skickar tillbaka till anroparen.

    > [!TIP]
    > Alla steg visar också hur lång tid det tog efter att begäran togs emot av API Management.

1. På fliken **meddelande** visas platsen för spårnings data som lagras i Azure Blob Storage i **OCP-APIM-trace-location** -huvudet. Om det behövs går du till den här platsen för att hämta spårningen.

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Spårnings plats i Azure Storage":::
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Spåra ett exempel anrop
> * Granska bearbetnings steg för begäran

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Använd revideringar](api-management-get-started-revise-api.md)
