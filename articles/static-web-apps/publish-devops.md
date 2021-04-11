---
title: 'Självstudie: publicera Azures statiska Web Apps med Azure-DevOps'
description: Lär dig hur du använder Azure DevOps för att publicera Azures statiska Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 472cf7b69078b3247c393ff65139bc29e5683a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639381"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Självstudie: publicera Azures statiska Web Apps med Azure-DevOps

Den här artikeln visar hur du distribuerar till [Azures statiska Web Apps](./overview.md) med [Azure-DevOps](https://dev.azure.com/).

I de här självstudierna får du lära dig att:

- Konfigurera en Azure-statisk Web Apps webbplats
- Skapa en Azure DevOps-pipeline för att skapa och publicera en statisk webbapp

## <a name="prerequisites"></a>Förutsättningar

- **Aktivt Azure-konto:** Om du inte har någon kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).
- **Azure DevOps-projekt:** Om du inte har någon kan du [skapa ett projekt kostnads fritt](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Azure DevOps-pipeline:** Om du behöver hjälp med att komma igång, se [skapa din första pipeline](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Skapa en statisk webbapp i en Azure DevOps-lagringsplats

  > [!NOTE]
  > Om du har en befintlig app i din lagrings plats kan du gå vidare till nästa avsnitt.

1. Navigera till din Azure DevOps-lagringsplats.

1. Välj **Importera** för att börja importera ett exempel program.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps lagrings platsen":::

1. I **klon-URL** anger du `https://github.com/staticwebdev/vanilla-api.git` .

1. Välj **Importera**.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. Navigera till [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs**.

1. Sök efter **statiska Web Apps**.

1. Välj **statisk Web Apps (förhands granskning)**.

1. Välj **Skapa**.

1. Under _distributions information_ kontrollerar du att du väljer **annat**. På så sätt kan du använda koden i din Azure DevOps-lagringsplats.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Distributions information – övrigt":::

1. När distributionen har slutförts går du till den nya statiska Web Apps resursen.

1. Välj **Hantera distributions-token**.

1. Kopiera **distributions-token** och klistra in den i en text redigerare för användning på en annan skärm.

    > [!NOTE]
    > Det här värdet är inställt för tillfället eftersom du kopierar och klistrar in fler värden i kommande steg.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Distributions-token":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Skapa pipeline-uppgiften i Azure DevOps

1. Navigera till den Azure DevOps-lagringsplats som skapades tidigare.

1. Välj **Konfigurera build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Bygg-pipeline":::

1. På skärmen *Konfigurera din pipeline* väljer du **starter pipeline**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfigurera pipeline":::

1. Kopiera och klistra in följande YAML i din pipeline.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Om du inte använder exempel appen, värdena för `app_location` , `api_location` och `output_location` måste ändras för att matcha värdena i ditt program.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Värdet är självhanterat och konfigureras manuellt.

1. Välj **variabler**.

1. Skapa en ny variabel.

1. Namnge variabeln **deployment_token** (matchar namnet i arbets flödet).

1. Kopiera den Licenstoken som du tidigare klistrade in i en text redigerare.

1. Klistra in i fältet för distributions-token i rutan _värde_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabel-token":::

1. Välj **Behåll detta värde hemlighet**.

1. Välj **OK**.

1. Välj **Spara** för att återgå till pipeline-yaml.

1. Välj **Spara och kör** för att öppna dialog rutan _Spara och köra_ .

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

1. Välj **Spara och kör** för att köra pipelinen.

1. När distributionen har slutförts går du till **Översikt över** Azures statiska Web Apps som innehåller länkar till distributions konfigurationen. Observera att _käll_ länken pekar på grenen och platsen för Azure DevOps-lagringsplatsen.

1. Välj **URL: en** för att se din nyligen distribuerade webbplats.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Distributions plats":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera statisk Azure-Web Apps](./configuration.md)
