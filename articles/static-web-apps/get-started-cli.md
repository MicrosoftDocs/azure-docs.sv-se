---
title: 'Snabb start: skapa din första statiska plats med Azures statiska Web Apps med hjälp av CLI'
description: Lär dig att distribuera en statisk plats till Azures statiska Web Apps med Azure CLI.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 00892b61cd23ee38ff3d63f8b61391ff1bffdc90
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616413"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Snabb start: skapa din första statiska plats med Azure CLI

Azures statiska Web Apps publicerar en webbplats i en produktions miljö genom att bygga appar från en GitHub-lagringsplats. I den här snabb starten distribuerar du ett webb program till Azures statiska webb program med hjälp av Azure CLI.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Krav

- [GitHub](https://github.com)-konto
- [Personlig GitHub-åtkomsttoken](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) -konto
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) installerat (version 2.8.0 och senare)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Växla sedan till den nya mappen med hjälp av följande kommando.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

Nu när du har skapat databasen kan du skapa en statisk webbapp från Azure CLI.

> [!IMPORTANT]
> Se till att du är i mappen _min-First-static-Web-App_ i terminalen.

1. Logga in på Azure CLI med hjälp av följande kommando.

    ```bash
    az login
    ```

1. Skapa en ny statisk webbapp från din lagrings plats.

    # <a name="no-framework"></a>[Inget ramverk](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Ersätt det här värdet med ett befintligt namn på en Azure-resurs grupp.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Ersätt det här värdet med ditt GitHub-användarnamn.

    - `<LOCATION>`: Ersätt det här värdet med platsen närmast dig. Alternativen är: _Central_, _asienöstra_, _EastUS2_, _WestEurope_ och _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Ersätt det här värdet med [GitHub-personliga](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) åtkomsttoken som du skapade tidigare.

    Nu kan du Visa den skapade appen i Azure.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Sök efter **min-First-Web-static-app** från det övre Sök fältet.

1. Välj **min-första-webb-statisk-app**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet kan du ta bort den statiska Azure-Web Apps-instansen genom att köra följande kommando:

```bash
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
