---
title: 'Snabbstart: Skapa en CI/CD-pipeline för Java – Azure DevOps Starter'
description: Lär dig att använda den förenklade Azure DevOps Starter-upplevelsen för att konfigurera en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Java-app i Azure Pipelines.
services: vsts
documentationcenter: vs-devops-build
author: mlearned
ms.author: mlearned
manager: gwallace
ms.date: 03/24/2020
ms.prod: devops
ms.technology: devops-cicd
ms.topic: quickstart
ms.workload: web
ms.tgt_pltfrm: na
ms.custom:
- mvc
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 30ee97e0928f7b2995bd7bf02bec15bb8a46f7e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535426"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>Konfigurera en CI/CD-pipeline för en Java-app med Azure DevOps Starter

I den här snabbstarten använder du den förenklade Azure DevOps Starter-upplevelsen för att konfigurera en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Java-app i Azure Pipelines. Du kan använda Azure DevOps Starter för att konfigurera allt du behöver för att utveckla, distribuera och övervaka din app. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Ett [Azure DevOps-konto](https://azure.microsoft.com/services/devops/) och en organisation.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Starter skapar en CI/CD-pipeline i Azure Pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Starter skapar även Azure-resurser i valfri Azure-prenumeration.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I sökrutan skriver du **DevOps Starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps Starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Välj Java-exempelprogrammet. Java-exemplet innehåller ett val av flera programramverk.

1. Det exempelramverk som är standard är Spring. Låt standardinställningen vara och välj **Nästa**.  Web App for Containers är det distributionsmål som är standard. Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här. 

2. Låt standardinställningen för tjänsten vara och välj **Nästa**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny Azure DevOps-organisation eller använd en befintlig organisation. 
   
   1. Välj ett namn för projektet. 
   
   1. Välj din Azure-prenumeration och plats, välj ett namn för programmet och välj sedan **Klar**.  
   Efter några minuter visas DevOps Starter-instrumentpanelen i Azure Portal. Ett exempelprogram konfigureras i en lagringsplats i din Azure DevOps-organisation, en version körs och programmet distribueras till Azure. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.
   
2. Visa ditt körande program genom att välja **Bläddra**.
   
   ![Visa programmets instrumentpanel i Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Starter konfigurerade automatiskt en CI-version och lanseringsutlösare.  Nu är du redo att samarbeta med ett team på en Java-app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps Starter skapar en Git-lagringsplats i Azure Repos eller GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om DevOps Starter-instrumentpanelen väljer du länken för din huvudgren. Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren. Du kan klona Git-lagringsplatsen till din favorit-IDE. I följande steg kan du använda webbläsaren för att göra och genomföra kodändringar direkt till huvudgrenen.

1. På vänster sida i webbläsaren går du till filen **src/main/webapp/index.html**.

1. Välj **Redigera** och gör sedan en ändring i en del av texten.
    Du kan till exempel ändra en del av texten för en av div-taggarna.

1. Välj **Incheckning** och spara sedan ändringarna.

1. Gå till DevOps Starter-instrumentpanelen i webbläsaren.   
Du bör nu se att en version håller på att skapas. De ändringar som du just utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

 I föregående steg konfigurerade DevOps Starter automatiskt en fullständig CI/CD-pipeline. Utforska och anpassa pipelinen efter behov. Utför följande steg för att bekanta dig med bygg- och versionspipelines.

1. Längst upp på DevOps Starter-instrumentpanelen väljer du **Skapa pipelines.** Länken öppnar en webbläsarflik och bygg-pipelinen för det nya projektet.

1. Peka på **fältet Status** och välj sedan ellipsen (...). Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel att köa en ny version, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata som används för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
I fönsterrutan **Historik** ser du en spårningslogg över de senaste ändringarna för versionen.  Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  DevOps Starter skapade automatiskt en CI-utlösare, och varje genomförande till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  
 DevOps Starter skapar en lanseringspipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) till vänster intill din versionspipeline och välj sedan **Redigera**. Versionspipelinen innehåller en pipeline som definierar versionsprocessen.  
    
12. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**. Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

1. Välj ellipsen (...) bredvid en av dina versioner och välj sedan **Öppna**. Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**. Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade CI/CD-processen skapades bygg- och versionspipelines automatiskt. Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Mer information om CI/CD-pipelinen finns i:

> [!div class="nextstepaction"]
> [Anpassa CD-process](/azure/devops/pipelines/release/define-multistage-release-process)
