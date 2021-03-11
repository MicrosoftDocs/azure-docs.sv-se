---
title: 'Självstudie: Distribuera Node.js appar som drivs av Azure Cosmos DB med Azure DevOps starter'
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Med DevOps Starter kan du distribuera din Node.js-app som drivs av Azure Cosmos DB till Windows Web App med några få enkla steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548707"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Distribuera Node.js appar som drivs av Azure Cosmos DB med DevOps starter

Azure DevOps starter erbjuder en strömlinjeformad upplevelse där du kan skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig distribution (CD) till Azure. Du gör detta genom att använda din befintliga kod och git-lagringsplats (lagrings platsen) eller genom att välja ett exempel program.

DevOps Starter är också:

* Skapar automatiskt Azure-resurser, till exempel Azure Cosmos DB, Azure Application insikter, Azure App Service och App Service planer

* Skapar och konfigurerar en versions pipeline för CI/CD i Azure DevOps

I de här självstudierna får du:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en Node.js app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration
> * Granska Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomför ändringarna i git och distribuera dem automatiskt till Azure
> * Rensa resurserna

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration, som du kan få via [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) kostnads fritt.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Använd DevOps starter för att distribuera Node.js app

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser, till exempel Azure Cosmos DB, Application Insights, App Service och App Service planer i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Välj **Node.js** som körnings miljö och välj sedan **Nästa**. Under **Välj ett program ramverk** väljer du **Express.js**.

1. Aktivera avsnittet **Lägg till en databas** för **Cosmos DB** och välj sedan **Nästa**.

    ![Lägg till en databas](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter har stöd för olika program ramverk, till exempel **Express.js**, **exempel Node.js app** och **Sail.js**. I den här självstudien använder vi **Express.js**.

1. Välj en Azure-tjänst för att distribuera programmet och välj sedan **Nästa**. Alternativen omfattar Windows Web App, Azure Kubernetes service och Azure Web App for Containers. I den här självstudien använder vi **Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure-DevOps och Azure-prenumerationen

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare inställningar för Azure-konfigurationen eller identifiera pris nivån och platsen väljer du **ytterligare inställningar**. I det här fönstret visas olika alternativ för att konfigurera pris nivån och platsen för Azure-tjänster.

1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.

1. Processen har slutförts efter några minuter. Ett exempel på en Node.js app har kon figurer ATS i en git-lagrings platsen i din Azure DevOps-organisation. Sedan skapas Azure Cosmos DB, App Service, App Service plan och Application Insights resurser, samt en CI/CD-pipeline. Din app distribueras sedan till Azure.

   När alla dessa processer är slutförda visas start-instrumentpanelen för Azure DevOps i Azure Portal. Du kan också gå till DevOps starter-instrumentpanelen direkt från **alla resurser** i Azure Portal.

   Den här instrument panelen ger insyn i din Azure DevOps Code-lagringsplats, din CI/CD-pipeline och din Azure Cosmos DB databas. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Välj **Azure Cosmos DB** på höger sida av instrument panelen för att visa dessa alternativ.

## <a name="examine-azure-cosmos-db"></a>Granska Azure Cosmos DB

DevOps starter konfigurerar automatiskt Azure Cosmos DB, som du kan utforska och anpassa. Gör så här för att bekanta dig med Azure Cosmos DB:

1. Gå till DevOps starter-instrumentpanelen.

    ![DevOps Projects instrument panel](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Välj Azure Cosmos DB till höger. Ett fönster öppnas för Azure Cosmos DB. I den här vyn kan du utföra olika åtgärder, till exempel övervakning av åtgärder och söknings loggar.

    ![Azure Cosmos DBs fönster](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps starter konfigurerar automatiskt en CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps starter-instrumentpanelen.

1. Välj hyperlänken under **build**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Fönstret build](_img/azure-devops-project-cosmos-db/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, till exempel hämtning av källkod från git-lagrings platsen, skapande av program, körning av enhets test och publicering av utdata som används för distributioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till lagrings platsen startar en ny version. Du kan välja att ta med eller undanta grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande och välj sedan **Spara** i list rutan **Spara & kö** .

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps starter skapar och konfigurerar automatiskt de nödvändiga stegen för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Gå till **pipeliner** och välj **versioner**.

1. Välj **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. Till höger om **Drop** -ikonen väljer du **utlösare för kontinuerlig distribution**. Den här versionen av pipelinen har aktiverat en kontinuerlig distributions utlösare, som utför en distribution varje gång en ny versions artefakt är tillgänglig. Du kan inaktivera utlösaren så att distributionen körs manuellt.

1. Till höger väljer du avsnitts **Visa versioner** för att visa en historik över versioner.

1. Välj den version som ska Visa pipelinen. Välj valfri miljö för att kontrol lera sammanfattningen av utgåvor, COMMIT eller associerade arbets objekt.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Genomför kod ändringar och kör CI/CD-pipeline

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Du är nu redo att samar beta med ett team i din app genom att använda en CI/CD-process som distribuerar ditt senaste arbete till din App Service. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. På menyn Azure-DevOps väljer du **databaser** och sedan **filer**. Gå sedan till din lagrings platsen.

1. Lagrings platsen innehåller redan kod baserat på det program språk som du valde i skapande processen. Öppna filen **program/vyer/index. pug** .

1. Välj **Redigera** och gör sedan en ändring av **rad nummer 15**. Du kan till exempel ändra till "min första distribution till Azure App Service som drivs av Azure Cosmos DB."

1. I det övre högra hörnet väljer du **genomför** och väljer sedan **genomför** igen för att skicka din ändring.

     Efter några sekunder startar en version i Azure DevOps och en version körs för att distribuera ändringarna. Övervaka Bygg status på DevOps starter-instrumentpanelen eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de relaterade resurserna som du har skapat när du inte behöver dem längre. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en Node.js app som drivs av Azure Cosmos DB
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska Azure Cosmos DB
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

Mer information och nästa steg finns i [definiera en pipeline för kontinuerlig distribution i flera steg (CD)](/azure/devops/pipelines/release/define-multistage-release-process) .