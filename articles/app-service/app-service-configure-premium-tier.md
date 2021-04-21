---
title: Konfigurera PremiumV3-nivå
description: Lär dig bättre prestanda för din webb-, mobil- och API-app i Azure App Service genom att skala till den nya prisnivån PremiumV3.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fbd841626e2a074bc0a35cd1b4ac094e267b34a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833354"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurera PremiumV3-nivån för Azure App Service

Den nya **prisnivån PremiumV3** ger snabbare processorer, SSD-lagring och fyrdubbelt förhållande mellan minne och kärna för de befintliga prisnivåerna (dubbla **PremiumV2-nivån).** Med prestandafördelen kan du spara pengar genom att köra dina appar på färre instanser. I den här artikeln får du lära dig hur du skapar en app på **PremiumV3-nivå** eller skalar upp en app till **PremiumV3-nivån.**

## <a name="prerequisites"></a>Förutsättningar

Om du vill skala upp en app till **PremiumV3** måste du ha en Azure App Service-app som körs på en prisnivå som är lägre än **PremiumV3,** och appen måste köras i en App Service-distribution som stöder PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3-tillgänglighet

**PremiumV3-nivån** är tillgänglig för både interna appar och containerappar, inklusive både Windows-containrar och Linux-containrar.

> [!NOTE]
> Alla Windows-containrar som körs på **Premium-containernivån** under förhandsversionsperioden fortsätter att fungera som de är, men **Premium-containernivån** fortsätter att vara kvar som förhandsversion. **PremiumV3-nivån** är den officiella ersättningen för **Premium-containernivån.** 

**PremiumV3** är tillgängligt i vissa Azure-regioner och tillgängligheten i ytterligare regioner läggs till kontinuerligt. Om du vill se om det är tillgängligt i din region kör du följande Azure CLI-kommando i [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Skapa en app på PremiumV3-nivå

Prisnivån för en App Service definieras i den [App Service plan](overview-hosting-plans.md) den körs på. Du kan skapa en App Service plan sig själv eller som en del av appskapandet.

När du konfigurerar App Service plan i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer **du Prisnivå.** 

Välj **Produktion** och välj sedan **P1V3,** **P2V3** eller **P3V3** och klicka sedan på **Använd.**

![Skärmbild som visar de rekommenderade prisnivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Om du inte ser **P1V3,** **P2V3** och **P3V3** som alternativ, eller om alternativen är nedtonade, är **PremiumV3** sannolikt inte tillgängligt i den underliggande App Service-distributionen som innehåller App Service plan. Mer [information finns i Skala upp från en resursgrupp och regionkombination](#unsupported) som inte stöds.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Skala upp en befintlig app till PremiumV3-nivå

Kontrollera att PremiumV3 är tillgängligt **innan du skalar** en befintlig app till **PremiumV3-nivå.** Mer information finns i [PremiumV3 availability](#availability). Om den inte är tillgänglig kan du se [Skala upp från en kombination av resursgrupp och region som inte stöds.](#unsupported)

Beroende på din värdmiljö kan uppskalning kräva extra steg. 

I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>öppnar du App Service appsidan.

I det vänstra navigeringsfönstret på App Service app väljer du **Skala upp (App Service plan).**

![Skärmbild som visar hur du skalar upp din App Service-plan.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj **Produktion** och välj **sedan P1V3,** **P2V3** eller **P3V3** och klicka sedan på **Tillämpa.**

![Skärmbild som visar de rekommenderade prisnivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden slutförs korrekt visar appens översiktssida att den nu är på en **PremiumV3-nivå.**

![Skärmbild som visar prisnivån PremiumV3 på appens översiktssida.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett fel

Vissa App Service-planer kan inte skalas upp till PremiumV3-nivån om den underliggande App Service-distributionen inte stöder PremiumV3. Mer [information finns i Skala upp från en resursgrupp och regionkombination](#unsupported) som inte stöds.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skala upp från en kombination av resursgrupp och region som inte stöds

Om din app körs i en App Service-distribution där **PremiumV3** inte är tillgängligt, eller om din app körs i en region som för närvarande inte stöder **PremiumV3,** måste du distribuera om appen för att dra nytta av **PremiumV3.**  Du kan välja mellan två alternativ:

- Skapa en app i en ny resursgrupp och med en ny App Service plan. När du skapar App Service plan väljer du en **PremiumV3-nivå.** Det här steget säkerställer att App Service plan distribueras till en distributionsenhet som stöder **PremiumV3.** Distribuera sedan om programkoden till den nyligen skapade appen. Även om du skalar App Service plan ned till en lägre nivå för att spara kostnader kan du alltid skala tillbaka upp till **PremiumV3** eftersom distributionsenheten stöder det.
- Om din app redan körs på en befintlig **Premium-nivå** kan du klona appen med alla appinställningar, anslutningssträngar och distributionskonfiguration till en ny resursgrupp i en ny App Service-plan som använder **PremiumV3.**

    ![Skärmbild som visar hur du klonar din app.](media/app-service-configure-premium-tier/clone-app.png)

    På sidan **Klona app** kan du skapa en App Service plan **med PremiumV3** i den region du vill ha och ange de appinställningar och den konfiguration som du vill klona.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Flytta från Premium-container till Premium V3 SKU

Om du har en app som använder förhandsversionen premiumcontainer-SKU och vill flytta till den nya Premium V3-SKU:n, måste du distribuera om appen för att dra nytta av **PremiumV3.** Om du vill göra detta kan du se det första alternativet i Skala upp från en kombination [av resursgrupp och region som inte stöds](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera appskapandet på **PremiumV3-nivån** med skript med hjälp av [Azure CLI eller](/cli/azure/install-azure-cli) [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en App Service plan i _P1V3_. Du kan köra den i Cloud Shell. Alternativen för `--sku` är P1V3, _P2V3_ och _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando skapar en App Service plan i _P1V3_. Alternativen för är `-WorkerSize` _Liten,_ _Medel_ och _Stor._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Fler resurser

[Skala upp en app i Azure](manage-scale-up.md) 
 [Skala instansantal manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md)
