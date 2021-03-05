---
title: 'Snabb start: skapa din första statiska plats med Azures statiska Web Apps'
description: Lär dig att distribuera en statisk plats till Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 2d7a2dcbbd0b6e9a10ca8af93798bfddbee94ee3
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182663"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Snabb start: skapa din första statiska plats med Azures statiska Web Apps

Azures statiska Web Apps publicerar en webbplats i en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten distribuerar du ett webb program till Azures statiska webbappar med Visual Studio Code-tillägget.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com)-konto
- [Azure](https://portal.azure.com) -konto
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure Static Web Apps-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Installera Git](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Öppna sedan Visual Studio Code och gå till **fil > Öppna mapp** för att öppna den lagrings plats som du precis har klonat till din dator i redigeraren.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. I Visual Studio Code väljer du Azure-logotypen i aktivitetsfältet för att öppna Azure-tilläggsfönstret.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-logotyp":::

    > [!NOTE]
    > Azure- och GitHub-inloggning krävs. Om du inte redan har loggat in på Azure och GitHub från Visual Studio Code, uppmanas du att logga in till båda under skapandeprocessen.

1. Placera musen över etiketten _Static Web Apps_ och välj **plustecknet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Programnamn":::

1. Kommandot palett visas längst upp i redigeraren och du blir ombedd att namnge ditt program.

    Skriv **min-första-statiska-webbapp** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Skapa en statisk webbapp":::

1. Välj **huvudgenen** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Namn på gren":::

1. Välj **/** som plats för program koden och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Plats för programkod":::

1. Tillägget söker efter API-platsen i ditt program. Den här artikeln implementerar inte ett API.

    Välj **Hoppa över för tillfället** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-plats":::

1. Välj den plats där filer skapas för produktion i din app.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

    Avmarkera rutan och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Sökväg till app-filer":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Skriv **förd/vinkel-Basic** och tryck på **RETUR**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Sökväg för Angular-appfiler":::

    # <a name="react"></a>[React](#tab/react)

    Skriv **build** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Sökväg för React-appfiler":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Skriv **dist** och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Sökväg för Vue-appfiler":::

    ---

1. Välj platsen närmast dig och tryck på **Retur**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Resursplats":::

1. När appen har skapats visas ett bekräftelsemeddelande i Visual Studio Code.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Skapad bekräftelse":::

1. I fönstret Visual Studio Code Explorer navigerar du till den nod som har ditt prenumerations namn och expanderar den. Observera att det kan ta några minuter innan distributionen har slutförts. Gå sedan tillbaka till avsnittet med den statiska Web Apps och välj namnet på din app och högerklicka sedan på min-första statiska webb-app och välj Öppna i portalen för att Visa appen i Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Öppna Portal":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen via tillägget.

I fönstret Visual Studio Code Explorer går du tillbaka till avsnittet för _statisk Web Apps_ och högerklickar på **min-första-statisk-Web-App** och väljer **ta bort**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Ta bort app":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
