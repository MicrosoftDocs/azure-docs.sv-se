---
title: Flytta Logic Apps över prenumerationer, resurs grupper eller regioner
description: Migrera Logic Apps eller integrations konton till andra Azure-prenumerationer, resurs grupper eller platser (regioner)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87826219"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Flytta logiska app-resurser till andra Azure-resurs grupper, regioner eller prenumerationer

Om du vill migrera din Logic app eller relaterade resurser till en annan Azure-resurs, region eller prenumeration har du olika sätt att slutföra dessa uppgifter, till exempel Azure Portal, Azure PowerShell, Azure CLI och REST API. Innan du flyttar resurser bör du gå igenom följande överväganden: 

* Du kan bara flytta [vissa resurs typer för logiska appar](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) mellan Azures resurs grupper eller prenumerationer.

* Kontrol lera [gränserna](../logic-apps/logic-apps-limits-and-config.md) för antalet Logic app-resurser som du kan ha i din Azure-prenumeration och i varje Azure-region. Dessa begränsningar påverkar om du kan flytta vissa resurs typer när regionen är densamma i prenumerationer eller resurs grupper. Du kan till exempel bara ha ett integrations konto på kostnads fri nivå för varje Azure-region i varje Azure-prenumeration.

* När du flyttar resurser skapar Azure nya resurs-ID: n. Kontrol lera därför att du använder de nya ID: na i stället och uppdaterar eventuella skript eller verktyg som är associerade med de flyttade resurserna.

* När du har migrerat Logi Kap par mellan prenumerationer, resurs grupper eller regioner måste du återskapa eller auktorisera om anslutningar som kräver öppen autentisering (OAuth).

* Du kan bara flytta en [integrerings tjänst miljö (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) till en annan resurs grupp som finns i samma Azure-region eller Azure-prenumeration. Du kan inte flytta en ISE till en resurs grupp som finns i en annan Azure-region eller Azure-prenumeration. Efter en sådan flyttning måste du också uppdatera alla referenser till ISE i dina Logic app-arbetsflöden, integrations konton, anslutningar och så vidare.

## <a name="prerequisites"></a>Förutsättningar

* Samma Azure-prenumeration som användes för att skapa den Logic app eller det integrations konto som du vill flytta

* Resurs ägar behörigheter för att flytta och konfigurera de resurser som du vill använda. Lär dig mer om [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Flytta resurser mellan prenumerationer

Om du vill flytta en resurs, till exempel en Logic app eller ett integrations konto, till en annan Azure-prenumeration kan du använda Azure Portal, Azure PowerShell, Azure CLI eller REST API. Dessa steg beskriver Azure Portal som du kan använda när resursens region förblir densamma. Andra steg och allmän förberedelse finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer den logiska app-resurs som du vill flytta.

1. På resurs sidan **Översikt** bredvid **prenumeration** väljer du länken **ändra** .

1. På sidan **Flytta resurser** väljer du den logiska app-resurs och eventuella relaterade resurser som du vill flytta.

1. I listan **prenumeration** väljer du mål prenumerationen.

1. Välj mål resurs grupp i listan **resurs grupp** . Om du vill skapa en annan resurs grupp väljer du **skapa en ny grupp**.

1. För att bekräfta att skript eller verktyg som är associerade med de flyttade resurserna fungerar inte förrän du uppdaterar dem med de nya resurs-ID: n, markerar du rutan bekräftelse och väljer sedan **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Flytta resurser mellan resurs grupper

Om du vill flytta en resurs, till exempel en Logic app, ett integrations konto eller en [integrerings tjänst miljö (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md)till en annan Azure-resurs grupp, kan du använda Azure Portal, Azure PowerShell, Azure CLI eller REST API. Dessa steg beskriver Azure Portal som du kan använda när resursens region förblir densamma. Andra steg och allmän förberedelse finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Innan du faktiskt flyttar resurser mellan grupper kan du testa om du kan flytta resursen till en annan grupp. Mer information finns i [Verifiera din flytt](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer den logiska app-resurs som du vill flytta.

1. På sidan **Översikt** på resursen, bredvid **resurs grupp**, väljer du länken **ändra** .

1. På sidan **Flytta resurser** väljer du den logiska app-resurs och eventuella relaterade resurser som du vill flytta.

1. Välj mål resurs grupp i listan **resurs grupp** . Om du vill skapa en annan resurs grupp väljer du **skapa en ny grupp**.

1. För att bekräfta att skript eller verktyg som är associerade med de flyttade resurserna fungerar inte förrän du uppdaterar dem med de nya resurs-ID: n, markerar du rutan bekräftelse och väljer sedan **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Flytta resurser mellan regioner

När du vill flytta en Logic app till en annan region beror alternativen på hur du har skapat din Logic app. Baserat på det alternativ som du väljer måste du återskapa eller auktorisera anslutningarna i din Logic app.

* I Azure Portal återskapar du Logic-appen i den nya regionen och konfigurerar om arbets flödes inställningarna. Du kan spara tid genom att kopiera den underliggande arbets flödes definitionen och anslutningar från käll appen till mål programmet. Om du vill visa koden bakom en Logic app i Logic App Designer-verktygsfältet väljer du **kodvyn**.

* Genom att använda Visual Studio och Azure Logic Apps verktyg för Visual Studio kan du [öppna och ladda ned din Logic app](../logic-apps/manage-logic-apps-with-visual-studio.md) från Azure Portal som en [Azure Resource Manager mall](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Den här mallen är oftast klar för distribution och innehåller resurs definitionerna för din Logic app, inklusive själva arbets flödet och anslutningar. Mallen deklarerar också parametrar för de värden som ska användas vid distributionen. På så sätt kan du enkelt ändra var och hur du distribuerar Logic-appen utifrån dina behov. Om du vill ange plats och annan nödvändig information för distribution kan du använda en separat parameter fil.

* Om du har skapat och distribuerat din Logic app med hjälp av verktyg för kontinuerlig integrering (CI) och kontinuerlig leverans (CD), till exempel Azure-pipelines i Azure DevOps, kan du distribuera din app till en annan region med hjälp av dessa verktyg.

Mer information om mallar för att distribuera Logic Apps finns i följande avsnitt:

* [Översikt: Automatisera distribution av Azure Logic Apps med hjälp av Azure Resource Manager mallar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Hitta, öppna och ladda ned din Logic app från Azure Portal till Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Skapa Azure Resource Manager mallar för Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuera Azure Resource Manager-mallar för Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Relaterade resurser

Vissa Azure-resurser, till exempel lokala data Gateway-resurser i Azure, kan finnas i en region som skiljer sig från de Logic Apps som använder dessa resurser. Andra Azure-resurser, t. ex. länkade integrations konton, måste dock finnas i samma region som dina Logic Apps. Beroende på ditt scenario kontrollerar du att dina Logi Kap par har åtkomst till de resurser som dina appar förväntar sig finnas i samma region.

Om du till exempel vill länka en Logic app till ett integrations konto måste båda resurserna finnas i samma region. I scenarier som haveri beredskap vill du vanligt vis använda integrations konton som har samma konfiguration och artefakter. I andra scenarier kan du behöva integrations konton med olika konfigurationer och artefakter.

Anpassade anslutningar i Azure Logic Apps är synliga för anslutningens författare och användare som har samma Azure-prenumeration och samma Azure Active Directory-klient. Dessa anslutningar är tillgängliga i samma region där Logic Apps distribueras. Mer information finns i [Dela anpassade anslutningsappar i organisationen](/connectors/custom-connectors/share).

Den mall som du hämtar från Visual Studio innehåller bara resurs definitionerna för din Logic app och dess anslutningar. Så om din Logic app använder andra resurser, till exempel ett integrations konto och B2B-artefakter, till exempel partner, avtal och scheman, måste du exportera integrations kontots mall med hjälp av Azure Portal. Den här mallen innehåller resurs definitionerna för både integrations kontot och artefakterna. Mallen är dock inte helt parametriserad. Så du måste manuellt Parameterisera de värden som du vill använda för distribution.

### <a name="export-templates-for-integration-accounts"></a>Exportera mallar för integrations konton

1. Leta upp och öppna ditt integrations konto i [Azure Portal](https://portal.azure.com).

1. På integrations kontots meny väljer du **Exportera mall** under **Inställningar**.

1. I verktygsfältet väljer du **Ladda ned** och sparar mallen.

1. Öppna och redigera mallen för att Parameterisera de nödvändiga värdena för distribution.

## <a name="next-steps"></a>Nästa steg

[Flytta Azure-resurser till nya resurs grupper eller prenumerationer](../azure-resource-manager/management/move-resource-group-and-subscription.md)
