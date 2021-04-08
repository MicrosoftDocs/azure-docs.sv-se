---
title: Distribuera till befintligt virtuellt nätverk
description: Beskriver hur du gör det möjligt för användare av ditt hanterade program att välja ett befintligt virtuellt nätverk. Det virtuella nätverket kan ligga utanför det hanterade programmet.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84261291"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Använd ett befintligt virtuellt nätverk med Azure Managed Applications

Den här artikeln visar hur du definierar ett Azure-hanterat program som integrerar med ett befintligt virtuellt nätverk i konsumentens prenumeration. Med det hanterade programmet kan konsumenten bestämma om du vill skapa ett nytt virtuellt nätverk eller använda ett befintligt. Det befintliga virtuella nätverket kan ligga utanför den hanterade resurs gruppen.

## <a name="main-template"></a>Huvud mal len

Först ska vi titta på **mainTemplate.jspå** filen. Hela mallen för att distribuera en virtuell dator och dess associerade resurser visas nedan. Senare kommer du att titta närmare på de delar av mallen som är relaterade till att använda ett befintligt virtuellt nätverk.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Observera att det virtuella nätverket är [villkorligt distribuerat](../templates/conditional-resource-deployment.md). Konsumenten skickar ett parameter värde som anger om du vill skapa ett nytt eller använda ett befintligt virtuellt nätverk. Om konsumenten väljer ett nytt virtuellt nätverk distribueras resursen. Annars hoppas resursen över under distributionen.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

Variabeln för det virtuella nätverks-ID: t har två egenskaper. En egenskap returnerar resurs-ID när ett nytt virtuellt nätverk distribueras. Den andra egenskapen returnerar resurs-ID när ett befintligt virtuellt nätverk används. Resurs-ID: t för det befintliga virtuella nätverket innehåller namnet på den resurs grupp som innehåller det virtuella nätverket.

Under nätets ID skapas från värdet för det virtuella nätverks-ID: t. Värdet används för att matcha konsumenternas val.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

Nätverks gränssnittet har angetts till variabeln Undernäts-ID.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>GRÄNSSNITTs definition

Nu ska vi titta på **createUiDefinition.jspå** filen. Hela filen är:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

Filen innehåller ett virtuellt nätverks element.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Med det elementet kan konsumenten välja antingen ett nytt eller ett befintligt virtuellt nätverk.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Nytt eller befintligt virtuellt nätverk":::

I utmatningarna inkluderar du ett värde som anger om konsumenten har valt ett nytt eller befintligt virtuellt nätverk. Det finns också ett hanterat identitets värde.

> [!NOTE]
> Utmatning svärdet för den hanterade identiteten måste ha namnet **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar användar gränssnitts definitions filen finns i [CreateUiDefinition.jspå för Azure Managed Application Create Experience](create-uidefinition-overview.md).
