---
title: 'Självstudie: distribuera din ASP.NET-app till Azure Virtual Machines med hjälp av Azure DevOps starter'
description: Med DevOps Starter är det enkelt att komma igång med Azure och distribuera din ASP.NET-app till Azure Virtual Machines med några få enkla steg.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 3495d0bd2a446b6b3255887d9b4523eb5a70ac53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557326"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Självstudie: distribuera din ASP.NET-app till Azure Virtual Machines med hjälp av Azure DevOps starter

Med Azure DevOps starter får du en förenklad upplevelse där du kan ta med din befintliga kod och git-lagrings platsen eller välja ett exempel program för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps Starter är också:
* Skapar automatiskt Azure-resurser, till exempel en ny virtuell Azure-dator (VM).
* Skapar och konfigurerar en versionspipeline i Azure DevOps som innehåller en bygg-pipeline för CI.
* Konfigurerar en versionspipeline för CD. 
* Skapar en Azure Application Insights-resurs för övervakning.

I de här självstudierna får du:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera ASP.NET-appen
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>Använd DevOps starter för att distribuera ASP.NET-appen

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser som virtuella datorer i den Azure-prenumeration som du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett programramverk** väljer du **ASP.NET**. Välj sedan **Nästa**. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här. 

1. Välj den virtuella datorn och sedan **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din prenumeration för Azure-tjänster. Du kan också välja länken **Ändra** och sedan ange mer konfigurationsinformation, till exempel platsen för Azure-resurser.
 
1. Ange ett namn på virtuell dator, användarnamn och lösenord för din nya virtuella Azure-datorresurs och välj sedan **Klar**. Efter några minuter är din virtuella Azure-dator klar. Ett exempelprogram för ASP.NET konfigureras i en lagringsplats i Azure DevOps-organisationen, en version och release körs och programmet distribueras till den nyskapade virtuella Azure-datorn. 

   När den är klar visas DevOps starter-instrumentpanelen i Azure Portal. Du kan också navigera till instrumentpanelen direkt från **Alla resurser** i Azure-portalen. 

   Instrumentpanelen ger insyn i Azure DevOps-kodlagringsplatsen, din CI/CD-pipeline och programmet som körs i Azure.   

   ![Instrumentpanelsvy](_img/azure-devops-project-vms/vm-starter-dashboard.png)

DevOps starter konfigurerar automatiskt en CI-version och versions utlösare som distribuerar kod ändringar till din lagrings platsen. Du kan konfigurera fler alternativ i Azure DevOps. Visa ditt körande program genom att välja **Bläddra**.
    
## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen
 
DevOps starter konfigurerade automatiskt en CI/CD-pipeline i Azure-pipeliner. Du kan utforska och anpassa pipelinen. För att bekanta dig med bygg-pipelinen gör du följande:

1. Välj **Bygg pipelines** överst på DevOps starter-instrumentpanelen. En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **status** och välj ellipsen (...). En meny visar flera alternativ, till exempel att köa en ny version, pausa en version och redigera build-pipeline.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till lagrings platsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

DevOps starter skapar och konfigurerar automatiskt de nödvändiga stegen för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar också en CD-pipeline som tillhandahåller CD:n för den virtuella Azure-datorn. Om du vill veta mer om Azure DevOps CD-pipelinen kan du göra följande:

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  DevOps starter skapar en versions pipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) bredvid din releasepipeline och välj sedan **Redigera**. Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Bredvid ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Längst till vänster väljer du **Uppgifter** och sedan din miljö. Uppgifter är de aktiviteter som distributionsprocessen kör och de grupperas ihop i faser. Den här versionspipelinen sker i två faser:

    - Den första fasen innehåller en uppgift för distribution av Azure-resursgrupper som gör två saker:
     
        - Konfigurerar den virtuella datorn för distribution
        -   Lägger till den nya virtuella datorn till en Azure DevOps-distributionsgrupp. Distributions gruppen för virtuella datorer i Azure DevOps hanterar logiska grupper av distributions mål datorer
     
    - I den andra fasen skapar en IIS Web App Manage-uppgift en IIS-webbplats på den virtuella datorn. En andra IIS Web App Deploy-uppgift skapas för att distribuera platsen.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**. Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure 

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Kodändringarna startar CI/CD-processen och distribuerar automatiskt dina ändringar till IIS-webbplatsen på den virtuella Azure-datorn.

1. I den vänstra rutan väljer **Kod** och går sedan till din lagringsplats.

1. Gå till katalogen *Views\Home* och välj ellipsen (...) bredvid filen *Index.cshtml*. Välj sedan **Redigera**.

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna. Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka Bygg status på Start-instrumentpanelen i DevOps eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts kan du uppdatera ditt program för att verifiera dina ändringar.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurera övervakning med Azure Application Insights

Med Azure Application Insights kan du enkelt övervaka ett programs prestanda och användning. DevOps starter konfigurerar automatiskt en Application Insights resurs för ditt program. Du kan konfigurera ytterligare aviseringar och övervakningsfunktioner efter behov.

1. Gå till DevOps starter-instrumentpanelen i Azure Portal. 

1. Nere till höger väljer du **Application Insights**-länken för din app. Fönsterrutan **Application Insights** öppnas. Den här vyn innehåller övervakningsinformation om användning, prestanda och tillgänglighet för din app.

   ![Fönsterrutan Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Välj **Tidsintervall** och sedan **Senaste timmen**. Välj **Uppdatera** för att filtrera resultaten. Nu kan du se all aktivitet från de senaste 60 minuterna. 
    
1. Om du vill avsluta tidsintervallet väljer du **x**.

1. Välj **Aviseringar** och sedan **Lägg till metrisk varning**. 

1. Ange ett namn för aviseringen.

1. I listrutan **Statistik** undersöker du statistik om aviseringar. Standardaviseringen är för en **serversvarstid som är större än 1 sekund**. Du kan enkelt konfigurera en mängd olika aviseringar för att förbättra övervakningsfunktionerna i din app.

1. Välj kryssrutan **Meddela via e-postägare, deltagare och läsare**. Alternativt kan du utföra ytterligare åtgärder när en avisering visas genom att köra Azure-logikapp.

1. Skapa aviseringen genom att välja **OK**. Efter en liten stund visas aviseringen som aktiv på instrumentpanelen. 

1. Avsluta området **Aviseringar** och gå tillbaka till fönsterrutan **Application Insights**.

1. Välj **Tillgänglighet** och sedan **Lägg till test**. 

1. Ange ett testnamn och välj sedan **Skapa**. Ett enkelt pingtest skapas för att kontrollera tillgängligheten för ditt program. Testresultaten blir tillgängliga efter några minuter och Application Insights-instrumentpanelen visar en tillgänglighetsstatus.

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort den virtuella Azure-datorn och relaterade resurser som du skapade i den här självstudien. Det gör du genom att använda **borttagnings** funktionen på DevOps starter-instrumentpanelen. 

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. *Borttagnings* funktionen förstör data som skapats av projektet i DevOps starter i både Azure-och Azure-DevOps och du kan inte hämta det. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps starter-instrumentpanelen i Azure Portal.
1. Välj **Ta bort** i det övre högra hörnet. 
1. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera ASP.NET-appen
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](/azure/devops/pipelines/release/define-multistage-release-process)