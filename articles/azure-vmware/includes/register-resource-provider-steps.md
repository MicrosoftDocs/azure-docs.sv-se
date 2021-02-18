---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653182"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration. Mer information om resurs leverantörer finns i [Azure Resource providers och-typer](/azure/azure-resource-manager/management/resource-providers-and-types).

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
