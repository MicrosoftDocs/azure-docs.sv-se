---
title: 'Självstudie: Distribuera ASP.NET-appen och Azure SQL Database kod med hjälp av Azure DevOps starter'
description: DevOps starter gör det enkelt att komma igång med Azure. Med DevOps Starter kan du distribuera ASP.NET-appen och Azure SQL Database kod med några enkla steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e85b698e1b3c3a1c3e8730ab85e8b72b7b0d9f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548486"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Självstudie: Distribuera ASP.NET-appen och Azure SQL Database kod med hjälp av Azure DevOps starter

Med Azure DevOps starter får du en förenklad upplevelse där du kan ta med din befintliga kod och git-lagrings platsen eller välja ett exempel program för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps Starter är också:
* Skapar automatiskt Azure-resurser, till exempel en databas i Azure SQL Database.
* Skapar och konfigurerar en versionspipeline i Azure Pipelines som innehåller en bygg-pipeline för CI.
* Konfigurerar en versionspipeline för CD. 
* Skapar en Azure Application Insights-resurs för övervakning.

I de här självstudierna får du:

> [!div class="checklist"]
> * Använd Azure DevOps starter för att distribuera ASP.NET-appen och Azure SQL Database kod
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Ansluta till Azure SQL Database 
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>Skapa ett projekt i DevOps Projects för en ASP.NET-app och Azure SQL Database

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser, till exempel Azure SQL Database, i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett programramverk** väljer du **ASP.NET**.

1. Välj **Lägg till en databas** och sedan **Nästa**. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här. 
    
1. Välj **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din prenumeration för Azure-tjänster. Alternativt kan du välja **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera användarnamnet i avsnittet **Inloggningsuppgifter för databasserver**. Lagra användarnamnet för kommande steg i den här självstudien. Om du utför det här valfria steget avslutar du konfigurationen av Azure-området innan du väljer **Klar**.
 
1. Välj **Klar**. Efter några minuter slutförs processen och DevOps starter-instrumentpanelen öppnas i Azure Portal. Du kan också navigera till instrumentpanelen direkt från **Alla resurser** i Azure-portalen. Till höger väljer du **Bläddra** för att visa programmet som körs.
    
## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps starter konfigurerar automatiskt en fullständig CI/CD-pipeline i Azure databaser. Du kan utforska och anpassa pipelinen. För att bekanta dig med Azure DevOps-bygg-pipelinen gör du följande:

1. Välj **Bygg pipelines** överst på DevOps starter-instrumentpanelen. En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **status** och välj ellipsen (...). En meny visar flera alternativ, till exempel att köa en ny version, pausa en version och redigera build-pipeline.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till databasen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

DevOps starter skapar och konfigurerar automatiskt de nödvändiga stegen för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar också en CD-pipeline som tillhandahåller CD:n för den virtuella Azure-datorn. Om du vill veta mer om Azure DevOps CD-pipelinen kan du göra följande:

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**. DevOps starter skapar en versions pipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) bredvid din releasepipeline och välj sedan **Redigera**. Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

    DevOps starter konfigurerar ett slumpmässigt SQL-lösenord och använder det för versions pipelinen.
    
1. Längst till vänster väljer du **Variabler**. 

   > [!NOTE]
   > Utför endast följande steg om du har ändrat lösenordet för SQL Server. Det finns en lösenordsvariabel.
  
1. Bredvid rutan **Värde** väljer du hänglåsikonen, anger det nya lösenordet och väljer därefter **Spara**.

1. Längst till vänster väljer du **Uppgifter** och sedan din miljö. Uppgifter är de aktiviteter som distributionsprocessen kör och de grupperas ihop i faser. Den här versionspipelinen har enda fas som innehåller en *Azure App Service-distribution* och *Azure SQL Database-distribution*-uppgift.

1. Välj uppgiften *Kör Azure SQL* och undersök de olika egenskaperna som används för SQL-distribueringen. Under **Distributionspaket** använder aktiviteten en *SQL DACPAC-fil*.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**. Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure 

 > [!NOTE]
 > Följande procedur testar CI/CD-pipeline med en enkel textändring. Du kan även göra en ändring i SQL Server-schemat i tabellen för att testa SQL-distributionsprocessen.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att genomföra ändringarna för lagringsplatsen. Kodändringarna initierar CI/CD-processen och distribuerar automatiskt dina ändringar till Azure.

1. I den vänstra rutan väljer **Kod** och går sedan till din lagringsplats.

1. Gå till katalogen *SampleWebApplication\Views\Home* och välj sedan ellipsen (...) bredvid filen *Index.cshtml* och välj sedan **Redigera**. 

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna. Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka Bygg status på Start-instrumentpanelen i DevOps eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts kan du uppdatera ditt program för att verifiera dina ändringar.

## <a name="connect-to-azure-sql-database"></a>Ansluta till Azure SQL Database

Du måste ha rätt behörighet för att kunna ansluta till Azure SQL Database.

1. På instrument panelen för DevOps starter väljer du **SQL Database** för att gå till hanterings sidan för SQL Database.
   
1. Välj **Konfigurera serverbrandvägg** och välj **Lägg till klientens IP-adress**. 

1. Välj **Spara**. Din klient-IP-adress har nu åtkomst till SQL Server Azure-resursen.

1. Gå tillbaka till fönstret **SQL Database**. 

1. På höger sida väljer du servernamn för att gå till konfigurationssidan för **SQL Server**.

1. Välj **Återställ lösenord**, ange ett lösenord för inloggning för SQL Server och välj sedan **Spara**. Tänk på att behålla det här lösenordet för att använda det senare i den här självstudien.

    Du kan nu välja att använda klient verktyg som SQL Server Management Studio eller Visual Studio för att ansluta till SQL Server och Azure SQL Database. Använd egenskapen **servernamn** för att ansluta.

    Om du inte ändrade databasanvändarnamnet när du först konfigurerade projektet i DevOps Projects är ditt användarnamn den lokala delen av din e-postadress. Om din e-postadress till exempel är *johndoe \@ Microsoft.com*, är ditt användar namn *johndoe*.

   > [!NOTE]
   > Om du ändrar lösen ordet för SQL-inloggningen måste du ändra lösen ordet i pipeline-variabeln release enligt beskrivningen i avsnittet [Undersök CD-pipeline](#examine-the-cd-pipeline) .

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort Azure SQL Database och relaterade resurser som du har skapat i den här självstudien. Det gör du genom att använda **borttagnings** funktionen på DevOps starter-instrumentpanelen.

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. *Borttagnings* funktionen förstör data som skapats av projektet i DevOps starter i både Azure-och Azure-DevOps och du kan inte hämta det. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps starter-instrumentpanelen i Azure Portal.
2. Välj **Ta bort** i det övre högra hörnet. 
3. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd Azure DevOps starter för att distribuera ASP.NET-appen och Azure SQL Database kod
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure
> * Ansluta till Azure SQL Database 
> * Rensa resurser

Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>Videoklipp

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]