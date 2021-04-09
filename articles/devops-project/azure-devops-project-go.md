---
title: 'Snabb start: skapa en CI/CD-pipeline för go-programspråket med hjälp av Azure DevOps starter'
description: DevOps starter gör det enkelt att komma igång med Azure. Det hjälper dig att starta en webbapp med programmeringsspråket Go på en Azure-tjänst med några enkla steg.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 6d6181686eaeb90d4fcdae0231430623b84e2c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548520"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Skapa en CI/CD-pipeline för go med Azure DevOps starter

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din go-app med hjälp av Azure DevOps starter. DevOps starter fören klar den inledande konfigurationen av en pipeline för build och version av Azure DevOps.

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>Välj en exempelapp och en Azure-tjänst

1. Välj **Go**-exempelappen och välj sedan **Nästa**.  
    
1. Standardramverket är **Enkel Go-app**. Välj **Nästa**.  Det appramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten att distribuera. 
    
1. Låt den Azure-tjänst som är standard vara och välj **Nästa**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation. 

1. Ange ett namn för ditt Azure DevOps-projekt. 

1. Välj din Azure-prenumeration och plats, ange ett namn för appen och välj sedan **Klar**. Efter några minuter visas DevOps starter-instrumentpanelen i Azure Portal. En exempelapp konfigureras i en lagringsplats i din Azure DevOps-organisation, en version körs och appen distribueras till Azure. 

    Instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och appen i Azure. Till höger väljer du **Bläddra** för att visa den körande appen.

    ![Instrumentpanelsvy](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Checka in kodändringarna och kör CI/CD

DevOps starter skapar en git-lagrings platsen i Azure databaser eller GitHub. Visa lagringsplatsen och gör kodändringar i appen genom att utföra följande:

1. På DevOps starter går du till vänster och väljer länken för huvud grenen. Länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa URL för klonen av lagringsplatsen väljer du **Klona** längst upp till höger. Du kan klona Git-lagringsplatsen i din favorit-IDE. I de följande stegen kan du använda webbläsaren för att göra och bekräfta kod ändringar direkt till huvud grenen.

1. Längst till vänster går du till filen *views/index.html* och väljer sedan **Redigera**.

1. Gör en ändring i filen. Du kan till exempel ändra text i en av div-taggarna.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps Projects-instrumentpanelen. En version bör pågå. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

DevOps starter konfigurerar automatiskt en fullständig CI/CD-pipeline i Azure databaser. Utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att göra följande:

1. Gå till DevOps starter-instrumentpanelen.

1. Överst på sidan väljer du **Bygg-pipelines**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

1. Peka på fältet **status** och välj ellipsen (...). En meny visar flera alternativ, till exempel att köa en ny version, pausa en version och redigera build-pipeline.

1. Välj **Redigera**.

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande, välj **Spara och köa** och sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till lagrings platsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Build and Release** (Byggen och versioner) och sedan **Versioner**.  DevOps starter skapar en versions pipeline för att hantera distributioner till Azure.

1. Välj ellipsen (...) bredvid din releasepipeline och välj sedan **Redigera**. Versionspipelinen innehåller en *pipeline* som definierar släpprocessen.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte tidigare skapar de utdata som används för artefakten. 

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning. 

1. Till vänster väljer du **Uppgifter**. Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Välj ellipsen (...) intill en version och välj sedan **Öppna**. Du kan utforska flera menyer, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. 

1. Välj **loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort Azure App Service-instansen och relaterade resurser som du skapade i den här snabbstarten. Det gör du genom att använda **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Se följande om du vill lära dig mer om att ändra dessa bygg- och versionspipelines för att uppfylla behoven i ditt team:

> [!div class="nextstepaction"]
> [Definiera din CD-pipeline med flera steg](/azure/devops/pipelines/release/define-multistage-release-process)