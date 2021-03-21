---
title: 'Självstudie: publicera en VuePress-webbplats till Azure static Web Apps'
description: I den här självstudien får du lära dig hur du distribuerar ett VuePress-program till Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 6f0616df885a7f8fcd76337c810bc368aa02f3c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650459"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Självstudie: publicera en VuePress-webbplats till Azures statiska Web Apps för hands version

Den här artikeln visar hur du skapar och distribuerar ett [VuePress](https://vuepress.vuejs.org/) -webbprogram till [azures statiska Web Apps](overview.md). Det slutliga resultatet är ett nytt Azure static Web Apps-program med associerade GitHub-åtgärder som ger dig kontroll över hur appen byggs och publiceras.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa en VuePress-app
> - Konfigurera en statisk Azure-Web Apps
> - Distribuera VuePress-appen till Azure

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://github.com/join).
- [Node.js](https://nodejs.org) installerat.

## <a name="create-a-vuepress-app"></a>Skapa en VuePress-app

Skapa en VuePress-app från kommando rads gränssnittet (CLI):

1. Skapa en ny mapp för VuePress-appen.

   ```bash
   mkdir static-site
   ```

1. Lägg till en _Readme.MD_ -fil i mappen.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Initiera _package.jspå_ filen.

   ```bash
   npm init -y
   ```

1. Lägg till VuePress som en `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Öppna _package.js_ filen i en text redigerare och Lägg till ett build-kommando i [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) avsnittet.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Skapa en _. gitignore_ -fil för att utesluta mappen _\_ moduler i noden_ .

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Initiera en git-lagrings platsen.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Skicka ditt program till GitHub

Du behöver en lagrings plats på GitHub för att ansluta till Azures statiska Web Apps. Följande steg visar hur du skapar en lagrings plats för din webbplats.

1. Skapa en tom GitHub-lagrings platsen (skapa inte ett viktigt) från [https://github.com/new](https://github.com/new) namngiven **vuepress-static-app**.

1. Lägg till GitHub-lagringsplatsen som en fjärr anslutning till din lokala lagrings platsen. Se till att lägga till ditt GitHub-användarnamn i stället för `<YOUR_USER_NAME>` plats hållaren i följande kommando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Skicka din lokala lagrings platsen upp till GitHub.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>Distribuera din webbapp

Följande steg visar hur du skapar ett nytt statiskt Web Apps-program och distribuerar det till en produktions miljö.

### <a name="create-the-application"></a>Skapa programmet

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **Skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Klicka på **Statiska webbappar (förhandsversion)**
1. Klicka på **Skapa**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Skapa en statisk Web Apps (för hands version) i portalen":::

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för **prenumeration**.

1. I _resurs grupp_ väljer du **ny**. I _nytt resurs grupp namn_ anger du **vuepress-static-app** och väljer **OK**.

1. Sedan ett namn för din app i rutan **namn** . Giltiga tecken är `a-z` , `A-Z` `0-9` och `-` .

1. För _region_ väljer du en tillgänglig region nära dig.

1. För _SKU_ väljer du **kostnads fri**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Information ifylld":::

1. Klicka på knappen **Logga in med GitHub** .

1. Välj den **organisation** som du skapade lagrings platsen under.

1. Välj den **vuepress-statiska-appen** som _lagrings plats_ .

1. För _grenen_ Select **main**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Slutförd GitHub-information":::

### <a name="build"></a>Skapa

Sedan lägger du till de konfigurations inställningar som bygg processen använder för att skapa din app. Följande inställningar konfigurerar arbets flödes filen för GitHub-åtgärd.

1. Klicka på **Nästa: Skapa >** för att redigera versionskonfigurationen

1. Ange _app-plats_ till **/** .

1. Ange _platsen för appens artefakt_ till **. vuepress/dist**.

Ett värde för _API-platsen_ är inte nödvändigt eftersom du inte distribuerar ett API för tillfället.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Build-inställningar":::

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.

1. Klicka på **skapa** för att starta skapandet av den statiska Azure-Web Apps och etablera en GitHub-åtgärd för distribution.

1. När distributionen är klar klickar du på **resurs**.

1. Öppna det distribuerade programmet genom att klicka på _URL_ -länken på resurs skärmen. Du kan behöva vänta en minut eller två för att GitHub-åtgärden ska slutföras.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Distribuerat program":::

### <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](custom-domain.md)
