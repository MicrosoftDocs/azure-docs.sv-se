---
title: Ta bort resurser från Azure
description: Ta bort resurser från Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716087"
---
# <a name="delete-resources-from-azure"></a>Ta bort resurser från Azure

I den här artikeln beskrivs hur du tar bort resurser från Azure.

> [!WARNING]
> När du tar bort resurser enligt beskrivningen i den här artikeln går de här åtgärderna inte att ångra.

Om du tar bort en instans från Kubernetes i läget för indirekt anslutning tas den inte bort från Azure. Om du tar bort en instans från Azure tas den inte bort från Kubernetes. För läget för indirekt anslutning är borttagning av en resurs en process i två steg och detta kommer att förbättras i framtiden. Kubernetes är sanningskällan och portalen uppdateras för att återspegla den.

I vissa fall kan du behöva ta bort resurser Azure Arc-aktiverade datatjänster Azure manuellt.  Du kan ta bort dessa resurser med något av följande alternativ.

- [Ta bort resurser från Azure](#delete-resources-from-azure)
  - [Ta bort en hel resursgrupp](#delete-an-entire-resource-group)
  - [Ta bort specifika resurser i resursgruppen](#delete-specific-resources-in-the-resource-group)
  - [Ta bort resurser med Hjälp av Azure CLI](#delete-resources-using-the-azure-cli)
    - [Ta bort sql-hanterade instansresurser med hjälp av Azure CLI](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Ta bort PostgreSQL Hyperscale-servergruppresurser med hjälp av Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Ta Azure Arc datastyrenhetsresurser med hjälp av Azure CLI](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Ta bort en resursgrupp med hjälp av Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Ta bort en hel resursgrupp

Om du har använt en specifik och dedikerad resursgrupp för  Azure Arc-aktiverade datatjänster och du vill ta bort allt i resursgruppen kan du ta bort resursgruppen, vilket tar bort allt i den.  

Du kan ta bort en resursgrupp i Azure Portal genom att göra följande:

- Bläddra till resursgruppen i den Azure Portal där Azure Arc-aktiverade datatjänster har skapats.
- Klicka på **knappen Ta bort resursgrupp.**
- Bekräfta borttagningen genom att ange resursgruppens namn och klicka på **Ta bort.**

## <a name="delete-specific-resources-in-the-resource-group"></a>Ta bort specifika resurser i resursgruppen

Du kan ta bort Azure Arc-aktiverade datatjänster resurser i en resursgrupp i Azure Portal genom att göra följande:

- Bläddra till resursgruppen i den Azure Portal där Azure Arc-aktiverade datatjänster har skapats.
- Markera alla resurser som ska tas bort.
- Klicka på knappen Ta bort.
- Bekräfta borttagningen genom att skriva Ja och klicka på **Ta bort.**

## <a name="delete-resources-using-the-azure-cli"></a>Ta bort resurser med Hjälp av Azure CLI

Du kan ta bort specifika Azure Arc-aktiverade datatjänster resurser med hjälp av Azure CLI.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Ta bort sql-hanterade instansresurser med hjälp av Azure CLI

Om du vill ta bort sql-hanterade instansresurser från Azure med hjälp av Azure CLI ersätter du platshållarvärdena i kommandot nedan och kör det.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Ta bort PostgreSQL Hyperskala-servergruppresurser med hjälp av Azure CLI

Om du vill ta bort en PostgreSQL Hyperscale-servergruppsresurs från Azure med hjälp av Azure CLI ersätter du platshållarvärdena i kommandot nedan och kör den.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Ta Azure Arc datastyrenhetsresurser med hjälp av Azure CLI

> [!NOTE]
> Innan du Azure Arc en datakontrollant bör du ta bort alla databasinstansresurser som den hanterar.

Om du vill Azure Arc en datakontrollant från Azure med hjälp av Azure CLI ersätter du platshållarvärdena i kommandot nedan och kör den.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Ta bort en resursgrupp med hjälp av Azure CLI

Du kan också använda Azure CLI för att [ta bort en resursgrupp.](../../azure-resource-manager/management/delete-resource-group.md)