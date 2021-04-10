---
title: Distribuera ASP.NET Core-appar till Azure Kubernetes-tjänsten med Azure DevOps starter
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Med DevOps Starter kan du distribuera din ASP.NET Core-app med Azure Kubernetes service (AKS) med några få enkla steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 9ccf28f5431a92f71b1c18e609639d0abf309c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590857"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>Distribuera ASP.NET Core-appar till Azure Kubernetes-tjänsten med Azure DevOps starter

Med Azure DevOps starter får du en förenklad upplevelse där du kan ta med din befintliga kod och git-lagrings platsen eller välja ett exempel program för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure. 

DevOps Starter är också:

* Skapar automatiskt Azure-resurser, till exempel Azure Kubernetes Service (AKS).
* Skapar och konfigurerar en versionspipeline i Azure DevOps som konfigurerar en versionspipeline för CI/CD.
* Skapar en Azure Application Insights-resurs för övervakning.
* Gör så att [Azure Monitor för containrar](../azure-monitor/containers/container-insights-overview.md) kan övervaka prestanda för containerarbetsbelastningar på AKS-klustret

I de här självstudierna får du:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en ASP.NET Core app till AKS
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>Använd DevOps starter för att distribuera en ASP.NET Core app till AKS

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser, till exempel ett AKS-kluster, i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Välj **.NET** och sedan **Nästa**.

1. Under **Välj ett program ramverk** väljer du **ASP.net Core** och väljer sedan **Nästa**.

1. Välj **Kubernetes Service**, och välj sedan **Nästa**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din Azure-prenumeration.

1. Välj **Ändra** för att se ytterligare Azure-konfigurationsinställningar och identifiera antalet noder för AKS-klustret. I den här fönsterrutan visas olika alternativ här för konfiguration av typ och plats för Azure-tjänster.
 
1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**. Efter några minuter är processen klar. En ASP.NET Core-exempelapp konfigureras i en Git-lagringsplats i din Azure DevOps-organisation, ett AKS-kluster skapas, en CI/CD-pipeline körs och appen distribueras till Azure. 

    När allt detta är klart visas instrument panelen för Azure DevOps starter i Azure Portal. Du kan också gå till DevOps starter-instrumentpanelen direkt från **alla resurser** i Azure Portal. 

    Den här instrumentpanelen ger insyn i din Azure DevOps-kodlagringsplats, din CI/CD-pipeline och ditt AKS-kluster. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Till höger väljer du **Bläddra** för att visa den körande appen.

## <a name="examine-the-aks-cluster"></a>Granska AKS-kluster

DevOps starter konfigurerar automatiskt ett AKS-kluster som du kan utforska och anpassa. För att bekanta dig med AKS-klustret gör du följande:

1. Gå till DevOps starter-instrumentpanelen.

1. Välj AKS-tjänsten till höger. Ett fönster öppnas för AKS-klustret. Från den här vyn kan du utföra olika åtgärder som att övervaka hälsotillstånd för containrar, söka i loggar och öppna Kubernetes-instrumentpanelen.

1. Till höger väljer du **Visa Kubernetes-instrumentpanelen**. Du kan också följa stegen för att öppna Kubernetes-instrumentpanelen.

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps starter konfigurerar automatiskt en CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps starter-instrumentpanelen.

1. Välj **Bygg pipelines** överst på DevOps starter-instrumentpanelen.  En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **status** och välj ellipsen (...).  En meny visar flera alternativ, till exempel att köa en ny version, pausa en version och redigera build-pipeline.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till lagrings platsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps starter skapar och konfigurerar automatiskt de nödvändiga stegen för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  DevOps starter skapar en versions pipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) bredvid din releasepipeline och välj sedan **Redigera**. Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**. Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Genomföra ändringar av Azure Repos och distribuera dem automatiskt till Azure 

 > [!NOTE]
 > Följande procedur testar CI/CD-pipeline med en enkel textändring.

Nu är du redo att samarbeta med ett team på din app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. I menyn Azure-DevOps väljer du **koda**  >  **filer** och går sedan till din lagrings platsen.

1. Gå till katalogen *Views\Home* och välj ellipsen (...) bredvid filen *Index.cshtml*. Välj sedan **Redigera**.

1. Gör en ändring i filen, till exempel att lägga till lite text i en av div-taggarna. 

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna. Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka Bygg status på DevOps starter-instrumentpanelen eller i webbläsaren med din Azure DevOps-organisation.

1. När versionen har slutförts uppdaterar du appen för att verifiera dina ändringar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du testar kan du rensa bland resurserna för att undvika att behöva betala fler avgifter. När de inte längre behövs kan du ta bort AKS-klustret och relaterade resurser som du skapade i den här självstudien. Det gör du genom att använda **borttagnings** funktionen på DevOps starter-instrumentpanelen.

> [!IMPORTANT]
> Följande procedur tar permanent bort resurser. *Borttagnings* funktionen förstör data som skapats av projektet i DevOps starter i både Azure-och Azure-DevOps och du kan inte hämta det. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps starter-instrumentpanelen i Azure Portal.
1. Välj **Ta bort** i det övre högra hörnet. 
1. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en ASP.NET Core app till AKS
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska AKS-kluster
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

Mer information om att använda Kubernetes-instrumentpanelen finns:

> [!div class="nextstepaction"]
> [Använda Kubernetes-instrumentpanelen](../aks/kubernetes-dashboard.md)