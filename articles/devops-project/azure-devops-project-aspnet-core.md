---
title: 'Snabb start: skapa en CI/CD-pipeline för .NET med Azure DevOps starter'
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Det hjälper dig att starta en .NET-app på en Azure-tjänst med några enkla få steg.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/23/2021
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 75531b9a75c2d28ad665748a9be4e37e2056b451
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547976"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Skapa en CI/CD-pipeline för .NET med Azure DevOps-startpaketet

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt .NET Core-eller ASP.NET-program med DevOps starter. DevOps starter fören klar den inledande konfigurationen av en pipeline för build och release i Azure pipelines.

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps starter skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Microsoft Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny. 

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj **.NET**-exempelprogrammet. .NET-exempel innehåller antingen ASP.NET-ramverket med öppen källkod eller det plattformsoberoende .NET Core-ramverket.

   ![.NET-ramverk](_img/azure-devops-project-aspnet-core/select-dotnet.png)
   
   > [!NOTE]
   > Standard alternativet för att konfigurera DevOps Starter är med **GitHub**, men den här inställningen kan ändras från guiden.
2. Det här exemplet är ett ASP.NET Core MVC-program. Välj **.net Core** Application Framework och välj sedan **Nästa**.    
    
3. Välj **Windows-webbapp** som distributions mål och välj sedan **Nästa**. Du kan också välja andra Azure-tjänster för din distribution. Program ramverket som du valde tidigare avgör vilken typ av distributions mål för Azure-tjänsten som är tillgänglig här.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Ange ett **projekt namn**.

2. Skapa en ny kostnads fri **Azure DevOps-organisation** eller Välj en befintlig organisation i list rutan.

3. Välj din **Azure-prenumeration**, ange ett namn för din **webbapp** eller gör standardvärdet och välj sedan **slutfört**. Efter några minuter visas översikten DevOps starter Deployment i Azure Portal. 

4. Välj **gå till resurs** för att Visa DevOps starter-instrumentpanelen. I det övre högra hörnet fäster du **projektet** på instrument panelen för snabb åtkomst. En exempel app har kon figurer ATS i en lagrings platsen i din **Azure DevOps-organisation**. En version körs och din app distribueras till Azure.

5. Instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och appen i Azure. Välj **Bläddra** till höger under Azure-resurser för att Visa appen som körs.

   ![Instrumentpanelsvy](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps starter skapade en git-lagringsplats i Azure databaser eller GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om instrument panelen för DevOps starter väljer du länken för **huvud** grenen. Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

2. I de följande stegen kan du använda webbläsaren för att göra och bekräfta kod ändringar direkt till **huvud** grenen. Du kan också klona git-lagringsplatsen i din favorit-IDE genom att välja **klona** från det övre högra hörnet på sidan databas. 

3. Till vänster navigerar du program fil strukturen till **Application/ASPNET-Core-dotNet-Core/pages/index. cshtml**.

4. Välj **Redigera** och gör en ändring i h2-rubriken. Skriv till exempel **Kom igång direkt med Azure DevOps-Startstarter** eller gör några andra ändringar.

      ![Ändringar i koden](_img/azure-devops-project-aspnet-core/codechange.png)

5. Välj **genomför**, lämna en kommentar och välj **genomför** igen.

6. Gå till Start-instrumentpanelen för Azure DevOps i webbläsaren.  Du bör nu se att en version håller på att skapas. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående steg konfigurerade Azure DevOps starter automatiskt en fullständigt CI/CD-pipeline. Utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att utföra följande steg.

1. Välj **Bygg pipelines** överst på DevOps starter-instrumentpanelen. Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

1. Välj ellipsen (...).  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel köa en ny version, göra en paus och redigera build-pipeline.

1. Välj **Redigera**.

    ![Bygg-pipeline](_img/azure-devops-project-aspnet-core/builddef.png)

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata som används för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
I fönsterrutan **Historik** ser du en spårningslogg över de senaste ändringarna för versionen.  Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps starter skapade automatiskt en CI-utlösare och varje incheckning till databasen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Byggen och versioner** och sedan **Versioner**.  
DevOps starter skapar en versions pipeline för att hantera distributioner till Azure.

1.  Välj ellipsen (...) till vänster intill din versionspipeline och välj sedan **Redigera**. Versionspipelinen innehåller en pipeline som definierar versionsprocessen.  

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

1. Till vänster väljer du **Uppgifter**.  Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

1. Välj ellipsen (...) bredvid någon av dina versioner och välj sedan **Öppna**. Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **loggar**. Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Se den här självstudien för att lära dig mer om att ändra dessa bygg- och versionspipelines för att uppfylla behoven i ditt team:

> [!div class="nextstepaction"]
> [Anpassa CD-process](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>Videoklipp

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
