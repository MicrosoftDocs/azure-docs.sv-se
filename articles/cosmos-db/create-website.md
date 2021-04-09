---
title: Distribuera en webbapp med en mall – Azure Cosmos DB
description: Lär dig hur du distribuerar ett Azure Cosmos-konto, Azure App Service Web Apps och ett exempel webb program med en Azure Resource Manager-mall.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333587"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Distribuera Azure Cosmos DB och Azure App Service med en webbapp från GitHub med en Azure Resource Manager mall
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här självstudien visar hur du gör en "ingen touch"-distribution av ett webb program som ansluter till Azure Cosmos DB vid första körningen utan att behöva klippa ut och klistra in anslutnings information från Azure Cosmos DB till `appsettings.json` eller till program inställningarna för Azure App Services i Azure Portal. Alla dessa åtgärder utförs med en Azure Resource Manager-mall i en enda åtgärd. I det här exemplet ska vi Distribuera Azure Cosmos DB att göra [exempel](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) från en [själv studie kurs om webb program](sql-api-dotnet-application.md).

Resource Manager-mallar är relativt flexibla och gör det möjligt att skapa komplexa distributioner över alla tjänster i Azure. Detta omfattar avancerade uppgifter som att distribuera program från GitHub och mata in anslutnings information i Azure App Service programmets program inställningar i Azure Portal. I den här självstudien får du lära dig hur du gör följande saker med en enda Resource Manager-mall.

* Distribuera ett Azure Cosmos-konto.
* Distribuera en Azure App Service värd plan.
* Distribuera en Azure App Service.
* Mata in slut punkten och nycklarna från Azure Cosmos-kontot i program inställningarna för App Service i Azure Portal.
* Distribuera ett webb program från en GitHub-lagringsplats till App Service.

Den resulterande distributionen har ett fullständigt fungerande webb program som kan ansluta till Azure Cosmos DB utan att behöva klippa ut och klistra in Azure Cosmos DBs slut punkts-URL eller autentiseringsinställningar från Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

> [!TIP]
> Även om den här självstudien inte antar tidigare erfarenhet med Azure Resource Manager mallar eller JSON, bör du ändra de refererade mallarna eller distributions alternativen och sedan krävs kunskap om var och en av dessa områden.

## <a name="step-1-deploy-the-template"></a>Steg 1: Distribuera mallen

Först väljer du knappen **distribuera till Azure** nedan för att öppna Azure Portal för att skapa en anpassad distribution. Du kan också Visa Azures resurs hanterings mall från [galleriet för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

När du är i Azure Portal väljer du den prenumeration som du vill distribuera till och väljer eller skapar en ny resurs grupp. Fyll sedan i följande värden.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Skärm bild av användar gränssnittet för mall distribution":::

* **Region** – detta krävs av Resource Manager. Ange samma region som används av plats parametern där dina resurser finns.
* **Program namn** – det här namnet används av alla resurser för den här distributionen. Se till att välja ett unikt namn för att undvika konflikter med befintliga Azure Cosmos DB-och App Service-konton.
* **Plats** – den region där dina resurser distribueras.
* **App Service plans nivå** – App Service-planens pris nivå.
* **App Service plans instanser** – antalet arbetare för App Service-planen.
* **Lagrings-URL** – lagrings platsen för webb programmet på GitHub.
* **Gren** – grenen för GitHub-lagringsplatsen.
* **Databas namn** – namnet på Azure Cosmos-databasen.
* **Container namn** – namnet på Azure Cosmos-containern.

När du har fyllt i värdena väljer du knappen **skapa** för att starta distributionen. Det här steget bör ta mellan 5 och 10 minuter att slutföra.

> [!TIP]
> Mallen verifierar inte att det Azure App Service namn och Azure Cosmos-kontonamn som anges i mallen är giltiga och tillgängliga. Vi rekommenderar starkt att du kontrollerar tillgängligheten för de namn du planerar att tillhandahålla innan du skickar distributionen.


## <a name="step-2-explore-the-resources"></a>Steg 2: utforska resurserna

### <a name="view-the-deployed-resources"></a>Visa de distribuerade resurserna

När mallen har distribuerat resurserna kan du nu se var och en av dem i din resurs grupp.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Resursgrupp":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Visa Cosmos DB slut punkt och nycklar

Öppna sedan Azure Cosmos-kontot i portalen. Följande skärm bild visar slut punkten och nycklarna för ett Azure Cosmos-konto.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos-nycklar":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Visa Azure Cosmos DB nycklar i program inställningar

Gå sedan till Azure App Service i resurs gruppen. Klicka på fliken konfiguration om du vill visa program inställningarna för App Service. Program inställningarna innehåller det Cosmos DB konto och primär nyckel värden som krävs för att ansluta till Cosmos DB samt de databas-och behållar namn som skickades in från mallen distribution.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Programinställningar":::

### <a name="view-web-app-in-deployment-center"></a>Visa webbapp i distributions Center

Gå sedan till distributions centret för App Service. Här visas databas platser för GitHub-lagringsplatsen som skickades till mallen. Statusen nedan indikerar att det är klart (aktiv), vilket innebär att programmet har distribuerats och startats.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Distributionscenter":::

### <a name="run-the-web-application"></a>Köra webbprogrammet

Klicka på **Bläddra** längst upp i distributions Center för att öppna webb programmet. Webb programmet öppnas på Start skärmen. Klicka på **Skapa ny** och ange data i fälten och klicka på Spara. Skärmen som visas visar de data som sparas i Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Start skärmen":::

## <a name="step-3-how-does-it-work"></a>Steg 3: Hur fungerar det?

Det krävs tre element för att det ska fungera.

### <a name="reading-app-settings-at-runtime"></a>Läser appinställningar vid körning

Först måste programmet begära Cosmos DB slut punkt och nyckel i `Startup` -klassen i ASP.NET MVC-webbappen. [Cosmos DB att göra-exemplet](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) kan köras lokalt där du kan ange anslutnings informationen till appsettings.jspå. När den distribueras distribueras dock den här filen med appen. Om dessa rader i rött inte kan komma åt inställningarna från appsettings.jspå, kommer den att försöka från program inställningarna i Azure App Service.

:::image type="content" source="./media/create-website/startup.png" alt-text="Skärm bild som visar en metod med flera String-variabler som är markerade i rött, inklusive databaseName, containerName, konto och nyckel.":::

### <a name="using-special-azure-resource-management-functions"></a>Använda särskilda funktioner för Azure-resurs hantering

För att dessa värden ska vara tillgängliga för programmet när de distribueras kan Azure Resource Manager-mallen fråga efter dessa värden från Cosmos DB-kontot med hjälp av särskilda funktioner för Azure-resurs hantering, inklusive [referens](../azure-resource-manager/templates/template-functions-resource.md#reference) -och [listnycklar](../azure-resource-manager/templates/template-functions-resource.md#listkeys) som hämtar värdena från Cosmos DB-kontot och infogar dem i program inställnings värden med nyckel namn som matchar det som används i programmet ovan i formatet {section: Key}. Till exempel `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Mallens nycklar":::

### <a name="deploying-web-apps-from-github"></a>Distribuera webb program från GitHub

Slutligen måste vi distribuera webb programmet från GitHub till App Service. Detta görs med hjälp av JSON nedan. Två saker att vara försiktiga med är typ och namn för den här resursen. Både- `"type": "sourcecontrols"` och- `"name": "web"` egenskapsvärdena är hårdkodade och ska inte ändras.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Distribuera från GitHub":::

## <a name="next-steps"></a>Nästa steg

Grattis! Du har distribuerat Azure Cosmos DB, Azure App Service och ett exempel webb program som automatiskt har den anslutnings information som krävs för att ansluta till Cosmos DB, allt i en enda åtgärd och utan att behöva klippa ut och klistra in känslig information. Med den här mallen som start punkt kan du ändra den för att distribuera dina egna webb program på samma sätt.

* För Azure Resource Manager mal len för det här exemplet går du till [galleriet för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* För käll koden för exempel appen går du till [Cosmos DB att göra-appen på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
