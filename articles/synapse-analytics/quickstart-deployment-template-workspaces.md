---
title: 'Snabbstart: Skapa en Azure Synapse arbetsyta Azure Resource Manager mall'
description: Lär dig hur du skapar en Synapse-arbetsyta med Azure Resource Manager mall (ARM-mall).
services: azure-resource-manager
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 7317b7f51c6d0f9d72e3aad81794a569276d2145
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566128"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Snabbstart: Skapa en Azure Synapse med en ARM-mall

Den Azure Resource Manager mallen (ARM-mall) skapar en Azure Synapse arbetsyta med underliggande Data Lake Storage. Arbetsytan Azure Synapse ett skyddat samarbete för analysprocesser i Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Du kan granska mallen genom att välja **länken** Visualisera. Välj sedan **Redigera mall.**

[![Visualisera](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Mallen definierar två resurser:

- Lagringskonto
- Arbetsyta

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen. Den här mallen skapar en Synapse-arbetsyta.

   [![Distribuera till Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Ange eller uppdatera följande värden:

   - **Prenumeration:** Välj en Azure-prenumeration.
   - **Resursgrupp:** Välj **Skapa ny,** ange ett unikt namn för resursgruppen och välj **OK.** En ny resursgrupp underlättar resursrensning.
   - **Region:** Välj en region.  Välj till exempel **USA, centrala**.
   - **Namn:** Ange ett namn för din arbetsyta.
   - **SQL-administratörsinloggning:** Ange administratörens användarnamn för SQL Server.
   - **SQL-administratörslösenord:** Ange administratörslösenordet för SQL Server.
   - **Tagga** värden: Acceptera standardvärdet.
   - **Granska och skapa:** Välj.
   - **Skapa:** Välj.

## <a name="next-steps"></a>Nästa steg

Om du vill veta Azure Synapse Analytics och Azure Resource Manager kan du fortsätta med artiklarna nedan.

- Läs en [översikt över Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
