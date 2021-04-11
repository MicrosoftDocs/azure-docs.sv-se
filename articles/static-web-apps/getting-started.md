---
title: 'Snabb start: skapa din första statiska plats med Azures statiska Web Apps'
description: Lär dig att distribuera en statisk plats till Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449212"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Snabb start: skapa din första statiska plats med Azures statiska Web Apps

Azure static Web Apps publicerar en webbplats genom att skapa appar från en kod lagrings plats. I den här snabb starten distribuerar du ett program till Azures statiska webbappar med Visual Studio Code-tillägget.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com)-konto
- [Azure](https://portal.azure.com) -konto
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installera Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Öppna sedan Visual Studio Code och gå till **fil > Öppna mapp** för att öppna den lagrings plats som du har klonat till din dator i redigeraren.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. I Visual Studio Code väljer du Azure-logotypen i aktivitetsfältet för att öppna Azure-tilläggsfönstret.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logotyp":::

    > [!NOTE]
    > Azure- och GitHub-inloggning krävs. Om du inte redan har loggat in på Azure och GitHub från Visual Studio Code, uppmanas du att logga in till båda under skapandeprocessen.

1. Under etiketten _statisk Web Apps_ väljer du **plus tecknet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Programnamn":::

1. Kommandot palett visas längst upp i redigeraren och du blir ombedd att namnge ditt program.

    Skriv **min-första-statiska-webbapp** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Skapa en statisk webbapp":::

1. Välj de för inställningar som matchar din program typ.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Program för inställningar: inget ramverk":::

    Ange **./** som platsen för programfilerna

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Plats för programfiler":::

    Välj **hoppa över för tillfället** som plats för Azure Functions-API: et

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-plats":::

    Ange **./** som bygg utmatnings plats

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Utmatnings plats för program version":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Program för inställningar: vinkel":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Program för inställningar: reagera":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Program för inställningar: Vue":::

    ---

1. Välj platsen närmast dig och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resursplats":::

1. När appen har skapats visas ett bekräftelsemeddelande i Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Skapad bekräftelse":::

    Klicka sedan på knappen **Öppna åtgärder i GitHub**. På den här sidan visas programmets versions status.

    När GitHub-åtgärden har slutförts kan du gå till den publicerade webbplatsen.

1. Om du vill visa webbplatsen i webbläsaren högerklickar du på projektet i tillägget för statisk Web Apps och väljer **Bläddra webbplats**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Bläddra på webbplats":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen via tillägget.

I fönstret Visual Studio Code Explorer går du tillbaka till avsnittet för _statisk Web Apps_ och högerklickar på **min-första-statisk-Web-App** och väljer **ta bort**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Ta bort app":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
