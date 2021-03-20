---
title: 'Snabb start: skapa en Azure Synapse-arbetsyta Azure Resource Manager mall'
description: Lär dig hur du skapar en Synapse-arbetsyta med hjälp av Azure Resource Manager mall (ARM-mall).
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92740373"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Snabb start: skapa en Azure dataSynapses-arbetsyta med en ARM-mall

Med den här Azure Resource Manager mallen (ARM-mallen) skapas en Azure Synapse-arbetsyta med underliggande Data Lake Storage. Azure dataSynapses-arbetsytan är en skydds bara samarbets gränser för analys processer i Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Du kan granska mallen genom att välja länken **visualisera** . Välj sedan **Redigera mall**.

[![Visualisera](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Mallen definierar två resurser:

- Lagringskonto
- Arbetsyta

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen. Den här mallen skapar en Synapse-arbetsyta.

   [![Distribuera till Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Ange eller uppdatera följande värden:

   - **Prenumeration**: Välj en Azure-prenumeration.
   - **Resurs grupp**: Välj **Skapa ny** och ange ett unikt namn för resurs gruppen och välj **OK**. En ny resurs grupp gör det lättare att rensa resursen.
   - **Region**: Välj en region.  Välj till exempel **USA, centrala**.
   - **Namn**: Ange ett namn för din arbets yta.
   - **SQL-administratör inloggning**: Ange administratörs användar namnet för SQL Server.
   - **SQL-administratörs lösen ord**: Ange administratörs lösen ordet för SQL Server.
   - **Tagg värden**: Acceptera standardvärdet.
   - **Granska och skapa**: Välj.
   - **Skapa**: Välj.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Synapse Analytics och Azure Resource Manager kan du fortsätta till artiklarna nedan.

- Läs en [Översikt över Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
