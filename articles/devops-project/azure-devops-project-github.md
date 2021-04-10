---
title: 'Självstudie: skapa en CI/CD-pipeline för din befintliga kod med hjälp av Azure DevOps starter'
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Med några enkla steg kan DevOps Projects hjälpa dig att använda din egen kod och GitHub-lagrings platsen för att starta en app på en Azure-tjänst.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566608"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Skapa en CI/CD-pipeline för GitHub-lagrings platsen med Azure DevOps starter

Azure DevOps starter presenterar en förenklad process för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure. Du kan ta med din befintliga kod och git-lagrings platsen, eller så kan du välja ett exempel program.

Du kommer att:

> [!div class="checklist"]
> * Använd DevOps starter för att skapa en CI/CD-pipeline
> * Konfigurera åtkomst till din GitHub-lagringsplats och välja ett ramverk
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Granska Azure Pipelines-CI/CD-pipelinen
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Åtkomst till en GitHub eller extern git-lagrings platsen som innehåller .NET, Java, PHP, Node.js, python eller statisk webb kod.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Azure DevOps starter skapar en CI/CD-pipeline i Azure-pipeliner. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. Azure DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Välj **Ha med din egen kod** och sedan **Nästa**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurera åtkomst till din GitHub-lagrings platsen och välj ett ramverk

1. Välj antingen **GitHub** eller en extern **git** Code-lagringsplats. I den här självstudien väljer du **GitHub**. Du kan behöva autentisera med GitHub första gången så att Azure kan komma åt din GitHub-lagringsplats.

1. Välj en **lagrings plats** och en **gren** och välj sedan **Nästa**.

1. Om du använder Docker-behållare ändrar du **appens Dockerized** till **Ja**. I den här självstudien lämnar du **inget** markerat och väljer sedan **Nästa**. Om du vill ha mer information om hur du använder Docker-behållare, hovrar du över **i** -ikonen.

   ![Val av program ramverk i nedrullningsbar meny](_img/azure-devops-project-github/appframework.png)

1. I list menyerna väljer du en **program körning** och ett **program ramverk** och väljer sedan **Nästa**. Program ramverket styr vilken typ av distributions mål för Azure-tjänsten som är tillgänglig.

1. Välj en **Azure-tjänst** för att distribuera programmet och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Ange ett namn för **projekt namn**.

1. Skapa en ny kostnads fri organisation i **Azure DevOps Organization** eller Välj en befintlig organisation i den nedrullningsbara menyn.

1. Välj din prenumeration i **Azure-prenumerationen** och ange antingen ett namn i **webbappen** eller Använd standardvärdet. Välj en **plats** och välj sedan **färdig**. Efter några minuter visas översikten DevOps starter Deployment i Azure Portal.

1. Välj **gå till resurs** för att Visa DevOps starter-instrumentpanelen. I det övre högra hörnet fäster du **projektet** på instrument panelen för snabb åtkomst. Azure DevOps starter konfigurerar automatiskt en CI-version och versions utlösare. Din kod finns kvar i din GitHub-lagrings platsen eller någon annan extern lagrings platsen och en exempel-app har kon figurer ATS i en lagrings platsen i **Azure DevOps-organisationen**. Azure DevOps starter kör bygget och distribuerar appen till Azure.

   ![Vyn instrument panel för Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Instrument panelen visar din kod lagrings platsen, din CI/CD-pipeline och din app i Azure. Till höger, under Azure-resurser, väljer du **Bläddra** för att Visa appen som körs.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure

Du är nu redo att samar beta med en app med ett team. CI/CD-processen distribuerar automatiskt ditt senaste arbete till din webbplats. Varje förändring i GitHub-lagrings platsen startar en version i Azure DevOps och en CD-pipeline kör en distribution till Azure.

1. Från din DevOps starter-instrumentpanel väljer du **databaser**. Din GitHub-lagringsplats öppnas i en ny flik i webbläsaren. Gör en ändring i programmet och välj sedan **genomför ändringar**.

1. Efter en liten stund startar en version i Azure Pipelines. Du kan övervaka build-statusen på DevOps starter-instrumentpanelen. Du kan också övervaka den i din Azure DevOps-organisation genom att välja fliken **Bygg pipeliner** från start-instrumentpanelen för DevOps.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Granska Azure Pipelines-CI/CD-pipelinen

Azure DevOps starter konfigurerar automatiskt en CI/CD-pipeline i Azure-pipeliner. Utforska och anpassa pipelinen efter behov. Utför följande för att bekanta dig med bygg- och versionspipelines:

1. Välj **Bygg pipeliner** från start-instrumentpanelen DevOps.

1. När sidan för **Azure-pipelinen** öppnas visas en historik över de senaste build-versionerna och status för varje version.

   ![Sidan för Azure pipeline-versioner](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. I det övre högra hörnet på sidan **versioner** kan du välja **Redigera** för att ändra den aktuella versionen, **kön** för att lägga till en ny version eller knappen lodrät ellips (**&#8942;**) för att öppna en meny med fler alternativ. Välj **Redigera**.

1. Bygget gör olika uppgifter, till exempel hämtning av källor från lagrings platsen, återställning av beroenden och publicering av utdata för distributioner. Till höger, under **namn**, ändrar du namnet på Build-pipelinen till något mer beskrivande. Välj **spara & kö** och välj sedan **Spara**. Ange en kommentar och välj sedan **Spara** igen.

   ![Sidan Azure DevOps-versioner](_img/azure-devops-project-github/buildpage.png)

1. Om du vill se en gransknings historik för de senaste ändringarna för versionen väljer du fliken **Historik** .  Azure DevOps spårar alla ändringar som görs i den bygga pipelinen och gör det möjligt att jämföra versioner.

1. Välj fliken **utlösare** . Azure DevOps Projects skapar automatiskt en CI-utlösare med vissa standardinställningar. Du kan ställa in utlösare, till exempel **aktivera kontinuerlig integrering** för att köra en version varje gången du genomför en kod ändring. Du kan också ställa in utlösare för att schemalägga versioner att köras vid specifika tidpunkter.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Azure App Service och de relaterade resurserna som du skapade i den här självstudien kan du ta bort dem. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade din CI/CD-process i den här självstudien skapade du automatiskt en pipeline för build och release i Azure DevOps Projects. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov.

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](/azure/devops/pipelines/release/define-multistage-release-process)

Mer information om program övervakning finns i:
  
 > [!div class="nextstepaction"]
 > [Vad är Azure Monitor?](../azure-monitor/overview.md)