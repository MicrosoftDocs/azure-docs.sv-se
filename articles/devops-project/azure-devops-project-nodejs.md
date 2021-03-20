---
title: Skapa en CI/CD-pipeline för en PWA med GatsbyJS och Azure DevOps starter
description: Lär dig hur du skapar en NodeJS progressiv webbapp (PWA) med GatsbyJS och den förenklade funktionen för att skapa starter i Azure DevOps.
ms.prod: devops
ms.technology: devops-cicd
documentationcenter: vs-devops-build
author: mijacobs
ms.author: mijacobs
ms.workload: web
ms.topic: quickstart
ms.date: 03/24/2020
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d6d8a10dffa3801fe343b3b479265fac5612b021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556572"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Skapa en CI/CD-pipeline i Azure-pipelines för Node.js med Azure DevOps starter

I den här snabb starten skapar du en NodeJS progressiv webbapp (PWA) med [GatsbyJS](https://www.gatsbyjs.org/) och den förenklade skapande upplevelsen för Azure DevOps starter. När du är färdig har du en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din PWA i Azure-pipelines. Azure DevOps starter ställer upp vad du behöver för att utveckla, distribuera och övervaka.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- En [Azure DevOps](https://azure.microsoft.com/services/devops/) -organisation.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj **skapa en resurs** i den vänstra rutan. 

   ![Skapa en Azure-resurs i Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj Node.js-exempelprogrammet.   

    ![Välj Node.js exemplet](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Exempelramverket som är standard är **Express.js**. Ändra valet till **enkel Node.js app** och välj sedan **Nästa**. 

    ![Välj den enkla Node.js-appen](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. De distributions mål som är tillgängliga i det här steget styrs av program ramverket som valts i steg 2. I det här exemplet är **Windows Web Apps** standard distributions målet. Lämna **Web App for containers** ange och välj **Nästa**.

    ![Välj distributions mål](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurera ett projekt namn och en Azure-prenumeration

1. I det sista steget i arbets flödet för skapande av DevOps starter tilldelar du ett projekt namn, väljer en Azure-prenumeration och väljer **klar**.  

    ![Tilldela ett projekt namn och välj en prenumeration](_img/azure-devops-project-nodejs/assign-project-name.png)

1. En sammanfattnings sida visas när projektet har skapats och programmet distribueras till Azure. Efter en kort period skapas ett projekt i din [Azure DevOps-organisation](https://dev.azure.com/) som innehåller en git-lagrings platsen, ett kanban-kort, en distributions pipeline, test planer och de artefakter som krävs av din app.  

## <a name="managing-your-project"></a>Hantera ditt projekt

1. Navigera till **alla resurser** och hitta dina DevOps-startstarter. Välj din **DevOps-StartStart**.

    ![Azure DevOps-instrumentpanel i resurs lista](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Du dirigeras till en instrument panel som ger insyn i projektets start sida, kod lagrings plats, CI/CD-pipeline och en länk till appen som körs. Välj **Start sidan för projektet** om du vill visa ditt program i **Azure DevOps** och i en annan webbläsare väljer du **programmets slut punkt** för att Visa Live Sample-appen. Vi ändrar det här exemplet senare för att använda GatsbyJS-genererade PWA.

    ![Azure DevOps-instrumentpanel](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Från ditt Azure DevOps-projekt kan du bjuda in team medlemmar att samar beta och upprätta en Kanban-tavla för att börja spåra ditt arbete. Mer information finns [här](/azure/devops/user-guide/what-is-azure-devops).

![Översikt över Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klona lagrings platsen och installera din Gatsby-PWA

DevOps starter skapar en git-lagringsplats i Azure databaser eller GitHub. I det här exemplet har du skapat en Azure-lagrings platsen. Nästa steg är att klona lagrings platsen och göra ändringar.

1. Välj **databaser** från ditt **DevOps-projekt** och klicka sedan på **klona**.  Det finns olika mekanismer för att klona git-lagrings platsen till Skriv bordet.  Välj den som passar din utvecklings upplevelse.  

    ![Klona lagringsplatsen](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. När lagrings platsen har klonat till Skriv bordet gör du några ändringar i Start mal len. Börja med att installera GatsbyJS CLI från terminalen.

   ```powershell
    npm install -g gatsby
   ```

1. Från terminalen navigerar du till roten för din lagrings platsen. Den bör innehålla tre mappar som ser ut så här:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Vi vill inte att alla filer ska finnas i programmappen eftersom vi kommer att ersätta den med en Gatsby-StartStart. Kör följande kommandon, i ordning, för att trimma det nedåt.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Använd Gatsby CLI för att generera en PWA-exempel. Kör `gatsby new` från terminalen för att starta PWA-guiden och välj `gatsby-starter-blog` för din start-mall. Det bör likna det här exemplet:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Nu har du en mapp med namnet `my-gatsby-project` . Byt namn på den till `Application` och kopiera den `Dockerfile` till den.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. Öppna Dockerfile i din favorit redigerare och ändra den första raden från `FROM node:8` till `FROM node:12` . Den här ändringen säkerställer att din behållare använder Node.js version 12. x i stället för version 8. x. GatsbyJS kräver fler moderna versioner av Node.js.

1. Öppna sedan package.jsfilen i programmappen och redigera [fältet skript](https://docs.npmjs.com/files/package.json#scripts) för att se till att utvecklings-och produktions servrarna lyssnar på alla tillgängliga nätverks gränssnitt (till exempel 0.0.0.0) och port 80. Utan de här inställningarna kan behållar App Service inte dirigera trafik till Node.js-appen som körs i behållaren. `scripts`Fältet bör se ut ungefär så här. Mer specifikt vill du ändra `develop` -, `serve` -och- `start` målen från standardvärdena.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>Redigera dina CI/CD-pipeliner

1. Innan du genomför koden i föregående avsnitt gör du några ändringar i pipelinen för build och release. Redigera din "Build pipeline" och uppdatera noden så att den använder Node.js version 12. x. Ange fältet **aktivitets version** till 1. x och **versions** fältet till 12. x.

    ![Uppdatera Node.js till 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. I den här snabb starten skapar vi inte enhets test och vi inaktiverar de här stegen i vår versions pipeline. När du skriver tester kan du återaktivera de här stegen. Högerklicka för att välja de aktiviteter som är märkta med **Installera test beroenden** och **Kör enhets test** och inaktivera dem.

    ![Inaktivera Bygg test](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Redigera din versions pipeline.

    ![Redigera pipelinen för version](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Som med build-pipeline ändrar du nodens aktivitet så att 12. x används och inaktiverar de två test uppgifterna. Din utgåva bör likna den här skärm bilden.

    ![Pipeline för slutförd version](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. På vänster sida i webbläsaren går du till filen **views/index.pug**.

1. Välj **Redigera** och gör en ändring i h2-rubriken.  Skriv till exempel **Kom igång direkt med Azure DevOps starter** eller gör några andra ändringar.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps starter-instrumentpanelen.   
Du bör nu se att en version håller på att skapas. De ändringar du gjort skapas automatiskt och distribueras via en CI/CD-pipeline.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Genomför ändringarna och granska Azure CI/CD-pipeline

I de föregående två stegen lade du till en Gatsby-genererad PWA till git-lagrings platsen och redigerade pipelines för att skapa och distribuera koden. Vi kan genomföra koden och se hur den fortskrider genom att bygga och släppa pipelinen.

1. Från roten av ditt projekts git-lagrings platsen i en Terminal kör du följande kommandon för att skicka koden till ditt Azure DevOps-projekt:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. En version startas så snart som `git push` slutförts. Du kan följa förloppet från **Azure DevOps-instrumentpanelen**.

3. Efter några minuter bör dina pipeliner för build och release avslutas och din PWA bör distribueras till en behållare. Klicka på länken för **program slut punkten** från instrument panelen ovan så visas ett Gatsby start projekt för Bloggar.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du har skapat när du inte behöver resurserna längre. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerar din CI/CD-process skapas automatiskt build-och release-pipeliner. Du kan ändra de här bygg-och release-pipelinen för att uppfylla ditt teams behov. Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Anpassa CD-process](/azure/devops/pipelines/release/define-multistage-release-process)
