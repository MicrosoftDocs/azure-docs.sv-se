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
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202050"
---
# <a name="delete-resources-from-azure"></a>Ta bort resurser från Azure

> [!NOTE]
>  Alternativen för att ta bort resurser i den här artikeln går inte att ångra!

> [!NOTE]
>  Eftersom det enda anslutnings läge som erbjuds för Azure Arc-aktiverade data tjänster är för närvarande i läget för indirekt anslutning, tar borttagning av en instans från Kubernetes inte bort från Azure och om du tar bort en instans från Azure tas den inte bort från Kubernetes.  För tillfället är det en två stegs process att ta bort en resurs och detta kommer att förbättras i framtiden.  Kubernetes är källan till sanningen och Azure kommer att uppdateras för att avspegla det.

I vissa fall kan du behöva manuellt ta bort Azure Arc-aktiverade data Services-resurser i Azure Resource Manager (ARM).  Du kan ta bort dessa resurser med något av följande alternativ.

- [Ta bort resurser från Azure](#delete-resources-from-azure)
  - [Ta bort en hel resurs grupp](#delete-an-entire-resource-group)
  - [Ta bort vissa resurser i resurs gruppen](#delete-specific-resources-in-the-resource-group)
  - [Ta bort resurser med Azure CLI](#delete-resources-using-the-azure-cli)
    - [Ta bort SQL Managed Instance-resurser med hjälp av Azure CLI](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Ta bort PostgreSQL-resurser för storskalig Server grupp med hjälp av Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Ta bort Azure Arc data Controller-resurser med Azure CLI](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Ta bort en resurs grupp med Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Ta bort en hel resurs grupp
Om du har använt en särskild och dedikerad resurs grupp för Azure Arc-aktiverade data tjänster och du vill ta bort *allt* i resurs gruppen kan du ta bort resurs gruppen som tar bort allt i den.  

Du kan ta bort en resurs grupp i Azure Portal genom att göra följande:

- Bläddra till resurs gruppen i den Azure Portal där Azure Arc-aktiverade data Services-resurser har skapats.
- Klicka på knappen **ta bort resurs grupp** .
- Bekräfta borttagningen genom att ange resurs gruppens namn och klicka på **ta bort**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Ta bort vissa resurser i resurs gruppen

Du kan ta bort vissa Azure Arc-aktiverade data Services-resurser i en resurs grupp i Azure Portal genom att göra följande:

- Bläddra till resurs gruppen i den Azure Portal där Azure Arc-aktiverade data Services-resurser har skapats.
- Markera alla resurser som ska tas bort.
- Klicka på knappen Ta bort.
- Bekräfta borttagningen genom att skriva "Ja" och klicka på **ta bort**.

## <a name="delete-resources-using-the-azure-cli"></a>Ta bort resurser med Azure CLI

Du kan ta bort vissa Azure Arc-aktiverade data Services-resurser med hjälp av Azure CLI.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Ta bort SQL Managed Instance-resurser med hjälp av Azure CLI

Om du vill ta bort SQL-hanterade instans resurser från Azure med hjälp av Azure CLI ersätter du plats hållarnas värden i kommandot nedan och kör det.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Ta bort PostgreSQL-resurser för storskalig Server grupp med hjälp av Azure CLI

För att ta bort en PostgreSQL Server grupp resurs från Azure med Azure CLI ersätter du plats hållarna i kommandot nedan och kör det.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Ta bort Azure Arc data Controller-resurser med Azure CLI

> [!NOTE]
> Innan du tar bort en Azure Arc-datakontrollant bör du ta bort alla databas instans resurser som den hanterar.

Om du vill ta bort en Azure båge-dataenhet från Azure med Azure CLI ersätter du plats hållarna i kommandot nedan och kör det.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Ta bort en resurs grupp med Azure CLI

Du kan också använda Azure CLI för att [ta bort en resurs grupp](../../azure-resource-manager/management/delete-resource-group.md).