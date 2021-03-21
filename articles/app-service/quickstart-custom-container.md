---
title: 'Snabb start: kör en anpassad behållare på App Service'
description: Kom igång med behållare på Azure App Service genom att distribuera din första anpassade behållare.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745085"
---
# <a name="run-a-custom-container-in-azure"></a>Köra en anpassad behållare i Azure

::: zone pivot="container-windows"
[Azure App Service](overview.md) har fördefinierade programstackar i Windows som ASP.NET eller Node.js, som körs i IIS. Den förkonfigurerade Windows container-miljön låser upp operativ systemet från administrativ åtkomst, program varu installationer, ändringar i den globala sammansättningscachen och så vidare. Mer information finns i [operativ systemets funktioner på Azure App Service](operating-system-functionality.md). Om programmet kräver mer åtkomst än den förkonfigurerade miljön tillåter kan du istället distribuera en anpassad Windows-container.

Den här snabb starten visar hur du distribuerar en ASP.NET-app i en Windows-avbildning till [Docker Hub](https://hub.docker.com/) från Visual Studio. Du kör appen i en anpassad behållare i Azure App Service.

> [!NOTE]
> Windows-behållare är begränsade till Azure Files och har för närvarande inte stöd för Azure-blob.


## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- <a href="https://hub.docker.com/" target="_blank">Registrera dig för ett Docker Hub-konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installera Docker för Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Växla Docker för att köra Windows-containrar</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installera Visual Studio 2019</a> med arbets belastningarna **ASP.net och webb utveckling** och **Azure Development** . Om du redan har installerat Visual Studio 2019:

    - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp**  >  **sökning efter uppdateringar**.
    - Lägg till arbets belastningarna i Visual Studio genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa en ASP.NET-webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**, leta upp och välj **ASP.net webb program (.NET Framework)** för C# och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du programmet _myfirstazurewebapp_ och väljer sedan **skapa**.

   ![Konfigurera ditt webbapp](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Du kan distribuera alla typer av ASP.NET-webbappar till Azure. I den här snabb starten väljer du **MVC** -mallen.

1. Välj **Docker-support** och se till att autentiseringen är inställd på **Ingen autentisering**. Välj **Skapa**.

   ![Skapa ASP.NET-webbprogram](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Om filen _Dockerfile_ inte öppnas automatiskt öppnar du den från **Solution Explorer**.

1. Du behöver en [överordnad avbildning som stöds](configure-custom-container.md#supported-parent-images). Ändra den överordnade avbildningen genom att ersätta raden `FROM` med följande kod och spara filen:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Från Visual Studio-menyn väljer du **Felsök**  >  **Start utan fel sökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicera till Docker Hub

1. I **Solution Explorer** högerklickar du på projektet **Myfirstazurewebapp** och väljer **publicera**.

1. Välj **App Service** och välj sedan **publicera**.

1. I Välj ett **publicerings mål** väljer du **container Registry** och **Docker Hub** och klickar sedan på **publicera**.

   ![Publicera från projektöversiktssidan](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Ange dina autentiseringsuppgifter för Docker Hub-kontot och välj **Spara**.

   Vänta tills distributionen har slutförts. Sidan **publicera** visar nu det databas namn som ska användas senare.

   ![Skärm bild som visar databas namnet.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Kopiera namnet på lagringsplatsen för senare bruk.

## <a name="create-a-windows-container-app"></a>Skapa en Windows-containerapp

1. Logga in på [Azure-portalen]( https://portal.azure.com).

1. Välj **Skapa en resurs** längst upp till vänster i Azure Portal.

1. Sök efter **Web App for containers** i sökrutan ovanför Azure Marketplace-resurser och välj **skapa**.

1. I **webbapp skapar** väljer du din prenumeration och en **resurs grupp**. Du kan skapa en ny resurs grupp om det behövs.

1. Ange ett namn på appen, till exempel *Win-container-demo* och välj **Windows** för **operativ system**. Välj **Nästa: Docker** för att fortsätta.

   ![Skapa en Web App for Containers](media/quickstart-custom-container/create-web-app-continer.png)

1. För **avbildnings källa** väljer du **Docker Hub** och för **bild och tagg** anger du det databas namn som du kopierade i [Publicera till Docker Hub](#publish-to-docker-hub).

   ![Konfigurera du är Web App for Containers](media/quickstart-custom-container/configure-web-app-continer.png)

    Om du har en anpassad avbildning någon annanstans för ditt webbprogram, till exempel i [Azure Container Registry](../container-registry/index.yml) eller på en annan privat lagringsplats, kan du konfigurera den här.

1. Välj **Granska och skapa** och **skapa** och vänta sedan på att Azure ska skapa de nödvändiga resurserna.

## <a name="browse-to-the-container-app"></a>Bläddra till containerappen

När Azure-åtgärden är klar visas ett meddelande.

![Distributionen lyckades](media/quickstart-custom-container/portal-create-finished.png)

1. Klicka på **Gå till resurs**.

1. I översikten över den här resursen följer du länken bredvid **URL**.

En ny webb sida öppnas på följande sida:

![Windows container app startar](media/quickstart-custom-container/app-starting.png)

Vänta några minuter och försök igen, tills du kommer till standardvälkomstsidan för ASP.NET:

![Windows container App körs](media/quickstart-custom-container/app-running-vs.png)

**Grattis!** Du kör din första anpassade Windows-container i Azure App Service.

## <a name="see-container-start-up-logs"></a>Se containerns startloggar

Det kan ta lite tid för Windows-containern att läsas in. Om du vill se förloppet navigerar du till följande URL genom att ersätta *\<app_name>* med namnet på din app.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

De strömmade loggarna ser ut så här:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och omdistribuera

1. Öppna  **vyer**  >  **Start**  >  **index. cshtml** i Solution Explorer i Visual Studio.

1. Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Om du vill distribuera om till Azure högerklickar du på projektet **myfirstazurewebapp** i **Solution Explorer** och väljer **publicera**.

1. På publiceringssidan väljer du **Publicera** och väntar tills publiceringen ska slutföras.

1. Om du vill uppmana App Service att hämta den nya avbildningen från Docker Hub startar du om appen. Tillbaka på sidan app i portalen klickar du på **starta om**  >  **Ja**.

   ![Starta om webbapp i Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Bläddra till container](#browse-to-the-container-app) igen. När du uppdaterar webbsidan ska appen återgå till sidan ”Startar” först och sedan visa den uppdaterade igen efter några minuter.

![Uppdaterad webbapp i Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Windows-container i Azure](tutorial-custom-container.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service på Linux ger fördefinierade program stackar i Linux med stöd för språk som .NET, PHP, Node.js och andra. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. Den här snabb starten visar hur du distribuerar en avbildning från en [Azure Container Registry](../container-registry/index.yml) (ACR) till App Service.

## <a name="prerequisites"></a>Förutsättningar

* Ett [Azure-konto](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure App Service-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Du kan använda det här tillägget för att skapa, hantera och distribuera Linux-Web Apps på Azure-plattformen as a Service (PaaS).
* [Docker-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Du kan använda det här tillägget för att förenkla hanteringen av lokala Docker-avbildningar och-kommandon och för att distribuera inbyggda app-avbildningar till Azure.

## <a name="create-an-image"></a>Skapa en avbildning

För att slutföra den här snabb starten behöver du en lämplig webb program avbildning som lagras i en [Azure Container Registry](../container-registry/index.yml). Följ anvisningarna i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](../container-registry/container-registry-get-started-portal.md), men Använd `mcr.microsoft.com/azuredocs/go` avbildningen i stället för `hello-world` avbildningen. [Exempel-Dockerfile finns i Azure samples lagrings platsen](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Se till att ange alternativet för **Administratörs användare** för att **Aktivera** när du skapar behållar registret. Du kan också ställa in den från avsnittet **åtkomst nycklar** på register sidan i Azure Portal. Den här inställningen krävs för att App Service åtkomst.

## <a name="sign-in"></a>Logga in

Starta sedan VS Code och logga in på ditt Azure-konto med hjälp av App Service tillägget. Det gör du genom att välja Azure-logotypen i aktivitets fältet, gå till **App Service** Explorer och sedan välja **Logga in på Azure** och följa instruktionerna.

![Logga in på Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Kontrollera krav

Nu kan du kontrol lera om alla nödvändiga komponenter har installerats och kon figurer ATS korrekt.

I VS Code bör du se din Azure-e-postadress i statusfältet och din prenumeration i **App Service** Explorer.

Kontrol lera sedan att du har Docker installerat och körs. Följande kommando visar Docker-versionen om den körs.

```bash
docker --version
```

Se slutligen till att Azure Container Registry är ansluten. Det gör du genom att välja Docker-logo typen i aktivitets fältet och sedan navigera till **register**.

![Skärm bild visar värdet för register värden med Azure Expanded och en fil med tillägget dot i o filename.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Distribuera avbildningen till Azure App Service

Nu när allt har kon figurer ATS kan du distribuera avbildningen till [Azure App Service](https://azure.microsoft.com/services/app-service/) direkt från Docker-utöknings Utforskaren.

Hitta avbildningen under noden **register** i **Docker** Explorer och expandera den för att visa dess taggar. Högerklicka på en tagg och välj sedan **distribuera avbildning till Azure App Service**.

Härifrån följer du anvisningarna för att välja en prenumeration, ett globalt unikt app-namn, en resurs grupp och en App Service-plan. Välj **B1 Basic** för pris nivån och en region.

Efter distributionen är din app tillgänglig på `http://<app name>.azurewebsites.net` .

En **resurs grupp** är en namngiven samling av alla dina programs resurser i Azure. En resurs grupp kan till exempel innehålla en referens till en webbplats, en databas och en Azure-funktion.

En **App Service plan** definierar de fysiska resurser som ska användas som värd för din webbplats. I den här snabb starten används en **grundläggande** värd plan i **Linux** -infrastrukturen, vilket innebär att platsen kommer att finnas på en Linux-dator bredvid andra webbplatser. Om du börjar med **Basic** -planen kan du använda Azure Portal för att skala upp så att din enda plats körs på en dator.

## <a name="browse-the-website"></a>Bläddra på webbplatsen

Panelen **utdata** öppnas under distributionen för att visa status för åtgärden. När åtgärden har slutförts söker du efter den app som du skapade i **App Service** Explorer, högerklickar på den och väljer sedan **Bläddra webbplats** för att öppna webbplatsen i webbläsaren.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera tilläggs paketet för [Azure tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .

Kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

::: zone-end
