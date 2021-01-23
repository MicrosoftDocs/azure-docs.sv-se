---
title: Importera en Azure-Funktionsapp som ett API i API Management
titleSuffix: Azure API Management
description: Den här artikeln visar hur du importerar en Azure-Funktionsapp till Azure API Management som ett API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: f66395b1e0f45f1e80cd0ac93bf8c9ae8674a0f2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732970"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importera en Azure-funktionsapp som API i Azure API Management | Microsoft Docs

Azure API Management har funktioner för att importera Azure-funktionsappar som nya API:er eller lägga till dem till befintliga API:er. Processen genererar automatiskt en värdnyckel i Azure-funktionsappen, som sedan tilldelas till ett namngivet värde i Azure API Management.

Den här artikeln visar hur du importerar en Azure-funktionsapp som API i Azure API Management. Vi beskriver också testprocessen.

Du lär dig hur du:

> [!div class="checklist"]
> * Importera en Azure-funktionsapp som API
> * Lägga till en Azure-funktionsapp till ett API
> * Visa den nya Azure-funktionsappsvärdnyckeln och det namngivna Azure API Management-värdet
> * Testa API:et i Azure Portal
> * Testa API:et i utvecklarportalen

## <a name="prerequisites"></a>Krav

* Slutför snabbstarten [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
* Se till att du har en Azure Funktions-app i din prenumeration. Mer information finns i [Skapa en Azure-funktionsapp](../azure-functions/functions-get-started.md). Det måste innehålla funktioner med HTTP-utlösare och inställningar för auktoriseringsnivå inställda på *Anonym* eller *Funktion*.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importera en Azure-funktionsapp som nytt API

Följ stegen nedan och skapa ett nytt API från en Azure-funktionsapp.

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.

2. I listan **Lägg till ett nytt API** väljer du **Funktionsapp**.

    ![Skärm bild som visar panelen Funktionsapp.](./media/import-function-app-as-api/add-01.png)

3. Klicka på **Bläddra** för att välja funktioner att importera.

    ![Skärm bild som visar knappen Bläddra.](./media/import-function-app-as-api/add-02.png)

4. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    ![Skärm bild som visar Funktionsapp avsnittet.](./media/import-function-app-as-api/add-03.png)

5. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    ![Skärm bild som visar Funktionsapp som du vill importera funktioner från och knappen Välj.](./media/import-function-app-as-api/add-04.png)

6. Välj de funktioner du vill importera och klicka på **Välj**.

    ![Skärm bild som visar de funktioner som ska importeras och knappen Välj.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Du kan endast importera de funktioner som är baserade på HTTP-utlösare och som har auktoriseringsnivåinställningen *Anonym* eller *Funktion*.

7. Växla till vyn **Fullständig** och tilldela **Produkten** till ditt nya API. Vid behov kan du ange andra fält när du skapar eller konfigurerar dem senare genom att gå till fliken **Inställningar** . Inställningarna beskrivs i själv studie kursen [Importera och publicera din första API](import-and-publish.md#import-and-publish-a-backend-api) .
8. Klicka på **Skapa**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Lägg till Azure-funktionsapp till befintligt API

Följ stegen nedan för att lägga till Azure-funktionsapp till befintligt API.

1. I tjänsteinstansen **Azure API Management** väljer du **API:er** i menyn till vänster.

2. Välj ett API som du vill importera en Azure-funktionsapp till. Klicka på **...** och välj **Importera** på snabbmenyn.

    ![Skärm bild som visar meny alternativet importera.](./media/import-function-app-as-api/append-01.png)

3. Klicka på rutan **Funktionsapp**.

    ![Skärm bild som visar panelen Funktionsapp.](./media/import-function-app-as-api/append-02.png)

4. I popup-fönstret klickar du på **Bläddra**.

    ![Skärm bild som visar knappen Bläddra.](./media/import-function-app-as-api/append-03.png)

5. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    ![Skärm bild som visar en lista över funktions program.](./media/import-function-app-as-api/add-03.png)

6. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    ![Skärm bild som visar Funktionsapp som du vill importera funktioner från.](./media/import-function-app-as-api/add-04.png)

7. Välj de funktioner du vill importera och klicka på **Välj**.

    ![Skärm bild som visar functnios som du vill importera.](./media/import-function-app-as-api/add-05.png)

8. Klicka på **Importera**.

    ![Lägg till från funktionsapp](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Auktoriseringsregeln

Import av en Azure-funktionsapp genererar automatiskt:

* Värd nyckel i Funktionsapp med namnet APIM-{namnet på *din Azure API Management*-tjänstinstans},
* Namngivet värde i Azure API Management-instansen med namnet {*ditt Azure Funktionsapp instance Name*}-Key, som innehåller den skapade värd nyckeln.

För API: er som skapats efter 4 april 2019, skickas värd nyckeln i HTTP-begäranden från API Management till Funktionsapp i en rubrik. Äldre API: er skickar värd nyckeln som [en frågeparameter](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Det här beteendet kan ändras via `PATCH Backend` [REST API-anropet](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) på den *Server dels* enhet som är associerad med Funktionsapp.

> [!WARNING]
> Om du tar bort eller ändrar värdet för antingen värdnyckeln för Azure-funktionsappen eller det namngivna Azure API Management-värdet bryts kommunikationen mellan tjänsterna. Värdena synkroniseras inte automatiskt.
>
> Om du behöver rotera värdnyckeln ska du se till att det namngivna värdet i Azure API Management också ändras.

### <a name="access-azure-function-app-host-key"></a>Komma åt värdnyckel för Azure-funktionsapp

1. Öppna instansen av Azure-funktionsappen.

2. Välj **Funktionsappinställningar** i översikten.

    ![Skärm bild som visar alternativet för inställning av funktions appar.](./media/import-function-app-as-api/keys-02-a.png)

3. Nyckeln finns i avsnittet **Värdnycklar**.

    ![Skärm bild som markerar avsnittet värd nycklar.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Komma åt det namngivna värdet i Azure API Management

Öppna instansen av Azure API Management och välj **Namngivna värden** på menyn till vänster. Azure-funktionsappnyckeln finns där.

![Lägg till från funktionsapp](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testa det nya API: et i Azure Portal

Du kan anropa åtgärder direkt från Azure-portalen. Azure-portalen är ett praktiskt sätt att visa och testa åtgärderna i ett API.  

1. Välj det API som du skapade i föregående avsnitt.

2. Välj fliken **Test**.

3. Välj en åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. En av rubrikerna är **OCP-APIM-Subscription-Key** för prenumerations nyckeln för den produkt som är associerad med detta API. Om du skapade API Management-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 

4. Välj **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
