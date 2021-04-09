---
title: 'Självstudie: publicera en Gatsby-webbplats till Azure static Web Apps'
description: I den här självstudien får du lära dig hur du distribuerar ett Gatsby-program till Azure static Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 4430ed34858077b13b4fec69756c1c7e9f3ef7ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652383"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Självstudie: publicera en Gatsby-webbplats till Azures statiska Web Apps för hands version

Den här artikeln visar hur du skapar och distribuerar ett [Gatsby](https://gatsbyjs.org) -webbprogram till [Azure static Web Apps](overview.md). Det slutliga resultatet är en ny statisk Web Apps plats (med associerade GitHub-åtgärder) som ger dig kontroll över hur appen byggs och publiceras.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa en Gatsby-app
> - Konfigurera en statisk Azure-Web Apps webbplats
> - Distribuera Gatsby-appen till Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. Om du inte har någon kan du [skapa ett konto kostnads fritt](https://github.com/join).
- [Node.js](https://nodejs.org) installerat.

## <a name="create-a-gatsby-app"></a>Skapa en Gatsby-app

Skapa en Gatsby-app med Gatsby kommando rads gränssnitt (CLI):

1. Öppna en Terminal
1. Använd [npx](https://www.npmjs.com/package/npx) -verktyget för att skapa en ny app med Gatsby cli. Det kan ta några minuter.

   ```bash
   npx gatsby new static-web-app
   ```

1. Navigera till den nyligen skapade appen

   ```bash
   cd static-web-app
   ```

1. Initiera en git-lagrings platsen

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Skicka ditt program till GitHub

Du måste ha en lagrings plats på GitHub för att skapa en ny Azure static Web Apps-resurs.

1. Skapa en tom GitHub-lagringsplats (skapa inte ett viktigt) från [https://github.com/new](https://github.com/new) namngiven **Gatsby-static-Web-App**.

1. Lägg sedan till GitHub-lagringsplatsen som du nyss skapade som en fjärr anslutning till din lokala lagrings platsen. Se till att lägga till ditt GitHub-användarnamn i stället för `<YOUR_USER_NAME>` plats hållaren i följande kommando.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. Skicka din lokala lagrings plats upp till GitHub.

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

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Skapa en statisk Web Apps (för hands version) i portalen":::

1. Acceptera prenumerationen som visas i listan eller Välj en ny i list rutan för _prenumeration_.

1. I _resurs grupp_ väljer du **ny**. I _nytt resurs grupp namn_ anger du **Gatsby-static-Web-App** och väljer **OK**.

1. Sedan ett namn för din app i rutan **namn** . Giltiga tecken är `a-z` , `A-Z` `0-9` och `-` .

1. För _region_ väljer du en tillgänglig region nära dig.

1. För _SKU_ väljer du **kostnads fri**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Information ifylld":::

1. Klicka på knappen **Logga in med GitHub** .

1. Välj den **organisation** som du skapade lagrings platsen för.

1. Välj **Gatsby-static-Web-App** som _lagrings plats_ .

1. För _grenen_ Select **main**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Slutförd GitHub-information":::

### <a name="build"></a>Skapa

Lägg sedan till de konfigurations inställningar som bygg processen använder för att skapa din app.

1. Klicka på **Nästa: Skapa >** för att redigera versionskonfigurationen

1. Om du vill konfigurera inställningarna för steget i GitHub-åtgärder anger du _appens plats_ till **/** .

1. Ställ in _appens artefakt plats_ på **offentlig**.

   Ett värde för _API-platsen_ är inte nödvändigt eftersom du inte distribuerar ett API för tillfället.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Build-inställningar":::

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.

1. Klicka på **skapa** för att starta skapandet av den app service statiska webbappen och etablera en GitHub-åtgärd för distribution.

1. När distributionen är klar klickar du på **resurs**.

1. Öppna det distribuerade programmet genom att klicka på _URL_ -länken på resurs skärmen. Du kan behöva vänta en minut eller två för att GitHub-åtgärden ska slutföras.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Distribuerat program":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](custom-domain.md)
