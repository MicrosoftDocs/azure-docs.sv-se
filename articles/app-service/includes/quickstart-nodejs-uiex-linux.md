---
title: 'Snabb start: skapa en Node.js Web App Linux'
description: Distribuera din första Node.js Hello World till Azure App Service på några minuter.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102109045"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. distribuera till Azure App Service från Visual Studio Code

1. Öppna mappen program i Visual Studio Code.

    ```bash
    code .
    ```

1. I **Azure App Service** Explorer väljer **du logga in på Azure...** och följer instruktionerna. När du har loggat in ska Explorer Visa namnet på din Azure-prenumeration.

    ![Logga in på Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
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

1. Välj **Skapa ny webbapp**, som distribueras till App Service på Linux som standard.

1. Ange ett globalt unikt <abbr title="Giltiga tecken för ett app-namn är "a-z", "0-9" och "-".">name</abbr> för din webbapp och tryck på **RETUR**. 

1. Välj din **Node.js-version**, LTS rekommenderas.

    Meddelande kanalen visar de Azure-resurser som skapas för din app.

1. Välj **Ja** när du uppmanas att uppdatera konfigurationen så att den körs `npm install` på mål servern. Din app distribueras sedan.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Skärm bild av prompten för att uppdatera konfigurationen på mål servern med knappen Ja markerad.":::

1. Välj **Ja** när du uppmanas att uppdatera arbets ytan så att senare distributioner automatiskt riktar sig till samma app service-webbapp. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Skärm bild av prompten för att uppdatera din arbets yta med knappen Ja markerad.":::




1. När distributionen är klar väljer du **Bläddra webbplats** i prompten för att visa din nyligen distribuerade webbapp.

<br/>
<details>
<summary><strong>Felsökning</strong></summary>

Kontrol lera följande om du inte kunde utföra de här stegen:

* Se till att ditt program lyssnar på porten som tillhandahålls av PORT miljö variabeln: `process.env.PORT` .

* Om du ser felet **"du har inte behörighet att visa den här katalogen eller sidan."**, kunde programmet antagligen inte starta korrekt. Granska logg resultatet för att hitta och åtgärda felet. 

</details>

<br>

[Rapportera ett problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>


