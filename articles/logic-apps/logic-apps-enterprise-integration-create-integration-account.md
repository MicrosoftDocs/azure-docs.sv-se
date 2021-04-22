---
title: Skapa eller hantera B2B-integrationskonton
description: Skapa, länka och hantera integrationskonton för företagsintegrering med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: ae5ca6ac822dabd32b6463c3a742901f32b34323
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862263"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Skapa och hantera integrationskonton för B2B-företagsintegrationer i Azure Logic Apps

Innan du kan skapa [Enterprise-integrering och B2B-lösningar](../logic-apps/logic-apps-enterprise-integration-overview.md) med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) behöver du skapa ett integrationskonto, vilket är en separat Azure-resurs som ger en säker, skalbar, och hanterbar container för de integreringsartefakter som du definierar och använder med dina arbetsflöden för logikappar.

Du kan till exempel skapa, lagra och hantera B2B-artefakter, till exempel handelspartner, avtal, kartor, scheman, certifikat och batchkonfigurationer. Innan logikappen kan arbeta med dessa artefakter och använda anslutningsapparna för Logic Apps B2B måste du länka ditt [integrationskonto](#link-account) till logikappen. Både ditt integrationskonto och logikappen måste finnas på *samma* plats eller region.

> [!IMPORTANT]
> Beroende på vilken typ av integrationskonto du väljer medför skapandet av ett integrationskonto kostnader. Mer information finns i Logic Apps [och faktureringsmodeller och](logic-apps-pricing.md#integration-accounts) [Logic Apps priser.](https://azure.microsoft.com/pricing/details/logic-apps/)

Det här avsnittet visar hur du utför följande uppgifter:

* Skapa ditt integrationskonto.

  > [!TIP]
  > Information om hur du skapar ett integrationskonto i [en integreringstjänstmiljö](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)finns i [Skapa integrationskonton i en ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

* Länka ditt integrationskonto till en logikapp.

* Ändra prisnivån för ditt integreringskonto.

* Ta bort länken till ditt integrationskonto från en logikapp.

* Flytta ditt integrationskonto till en annan Azure-resursgrupp eller -prenumeration.

* Ta bort ditt integrationskonto.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Skapa integrationskonto

### <a name="portal"></a>[Portal](#tab/azure-portal)

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det [här avsnittet, Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)eller [Azure CLI](/cli/azure/resource#az_resource_create).

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. Välj **Skapa en resurs** på Azure-huvudmenyn. I sökrutan anger du "integrationskonto" som filter och väljer **Integrationskonto.**

   ![Skapa ett nytt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Under **Integrationskonto** väljer du **Skapa**.

   ![Välj "Lägg till" för att skapa integrationskontot](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Ange den här informationen om ditt integrationskonto:

   ![Ange information om integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Namn** | Yes | <*integration-account-name*> | Integrationskontots namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ), parenteser ( `(` , ), och punkter ( `)` `.` ). I det här exemplet används "Fabrikam-Integration". |
   | **Prenumeration** | Yes | <*Azure-prenumerationens namn*> | Azure-prenumerationens namn |
   | **Resursgrupp** | Yes | <*Azure-resource-group-name*> | Namnet på den [Azure-resursgrupp som](../azure-resource-manager/management/overview.md) ska användas för att organisera relaterade resurser. I det här exemplet skapar du en ny resursgrupp med namnet "FabrikamIntegration-RG". |
   | **Prisnivå** | Yes | <*prisnivå*> | Prisnivån för integrationskontot, som du kan ändra senare. I det här exemplet väljer du **Kostnadsfri.** Mer information finns i de här ämnena: <p>- [Logic Apps prismodell](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Plats** | Yes | <*Azure-region*> | Den region där metadata för ditt integrationskonto ska lagras. Välj antingen samma plats som logikappen eller skapa dina logikappar på samma plats som ditt integrationskonto. I det här exemplet använder du "USA, västra". <p>**Obs!** Om du vill skapa ett integrationskonto i [en integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)väljer du den ISE:n som plats. Mer information finns i Skapa [integrationskonton i en ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Log Analytics** | No | Av, På | Behåll inställningen **Av** för det här exemplet. |
   |||||

1. När du är klar väljer du **Skapa**.

   När distributionen är klar öppnar Azure ditt integreringskonto.

   ![Azure öppnar integrationskontot](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Innan logikappen kan använda ditt integrationskonto följer du nästa steg för att länka samman ditt integrationskonto och logikapp.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan skapa ett integrationskonto med hjälp av Azure CLI-kommandona i det här avsnittet.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Skapa ett integrationskonto

Använd dessa kommandon för att skapa ett integrationskonto.

1. Om du vill [lägga till tillägget az logic integration-account](/cli/azure/logic/integration-account) använder du kommandot az extension [add:](/cli/azure/extension#az_extension_add)

   ```azurecli
   az extension add –-name logic
   ```

1. Om du vill skapa en resursgrupp eller använda en befintlig resursgrupp kör du [kommandot az group create:](/cli/azure/group#az_group_create)

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Om du vill visa en lista med integrationskonton för en resursgrupp använder du kommandot [az logic integration-account list:](/cli/azure/logic/integration-account#az_logic_integration_account_list)

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Skapa ett integrationskonto genom att köra [kommandot az logic integration-account create:](/cli/azure/logic/integration-account#az_logic_integration_account_create)

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   Namnet på ditt integrationskonto får bara innehålla bokstäver, siffror, bindestreck (-), understreck (_), parenteser ((, )) och punkter (.).

   > [!TIP]
   > Om du vill skapa ett integrationskonto [i en Integration Service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)väljer du den ISE:n som plats. Mer information finns i Skapa [integrationskonton i en ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

   Om du vill visa ett specifikt integrationskonto använder du kommandot [az logic integration-account show:](/cli/azure/logic/integration-account#az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Du kan ändra din SKU eller prisnivå med hjälp av kommandot [az logic integration-account update:](/cli/azure/logic/integration-account#az_logic_integration_account_update)

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Mer information om priser finns i följande resurser:

   * [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/)

Om du vill importera ett integrationskonto med hjälp av en JSON-fil använder du [kommandot az logic integration-account import:](/cli/azure/logic/integration-account#az_logic_integration_account_import)

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Du kan ta bort ett integrationskonto med kommandot [az logic integration-account delete:](/cli/azure/logic/integration-account#az_logic_integration_account_delete)

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Innan logikappen kan använda ditt integrationskonto länkar du integrationskontot och logikappen till varandra. I nästa avsnitt beskrivs länkning.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Länka till logikapp

Om du vill ge dina logikappar åtkomst till ett integrationskonto som innehåller dina B2B-artefakter måste du först länka ditt integrationskonto till logikappen. Både logikappen och integrationskontot måste finnas i samma region. För att slutföra den här uppgiften kan du använda Azure Portal. Om du använder Visual Studio och logikappen finns i ett [Azure-resursgruppsprojekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)kan du länka logikappen till ett [integrationskonto](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)med hjälp av Visual Studio .

1. I Azure Portal du och öppnar logikappen.

1. I [Azure Portal](https://portal.azure.com)du en befintlig logikapp eller skapar en ny logikapp.

1. Välj Arbetsflödesinställningar under Inställningar på **logikappens** **meny.** Under **Integrationskonto** öppnar du **listan Välj ett integrationskonto.** Välj integrationskontot för att länka till logikappen.

   ![Välj ditt integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Slutför länkningen genom att välja **Spara**.

   ![Skärmbild som visar var du väljer Spara för att välja ditt integrationskonto.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   När ditt integrationskonto har länkats visar Azure ett bekräftelsemeddelande.

   ![Azure bekräftar länken](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Nu kan logikappen använda artefakterna i ditt integrationskonto plus B2B-anslutningsapparna, till exempel XML-verifiering och flat filkodning eller avkodning.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Ändra prisnivå

Om du vill [öka gränserna](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) för ett integrationskonto kan du uppgradera till en [högre prisnivå](#upgrade-pricing-tier)om det är tillgängligt. Du kan till exempel uppgradera från den kostnadsfria nivån till Basic-nivån eller standardnivån. Du kan också [nedgradera till en lägre nivå](#downgrade-pricing-tier)om det är tillgängligt. Mer information om priser finns i följande avsnitt:

* [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Uppgradera prisnivå

Om du vill göra den här ändringen kan du använda antingen Azure Portal eller Azure CLI.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I azure-huvudsökrutan anger du "integrationskonton" som filter och väljer **Integrationskonton.**

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton** väljer du det integrationskonto som du vill flytta. På menyn för ditt integrationskonto väljer du **Översikt.**

   ![På integrationskontomenyn väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. I fönstret Översikt väljer du **Uppgradera prisnivå**, som visar alla tillgängliga högre nivåer. När du väljer en nivå börjar ändringen omedelbart gälla.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du inte redan har gjort det installerar [du kraven för Azure CLI.](/cli/azure/get-started-with-azure-cli)

1. I Azure Portal du den [Azure Cloud Shell](../cloud-shell/overview.md) miljön.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommandotolken anger du [ **kommandot az resource**](/cli/azure/resource#az_resource_update)och anger till den högre nivå som du vill `skuName` använda.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Om du till exempel har Basic-nivån kan du ange `skuName` till `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nedgradera prisnivån

Om du vill göra den här ändringen använder du [Azure CLI](/cli/azure/get-started-with-azure-cli).

1. Om du inte redan har gjort det installerar [du kraven för Azure CLI.](/cli/azure/get-started-with-azure-cli)

1. I Azure Portal du den [Azure Cloud Shell](../cloud-shell/overview.md) miljön.

   ![Öppna Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. I kommandotolken anger du [ **kommandot az resource**](/cli/azure/resource#az_resource_update) och anger till den lägre nivå som du vill `skuName` använda.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Om du till exempel har standardnivån kan du ange `skuName` till `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Ta bort länk från logikapp

Om du vill länka logikappen till ett annat integrationskonto eller inte längre använda ett integrationskonto med logikappen tar du bort länken med hjälp av Azure Resource Explorer.

1. Öppna webbläsarfönstret och gå till [Azure Resource Explorer https://resources.azure.com) (](https://resources.azure.com). Logga in med samma autentiseringsuppgifter för Azure-kontot.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. I sökrutan anger du logikappens namn så att du kan hitta och välja din logikapp.

   ![Hitta och välj logikapp](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. I explorer-namnlisten väljer du **Läsa/skriva.**

   ![Aktivera läget "Läsa/skriva"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. På fliken **Data** väljer du **Redigera**.

   ![På fliken Data väljer du "Redigera"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Leta reda på objektet i `integrationAccount` redigeraren och ta bort egenskapen, som har det här formatet:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Exempel:

   ![Hitta integrationAccount-objekt](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. På fliken **Data** väljer du **Placera för** att spara ändringarna.

   ![Om du vill spara ändringarna väljer du "Placera"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. I Azure Portal du och väljer din logikapp. Under appens **arbetsflödesinställningar kontrollerar** du att egenskapen **Integrationskonto** nu visas som tom.

   ![Kontrollera att integrationskontot inte är länkat](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Flytta integrationskonto

Du kan flytta ditt integrationskonto till en annan Azure-resursgrupp eller Azure-prenumeration. När du flyttar resurser skapar Azure nya resurs-ID:er, så se till att du använder de nya ID:erna i stället och uppdatera eventuella skript eller verktyg som är associerade med de flyttade resurserna. Om du vill ändra prenumerationen måste du också ange en befintlig eller ny resursgrupp.

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet eller [Azure CLI.](/cli/azure/resource#az_resource_move)

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I azure-huvudsökrutan anger du "integrationskonton" som filter och väljer **Integrationskonton.**

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton** väljer du det integrationskonto som du vill flytta. På menyn för ditt integrationskonto väljer du **Översikt.**

   ![På integrationskontomenyn väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Bredvid **Resursgrupp eller** **Prenumerationsnamn** väljer du **ändra**.

   ![Ändra resursgrupp eller prenumeration](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Välj eventuella relaterade resurser som du också vill flytta.

1. Följ de här stegen för att ändra resursgruppen eller prenumerationen baserat på ditt val:

   * Resursgrupp: Välj **målresursgruppen** i listan Resursgrupp. Om du vill skapa en annan resursgrupp väljer du **Skapa en ny resursgrupp.**

   * Prenumeration: Välj **målprenumerationen** i listan Prenumeration. I listan **Resursgrupp** väljer du målresursgruppen. Om du vill skapa en annan resursgrupp väljer du **Skapa en ny resursgrupp.**

1. Om du vill bekräfta att skript eller verktyg som är associerade med de flyttade resurserna inte fungerar förrän du uppdaterar dem med de nya resurs-ID:erna markerar du bekräftelserutan och väljer **sedan OK.**

1. När du är klar ser du till att uppdatera alla skript med de nya resurs-ID:erna för de resurser som har flyttats.  

## <a name="delete-integration-account"></a>Ta bort integrationskonto

För den här uppgiften kan du använda antingen Azure Portal genom att följa stegen i det här avsnittet, [Azure CLI](/cli/azure/resource#az_resource_delete)eller [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I den huvudsakliga Azure-sökrutan anger du "integrationskonton" som filter och väljer **Integrationskonton**.

   ![Hitta integrationskonto](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure visar alla integrationskonton i dina Azure-prenumerationer.

1. Under **Integrationskonton** väljer du det integrationskonto som du vill ta bort. I menyn för ditt integrationskonto väljer du **Översikt.**

   ![I menyn för integrationskonto väljer du "Översikt"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. I fönstret Översikt väljer du Ta **bort.**

   ![I fönstret "Översikt" väljer du "Ta bort"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Bekräfta att du vill ta bort ditt integrationskonto genom att välja **Ja.**

   ![Bekräfta borttagningen genom att välja "Ja"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa handelspartner i ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Skapa avtal mellan partner i ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-agreements.md)
