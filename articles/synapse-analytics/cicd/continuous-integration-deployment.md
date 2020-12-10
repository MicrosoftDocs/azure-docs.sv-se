---
title: Kontinuerlig integrering och leverans för Synapse-arbetsytan
description: Lär dig hur du använder kontinuerlig integrering och leverans för att distribuera ändringar i arbets ytan från en miljö (utveckling, testning, produktion) till en annan.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: d38c57a8c8504e1e03406f7cd8a0b61725cb0511
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008098"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Kontinuerlig integrering och leverans för Azure dataSynapses-arbetsyta

## <a name="overview"></a>Översikt

Kontinuerlig integrering (CI) är en process som automatiserar genereringen och testningen av kod varje gång en grupp medlem genomför ändringar i versions kontrollen. Kontinuerlig distribution (CD) är en process för att bygga, testa, konfigurera och distribuera från flera testnings-eller utvecklings miljöer till en produktions miljö.

För Azure Synapse-arbetsytan, kontinuerlig integrering och leverans (CI/CD) flyttar alla entiteter från en miljö (utveckling, test, produktion) till en annan. För att kunna befordra arbets ytan till en annan arbets yta finns det två delar: Använd [Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) för att skapa eller uppdatera arbets ytans resurser (pooler och arbets yta). Migrera artefakter (SQL-skript, Notebook, Spark jobb definition, pipeliner, data uppsättningar, data flöden osv) med Synapse CI/CD-verktyg i Azure DevOps. 

Den här artikeln beskriver hur du kan använda Azure release pipeline för att automatisera distributionen av en Synapse-arbetsyta till flera miljöer.

## <a name="prerequisites"></a>Förutsättningar

-   Arbets ytan som används för utveckling har kon figurer ATS med en git-lagringsplats i Studio, se [käll kontroll i Synapse Studio](source-control.md).
-   Ett Azure DevOps-projekt har förberetts för att köra versions pipelinen.

## <a name="set-up-a-release-pipelines"></a>Konfigurera en versions pipeline

1.  Öppna projektet som skapats för versionen i [Azure-DevOps](https://dev.azure.com/).

1.  Välj **pipelines** på vänster sida av sidan och välj sedan **versioner**.

    ![Välj pipeliner, versioner](media/create-release-1.png)

1.  Välj **ny pipeline** eller, om du har befintliga pipeliner, väljer du **ny** och sedan **ny versions pipeline**.

1.  Välj den **tomma jobb** mal len.

    ![Välj tomt jobb](media/create-release-select-empty.png)

1.  I rutan **scen namn** anger du namnet på din miljö.

1.  Välj **Lägg till artefakt** och välj sedan den git-lagringsplats som kon figurer ATS med din utvecklings Synapse Studio. Välj den git-lagringsplats som du använde för att hantera ARM-mall för pooler och arbets yta. Om du använder GitHub som källa måste du skapa en tjänst anslutning för ditt GitHub-konto och pull-databaser. Mer information om [tjänst anslutning](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints) 

    ![Lägg till publicerings gren](media/release-creation-github.png)

1.  Välj grenen för ARM-mallen för resurs uppdatering. Välj **senaste från standard gren** för **standard versionen**.

    ![Lägg till ARM-mall](media/release-creation-arm-branch.png)

1.  Välj [publicerings grenen](source-control.md#configure-publishing-settings) för lagrings platsen för **standard grenen**. Som standard är publicerings grenen `workspace_publish` . Välj **senaste från standard gren** för **standard versionen**.

    ![Lägg till en artefakt](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Konfigurera en fas aktivitet för ARM-resursen skapa och uppdatera 

Lägg till en Azure Resource Manager distributions uppgift för att skapa eller uppdatera resurser, inklusive arbets ytan och pooler:

1. I vyn fas väljer du **Visa fas aktiviteter**.

    ![Vyn fas](media/release-creation-stage-view.png)

1. Skapa en ny uppgift. Sök efter **distribution av arm-mall** och välj sedan **Lägg till**.

1. I distributions aktiviteten väljer du prenumeration, resurs grupp och plats för mål arbets ytan. Ange autentiseringsuppgifter om det behövs.

1. I listan **åtgärd** väljer du **skapa eller uppdatera resurs grupp**.

1. Välj knappen med tre punkter (**...**) bredvid rutan **mall** . Bläddra efter Azure Resource Manager mall för mål arbets ytan

1. Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen.

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parameter värden för mål arbets ytan. 

1. Välj **stegvis** för **distributions läget**.
    
    ![distribution av arbets ytor och pooler](media/pools-resource-deploy.png)

1. Valfritt Lägg till **Azure PowerShell** för roll tilldelningen tilldela och uppdatera arbets ytan. Om du använder versions pipeline för att skapa en Synapse-arbetsyta läggs pipelinens tjänst objekt till som standard arbets ytans administratör. Du kan köra PowerShell för att ge andra konton åtkomst till arbets ytan. 
    
    ![bevilja behörighet](media/release-creation-grant-permission.png)

 > [!WARNING]
> I fullständigt distributions läge **raderas** resurser som finns i resurs gruppen men inte har angetts i den nya Resource Manager-mallen. Mer information finns i [Azure Resource Manager distributions lägen](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurera en fas uppgift för distribution av artefakter 

Använd [distributions tillägget Synapse-arbetsyta](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) för att distribuera andra objekt i Synapse-arbetsytan, t. ex. data uppsättning, SQL-skript, Notebook, Spark jobb definition, data flöde, pipeline, länkad tjänst, AUTENTISERINGSUPPGIFTER och IR (integration Runtime).  

1. Sök och hämta tillägget från **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Hämta tillägg](media/get-extension-from-market.png)

1. Välj en organisation för att installera tillägget. 

     ![Installera tillägget](media/install-extension.png)

1. Kontrol lera att Azure DevOps pipeline: s tjänst princip har beviljats behörigheten för prenumerationen och även tilldelats som arbets ytans administratör för mål arbets ytan. 

1. Skapa en ny uppgift. Sök efter **distributionen av Synapse-arbetsytan** och välj sedan **Lägg till**.

     ![Lägg till tillägg](media/add-extension-task.png)

1.  I uppgiften väljer du **...** bredvid rutan **mall** för att välja mallfilen.

1. Välj **...** bredvid rutan **mallparametrar** för att välja parameter filen.

1. Välj anslutning, resurs grupp och namn på mål arbets ytan. 

1. Välj **...** bredvid rutan **Åsidosätt mallparametrar** och ange önskade parameter värden för mål arbets ytan. 

    ![Distribuera Synapse-arbetsyta](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> I CI/CD-scenarier måste integrerings körnings typen (IR) i olika miljöer vara densamma. Om du till exempel har en lokal IR-anslutning i utvecklings miljön, måste samma IR också vara av typen egen värd i andra miljöer, till exempel test och produktion. Om du däremot delar integrerings körningar över flera steg, måste du konfigurera integration runtime som länkad egen värd i alla miljöer, till exempel utveckling, testning och produktion.

## <a name="create-release-for-deployment"></a>Skapa version för distribution 

När du har sparat alla ändringar kan du välja **Skapa version** för att skapa en version manuellt. För att automatisera skapandet av versioner, se [Azure DevOps release triggers](https://docs.microsoft.com/azure/devops/pipelines/release/triggers)

   ![Välj Skapa version](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Metod tips för CI/CD

Om du använder git-integrering med din Synapse-arbetsyta och har en CI/CD-pipeline som flyttar dina ändringar från utveckling till test och sedan till produktion, rekommenderar vi följande metod tips:

-   **Git-integrering**. Konfigurera endast din utvecklings Synapse-arbetsyta med git-integrering. Ändringar av test-och produktions arbets ytor distribueras via CI/CD och kräver inte git-integrering.
-   **Förbered pooler innan artefakter migreras**. Om du har SQL-skript eller antecknings bok ansluten till pooler i arbets ytan utveckling förväntas samma namn på pooler i olika miljöer. 
-   **Infrastruktur som kod (IaC)**. Hantering av infrastruktur (nätverk, virtuella datorer, belastningsutjämnare och nätverkstopologi) i en beskrivande modell använder samma version som DevOps-teamet för käll koden. 
-   **Andra**. Se [metod tips för ADF-artefakter](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)


