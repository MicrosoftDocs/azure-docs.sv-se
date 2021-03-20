---
title: Konfigurera PremiumV3-nivå
description: Lär dig hur du får bättre prestanda för din webb-, mobil-och API-app i Azure App Service genom att skala till den nya pris nivån för PremiumV3.
keywords: app service, azure app service, scale, scalable, app service plan, app service cost
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c187a1521762c0b69d42c81f464694729d3c02f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100594229"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Konfigurera PremiumV3-nivån för Azure App Service

Den nya pris nivån för **PremiumV3** ger dig snabbare processorer, SSD-lagring och fyrdubbla förhållandet mellan förhållandet mellan minne och kärna för de befintliga pris nivåerna (dubbla **PremiumV2** -nivån). Med prestanda fördelen kan du spara pengar genom att köra dina appar på färre instanser. I den här artikeln får du lära dig hur du skapar en app i **PremiumV3** -nivån eller skalar upp en app till **PremiumV3** -nivån.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skala upp en app till **PremiumV3** måste du ha en Azure App Service-app som körs på en pris nivå som är lägre än **PremiumV3** och appen måste köras i en app service-distribution som stöder PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 tillgänglighet

**PremiumV3** -nivån är tillgänglig för både inbyggda appar och behållar program, inklusive både Windows-behållare och Linux-behållare.

> [!NOTE]
> Alla Windows-behållare som körs på **Premium containernivå** under för hands versions perioden fortsätter att fungera som de är, men **Premium container** -nivån fortsätter att vara i för hands version. **PremiumV3** -nivån är den officiella ersättningen för **Premium container** nivån. 

**PremiumV3** är tillgänglig i vissa Azure-regioner och tillgänglighet i ytterligare regioner läggs till kontinuerligt. Om du vill se om den är tillgänglig i din region kör du följande Azure CLI-kommando i [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Skapa en app i PremiumV3-nivån

Pris nivån för en App Service-app definieras i [App Service plan](overview-hosting-plans.md) som den körs på. Du kan skapa en App Service plan separat eller som en del av att skapa appar.

När du konfigurerar App Service plan i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer du **pris nivå**. 

Välj **produktion**, välj sedan **P1V3**, **P2V3** eller **P3V3** och klicka sedan på **Verkställ**.

![Skärm bild som visar de rekommenderade pris nivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Om du inte ser alternativen **P1V3**, **P2V3** och **P3V3** som alternativ, eller om alternativen är nedtonade, är **PremiumV3** troligen inte tillgängligt i den underliggande app service distribution som innehåller App Service plan. Se [skala upp från en resurs grupp och en region kombination som inte stöds](#unsupported) för mer information.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Skala upp en befintlig app till PremiumV3-nivån

Innan du skalar en befintlig app till **PremiumV3** -nivån ser du till att **PremiumV3** är tillgängligt. Mer information finns i [PremiumV3-tillgänglighet](#availability). Om den inte är tillgänglig kan du läsa mer i [skala upp från en kombination av resurs grupp och region som inte stöds](#unsupported).

Beroende på din värd miljö kan det krävas ytterligare steg vid skalningen. 

Öppna din App Service app-sida i <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

I det vänstra navigerings fönstret på App Service app-sidan väljer du **skala upp (App Service plan)**.

![Skärm bild som visar hur du skalar upp din app service-plan.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Välj **produktion**, välj sedan **P1V3**, **P2V3** eller **P3V3** och klicka sedan på **Verkställ**.

![Skärm bild som visar de rekommenderade pris nivåerna för din app.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Om åtgärden slutförs, visar appens översikts sida att den nu finns på en **PremiumV3** -nivå.

![Skärm bild som visar pris nivån PremiumV3 på appens översikts sida.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Om du får ett fel meddelande

Vissa App Services planer kan inte skala upp till PremiumV3-nivån om den underliggande App Service distributionen inte stöder PremiumV3. Se [skala upp från en resurs grupp och en region kombination som inte stöds](#unsupported) för mer information.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Skala upp från en kombination av resurs grupp och region som inte stöds

Om din app körs i en App Service distribution där **PremiumV3** inte är tillgänglig, eller om din app körs i en region som för närvarande inte stöder **PremiumV3**, måste du distribuera om appen för att kunna dra nytta av **PremiumV3**.  Du kan välja mellan två alternativ:

- Skapa en app i en ny resurs grupp och med en ny App Service plan. När du skapar App Service plan väljer du en **PremiumV3** -nivå. Det här steget säkerställer att App Service plan distribueras till en distributions enhet som stöder **PremiumV3**. Distribuera sedan program koden till den nya appen. Även om du skalar App Service plan ned till en lägre nivå för att spara kostnader kan du alltid skala upp till **PremiumV3** eftersom distributions enheten stöder det.
- Om din app redan körs på en befintlig **Premium** -nivå kan du klona din app med alla appinställningar, anslutnings strängar och distributions konfiguration till en ny resurs grupp i en ny app service-plan som använder **PremiumV3**.

    ![Skärm bild som visar hur du klonar din app.](media/app-service-configure-premium-tier/clone-app.png)

    På sidan **klon app** kan du skapa en app service plan med **PremiumV3** i den region som du vill använda och ange de appinställningar och den konfiguration som du vill klona.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Flytta från Premium container till Premium v3 SKU

Om du har en app som använder SKU: n för för hands version och vill flytta till den nya Premium v3-SKU: n, måste du distribuera om appen för att kunna dra nytta av **PremiumV3**. För att göra detta, se det första alternativet i [skala upp från en resurs grupp och en region kombination som inte stöds](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera skapandet av appar i **PremiumV3** -nivån med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>Azure CLI

Följande kommando skapar en App Service plan i _P1V3_. Du kan köra den i Cloud Shell. Alternativen för `--sku` är P1V3, _P2V3_ och _P3V3_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando skapar en App Service plan i _P1V3_. Alternativen för `-WorkerSize` är _små_, _medel stora_ och _stora_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Fler resurser

[Skala upp en app i Azure](manage-scale-up.md) 
 [Skala antalet instanser manuellt eller automatiskt](../azure-monitor/autoscale/autoscale-get-started.md)
