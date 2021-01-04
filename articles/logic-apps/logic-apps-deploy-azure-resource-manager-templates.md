---
title: Distribuera mallar för logikappar
description: Lär dig hur du distribuerar Azure Resource Manager mallar som skapats för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705344"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Distribuera Azure Resource Manager-mallar för Azure Logic Apps

När du har skapat en Azure Resource Manager mall för din Logic app kan du distribuera mallen på följande sätt:

* [Azure-portalen](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST-API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuera via Azure Portal

Om du vill distribuera en Logic app-mall automatiskt till Azure kan du klicka på knappen för att **distribuera till Azure** , som loggar in på Azure Portal och där du uppmanas att ange information om din Logic app. Du kan sedan göra nödvändiga ändringar i Logic app-mallen eller parametrar.

[![Distribuera till Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Du uppmanas till exempel att ange följande information när du har loggat in på Azure Portal:

* Namn på Azure-prenumeration
* Resurs grupp som du vill använda
* Plats för Logic app
* Logikappens namn
* En test-URI
* Godkännande av de angivna villkoren

Mer information finns i de här ämnena:

* [Översikt: Automatisera distribution av Logic Apps med Azure Resource Manager mallar](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuera resurser med Azure Resource Manager mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

Om du vill distribuera en Logic app-mall från ett Azure-resurs grupps projekt som du skapade med Visual Studio, följer du dessa [steg för att manuellt distribuera din Logic app](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) till Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

Använd följande kommando för att distribuera till en *Azure-resurs grupp*:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i de här ämnena:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Använd följande kommando för att distribuera till en *Azure-resurs grupp*:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Mer information finns i de här ämnena:

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuera med Azure DevOps

För att distribuera Logic app-mallar och hantera miljöer använder team ofta ett verktyg som [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) i [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure-pipeliner tillhandahåller en [distributions uppgift för Azure-resursposter](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) som du kan lägga till i en pipeline för build eller release. För att du ska kunna distribuera och generera en release-pipeline behöver du också ett [huvud namn](../active-directory/develop/app-objects-and-service-principals.md)för Azure Active Directory (AD). Lär dig mer om att [använda tjänstens huvud namn med Azure-pipeliner](/azure/devops/pipelines/library/connect-to-azure).

Mer information om kontinuerlig integrering och kontinuerlig distribution (CI/CD) för Azure Resource Manager mallar med Azure pipelines finns i följande avsnitt och exempel:

* [Integrera Resource Manager-mallar med Azure-pipeline](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Självstudie: kontinuerlig integrering av Azure Resource Manager mallar med Azure-pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Exempel: Anslut till Azure Service Bus köer från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage konton från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en Function-app-åtgärd för Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrations konto från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Exempel: dirigera Azure-pipelines med hjälp av Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Här följer de allmänna stegen för att använda Azure-pipeliner:

1. Skapa en tom pipeline i Azure-pipeliner.

1. Välj de resurser som du behöver för pipelinen, till exempel din Logic app-mall och mallar för mallfiler, som du skapar manuellt eller som en del av bygg processen.

1. För Agent jobbet letar du reda på och lägger till **distributions uppgiften Azure resurs grupp** .

   ![Lägg till aktiviteten Azure Resource Group Deployment](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurera med ett [huvud namn för tjänsten](/azure/devops/pipelines/library/connect-to-azure).

1. Lägg till referenser till din Logic app-mall och mallfiler för mallar.

1. Fortsätt att bygga upp stegen i versions processen för alla andra miljöer, automatiserade tester och god kännare efter behov.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Auktorisera OAuth-anslutningar

Efter distributionen fungerar din Logic app från slut punkt till slut punkt med giltiga parametrar, men för att generera giltiga åtkomsttoken för [autentisering av dina autentiseringsuppgifter](../active-directory/develop/authentication-vs-authorization.md)måste du ändå tillåta eller använda förauktoriserade OAuth-anslutningar. Men du behöver bara distribuera och autentisera resurser för API-anslutning en gång, vilket innebär att du inte behöver ta med dessa anslutnings resurser i efterföljande distributioner, såvida du inte behöver uppdatera anslutnings informationen. Om du använder en pipeline för kontinuerlig integrering och kontinuerlig distribution distribuerar du bara uppdaterade Logic Apps resurser och behöver inte godkänna anslutningarna varje gång.

Här följer några förslag på hur du hanterar auktorisering av anslutningar:

* Förauktorisera och dela API-anslutningsfel mellan olika Logic-appar i samma region. API-anslutningar finns som Azure-resurser oberoende av Logic Apps. Även om Logic Apps har beroenden av API-anslutnings resurser, finns det inga beroenden för API-anslutnings resurser i Logi Kap par och de är kvar när du har tagit bort beroende Logic Apps Logic Apps kan också använda API-anslutningar som finns i andra resurs grupper. Logic App Designer stöder dock bara skapande av API-anslutningar i samma resurs grupp som dina Logic Apps.

  > [!NOTE]
  > Om du överväger att dela API-anslutningar ser du till att din lösning kan [hantera potentiella begränsnings problem](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). Begränsning sker på anslutnings nivå, så att återanvända samma anslutning över flera Logi Kap par kan öka risken för problem med begränsningen.

* Om inte ditt scenario innefattar tjänster och system som kräver multifaktorautentisering kan du använda ett PowerShell-skript för att ge medgivande för varje OAuth-anslutning genom att köra en kontinuerlig integrerings arbetare som ett normalt användar konto på en virtuell dator som har aktiva webbläsarsessionen med de auktoriseringar och medgivande som redan angetts. Du kan till exempel ändra syftet med det exempel skript som tillhandahålls av [LogicAppConnectionAuth-projektet i Logic Apps GitHub-lagrings platsen](https://github.com/logicappsio/LogicAppConnectionAuth).

* Auktorisera OAuth-anslutningar manuellt genom att öppna din Logic app i Logic App Designer, antingen i Azure Portal eller i Visual Studio.

* Om du använder ett [huvud namn](../active-directory/develop/app-objects-and-service-principals.md) för tjänsten Azure Active Directory (Azure AD) i stället för att auktorisera anslutningar, lär du dig att [Ange parametrar för tjänstens huvud namn i din Logic app-mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka logikappar](../logic-apps/monitor-logic-apps.md)
