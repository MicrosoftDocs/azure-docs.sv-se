---
title: 'Snabb start: skapa ett Automation-konto – Azure-mall'
titleSuffix: Azure Automation
description: Den här snabb starten visar hur du skapar ett Automation-konto med hjälp av Azure Resource Manager-mallen.
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 4d7d4b5af9b86fe5116dc29f6ed1a789cdbf2047
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581100"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Snabb start: skapa ett Automation-konto med hjälp av ARM-mall

Azure Automation levererar en molnbaserad automatiserings-och konfigurations tjänst som stöder konsekvent hantering i dina Azure-och icke-Azure-miljöer. Den här snabb starten visar hur du distribuerar en Azure Resource Manager-mall (ARM-mall) som skapar ett Automation-konto. Att använda en ARM-mall tar färre steg jämfört med andra distributions metoder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Den här exempel mal len utför följande:

* Automatiserar skapandet av en Azure Monitor Log Analytics-arbetsyta.
* Automatiserar skapandet av ett Azure Automation-konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan.
* Lägger till exempel på Automation-runbooks i kontot.

>[!NOTE]
>Det går inte att skapa Automation-kör som-kontot när du använder en ARM-mall. Om du vill skapa ett Kör som-konto manuellt från portalen eller med PowerShell, se [skapa kör som-konto](create-run-as-account.md).

När du har slutfört de här stegen måste du [Konfigurera diagnostikinställningar](automation-manage-send-joblogs-log-analytics.md) för ditt Automation-konto så att Runbook-jobbets status och jobb strömmar skickas till den länkade Log Analytics-arbetsytan.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionen för de resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| [Arbetsyta](/azure/templates/microsoft.operationalinsights/workspaces) | arbetsytor | 2020-03-01 – för hands version |
| [Automation-konto](/azure/templates/microsoft.automation/automationaccounts) | automatisering | 2020-01-13 – för hands version |
| [Länkade arbets ytor tjänster](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | arbetsytor | 2020-03-01 – för hands version |

### <a name="before-you-use-the-template"></a>Innan du använder mallen

Mallen JSON-parametrar har kon figurer ATS så att du kan ange:

* Namnet på arbets ytan.
* Regionen som arbets ytan ska skapas i.
* Namnet på Automation-kontot.
* Regionen som du skapar Automation-kontot i.

Följande parametrar i mallen anges med ett standardvärde för Log Analytics arbets ytan:

* *SKU: n* är som standard den pris nivå per GB som lanserades i pris sättnings modellen april 2018.
* *dataRetention* är som standard 30 dagar.

>[!WARNING]
>Om du vill skapa eller konfigurera en Log Analytics arbets yta i en prenumeration som har valt att använda pris sättnings modellen från april 2018 är den enda giltiga Log Analytics pris nivån *PerGB2018*.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som sannolikt används som standard konfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns i [distribuera resurser med ARM-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Om du är nybörjare på Azure Automation och Azure Monitor är det viktigt att du förstår följande konfigurations information. De kan hjälpa dig att undvika fel när du försöker skapa, konfigurera och använda en Log Analytics arbets yta som är länkad till det nya Automation-kontot.

* Granska [Ytterligare information](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) för att helt förstå konfigurations alternativ för arbets ytor, till exempel åtkomst kontrol läge, pris nivå, kvarhållning och kapacitets reservations nivå.

* Granska [mappningar för arbets ytor](how-to/region-mappings.md) för att ange de regioner som stöds infogade eller i en parameter fil. Endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration.

* Om du inte har använt Azure Monitor loggar och inte har distribuerat en arbets yta redan, bör du gå igenom [rikt linjerna för design av arbets ytor](../azure-monitor/logs/design-logs-deployment.md). Det hjälper dig att lära dig mer om åtkomst kontroll och förstå de design implementerings strategier som vi rekommenderar för din organisation.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Automation konto, en Log Analytics arbets yta och länkar Automation-kontot till arbets ytan.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Ange värdena.

3. Det kan ta några minuter att slutföra distributionen. När det är klart ser utdata ut ungefär så här:

    ![Exempel på resultat när distributionen är klar](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna det Automation-konto som du nyss skapade i Azure Portal. 

3. I den vänstra rutan väljer du **Runbooks**. På sidan **Runbooks** är listade tre självstudier som skapats med Automation-kontot.

    ![Självstudie-Runbooks som skapats med Automation-konto](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Välj **länkad arbets yta** i det vänstra fönstret. På sidan **länkad arbets yta** visas Log Analytics arbets ytan du angav tidigare länkad till ditt Automation-konto.

    ![Automation-konto som är länkat till arbets ytan Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort länken till Automation-kontot från Log Analytics arbets ytan och tar sedan bort Automation-kontot och arbets ytan.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett Automation-konto, en Log Analytics-arbetsyta och länkat dem tillsammans.

Om du vill veta mer kan du fortsätta till självstudierna för Azure Automation.

> [!div class="nextstepaction"]
> [Azure Automation själv studie kurser](learn/automation-tutorial-runbook-graphical.md)