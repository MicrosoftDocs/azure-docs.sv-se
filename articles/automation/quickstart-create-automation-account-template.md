---
title: 'Snabbstart: Skapa ett Automation-konto – Azure-mall'
titleSuffix: Azure Automation
description: Den här snabbstarten visar hur du skapar ett Automation-konto med hjälp Azure Resource Manager mallen.
services: automation
ms.author: magoedte
ms.date: 01/07/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 50a8c057a6fdf6350a18225cd2dc538942d6686d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538410"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Snabbstart: Skapa ett Automation-konto med hjälp av ARM-mall

Azure Automation en molnbaserad automatiserings- och konfigurationstjänst som stöder konsekvent hantering i dina Azure- och icke-Azure-miljöer. Den här snabbstarten visar hur du distribuerar en Azure Resource Manager (ARM-mall) som skapar ett Automation-konto. Att använda en ARM-mall tar färre steg jämfört med andra distributionsmetoder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Den här exempelmallen utför följande:

* Automatiserar skapandet av en Azure Monitor Log Analytics-arbetsyta.
* Automatiserar skapandet av ett Azure Automation konto.
* Länkar Automation-kontot till Log Analytics-arbetsytan.
* Lägger till Exempel på Automation-runbooks till kontot.

>[!NOTE]
>Det går inte att skapa Kör som-kontot för Automation när du använder en ARM-mall. Information om hur du skapar ett Kör som-konto manuellt från portalen eller med PowerShell finns i [Skapa Kör som-konto.](create-run-as-account.md)

När du har slutfört de [](automation-manage-send-joblogs-log-analytics.md) här stegen måste du konfigurera diagnostikinställningar för ditt Automation-konto för att skicka status för Runbook-jobb och jobbströmmar till den länkade Log Analytics-arbetsytan.

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-versioner

I följande tabell visas API-versionen för de resurser som används i det här exemplet.

| Resurs | Resurstyp | API-version |
|:---|:---|:---|
| [Arbetsyta](/azure/templates/microsoft.operationalinsights/workspaces) | arbetsytor | 2020-03-01-preview |
| [Automation-konto](/azure/templates/microsoft.automation/automationaccounts) | automatisering | 2020-01-13-preview |
| [Länkade tjänster för arbetsyta](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | arbetsytor | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Innan du använder mallen

JSON-parametermallen är konfigurerad så att du kan ange:

* Namnet på arbetsytan.
* Den region där arbetsytan ska skapas.
* Namnet på Automation-kontot.
* Regionen där Automation-kontot ska skapas.

Följande parametrar i mallen har angetts med ett standardvärde för Log Analytics-arbetsytan:

* *SKU* är som standard prisnivån per GB som släpptes i prismodellen från april 2018.
* *dataRetention* är som standard 30 dagar.

>[!WARNING]
>Om du vill skapa eller konfigurera en Log Analytics-arbetsyta i en prenumeration som har valt prismodellen för april 2018 är den enda giltiga Log Analytics-prisnivån *PerGB2018*.
>

JSON-mallen anger ett standardvärde för de andra parametrarna som troligen skulle användas som en standardkonfiguration i din miljö. Du kan lagra mallen i ett Azure Storage-konto för delad åtkomst i din organisation. Mer information om hur du arbetar med mallar finns [i Distribuera resurser med ARM-mallar och Azure CLI.](../azure-resource-manager/templates/deploy-cli.md)

Om du inte har Azure Automation och Azure Monitor är det viktigt att du förstår följande konfigurationsinformation. De kan hjälpa dig att undvika fel när du försöker skapa, konfigurera och använda en Log Analytics-arbetsyta som är länkad till ditt nya Automation-konto.

* Granska [ytterligare information för](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) att få en fullständig förståelse för konfigurationsalternativ för arbetsytan, till exempel åtkomstkontrollläge, prisnivå, kvarhållning och kapacitetsreservationsnivå.

* Granska [arbetsytemappningar](how-to/region-mappings.md) för att ange vilka regioner som stöds, eller i en parameterfil. Endast vissa regioner stöds för att länka en Log Analytics-arbetsyta och ett Automation-konto i din prenumeration.

* Om du inte har distribuerat Azure Monitor och inte redan har distribuerat en arbetsyta bör du läsa designvägledningen för [arbetsytan.](../azure-monitor/logs/design-logs-deployment.md) Den hjälper dig att lära dig mer om åtkomstkontroll och förstå de designimplementeringsstrategier som vi rekommenderar för din organisation.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Automation konto, en Log Analytics-arbetsyta och länkar Automation-kontot till arbetsytan.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Ange värdena.

3. Distributionen kan ta några minuter att slutföra. När du är klar ser utdata ut ungefär så här:

    ![Exempelresultat när distributionen är klar](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal du automationskontot som du nyss skapade. 

3. Välj **Runbooks** i det vänstra fönstret. På sidan **Runbooks** visas tre självstudie-runbooks som skapats med Automation-kontot.

    ![Självstudie om runbooks som skapats med Automation-konto](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Välj Länkad arbetsyta i **den vänstra rutan.** På sidan **Länkad arbetsyta** visas Log Analytics-arbetsytan som du angav tidigare länkad till ditt Automation-konto.

    ![Automation-konto som är länkat till Log Analytics-arbetsytan](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort länken till Automation-kontot från Log Analytics-arbetsytan och tar sedan bort Automation-kontot och arbetsytan.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Automation-konto, en Log Analytics-arbetsyta och länkade dem tillsammans.

Om du vill veta mer fortsätter du till självstudierna för Azure Automation.

> [!div class="nextstepaction"]
> [Azure Automation självstudier](learn/automation-tutorial-runbook-graphical.md)
