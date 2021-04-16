---
title: 'Snabbstart: Skapa din första statiska webbapp med Azure Static Web Apps med hjälp av Azure Portal'
description: Lär dig att distribuera en statisk plats Azure Static Web Apps med Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 09/03/2020
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 78af2290a2e71d349303d3913f8a40510eb9c6a0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531594"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Snabbstart: Skapa din första statiska plats i Azure Portal

Azure Static Web Apps publicerar en webbplats till en produktionsmiljö genom att skapa appar från en GitHub-lagringsplats. I den här snabbstarten distribuerar du en webbapp till Azure Static Web Apps med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa [ett kostnadsfritt utvärderingskonto.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Förutsättningar

- [GitHub](https://github.com)-konto
- [Azure-konto](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när lagringsplatsen har skapats kan du skapa en statisk webbapp från Azure Portal.

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Välj **Skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Välj **Static Web Apps (förhandsversion)**
1. Välj **Skapa**

I avsnittet _Grundläggande börjar_ du med att konfigurera din nya app och länka den till en GitHub-lagringsplats.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Avsnittet Grundläggande":::

1. Välj din _Azure-prenumeration_
1. Välj eller skapa en ny _resursgrupp_
1. Ge appen namnet **my-first-static-web-app**.
      1. Giltiga tecken är `a-z` (skiftlägesokänsligt), `0-9` och `-`.
1. Välj den _region_ som är närmast dig
1. Välj den **kostnadsfria** _SKU:n_
1. Välj knappen **Logga in med GitHub** och autentisera med GitHub

När du har loggat in med GitHub anger du lagringsplatsens information.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Information om lagringsplatsen":::

1. Välj önskad _organisation_
1. Välj **my-first-web-static-app** från _listrutan Lagringsplats_
1. Välj **main** i _listrutan_ Branch (Gren)

> [!NOTE]
> Om du inte ser några databaser kan du behöva auktorisera Azure Static Web Apps i GitHub. Bläddra till din GitHub-lagringsplats och **gå till Inställningar > Program > Auktoriserade OAuth-appar,** välj **Azure Static Web Apps** och välj **sedan Bevilja**. För organisationsdatabaser måste du vara ägare till organisationen för att bevilja behörigheterna.

1. I avsnittet _Build Details (Bygginformation)_ lägger du till konfigurationsinformation som är specifik för ditt önskade frontend-ramverk.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

    1. Välj **Anpassad** från _listrutan Build Presets (Skapa förinställningar)_
    1. Behåll standardvärdet i _rutan Appplats_
    1. Rensa standardvärdet från rutan _Api-plats_
    1. Lämna rutan _Appartefaktplats_ tom

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Välj **Angular** i _listrutan Build Presets (Skapa förinställningar)_
    1. Behåll standardvärdet i _rutan Appplats_
    1. Rensa standardvärdet från rutan _Api-plats_
    1. Skriv **dist/angular-basic** i rutan _Plats för appartefakt_

    # <a name="react"></a>[React](#tab/react)

    1. Välj **React** i _listrutan Build Presets (Skapa förinställningar)_
    1. Behåll standardvärdet i _rutan Appplats_
    1. Rensa standardvärdet från rutan _Api-plats_
    1. Skriv **build** i rutan _Plats för appartefakt_

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Välj **Vue.js** i _listrutan Build Presets (Skapa förinställningar)_
    1. Behåll standardvärdet i rutan _Appplats_
    1. Rensa standardvärdet från rutan _API-plats_
    1. Behåll standardvärdet i rutan _Plats för appartefakt_

    ---

1. Välj **Granska + skapa**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Knappen Skapa granska":::

    > [!NOTE]
    > Du kan redigera [arbetsflödesfilen för](github-actions-workflow.md) att ändra dessa värden när du har skapat appen.

1. Välj **Skapa**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Knappen Skapa":::

1. Välj **Gå till resurs**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Knappen Gå till resurs":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort Azure Static Web Apps-instansen med följande steg:

1. Öppna [Azure-portalen](https://portal.azure.com)
1. Sök efter **my-first-web-static-app** från det övre sökfältet
1. Välj appnamnet
1. Välj knappen **Ta** bort
1. Välj **Ja** för att bekräfta borttagningsåtgärden (den här åtgärden kan ta en stund att slutföra)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
