---
title: 'Snabbstart: Skapa din första statiska webbplats med Azure Static Web Apps'
description: Lär dig att distribuera en statisk plats till Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483859"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Snabbstart: Skapa din första statiska webbplats med Azure Static Web Apps

Azure Static Web Apps publicerar en webbplats genom att skapa appar från en koddatabas. I den här snabbstarten distribuerar du ett program till Azure Static Web Apps Visual Studio Code-tillägget.

Om du inte har en Azure-prenumeration kan du skapa [ett kostnadsfritt utvärderingskonto.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com)-konto
- [Azure-konto](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installera Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Öppna sedan Visual Studio Code och gå till Arkiv **> Öppna mapp** för att öppna lagringsplatsen som du klonade till datorn i redigeraren.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. I Visual Studio Code väljer du Azure-logotypen i aktivitetsfältet för att öppna Azure-tilläggsfönstret.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logotyp":::

    > [!NOTE]
    > Azure- och GitHub-inloggning krävs. Om du inte redan har loggat in på Azure och GitHub från Visual Studio Code, uppmanas du att logga in till båda under skapandeprocessen.

1. Under _Static Web Apps_ etiketten väljer du **plustecknet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Programnamn":::

1. Kommandot palte öppnas längst upp i redigeraren och du uppmanas att namnge programmet.

    Skriv **min-första-statiska-webbapp** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Skapa en statisk webbapp":::

1. Välj de förinställningar som matchar din programtyp.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Förinställningar för program: Inget ramverk":::

    Ange **./** som plats för programfilerna.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Plats för programfiler":::

    Välj **Hoppa över för tillfället** som plats för Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-plats":::

    Ange **./** som utdataplats för versionen.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Plats för utdata för programbygge":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Även om det finns en Angular-förinställning väljer du **alternativet** Anpassad så att du kan ange en lämplig utdataplats för det här programmet.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Förinställningar för program: Angular":::

    Ange **./** som plats för programfilerna.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Plats för programfiler: Angular":::

    Välj **Hoppa över för tillfället** som plats för Azure Functions API.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-plats: Angular":::

    Ange **dist/angular-basic** som utdataplats för versionen.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Plats för programbyggeutdata: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Förinställningar för program: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Förinställningar för program: Vue":::

    ---

1. Välj platsen närmast dig och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resursplats":::

1. När appen har skapats visas ett bekräftelsemeddelande i Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Skapad bekräftelse":::

    Klicka sedan på knappen **Open Actions in GitHub (Öppna åtgärder i GitHub).** Den här sidan visar programmets build-status.

    När GitHub-åtgärden är klar kan du bläddra till den publicerade webbplatsen.

1. Om du vill visa webbplatsen i webbläsaren högerklickar du på projektet i Static Web Apps och väljer **Bläddra på webbplatsen.**

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Bläddra på webbplatsen":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort Azure Static Web Apps-instansen via tillägget.

I Visual Studio Code Explorer går du _tillbaka till Static Web Apps_ och högerklickar på **my-first-static-web-app** och väljer **Ta bort**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Ta bort app":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
