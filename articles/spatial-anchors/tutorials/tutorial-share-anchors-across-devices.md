---
title: 'Självstudie: dela ankare mellan sessioner och enheter'
description: I den här självstudien får du lära dig hur du delar Azure spatial-ankare mellan Android/iOS-enheter i Unity med en backend-tjänst.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ed9b18144bb727af751fbcbbdd5cc6631afc40f
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "105727237"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Självstudie: dela spatiala ankare mellan sessioner och enheter

Molnbaserade ankare är en plattforms oberoende utvecklare som du kan använda för att skapa blandade verklighets upplevelser genom att använda objekt som bevarar sin plats mellan enheter över tid. 

I den här självstudien använder du [Azure spatiala ankare](../overview.md) för att skapa ankare under en session och sedan hitta dem på samma enhet eller en annan. Samma ankare kan också placeras av flera enheter på samma plats och samtidigt.

![Animering som visar spatiala ankare som skapas med en mobil enhet och som används med en annan enhet under loppet av dagar.](./media/persistence.gif)


I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core webbapp i Azure som du kan använda för att dela ankare och lagra ankare i minnet under en angiven tids period.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo-scenen i Unity-exemplet från våra snabb starter för att dra nytta av webbappen för delnings ankare.
> * Distribuera och kör ankarena till en eller flera enheter.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> Du använder Unity och en ASP.NET Core webbapp i den här självstudien, men metoden här är endast till för att ge ett exempel på hur du kan dela Azure spatiala ankare på andra enheter. Du kan använda andra språk och backend-tekniker för att uppnå samma mål.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Hämta exempel projektet

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Distribuera tjänsten delnings ankare

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Öppna Visual Studio och öppna sedan projektet i mappen *Sharing\SharingServiceSample* .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Du måste skapa en resurs grupp och en App Service plan innan du distribuerar tjänsten i Visual Studio Code.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Gå till <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>och logga sedan in på din Azure-prenumeration.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Välj **Ny** bredvid **Resursgrupp**.

Namnge resurs gruppen **myResourceGroup** och välj sedan **OK**.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Bredvid **Värdplan** väljer du **Ny**.

Använd de här inställningarna i fönstret **Konfigurera värd plan** :

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|App Service-plan| MySharingServicePlan | Namnet på App Service plan |
| Location | USA, västra | Data centret där webbappen finns |
| Storlek | Kostnadsfri | Den [pris nivå](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) som avgör värd funktioner |

Välj **OK**.

Öppna Visual Studio Code och öppna sedan projektet i *Sharing\SharingServiceSample* -mappen. 

Om du vill distribuera delnings tjänsten via Visual Studio Code följer du anvisningarna i <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2&preserve-view=true#open-it-with-visual-studio-code" target="_blank">publicera ett ASP.net Core-program till Azure med Visual Studio Code</a>. Börja med avsnittet "öppna med Visual Studio Code". Skapa inte ett annat ASP.NET-projekt enligt beskrivningen i föregående steg eftersom du redan har ett projekt som ska distribueras och publiceras: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Distribuera exempel appen

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat en ASP.NET Core webbapp i Azure och du har konfigurerat och distribuerat en Unity-app. Du har skapat spatialdata med appen och du har delat dem med andra enheter genom att använda din ASP.NET Core-webbapp.

Du kan förbättra din ASP.NET Core webbapp så att den använder Azure Cosmos DB för att spara lagringen av dina delade avstånds ankare. Genom att lägga till Azure Cosmos DB support kan du låta din ASP.NET Core webbapp Skapa en fäst punkt idag. Sedan kan du med hjälp av den fäst punkts identifierare som lagras i din webbapp låta appen returnera dagar senare för att hitta ankaret igen.

> [!div class="nextstepaction"]
> [Använd Azure Cosmos DB för att lagra ankare](./tutorial-use-cosmos-db-to-store-anchors.md)