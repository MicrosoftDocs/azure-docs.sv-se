---
title: Hantera App Service plan
description: Lär dig hur du utför olika uppgifter för att hantera en App Service plan, till exempel skapa, flytta, skala och ta bort.
keywords: App Service, Azure App Service, skala, App Service-plan, ändra, skapa, hantera, hantering
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 58ddf60a842a9583eae25a875f55227cee772a8a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582528"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Hantera ett App Service plan i Azure

En [Azure App Service-plan](overview-hosting-plans.md) tillhandahåller de resurser som en app service app måste köra. Den här guiden visar hur du hanterar en App Service plan.

## <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

> [!TIP]
> Om du har en App Service-miljön kan du läsa [skapa ett App Service plan i en app service-miljön](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Du kan skapa en tom App Service plan, eller så kan du skapa en plan som en del av att skapa en app.

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**.

   ![Skapa en resurs i Azure Portal.][createResource] 

1. Välj **ny**  >  **webbapp** eller någon annan typ av App Service-app.

   ![Skapa en app i Azure Portal.][createWebApp] 

2. Konfigurera avsnittet **instans information** innan du konfigurerar app service plan. Inställningar som **publicera** och **operativ system** kan ändra de tillgängliga pris nivåerna för din app service plan. **Region** fastställer var App Service plan skapas. 
   
3. I avsnittet **App Service plan** väljer du en befintlig plan eller skapar en plan genom att välja **Skapa nytt**.

   ![Skapa en App Service plan.][createASP] 

4. När du skapar en plan kan du välja pris nivån för den nya planen. I **SKU och storlek** väljer du **ändra storlek** för att ändra pris nivån. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Flytta en app till en annan App Service plan

Du kan flytta en app till en annan App Service plan, så länge käll planen och mål planen finns i _samma resurs grupp och geografiska region_.

> [!NOTE]
> Azure distribuerar varje ny App Service plan till en distributions enhet som internt kallas för ett webb utrymme. Varje region kan ha många webb utrymmen, men appen kan bara flyttas mellan planer som skapas i samma webb utrymme. Ett App Service-miljön är ett isolerat webb utrymme, så att appar kan flyttas mellan planer i samma App Service-miljön, men inte mellan planer i olika App Service miljöer.
>
> Du kan inte ange det webb utrymme du vill ha när du skapar en plan, men det är möjligt att se till att en plan skapas i samma webb utrymme som ett befintligt abonnemang. I korthet har alla planer som skapats med samma resurs grupp och regions kombination distribuerats till samma webb utrymme. Om du till exempel har skapat en plan i resurs grupp A och region B, så distribueras alla planer som du sedan skapar i resurs grupp A och region B till samma webb utrymme. Observera att det inte går att flytta webb utrymmen efter att de har skapats, så du kan inte flytta en plan till "samma webb utrymme" som en annan plan genom att flytta den till en annan resurs grupp.
> 

1. Sök efter och välj **app Services** i [Azure Portal](https://portal.azure.com)och välj den app som du vill flytta.

2. Välj **ändra App Service plan** på menyn till vänster.

3. I list rutan **App Service plan** väljer du en befintlig plan att flytta appen till. List rutan visar bara de planer som finns i samma resurs grupp och geografiska region som den aktuella App Service plan. Om det inte finns någon sådan plan kan du skapa en plan som standard. Du kan också skapa en ny plan manuellt genom att välja **Skapa ny**.

4. Om du skapar en plan kan du välja pris nivån för den nya planen. På **pris nivå** väljer du den befintliga nivån för att ändra den. 
   
   > [!IMPORTANT]
   > Om du flyttar en app från en högre nivå plan till en plan med lägre nivåer, till exempel från **D1** till **F1**, kan appen förlora vissa funktioner i mål planen. Om din app exempelvis använder TLS/SSL-certifikat kan du se det här fel meddelandet:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. När du är klar väljer du **OK**.
   
   ![App Service plan väljare.][change] 

## <a name="move-an-app-to-a-different-region"></a>Flytta en app till en annan region

Den region där appen körs är den region i App Service plan den finns i. Du kan dock inte ändra en App Service plan region. Om du vill köra din app i en annan region är det ett alternativ som är kloning av appar. Kloningen gör en kopia av din app i en ny eller befintlig App Service plan i vilken region som helst.

Du hittar **klonings app** i avsnittet **utvecklingsverktyg** i menyn.

> [!IMPORTANT]
> Kloning har vissa begränsningar. Du kan läsa om dem i [Azure App Service kloning av appar](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Skala en App Service plan

Om du vill skala upp en App Service plans pris nivå går du till [skala upp en app i Azure](manage-scale-up.md).

Information om hur du skalar ut antalet instanser för en app finns i [skala antalet instanser manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Ta bort ett App Service plan

För att undvika oväntade kostnader, tar App Service även bort planen som standard när du tar bort den senaste appen i en App Service plan. Om du väljer att behålla planen i stället bör du ändra planen till den **kostnads fria** nivån så att du inte debiteras.

> [!IMPORTANT]
> App Service planer som inte har några program kopplade till sig debiteras ändå, eftersom de fortsätter att reservera de konfigurerade VM-instanserna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skala upp en app i Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png