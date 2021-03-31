---
title: 'Självstudie: Distribuera ASP.NET-appar till Azure Functions med Azure DevOps starter'
description: Med Azure DevOps Starter är det enkelt att komma igång med Azure. Med DevOps Starter kan du distribuera ASP.NET-appen till Azure Functions med några enkla steg.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355482"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Distribuera till Azure Functions med DevOps starter

Med Azure DevOps starter får du en förenklad upplevelse där du kan ta med din befintliga kod och git-lagrings platsen eller välja ett exempel program för att skapa en pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) till Azure.

DevOps Starter är också:

* Skapar automatiskt Azure-resurser, till exempel Azure Functions

* Skapar och konfigurerar en versions pipeline i Azure DevOps för CI/CD

I de här självstudierna får du:

> [!div class="checklist"]
>* Använd DevOps starter för att distribuera en ASP.NET-app till Azure Function
>* Konfigurera Azure DevOps och en Azure-prenumeration
>* Granska Azure Function
>* Granska CI-pipelinen
>* Granska CD-pipelinen
>* Genomföra ändringar i Git och distribuera dem automatiskt till Azure
>* Rensa resurser

För närvarande är de körningar som stöds för functions **.net** och **Node.js**. Vi använder. NET Runtime för den här självstudien för att distribuera till Azure Functions. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få ett kostnads fritt via [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Använd DevOps starter för att distribuera en ASP.NET-app till Azure Functions

DevOps starter skapar en CI/CD-pipeline i Azure-pipelines. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar också Azure-resurser, till exempel en IoTHub, i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com)

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Välj **.NET** och sedan **Nästa**. Under **Välj ett program ramverk** väljer du **ASP.net** och klickar på **Nästa**.

1. Välj **Funktionsapp** och välj sedan **Nästa**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurera Azure-DevOps och Azure-prenumerationen

1. Ange ett namn för ditt Azure DevOps-projekt.

1. Skapa en ny Azure DevOps-organisation eller välj en befintlig organisation.

1. Välj din Azure-prenumeration.

1. Om du vill visa ytterligare konfigurations inställningar för Azure och identifiera pris nivån och platsen klickar du på **ytterligare inställningar**. I det här fönstret visas olika alternativ för att konfigurera pris nivån och platsen för Azure-tjänster.

1. Stäng Azure-konfigurationsområdet och välj sedan **Klar**.

1. Efter några minuter slutförs processen. Ett exempel på en ASP.NET-app har kon figurer ATS i en git-lagrings platsen i din Azure DevOps-organisation, en Funktionsapp och Application Insights skapas, en CI/CD-pipeline körs och din app distribueras till Azure.

   När allt detta är klart visas instrument panelen för Azure DevOps starter i Azure Portal. Du kan också gå till DevOps starter-instrumentpanelen direkt från **alla resurser** i Azure Portal.

   Den här instrument panelen ger insyn i din Azure DevOps Code-lagringsplats, din CI/CD-pipeline och din Azure-funktion. Du kan konfigurera fler CI/CD-alternativ i din Azure DevOps-pipeline. Välj **Funktionsapp** som ska visas till höger.

## <a name="examine-the-function-app"></a>Granska Funktionsapp

DevOps starter konfigurerar automatiskt Function-appen, som du kan utforska och anpassa. Gör så här för att bekanta dig med Function-appen:

1. Gå till DevOps starter-instrumentpanelen.

    ![DevOps Projects instrument panel](_img/azure-devops-project-functions/fapp-dashboard.png)

1. Välj Function-appen till höger. Ett fönster öppnas för Function-appen. I den här vyn kan du utföra olika åtgärder, till exempel åtgärder för övervakning, sökning av loggar.

    ![Funktionsapp](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

DevOps starter konfigurerar automatiskt en CI/CD-pipeline i Azure DevOps-organisationen. Du kan utforska och anpassa pipelinen. För att bekanta dig med den gör du följande:

1. Gå till DevOps starter-instrumentpanelen.

1. Klicka på hyperlänken under **bygge**. En webbläsarflik visar bygg-pipelinen för det nya projektet.

    ![Skärm bild som visar Azure Portal DevOps starter-instrumentpanel med en pil som pekar på en länk under build i pipeline för C I/C D.](_img/azure-devops-project-functions/build.png)

1. Välj **Redigera**. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, till exempel hämtning av källkod från git-lagrings platsen, skapande av program, körning av enhets test och publicering av utdata som används för distributioner.

1. Välj **Utlösare**. DevOps starter skapar automatiskt en CI-utlösare och varje incheckning till lagrings platsen startar en ny version. Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande och välj sedan **Spara** i list rutan **Spara & kö** .

1. Under ditt bygg-pipelinenamn väljer du **Historik**. I den här fönsterrutan visas en spårningslogg över de senaste ändringarna för versionen. Azure DevOps håller koll på alla ändringar som gjorts i bygg pipelinen och gör det möjligt att jämföra versioner.

## <a name="examine-the-cd-release-pipeline"></a>Granska CD-versionspipelinen

DevOps starter skapar och konfigurerar automatiskt de nödvändiga stegen för att distribuera från din Azure DevOps-organisation till din Azure-prenumeration. De här stegen innefattar att konfigurera en Azure-tjänstanslutning för att autentisera Azure DevOps till din Azure-prenumeration. Automationen skapar även en versionspipeline som tillhandahåller CD:n till Azure. Om du vill veta mer om versionspipelinen kan du göra följande:

1. Navigera till **pipelinen | Versioner**.

1. Klicka på **Redigera**.

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline som du undersökte i de föregående stegen skapar de utdata som används för artefakten.

1. På höger sida av ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**. Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.

1. Till höger väljer du alternativet för att **visa versioner** för att visa en historik över versioner.

1. Klicka på versionen för att Visa pipelinen. Klicka på valfri miljö för att kontrol lera **sammanfattningen av utgåvor, incheckningar**, associerade **arbets objekt**.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den här distributionen. Jämför versioner för att se skillnaderna i incheckning mellan distributioner.

1. Välj **Visa loggar**. Loggarna innehåller användbar information om distributionsprocessen. Du kan visa dem både under och efter distributionerna.

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

> [!NOTE]
> Följande procedur testar CI/CD-pipeline med en enkel textändring.

Du är nu redo att samar beta med ett team i din app genom att använda en CI/CD-process som automatiskt distribuerar det senaste arbetet till din Azure-funktion. Varje ändring i Git-lagringsplatsen startar en version i Azure DevOps och en CD-pipeline för kör en distribution till Azure. Följ proceduren i det här avsnittet eller använd en annan metod för att checka in ändringarna till lagringsplatsen. Du kan till exempel klona Git-lagringsplatsen i ditt favoritverktyg eller IDE och sedan push-överföra ändringarna till den här lagringsplatsen.

1. På menyn Azure-DevOps väljer du **databaser | Filer** och gå sedan till din lagrings platsen.

1. Databasen innehåller redan kod som heter **SampleFunctionApp** baserat på det program språk som du valde i skapande processen. Öppna filen **Application/SampleFunctionApp/Function1. cs** .

1. Välj **Redigera** och gör sedan en ändring av **rad numret 31** . Du kan till exempel uppdatera den till **Hej där! Välkommen till Azure Functions med DevOps starter**

1. Längst upp till höger väljer du **Checka in** och sedan **Checka in** igen för att push-överföra ändringarna.

1. Öppna filen **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Välj **Redigera** och gör sedan en ändring av **rad nummer 21**. Du kan till exempel uppdatera den till **Hej där! Välkommen till Azure Functions med Azure DevOps starter**.

     Efter en stund startar en version i Azure DevOps och därefter körs en version för att distribuera ändringarna. Övervaka Bygg status på DevOps starter-instrumentpanelen eller i webbläsaren med din Azure DevOps-organisation.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behöver dem. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Du kan även ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Du kan också använda det här CI/CD-mönstret som en mall för dina andra pipelines. I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en ASP.NET Core app till Azure Function
> * Konfigurera Azure DevOps och en Azure-prenumeration 
> * Granska Azure Function
> * Granska CI-pipelinen
> * Granska CD-pipelinen
> * Genomföra ändringar i Git och distribuera dem automatiskt till Azure
> * Rensa resurser

