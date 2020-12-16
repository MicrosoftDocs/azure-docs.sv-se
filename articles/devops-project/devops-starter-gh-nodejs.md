---
title: 'Snabb start: skapa ett CI/CD-arbetsflöde för Node.js-DevOps starter för GitHub för distribution till Azure'
description: DevOps starter gör det enkelt att komma igång med Azure med GitHub-åtgärder.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588819"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>Konfigurera CI/CD för en Node.js-app med DevOps starter med GitHub-åtgärder

I den här snabb starten använder du den förenklade DevOps-startupplevelsen för att skapa ett arbets flöde för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din Node.js-app med GitHub-åtgärder. Du kan använda DevOps starter för att konfigurera allt du behöver för att utveckla, distribuera och övervaka din app. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Ett [GitHub](https://github.com/)-konto.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps starter skapar ett CI/CD-arbetsflöde med GitHub-åtgärder. DevOps starter skapar också Azure-resurser i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Klicka på **Konfigurera DevOps starter med GitHub** på höger sida.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Kontrol lera att CI/CD-providern har valts som **GitHub-åtgärder**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Välj **Node.js**-exempelprogrammet. Node.js-exemplet innehåller ett val av flera programramverk.

1. Exempelramverket som är standard är **Express.js**. Låt standardinställningen vara och välj **Nästa**.   

2. Windows Web App är standard distributions målet. Det programramverk som du valde tidigare avgör vilken typ av distributionsmål som finns tillgängliga för Azure-tjänsten här. Låt standardinställningen för tjänsten vara och välj **Nästa**.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>Konfigurera GitHub-konto och en Azure-prenumeration 

1. Autentisera med GitHub.

   1. Klicka på knappen **auktorisera** . 
   
   1. Logga in på GitHub. Om du inte har ett GitHub-konto kan du registrera dig här även.

2. Välj en befintlig **GitHub-organisation**. 
   
   1. Välj ett namn för din GitHub-lagringsplats. 
   
   1. Välj din Azure-prenumeration och plats, välj ett namn för programmet och välj sedan **Klar**.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Efter några minuter visas DevOps starter-instrumentpanelen i Azure Portal. Ett exempel program har kon figurer ATS i en lagrings plats i din Azure DevOps-organisation, ett GitHub-arbetsflöde utlöses och ditt program distribueras till Azure. Den här instrument panelen ger insyn i din kod lagrings plats, GitHub-arbetsflöde och ditt program i Azure.
   
3. Visa ditt körande program genom att välja **Bläddra**.
    
    Instrument panelen innehåller information om GitHub-arbetsflöde och Azure-resurser. Om du vill visa information om GitHub-arbetsflöde som senaste körning, genomförande och jobb status måste du **auktorisera till GitHub**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

DevOps starter konfigurerade automatiskt ett GitHub-arbetsflöde med bygg-och distributions jobb med hjälp av GitHub-åtgärder. Nu är du redo att samarbeta med ett team på en Node.js-app med en CI/CD-process som automatiskt distribuerar ditt senaste arbete till din webbplats.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps starter skapar en lagrings plats i GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om instrument panelen för DevOps starter väljer du länken för huvud grenen. Den här länken öppnar en vy till den nyligen skapade GitHub-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren. Du kan klona Git-lagringsplatsen till din favorit-IDE. I de följande stegen kan du använda webbläsaren för att göra och bekräfta kod ändringar direkt till huvud grenen.

1. Gå till filen **/Application/views/index.pug** till vänster i webbläsaren.

1. Välj **Redigera** och gör sedan en ändring i en del av texten.
    Du kan till exempel ändra lite text för en av taggarna.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps starter-instrumentpanelen.   
Nu bör du se en pågående GitHub för arbets flödes jobb. De ändringar du gjort skapas automatiskt och distribueras via ett GitHub-arbetsflöde.

## <a name="view-the-github-workflow"></a>Visa GitHub-arbetsflödet

I det föregående steget konfigurerade DevOps starter automatiskt ett fullständigt GitHub-arbetsflöde. Utforska och anpassa arbets flödet efter behov. Vidta följande steg för att bekanta dig med arbets flödet.

1. Till vänster om instrument panelen för DevOps starter väljer du **GitHub-arbetsflöde**. Den här länken öppnar en flik i webbläsaren och GitHub-arbetsflödet för det nya projektet.
    > [!NOTE]
    > Byt inte namn på arbets flödes filen. Namnet på arbets flödes filen ska vara **DevOps-starter-Workflow. yml** för att instrument panelen ska återspegla ändringarna

1. Arbets flödets yaml-fil innehåller alla GitHub-åtgärder som krävs för att bygga och distribuera programmet. Klicka på alternativet **Redigera fil** för att anpassa arbets flödes filen.

1. På fliken **kod** i lagrings platsen klickar du på **genomför**. Den här vyn visar kodincheckningar som är associerade med den specifika distributionen.

1. På fliken **åtgärder** i lagrings platsen kan du Visa historiken för alla arbets flödes körningar av din lagrings plats.

1. Välj den **senaste körningen** för att visa alla jobb som kördes i arbets flödet.

1. Klicka på **jobben** för att visa detaljerade loggar för arbets flödes körningen. Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.

1. Klicka på fliken **pull-begäran** om du vill visa alla pull-begäranden på din lagrings plats

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser när de inte längre behövs. Använd **borttagnings** funktionen på DevOps starter-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

När du konfigurerade din CI/CD-process skapades GitHub-arbetsflödet automatiskt. Du kan ändra det här arbets flödet så att det passar ditt teams behov. Mer information om GitHub-åtgärder och arbets flöde finns i:

> [!div class="nextstepaction"]
> [Anpassa GitHub-arbetsflöde](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
