---
title: 'Självstudie: distribuera din Node.js-app till Azure Web App med DevOps starter för GitHub-åtgärder'
description: DevOps starter gör det enkelt att komma igång med Azure och distribuera din Node.js-app till Azure Web App med några få enkla steg.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588785"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Självstudie: Distribuera Node.js app till Azure Web App med DevOps starter för GitHub-åtgärder

DevOps starter för GitHub-åtgärder presenterar en förenklad upplevelse där du kan välja ett exempel program för att skapa ett arbets flöde för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för distribution till Azure. 

DevOps Starter är också:
* Skapar automatiskt Azure-resurser, till exempel en ny Azure-webbapp.
* Skapar och konfigurerar ett arbets flöde i GitHub som innehåller ett Bygg jobb för CI.
* Arbets flödet innehåller också ett distributions jobb för CD. 
* Skapar en Azure Application Insights-resurs för övervakning.

I de här självstudierna får du:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en Node.js app
> * Konfigurera GitHub och en Azure-prenumeration 
> * Granska GitHub-arbetsflödet
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Använd DevOps starter för att distribuera en Node.js app

DevOps starter skapar ett arbets flöde i GitHub. Du kan använda en befintlig GitHub-organisation. DevOps starter skapar också Azure-resurser som webbappar i den Azure-prenumeration du väljer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I rutan Sök skriver du **DevOps starter** och väljer sedan. Klicka på **Lägg till** för att skapa en ny.

    ![DevOps starter-instrumentpanelen](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Kontrol lera att CI/CD-providern har valts som **GitHub-åtgärder**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Välj **Node.js** och välj sedan **Nästa**.

1. Under **Välj ett program ramverk** väljer du **Express.js** och väljer sedan **Nästa**. Programramverket som du valde i föregående steg avgör vilka typer av distributionsmål som finns tillgängliga för Azure-tjänsten här. 

1. Välj **Windows-webbappen** och välj sedan **Nästa**.

## <a name="configure-github-and-an-azure-subscription"></a>Konfigurera GitHub och en Azure-prenumeration

1. **Auktorisera** GitHub och välj en befintlig GitHub-organisation. 

1. Ange ett namn för din **GitHub-lagringsplats**. 

1. Välj din prenumeration för Azure-tjänster. Du kan också välja länken **Ändra** och sedan ange mer konfigurationsinformation, till exempel platsen för Azure-resurser.
 
1. Ange ett namn på en webbapp och välj sedan **slutfört**. Efter några minuter är Azure-webbappen klar. Ett exempel på ett Node.js program har kon figurer ATS i en lagrings platsen i din GitHub-organisation, ett arbets flöde utlöses och ditt program distribueras till den nya Azure-webbappen.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   När den är klar visas DevOps starter-instrumentpanelen i Azure Portal. Du kan också navigera till instrumentpanelen direkt från **Alla resurser** i Azure-portalen. 

   Instrument panelen ger insyn i GitHub Code lagrings platsen, ditt CI/CD-arbetsflöde och ditt program som körs i Azure.   

   ![Instrumentpanelsvy](_img/azure-devops-project-nodejs/full-dashboard.png)

DevOps starter konfigurerar automatiskt en utlösare som distribuerar kod ändringar till din lagrings platsen.
    
## <a name="examine-the-github-workflow"></a>Granska GitHub-arbetsflödet

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

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps starter skapar en lagrings plats i GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om instrument panelen för DevOps starter väljer du länken för huvud grenen. Den här länken öppnar en vy till den nyligen skapade GitHub-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren. Du kan klona Git-lagringsplatsen till din favorit-IDE. I de följande stegen kan du använda webbläsaren för att göra och bekräfta kod ändringar direkt till huvud grenen.

1. Gå till filen **/Application/views/index.pug** till vänster i webbläsaren.

1. Välj **Redigera** och gör sedan en ändring i en del av texten.
    Du kan till exempel ändra lite text för en av taggarna.

1. Välj **Incheckning** och spara sedan ändringarna.

1. I webbläsaren går du till DevOps starter-instrumentpanelen.   
Nu bör du se en pågående GitHub för arbets flödes jobb. De ändringar du nyss gjort skapas och distribueras automatiskt via ett GitHub-arbetsflöde.

1. När distributionen är klar uppdaterar du ditt program för att verifiera ändringarna.

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
> Följande procedur tar permanent bort resurser. *Borttagnings* funktionen förstör data som skapats av projektet i DevOps starter i båda Azure och du kan inte hämta det. Använd den här proceduren först när du har läst anvisningarna noga.

1. Gå till DevOps starter-instrumentpanelen i Azure Portal.
1. Välj **Ta bort** i det övre högra hörnet. 
1. Vid uppmaningen väljer du **Ja** för att *permanent ta bort* resurserna.

Du kan också ändra arbets flödet så att det uppfyller behoven för ditt team. Du kan också använda det här CI/CD-mönstret som en mall för dina andra databaser. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd DevOps starter för att distribuera en Node.js app
> * Konfigurera GitHub och en Azure-prenumeration 
> * Granska GitHub-arbetsflödet
> * Genomföra ändringar i GitHub och automatiskt distribuera dem till Azure
> * Konfigurera övervakning med Azure Application Insights
> * Rensa resurser

Mer information om GitHub-åtgärder och arbets flöden finns i:

> [!div class="nextstepaction"]
> [Anpassa GitHub-arbetsflöde](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
