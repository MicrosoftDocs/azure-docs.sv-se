---
title: 'Självstudie: publicera en Jekyll-webbplats till Azure static Web Apps'
description: Lär dig hur du distribuerar ett Jekyll-program till Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: 673852f8f9aa81c838a7c1db68681bb9ee0b7e0b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862017"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Självstudie: publicera en Jekyll-webbplats till Azures statiska Web Apps för hands version

Den här artikeln visar hur du skapar och distribuerar ett [Jekyll](https://jekyllrb.com/) -webbprogram till [azures statiska Web Apps](overview.md).

I de här självstudierna får du lära dig att

> [!div class="checklist"]
>
> - Skapa en Jekyll-webbplats
> - Konfigurera en statisk Azure-Web Apps
> - Distribuera Jekyll-appen till Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Installera [Jekyll](https://jekyllrb.com/docs/installation/)
  - Du kan använda Windows-undersystemet för Linux och följa Ubuntu-instruktioner om det behövs.
- Ett Azure-konto med en aktiv prenumeration. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://github.com/join).

## <a name="create-jekyll-app"></a>Skapa Jekyll-app

Skapa en Jekyll-app med Jekyll kommando rads gränssnitt (CLI):

1. Från terminalen kör du Jekyll CLI för att skapa en ny app.

   ```bash
   jekyll new static-app
   ```

1. Navigera till den nyligen skapade appen.

   ```bash
   cd static-app
   ```

1. Initiera en ny git-lagringsplats.

   ```bash
    git init
   ```

1. Genomför ändringarna.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Skicka ditt program till GitHub

Azures statiska Web Apps använder GitHub för att publicera din webbplats. Följande steg visar hur du skapar en GitHub-lagringsplats.

1. Skapa en tom GitHub-lagrings platsen (skapa inte ett viktigt) från [https://github.com/new](https://github.com/new) namngiven **Jekyll-Azure-static**.

1. Lägg till GitHub-lagringsplatsen som en fjärr anslutning till din lokala lagrings platsen. Se till att lägga till ditt GitHub-användarnamn i stället för `<YOUR_USER_NAME>` plats hållaren i följande kommando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Skicka din lokala lagrings platsen upp till GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Distribuera din webbapp

Följande steg visar hur du skapar en ny statisk plats-app och distribuerar den till en produktions miljö.

### <a name="create-the-application"></a>Skapa programmet

1. Navigera till [Azure Portal](https://portal.azure.com).

1. Klicka på **skapa en resurs**.

1. Sök efter **statiska Web Apps**.

1. Klicka på **statisk Web Apps (förhands granskning)**.

1. Klicka på **Skapa**.

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. I _resurs grupp_ väljer du **ny**. I _nytt resurs grupp namn_ anger du **Jekyll-static-app** och väljer **OK**.

1. Ange sedan ett namn för din app i rutan _namn_ . Giltiga tecken är `a-z` , `A-Z` `0-9` och `-` .

1. För _region_ väljer du en tillgänglig region nära dig.

1. För _SKU_ väljer du **kostnads fri**.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Information ifylld":::

1. Klicka på knappen **Logga in med GitHub** .

1. Välj den **organisation** som du skapade lagrings platsen under.

1. Välj den **Jekyll-statiska-appen** som _lagrings plats_.

1. För _grenen_ Välj **huvud**.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Slutförd GitHub-information":::

### <a name="build"></a>Skapa

Sedan lägger du till de konfigurations inställningar som bygg processen använder för att skapa din app. Följande inställningar konfigurerar arbets flödes filen för GitHub-åtgärd.

1. Klicka på **Nästa: bygg >** om du vill redigera build-konfigurationen.

1. Ange _app-plats_ till **/_site**.

1. Lämna _appens artefakt plats_ tom.

   Ett värde för _API-platsen_ är inte nödvändigt eftersom du inte distribuerar ett API för tillfället.

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.

1. Klicka på **skapa** för att starta skapandet av den statiska Azure-Web Apps och etablera en GitHub-åtgärd för distribution.

1. Distributionen sker först, eftersom arbets flödes filen behöver vissa Jekyll inställningar. Om du vill lägga till de här inställningarna navigerar du till terminalen och drar bekräftelsen med åtgärden GitHub till din dator.

   ```bash
   git pull
   ```

1. Öppna appen Jekyll i en text redigerare och öppna filen _. GitHub/arbets flöden/Azure-Pages-<WORKFLOW_NAME>. yml_ .

1. Efter raden `- uses: actions/checkout@v2` lägger du till följande konfigurations block.

    ```yml
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1.59.1
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Genomför det uppdaterade arbets flödet och push-överför till GitHub.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Vänta tills GitHub-åtgärden har slutförts.

1. I fönstret _Översikt över_ Azure Portal klickar du på _URL_ -länken för att öppna det distribuerade programmet.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Distribuerat program":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](custom-domain.md)
