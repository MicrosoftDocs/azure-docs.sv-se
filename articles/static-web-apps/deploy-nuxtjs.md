---
title: 'Självstudie: Distribuera Server-renderade Nuxt.js webbplatser på Azure static Web Apps'
description: Skapa och distribuera Nuxt.js dynamiska platser med Azures statiska Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563474"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Distribuera Server-renderade Nuxt.js webbplatser på Azures statiska Web Apps för hands version

I den här självstudien lär du dig att distribuera en [Nuxt.js](https://nuxtjs.org) genererad statisk webbplats till [Azure static Web Apps](overview.md). För att börja, lär du dig att konfigurera, konfigurera och distribuera en Nuxt.js app. Under den här processen lär du dig också att hantera vanliga utmaningar ofta när du genererar statiska sidor med Nuxt.js

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- Ett GitHub-konto. [Skapa ett konto kostnads fritt](https://github.com/join).
- [Node.js](https://nodejs.org) installerat.

## <a name="set-up-a-nuxtjs-app"></a>Konfigurera en Nuxt.js app

Du kan skapa ett nytt Nuxt.js-projekt med hjälp av `create-nuxt-app` . I stället för ett nytt projekt börjar du med att klona en befintlig databas i den här självstudien. Den här lagrings platsen är konfigurerad för att visa hur du distribuerar en dynamisk Nuxt.js-app som en statisk plats.

1. Skapa en ny lagrings plats under ditt GitHub-konto från en mall-lagringsplats.
1. Navigera till [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. Namnge lagrings platsen **nuxtjs-starter**
1. Klona sedan den nya lagrings platsen till din dator. Se till att ersätta <YOUR_GITHUB_ACCOUNT_NAME> med ditt konto namn.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Navigera till den nyligen klonade Nuxt.js-appen:

   ```bash
   cd nuxtjs-starter
   ```

1. Installera beroenden:

    ```bash
    npm install
    ```

1. Starta Nuxt.js app i utvecklingen:

    ```bash
    npm run dev
    ```

Navigera till `http://localhost:3000` för att öppna appen, där du bör se följande webbplats som öppnas i din önskade webbläsare:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Starta Nuxt.js app":::

När du klickar på ett ramverk/bibliotek bör du se en informations sida om det valda objektet:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Sidan Details (Detaljer)":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Skapa en statisk webbplats från Nuxt.js build

När du skapar en Nuxt.js-webbplats med hjälp av skapas `npm run build` appen som en traditionell webbapp, inte en statisk plats. Använd följande program konfiguration för att skapa en statisk plats.

1. Uppdatera _package.jspå_ build-skriptet för att endast skapa en statisk plats med hjälp av `nuxt generate` kommandot:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    Nu när det här kommandot är på plats kommer statiska Web Apps att köra `build` skriptet varje gång du push-överför ett genomförande.

1. Skapa en statisk plats:

    ```bash
    npm run build
    ```

    Nuxt.js skapar den statiska platsen och kopierar den till en _förd_ -mapp i rot katalogen i din arbets katalog.

    > [!NOTE]
    > Den här mappen visas i _. gitignore_ -filen eftersom den ska genereras av CI/CD när du distribuerar.

## <a name="push-your-static-website-to-github"></a>Skicka din statiska webbplats till GitHub

Azure static Web Apps distribuerar din app från en GitHub-lagringsplats och fortsätter att göra detta för varje överförd incheckning till en viss gren. Använd följande kommandon för att synkronisera dina ändringar till GitHub.

1. Mellanlagra alla ändrade filer:

    ```bash
    git add .
    ```

1. Genomför alla ändringar

    ```bash
    git commit -m "Update build config"
    ```

1. Skicka ändringarna till GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Distribuera din statiska webbplats

Följande steg visar hur du länkar appen som du precis har push-överför till GitHub till Azure static Web Apps. I Azure kan du distribuera programmet till en produktions miljö.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Skapa en resurs för för hands versionen av Azure statisk Web Apps

1. Navigera till [Azure Portal](https://portal.azure.com)
1. Klicka på **Skapa en resurs**
1. Sök efter **Statiska webbappar**
1. Klicka på **Statiska webbappar (förhandsversion)**
1. Klicka på **Skapa**

1. Välj en prenumeration i list rutan *prenumeration* eller Använd standardvärdet.
1. Klicka på den **nya** länken under List rutan *resurs grupp* . I *nytt resurs grupp namn* skriver du **mystaticsite** och klickar på **OK**
1. Ange ett globalt unikt namn för din app i text rutan **namn** . Giltiga tecken är `a-z` , `A-Z` , `0-9` och `-` . Det här värdet används som URL-prefix för din statiska app i formatet `https://<APP_NAME>.azurestaticapps.net` .
1. I list rutan *region* väljer du en region som är närmast dig.
1. Välj **kostnads fritt** från List rutan SKU.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Skapa en statisk webbapp":::

### <a name="add-a-github-repository"></a>Lägg till en GitHub-lagringsplats

Det nya kontot för statisk Web Apps behöver åtkomst till lagrings platsen med din Nuxt.js-app så att den automatiskt kan distribuera incheckningar.

1. Klicka på **knappen Logga in med GitHub**
1. Välj den **organisation** som du skapade lagrings platsen för i Nuxt.js projektet, vilket kan vara ditt GitHub-användarnamn.
1. Sök efter och välj namnet på den databas som du skapade tidigare.
1. Välj **main** som gren i list rutan *gren* .

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="Ansluta GitHub":::

### <a name="configure-the-build-process"></a>Konfigurera Bygg processen

Azures statiska Web Apps skapas för att automatiskt utföra vanliga uppgifter som att installera NPM-moduler och köra `npm run build` under varje distribution. Det finns dock några inställningar, t. ex. mappen programkällfiler och den version av målmapp som du måste konfigurera manuellt.

1. Klicka på fliken **build (Bygg** ) för att konfigurera mappen statiska utdata.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Fliken bygge":::

1. Skriv **Dist** i text rutan *app artefakt plats* .

### <a name="review-and-create"></a>Granska och skapa

1. Klicka på knappen **Granska + skapa** för att kontrol lera att informationen är korrekt.
1. Klicka på **skapa** för att starta skapandet av resursen och etablera även en GitHub-åtgärd för distribution.
1. När distributionen är klar klickar du på **gå till resurs**
1. I _översikts_ fönstret klickar du på *URL* -länken för att öppna det distribuerade programmet. 

Om webbplatsen gör en omedelbar inläsning körs fortfarande åtgärds arbets flödet för bakgrunds GitHub. När arbets flödet är klart kan du klicka på uppdatera webbläsaren för att visa din webbapp.

Du kan kontrol lera status för åtgärdernas arbets flöden genom att gå till åtgärder för din lagrings plats:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Synkronisera ändringar

När du skapade appen skapade Azure static Web Apps en arbets flödes fil för GitHub åtgärder i din lagrings plats. Du måste överföra filen till din lokala lagrings plats så att git-historiken synkroniseras.

Gå tillbaka till terminalen och kör följande kommando `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Konfigurera dynamiska vägar

Navigera till den nyligen distribuerade platsen och klicka på någon av Ramverks-eller biblioteks logo typerna. I stället för att få en informations sida får du en felsida på 404.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 på dynamiska vägar":::

Orsaken till detta är Nuxt.js genererade den statiska platsen, men det gjorde det bara så för start sidan. Nuxt.js kan generera motsvarande statiska `.html` filer för alla `.vue` sidor-filer, men det finns ett undantag. 

Om sidan är en dynamisk sida finns det till exempel `_id.vue` inte tillräckligt med information för att generera en statisk HTML-kod från en sådan dynamisk sida. Du måste uttryckligen ange möjliga sökvägar för dynamiska vägar.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generera statiska sidor från dynamiska vägar

1. Uppdatera _nuxt.config.js_ -filen så att Nuxt.js använder en lista över alla tillgängliga data för att generera statiska sidor för varje ramverk/bibliotek:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` är en asynkron funktion, så du kan göra en begäran till ett API i den här funktionen och använda den returnerade listan för att generera Sök vägarna.

2. Skicka de nya ändringarna till GitHub-lagringsplatsen och vänta några minuter medan GitHub-åtgärder skapar din webbplats igen. När versionen har slutförts försvinner felet 404.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 på dynamiska vägar har åtgärd ATS":::

> [!div class="nextstepaction"]
> [Konfigurera en anpassad domän](custom-domain.md)
