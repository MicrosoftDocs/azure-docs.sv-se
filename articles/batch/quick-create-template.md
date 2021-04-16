---
title: Azure snabbstart – Skapa ett Batch-konto – Azure Resource Manager mall
description: Den här snabbstarten visar hur du skapar ett Batch-konto med hjälp av en ARM-mall.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: e3405e98e2753a308b6b34b392aa364a67f7fe5b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535587"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Snabbstart: Skapa ett Batch-konto med hjälp av ARM-mall

Du behöver ett Batch-konto för att skapa beräkningsresurser (pooler med beräkningsnoder) och Batch-jobb. Du kan länka ett Azure Storage-konto med ditt Batch-konto, vilket är användbart för att distribuera program och lagra indata och utdata för de flesta verkliga arbetsbelastningar. Den här snabbstarten visar hur du använder en Azure Resource Manager (ARM-mall) för att skapa ett Batch-konto, inklusive lagring. När du har slutfört den här snabbstarten kommer du att förstå huvudbegreppen för Batch-tjänsten och vara redo att testa Batch med mer realistiska arbetsbelastningar i större skala.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Du måste ha en aktiv Azure-prenumeration.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

- [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)Skapar ett lagringskonto.
- [Microsoft.Batch/batchAccounts:](/azure/templates/microsoft.batch/batchaccounts)Skapar ett Batch-konto.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Batch och ett lagringskonto.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Välj eller ange följande värden.

   ![Resource Manager mall, skapa Batch-konto, distribuera portal](media/quick-create-template/batch-template.png)

   - **Prenumeration**: välj en Azure-prenumeration.
   - **Resursgrupp:** välj **Skapa ny,** ange ett unikt namn för resursgruppen och klicka sedan på **OK.**
   - **Plats**: välj en plats. Välj till exempel **USA, centrala**.
   - **Batch-kontonamn:** Låt standardvärdet vara kvar.
   - **Lagringskontonku:** välj en typ av lagringskonto. Du kan till **exempel Standard_LRS**.
   - **Plats:** Lämna standardinställningen så att resurserna finns på samma plats som resursgruppen.
   - Jag godkänner villkoren som anges ovan: **Välj**.

1. Välj **Köp**.

Efter några minuter bör du se ett meddelande om att Batch-kontot har skapats.

I det här exemplet används Azure Portal för att distribuera mallen. Förutom de här Azure Portal kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen i Azure Portal genom att gå till resursgruppen som du skapade. På skärmen **Översikt** bekräftar du att Batch-kontot och lagringskontot finns.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande [självstudier](./tutorial-parallel-dotnet.md)kanske du vill lämna dessa resurser på plats. Eller, om du inte längre behöver dem, kan du ta bort resursgruppen [,](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)som även tar bort Batch-kontot och lagringskontot som du skapade.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Batch-konto och ett lagringskonto. Om du vill veta mer om Azure Batch ska du fortsätta med Azure Batch-självstudierna.

> [!div class="nextstepaction"]
> [Azure Batch-självstudier](./tutorial-parallel-dotnet.md)
