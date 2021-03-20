---
title: Snabb start – distribuera en webbapp till Azure Service Fabric nät
description: Den här snabbstarten beskriver hur du skapar en ASP.NET Core-webbplats och publicerar den till Azure Service Fabric Mesh med hjälp av Visual Studio.
author: georgewallace
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 665988f37d0afdb91bb074d8653cc3c24155966e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627019"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Snabbstart: Skapa och distribuera en webbapp till Azure Service Fabric Mesh

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk.

I den här snabbstarten ska du skapa ett nytt Service Fabric Mesh-program som består av en ASP.NET Core-webbapp, köra den i det lokala utvecklingsklustret och sedan publicera och köra den i Azure.

Du behöver en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du enkelt [skapa en kostnadsfri Azure-prenumeration](https://azure.microsoft.com/free/) innan du börjar. Du måste också [konfigurera utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Skapa ett Service Fabric Mesh-projekt

Öppna Visual Studio och välj **fil**  >  **nytt**  >  **projekt...**

I dialogrutan **Nytt projekt** dialogrutan, i rutan **Sök** längst upp skriver du `mesh`. Välj mallen **Service Fabric Mesh Application**. (Om du inte ser mallen kontrollerar du att du har installerat Mesh-SDK och förhandsversionen av VS-verktygen enligt anvisningarna om att [skapa en utvecklingsmiljö](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

I rutan **Namn** skriver du **ServiceFabricMesh1** och i rutan **Plats** anger du mappsökvägen där filerna för projektet ska lagras.

Se till att alternativet för att **skapa katalog för lösning** är markerat och klicka på **OK** för att skapa Service Fabric Mesh-projektet.

![Skärm bild som visar hur du skapar ett Service Fabric nät-projekt.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Skapa en tjänst

När du klickar på **OK** öppnas dialogrutan **Ny Service Fabric-tjänst**. Välj projekttypen **ASP.NET Core**, kontrollera att **Container OS** (Operativsystem för container) är inställt på **Windows** och klicka på **OK** för att skapa ASP.NET Core-projektet. 

![Visual Studio, dialogruta för nytt Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

Dialogrutan **Nytt ASP.NET Core-webbprogram** öppnas. Välj **Webbprogram** och klicka sedan på **OK**.

![Visual Studio, nytt ASP.NET-webbprogram](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Visual Studio skapar Service Fabric Mesh-programprojektet och ASP.NET Core-projektet.

## <a name="build-and-publish-to-your-local-cluster"></a>Skapa och publicera till ditt lokala kluster

En Docker-avbildning skapas och publiceras automatiskt till det lokala klustret så fort projektet läses in. Den här processen kan ta lite tid. Om du vill kan du övervaka Service Fabric-verktygens förlopp i **utdatafönstret** genom att välja objektet **Service Fabric-verktyg** i listrutan för fönstret **Utdata**. Du kan fortsätta att arbeta medan Docker-avbildningen distribueras.

När projektet har skapats klickar du på **F5** för att felsöka tjänsten lokalt. När den lokala distributionen är klar och Visual Studio kör ditt projekt öppnas ett webbläsarfönster med en exempelwebbsida.

När du är klar med granskningen av den nya distribuerade tjänsten stoppar du felsökningen av projektet genom att trycka på **Skift + F5** i Visual Studio.

## <a name="publish-to-azure"></a>Publicera till Azure

För att publicera Service Fabric Mesh-projektet till Azure högerklickar du på **Service Fabric Mesh-projektet** i Visual studio och väljer **Publicera**.

![Högerklicka på Service Fabric Mesh-projektet i Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Dialogrutan **Publicera Service Fabric-program** visas.

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Välj en **plats**. Den här artikeln använder **USA, östra**.

Under **resurs grupp** väljer du **\<Create New Resource Group...>** . Dialogrutan **Skapa resursgrupp** visas. Ange **Resursgruppsnamn** och **Plats**.  Den här snabbstarten använder platsen **USA, östra** och namnger gruppen **sfmeshTutorial1RG** (Välj ett unikt resursgruppnamn om organisationen har flera personer med samma prenumeration).  Klicka på **Skapa** för att skapa resursgruppen och återgå till publiceringsdialogrutan.

![Skärm bild som visar hur du skapar en ny resurs grupp.](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Gå tillbaka till dialog rutan **publicera Service Fabric program** under **Azure Container Registry** och välj **\<Create New Container Registry...>** . I dialogrutan **Create Container Registry** (Skapa containerregister) använder du ett unikt namn för **Container registry name** (Containerregisternamn). Ange en **plats** (i den här snabbstarten används **USA, östra**). Välj den **resursgrupp** som du skapade i föregående steg i listrutan, exempelvis **sfmeshTutorial1RG**. Välj **Basic** för **SKU** och klicka sedan på **Skapa** för att återgå till publiceringsdialogrutan.

![Visual Studio, dialogruta för ny Service Fabric Mesh-resursgrupp](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

I publiceringsdialogrutan klickar du på knappen **Publicera** för att distribuera Service Fabric Mesh-programmet till Azure.

När du publicerar till Azure första gången skickas dockeravbildningen till ACR (Azure Container Registry), vilket tar tid beroende på storleken på avbildningen. Efterföljande publiceringar av samma projekt går snabbare. Du kan övervaka distributionsförloppet genom att välja fönstret **Service Fabric-verktyg** i fönstret **Utdata** i Visual Studio. När distributionen är klar visas utdata för **Service Fabric Tools** (Service Fabric-verktyg) IP-adress och port för programmet i form av en webbadress.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen när den körs i Azure:

![Köra ett Service Fabric Mesh-webbprogram](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort alla resurser som du skapade för den här snabbstarten. Eftersom du har skapat en ny resursgrupp som fungerar som värd för både ACR och Service Fabric Mesh går det bra att ta bort den här resursgruppen, vilket är ett enkelt sätt att ta bort alla resurser som är associerade med den.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Du kan också ta bort resursgruppen [från Azure-portalen](https://portal.azure.com).

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att skapa och distribuera Service Fabric Mesh-program fortsätter du till självstudien.
> [!div class="nextstepaction"]
> [Skapa, felsöka och distribuera en webbapp för flera tjänster till Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
