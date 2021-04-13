---
title: 'Självstudie: Publicera Azure Static Web Apps med Azure DevOps'
description: Lär dig hur du använder Azure DevOps för att publicera Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365764"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Självstudie: Publicera Azure Static Web Apps med Azure DevOps

Den här artikeln visar hur du distribuerar till [Azure Static Web Apps](./overview.md) med [Hjälp av Azure DevOps.](https://dev.azure.com/)

I de här självstudierna får du lära dig att:

- Konfigurera en Azure Static Web Apps webbplats
- Skapa en Azure DevOps-pipeline för att skapa och publicera en statisk webbapp

## <a name="prerequisites"></a>Förutsättningar

- **Aktivt Azure-konto:** Om du inte har ett konto kan du [skapa ett konto utan kostnad.](https://azure.microsoft.com/free/)
- **Azure DevOps-projekt:** Om du inte har något kan du [skapa ett projekt utan kostnad.](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- **Azure DevOps-pipeline:** Om du behöver hjälp med att komma igång kan du [gå till Skapa din första pipeline.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Skapa en statisk webbapp på en Azure DevOps-lagringsplats

  > [!NOTE]
  > Om du har en befintlig app på lagringsplatsen kan du gå vidare till nästa avsnitt.

1. Gå till din Azure DevOps-lagringsplats.

1. Välj **Importera** för att börja importera ett exempelprogram.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-lagringsplatsen":::

1. I **Klon-URL** anger du `https://github.com/staticwebdev/vanilla-api.git` .

1. Välj **Importera**.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. Gå till [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs**.

1. Sök efter **Static Web Apps**.

1. Välj **Static Web Apps (förhandsversion)**.

1. Välj **Skapa**.

1. Under _Distributionsinformation ser_ du till att du väljer **Annan.** På så sätt kan du använda koden på Azure DevOps-lagringsplatsen.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Distributionsinformation – övrigt":::

1. När distributionen är lyckad navigerar du till den nya Static Web Apps resursen.

1. Välj **Hantera distributionstoken**.

1. Kopiera **distributionstoken och** klistra in den i en textredigerare för användning på en annan skärm.

    > [!NOTE]
    > Det här värdet reserveras för tillfället eftersom du ska kopiera och klistra in fler värden i kommande steg.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Distributionstoken":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Skapa pipelineuppgiften i Azure DevOps

1. Gå till Azure DevOps-lagringsplatsen som skapades tidigare.

1. Välj **Konfigurera build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Bygg-pipeline":::

1. På skärmen *Konfigurera din pipeline* väljer du **Startpipeline.**

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfigurera pipeline":::

1. Kopiera och klistra in följande YAML i din pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Om du inte använder exempelappen måste värdena för `app_location` , och ändras så att de matchar värdena i ditt `api_location` `output_location` program.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Värdet `azure_static_web_apps_api_token` är själv hanterat och konfigureras manuellt.

1. Välj **Variabler**.

1. Skapa en ny variabel.

1. Namnge variabeln **deployment_token** (matchar namnet i arbetsflödet).

1. Kopiera den distributionstoken som du klistrade in i en textredigerare tidigare.

1. Klistra in distributionstoken i _rutan_ Värde.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabeltoken":::

1. Välj **Behåll det här värdet hemligt.**

1. Välj **OK**.

1. Välj **Spara** för att återgå till din pipeline-YAML.

1. Välj **Spara och kör** för att öppna dialogrutan Spara _och_ kör.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Välj **Spara och kör för** att köra pipelinen.

1. När distributionen är lyckad går du till Azure Static Web Apps **Översikt** som innehåller länkar till distributionskonfigurationen. Observera att _källlänken_ nu pekar på grenen och platsen för Azure DevOps-lagringsplatsen.

1. Välj **URL:en** för att se din nyligen distribuerade webbplats.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Distributionsplats":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera Azure Static Web Apps](./configuration.md)
