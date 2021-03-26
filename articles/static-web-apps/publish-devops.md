---
title: 'Självstudie: publicera Azures statiska Web Apps med Azure-DevOps'
description: Lär dig hur du använder Azure DevOps för att publicera Azures statiska Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543574"
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

1. Använd en befintlig databas eller _Importera en lagrings plats_ på det sätt som visas nedan.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps lagrings platsen":::

1. Skapa en ny fil för din frontend-webbapp.

1. Kopiera och klistra in följande HTML-kod i den nya filen:

    ```html
    <!DOCTYPE html>
    <html lang="en">
  
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="styles.css">
      <title>Hello World!</title>
    </head>
  
    <body>
      <main>
        <h1>Hello World!</h1>
      </main>
    </body>
  
    </html>
    ```

1. Spara filen.

## <a name="create-a-static-web-app"></a>Skapa en statisk webbapp

1. Navigera till [Azure Portal](https://portal.azure.com).

1. Välj **Skapa en resurs**.

1. Sök efter **statiska Web Apps**.

1. Välj **statisk Web Apps (förhands granskning)**.

1. Välj **Skapa**.

1. Under _distributions information_ kontrollerar du att du väljer **annat**. På så sätt kan du använda koden i din Azure DevOps-lagringsplats.

    > [!NOTE]
    > Funktionen för att välja _andra_ är för närvarande att lanseras och är kanske inte tillgänglig ännu i alla Azure-prenumerationer.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Distributions information – övrigt":::

1. När distributionen är klar väljer du **Hantera distributions-token**.

1. Kopiera **distributions-token** och klistra in den i en text redigerare för användning på en annan skärm.

    > [!NOTE]
    > Det här värdet är inställt för tillfället eftersom du kopierar och klistrar in fler värden i kommande steg.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Distributions-token":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Skapa pipeline-uppgiften i Azure DevOps

1. Navigera till det Azure DevOps-projekt som skapades tidigare.

2. Skapa en ny **versions pipeline** och välj **Konfigurera build**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Bygg-pipeline":::

3. Kopiera och klistra in följande YAML i din pipeline.

    > [!NOTE]
    > Värdena som anges för _app_location_,_api_location_ och _output_location_ måste ändras för din app.  

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: frontend 
          api_location: api
          output_location: build
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    Konfigurera Azures statiska webb program indata enligt mappstrukturen för ditt program.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Värdet är självhanterat och konfigureras manuellt.

4. Välj **variabler**.

5. Skapa en ny variabel.

6. Namnge variabeln **deployment_token** (matchar namnet i arbets flödet).

7. Kopiera den Licenstoken som du tidigare klistrade in i en text redigerare.

8. Klistra in i fältet för distributions-token i rutan _värde_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variabel-token":::

9. Välj **OK**.

10. Välj **Spara och kör** pipelinen.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

11. När distributionen har slutförts går du till **Översikt över** Azures statiska Web Apps som innehåller länkar till distributions konfigurationen.

12. Välj **URL: en** för att se din nyligen distribuerade webbplats. Observera att _käll_ länken pekar på grenen och platsen för Azure DevOps-lagringsplatsen.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Distributions plats":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera statisk Azure-Web Apps](./configuration.md)
