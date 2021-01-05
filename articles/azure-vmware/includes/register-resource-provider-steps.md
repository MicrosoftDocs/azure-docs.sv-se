---
title: Registrera resurs leverantören för Azure VMware-lösningen
description: Steg för att registrera resurs leverantören för Azure VMware-lösningen.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770847"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Om du vill använda Azure VMware-lösningen måste du först registrera resurs leverantören med din prenumeration.  

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