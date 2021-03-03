---
title: 'Snabb start: skapa en Node.js webbapp – Windows'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter för Windows-plattformen.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 302e0dc79d13eedebf810df042dc31f78b173fb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748857"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. distribuera till Azure App Service från Visual Studio Code

1. Öppna mappen program i Visual Studio Code.

    ```bash
    code .
    ```

1. I **Azure App Service** Explorer väljer **du logga in på Azure...** och följer instruktionerna. När du har loggat in ska Explorer Visa namnet på din Azure-prenumeration.

    ![Logga in på Azure](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Felsöka Azure-inloggning</summary>
    
    Om du ser felet **"det går inte att hitta en prenumeration med namnet [prenumerations-ID]"** när du loggar in på Azure kan det bero på att du är bakom en proxyserver och inte kan komma åt Azure-API: et. Konfigurera `HTTP_PROXY` och `HTTPS_PROXY` miljövariabler med din proxyinformation i terminalen med hjälp av `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. I **Azure App Service** Explorer väljer du ikonen blå UPPIL för att distribuera appen till Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Skärm bild av Azure App tjänsten i VS Code som visar den blå pilens ikon vald.":::

1. Välj den katalog som för närvarande är öppen `nodejs-docs-hello-world` .

1. Välj **Skapa ny webbapp... Avancerad**, för att distribuera till App Service i Windows.

1. Ange ett globalt unikt <abbr title="Giltiga tecken för ett app-namn är "a-z", "0-9" och "-".">name</abbr> för din webbapp och tryck på **RETUR**. 
1. Välj **skapa en ny resurs grupp** och ange sedan ett namn för resurs gruppen, till exempel `AppServiceQS-rg` .
1. Välj din **Node.js-version**, LTS rekommenderas.

    Meddelande kanalen visar de Azure-resurser som skapas för din app.
1. Välj **Windows** för operativ systemet.
1. Välj **Skapa ny app service plan** och ange sedan ett namn för planen (till exempel `AppServiceQS-plan` ) och välj **F1 kostnads fritt** för pris nivån.
1. Välj **hoppa över för tillfället** när du tillfrågas om Application Insights.
1. Välj en region nära dig eller nära resurser som du vill ha åtkomst till.

1. Välj **Ja** när du uppmanas att uppdatera arbets ytan så att senare distributioner automatiskt riktar sig till samma app service-webbapp. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Skärm bild av prompten för att uppdatera din arbets yta med knappen Ja markerad.":::

1. Högerklicka på noden för App Service en gång till och välj **distribuera till webbapp**.

1. Högerklicka på noden för App Service en gång till och välj **Bläddra webbplats**.

    [Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. När distributionen är klar väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp.

<br>
<details>
<summary>Felsökning</summary>
* Se till att ditt program lyssnar på porten som tillhandahålls av PORT miljö variabeln: `process.env.PORT` .
* Om du ser felet **"du har inte behörighet att visa den här katalogen eller sidan."**, kunde programmet antagligen inte starta korrekt. Granska logg resultatet för att hitta och åtgärda felet. 

</details>

<br>

[Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>