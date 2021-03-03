---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750312"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration. Mer information om resurs leverantörer finns i [Azure Resource providers och-typer](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure Portal
 
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** på menyn i Azure-portalen.

1. I rutan **alla tjänster** anger du **prenumeration** och väljer sedan **prenumerationer**.

1. Välj den prenumeration i listan prenumeration som du vill visa.

1. Välj **resurs leverantörer** och ange **Microsoft. AVS** i sökningen. 
 
1. Om resurs leverantören inte är registrerad väljer du **Registrera**.