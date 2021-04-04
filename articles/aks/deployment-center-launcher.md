---
title: Distributions Center för Azure-Kubernetes
description: Med distributions Center i Azure DevOps blir det enklare att konfigurera en robust Azure DevOps-pipeline för ditt program
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932225"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Distributions Center för Azure-Kubernetes

Med distributions Center i Azure DevOps blir det enklare att konfigurera en robust Azure DevOps-pipeline för ditt program. Som standard konfigurerar Deployment Center en Azure DevOps-pipeline för att distribuera program uppdateringar till Kubernetes-klustret. Du kan utöka den konfigurerade Azure DevOps-pipeline och även lägga till mer avancerade funktioner: möjlighet att få godkännande innan du distribuerar, etablera ytterligare Azure-resurser, köra skript, uppgradera ditt program och till och med köra fler verifierings test.

I de här självstudierna får du:

> [!div class="checklist"]
> * Konfigurera en Azure DevOps-pipeline för att distribuera program uppdateringar till Kubernetes-klustret.
> * Granska pipeline för kontinuerlig integrering (CI).
> * Kontrol lera pipelinen för kontinuerlig leverans (CD).
> * Rensa resurserna.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Du kan få en kostnadsfritt med [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Ett Azure Kubernetes service-kluster (AKS).

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

1. Logga in på din [Azure Portal](https://portal.azure.com/).

1. Välj alternativet [Cloud Shell](../cloud-shell/overview.md) på höger sida av meny raden i Azure Portal.

1. Kör följande kommandon för att skapa AKS-klustret:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Distribuera program uppdateringar till ett Kubernetes-kluster

1. Gå till resurs gruppen som du skapade i föregående avsnitt.

1. Välj AKS-klustret och välj sedan **Deployment Center (för hands version)** på det vänstra bladet. Välj **Kom igång**.

   ![Skärm bild som visar Azure Portal med en pil som pekar på distributions Center.](media/deployment-center-launcher/settings.png)

1. Välj plats för koden och välj **Nästa**. Välj sedan en av de databaser som stöds för närvarande: **[Azure databaser](/azure/devops/repos/index)** eller **GitHub**.

    Azure databaser är en uppsättning versions kontroll verktyg som hjälper dig att hantera din kod. Oavsett om ditt program varu projekt är stort eller litet, är det en bra idé att använda versions kontroll så tidigt som möjligt.

    - **Azure-databaser**: Välj en lagrings plats från ditt befintliga projekt och din organisation.

        ![Azure-lagringsplatser](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: auktorisera och välj lagrings plats för ditt GitHub-konto.

        ![Animering visar en process i GitHub för att välja GitHub som källa och sedan välja din lagrings plats.](media/deployment-center-launcher/github.gif)


1. Distributions Center analyserar lagrings platsen och identifierar din Dockerfile. Om du vill uppdatera Dockerfile kan du redigera det identifierade port numret.

    ![Programinställningar](media/deployment-center-launcher/application-settings.png)

    Om lagrings platsen inte innehåller Dockerfile, visar systemet ett meddelande för att genomföra ett.

    ![Skärm bild som visar distributions Center med ett meddelande det gick inte att hitta Dockerfile i lagrings platsen.](media/deployment-center-launcher/dockerfile.png)

1. Välj ett befintligt behållar register eller skapa ett, och välj sedan **Slutför**. Pipelinen skapas automatiskt och köar en version i Azure- [pipeliner](/azure/devops/pipelines/index).

    Azure-pipelines är en moln tjänst som du kan använda för att automatiskt bygga och testa ditt kod projekt och göra det tillgängligt för andra användare. Azure-pipeliner kombinerar kontinuerlig integrering och kontinuerlig leverans för att ständigt testa och bygga koden och skicka den till alla mål.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Välj länken för att se den pågående pipelinen.

1. Du ser de slutförda loggarna när distributionen är klar.

    ![Skärm bild som visar distributions Center med version 1 markerad med en grön bock markerings ikon.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Granska CI-pipelinen

Distributions Center konfigurerar automatiskt Azure DevOps-organisationens CI/CD-pipeline. Pipelinen kan utforskas och anpassas.

1. Gå till instrument panelen för distributions Center.  

1. Välj build-nummer i listan med lyckade loggar för att Visa build-pipeline för projektet.

1. Välj ellipsen (...) i det övre högra hörnet. En meny visar flera alternativ, till exempel för att köa en ny version, för att bygga och redigera build-pipeline. Välj **Redigera pipeline**. 

1. Du kan granska de olika uppgifterna för din build-pipeline i det här fönstret. Versionen utför olika uppgifter, till exempel insamling av källor från git-lagringsplatsen, skapa en avbildning, skicka en avbildning till behållar registret och publicera utdata som används för distributioner.

1. Välj namnet på den bygga pipelinen överst i pipelinen.

1. Ändra namnet på din build-pipeline till något mer beskrivande, Välj **spara & kö** och välj sedan **Spara**.

1. Under din build-pipeline väljer du **Historik**. I den här rutan visas en gransknings historik för dina senaste versions ändringar. Azure DevOps övervakar alla ändringar som görs i den bygga pipelinen och gör att du kan jämföra versioner.

1. Välj **Utlösare**. Du kan ta med eller undanta grenar från CI-processen.

1. Välj **Kvarhållning**. Du kan ange principer för att behålla eller ta bort ett antal versioner, beroende på ditt scenario.

## <a name="examine-the-cd-pipeline"></a>Granska CD-pipelinen

Distributions Center skapar och konfigurerar automatiskt relationen mellan din Azure DevOps-organisation och din Azure-prenumeration. De steg som ingår är att konfigurera en Azure-tjänst anslutning för att autentisera din Azure-prenumeration med Azure DevOps. Den automatiserade processen skapar också en versions pipeline som ger kontinuerlig leverans till Azure.

1. Välj **pipeliner** och välj sedan **versioner**.

1. Om du vill redigera versions pipelinen väljer du **Redigera**.

1. Välj **släpp** från listan **artefakter** . I föregående steg skapar den pipeline som du undersökte de utdata som används för artefakten. 

1. Välj den **kontinuerliga distributions** utlösaren till höger om **Drop** -alternativet. Den här versionen av pipelinen har en aktive rad CD-utlösare som kör en distribution när det finns en ny versions artefakt. Du kan också inaktivera utlösaren för att kräva manuell körning för dina distributioner.

1. Om du vill granska alla uppgifter för din pipeline väljer du **uppgifter**. Versionen ställer in till i-miljön, konfigurerar- `imagePullSecrets` parametern, installerar Helm-verktyg och distribuerar Helm-diagrammen till Kubernetes-klustret.

1. Om du vill visa versions historiken väljer du **Visa versioner**.

1. Välj **version** för att se sammanfattningen. Välj någon av stegen för att utforska flera menyer, till exempel en versions sammanfattning, associerade arbets objekt och tester. 

1. Välj **Incheckningar**. I den här vyn visas kod incheckningar relaterade till den här distributionen. Jämför versioner för att se inchecknings skillnaderna mellan distributioner.

1. Välj **loggar**. Loggarna innehåller användbar distributions information som du kan visa under och efter distributioner.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort de relaterade resurserna som du skapade när du inte längre behöver dem. Använd funktionen Ta bort på DevOps Projects instrument panelen.

## <a name="next-steps"></a>Nästa steg

Du kan ändra dessa bygg- och versionspipelines för att tillgodose ditt teams behov. Eller så kan du använda den här CI/CD-modellen som en mall för dina andra pipeliner.
