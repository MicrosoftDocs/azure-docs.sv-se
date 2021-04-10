---
title: 'Självstudie: publicera en Hugo-webbplats till Azure static Web Apps'
description: Lär dig hur du distribuerar ett Hugo-program till Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652363"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Självstudie: publicera en Hugo-webbplats till Azures statiska Web Apps för hands version

Den här artikeln visar hur du skapar och distribuerar ett [Hugo](https://gohugo.io/) -webbprogram till [Azure static Web Apps](overview.md). Det slutliga resultatet är en ny Azure-statisk webbapp med associerade GitHub-åtgärder som ger dig kontroll över hur appen byggs och publiceras.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa en Hugo-app
> - Konfigurera en statisk Azure-Web Apps
> - Distribuera Hugo-appen till Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://github.com/join).

## <a name="create-a-hugo-app"></a>Skapa en Hugo-app

Skapa en Hugo-app med Hugo kommando rads gränssnitt (CLI):

1. Följ [installations guiden](https://gohugo.io/getting-started/installing/) för Hugo på ditt operativ system.

1. Öppna en Terminal

1. Kör Hugo CLI för att skapa en ny app.

   ```bash
   hugo new site static-app
   ```

1. Navigera till den nyligen skapade appen.

   ```bash
   cd static-app
   ```

1. Initiera en git-lagrings platsen.

   ```bash
    git init
   ```

1. Lägg sedan till ett tema till platsen genom att installera ett tema som en git-undermodul och sedan ange det i Hugo-konfigurations filen.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Genomför ändringarna.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Skicka ditt program till GitHub

Du behöver en lagrings plats på GitHub för att ansluta till Azures statiska Web Apps. Följande steg visar hur du skapar en lagrings plats för din webbplats.

1. Skapa en tom GitHub-lagrings platsen (skapa inte ett viktigt) från [https://github.com/new](https://github.com/new) namngiven **Hugo-static-app**.

1. Lägg till GitHub-lagringsplatsen som en fjärr anslutning till din lokala lagrings platsen. Se till att lägga till ditt GitHub-användarnamn i stället för `<YOUR_USER_NAME>` plats hållaren i följande kommando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Skicka din lokala lagrings platsen upp till GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Distribuera din webbapp

Följande steg visar hur du skapar en ny statisk plats-app och distribuerar den till en produktions miljö.

### <a name="create-the-application"></a>Skapa programmet

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **Skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Klicka på **Statiska webbappar (förhandsversion)**
1. Klicka på **Skapa**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Skapa en statisk Azure Web Apps-resurs i portalen":::

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. I _resurs grupp_ väljer du **ny**. I _nytt resurs grupp namn_ anger du **Hugo-static-app** och väljer **OK**.

1. Sedan ett namn för din app i rutan **namn** . Giltiga tecken är `a-z` , `A-Z` `0-9` och `-` .

1. För _region_ väljer du en tillgänglig region nära dig.

1. För _SKU_ väljer du **kostnads fri**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Information ifylld":::

1. Klicka på knappen **Logga in med GitHub** .

1. Välj den **organisation** som du skapade lagrings platsen under.

1. Välj den **Hugo-statiska-appen** som _lagrings plats_ .

1. För _grenen_ Select **main**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Slutförd GitHub-information":::

### <a name="build"></a>Skapa

Sedan lägger du till de konfigurations inställningar som bygg processen använder för att skapa din app. Följande inställningar konfigurerar arbets flödes filen för GitHub-åtgärd.

1. Klicka på **Nästa: Skapa >** för att redigera versionskonfigurationen

1. Ange _app-plats_ till **/** .

1. Ställ in _appens artefakt plats_ på **offentlig**.

   Ett värde för _API-platsen_ är inte nödvändigt eftersom du inte distribuerar ett API för tillfället.

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.

1. Klicka på **skapa** för att starta skapandet av den statiska Azure-Web Apps och etablera en GitHub-åtgärd för distribution.

1. Vänta tills GitHub-åtgärden har slutförts.

1. I fönstret Azure Portals _Översikt_ över nyligen skapade Azure static Web Apps-resurser klickar du på _URL_ -länken för att öppna det distribuerade programmet.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Distribuerat program":::

#### <a name="custom-hugo-version"></a>Anpassad Hugo-version

När du skapar en statisk webbapp genereras en [arbets flödes fil](./github-actions-workflow.md) som innehåller publicerings konfigurations inställningarna för programmet. Du kan ange en speciell Hugo-version i arbets flödes filen genom att ange ett värde för `HUGO_VERSION` i `env` avsnittet. Följande exempel på konfiguration visar hur du ställer in set-Hugo till en angiven version.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](custom-domain.md)
