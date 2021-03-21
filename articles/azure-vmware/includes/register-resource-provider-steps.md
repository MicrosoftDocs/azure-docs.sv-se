---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555906"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration. Mer information om resurs leverantörer finns i [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** på menyn i Azure-portalen.

1. I rutan **alla tjänster** anger du **prenumeration** och väljer sedan **prenumerationer**.

1. Välj den prenumeration i listan prenumeration som du vill visa.

1. Välj **resurs leverantörer** och ange **Microsoft. AVS** i sökningen. 
 
1. Om resurs leverantören inte är registrerad väljer du **Registrera**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Logga in på Azure-prenumerationen som du använder för distributionen av Azure VMware-lösningen via Azure CLI. Registrera `Microsoft.AVS` resurs leverantören med [AZ Provider register](/cli/azure/provider#az_provider_register) kommando:

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Du kan använda kommandot [AZ Provider List](/cli/azure/provider#az_provider_list) för att se alla tillgängliga providers.

---


 
