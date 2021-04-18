---
title: Importera en Azure-funktionsapp som API i API Management
titleSuffix: Azure API Management
description: Den här artikeln visar hur du importerar en Azure-funktionsapp till Azure API Management som ett API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599386"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Importera en Azure-funktionsapp som API i Azure API Management | Microsoft Docs

Azure API Management har funktioner för att importera Azure-funktionsappar som nya API:er eller lägga till dem till befintliga API:er. Processen genererar automatiskt en värdnyckel i Azure-funktionsappen, som sedan tilldelas till ett namngivet värde i Azure API Management.

Den här artikeln beskriver hur du importerar och testar en Azure-funktionsapp som ett API i Azure API Management. 

Du lär dig hur du:

> [!div class="checklist"]
> * Importera en Azure-funktionsapp som API
> * Lägga till en Azure-funktionsapp till ett API
> * Visa den nya Azure-funktionsappsvärdnyckeln och det namngivna Azure API Management-värdet
> * Testa API:et i Azure Portal

## <a name="prerequisites"></a>Förutsättningar

* Slutför [snabbstarten Create an Azure API Management instance (Skapa en Azure API Management-instans).](get-started-create-service-instance.md)
* Se till att du har en Azure Funktions-app i din prenumeration. Mer information finns i [Skapa en Azure-funktionsapp](../azure-functions/functions-get-started.md). Functions måste ha HTTP-utlösare och auktoriseringsnivå inställt *på Anonym* eller *Funktion*.

> [!NOTE]
> Du kan använda API Management för att Visual Studio Code för att importera och hantera dina API:er. Följ [självstudien API Management tilläggstillägg för](visual-studio-code-tutorial.md) att installera och komma igång.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Importera en Azure-funktionsapp som nytt API

Följ stegen nedan och skapa ett nytt API från en Azure-funktionsapp.

1. Gå till API Management-tjänsten i Azure Portal och välj **API:er** på menyn.

2. I listan **Lägg till ett nytt API** väljer du **Funktionsapp**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Skärmbild som visar panelen Funktionsapp.":::

3. Klicka på **Bläddra** för att välja funktioner att importera.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Skärmbild som visar knappen Bläddra.":::

4. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Skärmbild som visar avsnittet Funktionsapp.":::

5. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Skärmbild som visar den funktionsapp som du vill importera funktioner från och knappen Välj.":::

6. Välj de funktioner du vill importera och klicka på **Välj**.
    * Du kan bara importera funktioner som baseras på HTTP-utlösare med *auktoriseringsnivåer för anonym* *eller* funktion.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Skärmbild som visar de funktioner som ska importeras och knappen Välj.":::

7. Växla till vyn **Fullständig** och tilldela **Produkten** till ditt nya API. 
8. Om det behövs anger du andra fält under skapandet eller konfigurerar dem senare via **fliken** Inställningar. 
    * Inställningarna beskrivs i självstudien [Importera och publicera ditt första API.](import-and-publish.md#import-and-publish-a-backend-api)

    >[!NOTE]
    > Produkter är associationer med ett eller flera API:er som erbjuds till utvecklare via utvecklarportalen. Först måste utvecklare prenumerera på en produkt för att få åtkomst till API:et. När prenumerationen är slut får de en prenumerationsnyckel för alla API:er i produkten. Som skapare av API Management-instansen är du administratör och prenumererar på alla produkter som standard.
    >
    > Varje API Management instans levereras med två standardexempelprodukter:
    > - **Starter**
    > - **Obegränsat**

9. Klicka på **Skapa**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Lägg till Azure-funktionsapp till befintligt API

Följ stegen nedan för att lägga till Azure-funktionsapp till befintligt API.

1. I tjänsteinstansen **Azure API Management** väljer du **API:er** i menyn till vänster.

2. Välj ett API som du vill importera en Azure-funktionsapp till. Klicka på **...** och välj **Importera** på snabbmenyn.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Skärmbild som visar menyalternativet Importera.":::

3. Klicka på rutan **Funktionsapp**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Skärmbild som visar funktionsappens panel.":::

4. I popup-fönstret klickar du på **Bläddra**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Skärmbild som visar knappen Bläddra.":::

5. Klicka på avsnittet **Funktionsapp** och välj från listan över tillgängliga funktionsappar.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Skärmbild som visar listan över funktionsappar.":::

6. Hitta den funktionsapp du vill importera funktioner från, klicka på den och tryck på **Välj**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Skärmbild som visar den funktionsapp som du vill importera funktioner från.":::

7. Välj de funktioner du vill importera och klicka på **Välj**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Skärmbild som visar de funktioner som du vill importera.":::

8. Klicka **på Importera.**

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Lägg till från funktionsapp":::

## <a name="authorization"></a><a name="authorization"></a> Tillstånd

Import av en Azure-funktionsapp genererar automatiskt:

* Värdnyckel i funktionsappen med namnet apim-{*ditt Azure API Management-tjänstinstansnamn*},
* Namngivet värde i Azure API Management instansen med namnet {*instansnamnet }-key* för din Azure-funktionsapp, som innehåller den skapade värdnyckeln.

För API:er som skapats efter 4 april 2019 skickas värdnyckeln i HTTP-begäranden från API Management till funktionsappen i ett -huvud. Äldre API:er skickar värdnyckeln som [en frågeparameter](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Du kan ändra det här beteendet via `PATCH Backend` [REST API anropet för](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) *entiteten Backend* som är associerad med funktionsappen.

> [!WARNING]
> Om du tar bort eller ändrar antingen värdnyckeln för Azure-funktionsappen eller azure-API Management det namngivna värdet bryts kommunikationen mellan tjänsterna. Värdena synkroniseras inte automatiskt.
>
> Om du behöver rotera värdnyckeln ska du se till att det namngivna värdet i Azure API Management också ändras.

### <a name="access-azure-function-app-host-key"></a>Komma åt värdnyckel för Azure-funktionsapp

1. Öppna instansen av Azure-funktionsappen.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Skärmbild som visar val av din funktionsappinstans.":::

2. I avsnittet **Funktioner** på sidonavigeringsmenyn väljer du **Appnycklar.**

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Skärmbild som visar inställningsalternativet Funktionsappar.":::

3. Leta upp nycklarna under **avsnittet Värdnycklar.**

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Skärmbild som visar avsnittet Värdnycklar.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Komma åt det namngivna värdet i Azure API Management

Öppna instansen av Azure API Management och välj **Namngivna värden** på menyn till vänster. Azure-funktionsappnyckeln finns där.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Lägg till från funktionsapp":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Testa det nya API:et i Azure Portal

Du kan anropa åtgärder direkt från Azure-portalen. Azure-portalen är ett praktiskt sätt att visa och testa åtgärderna i ett API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Skärmbild som visar testproceduren.":::

1. Välj det API som du skapade i föregående avsnitt.

2. Välj fliken **Test**.

3. Välj den åtgärd som du vill testa.

    * Sidan visar fält för frågeparametrar och sidhuvuden. 
    * En av huvudena är "Ocp-Apim-Subscription-Key" för den produktnyckelnyckel som är associerad med detta API. 
    * Som skapare av API Management-instansen är du redan administratör, så nyckeln fylls i automatiskt. 

4. Välj **Skicka**.

    * När testet lyckas svarar backend med **200 OK och** vissa data.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
