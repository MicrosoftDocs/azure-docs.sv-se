---
title: Tagga en virtuell Azure-dator med hjälp av CLI
description: Lär dig mer om att tagga en virtuell dator med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897450"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Tagga en virtuell dator med hjälp av CLI

Den här artikeln beskriver hur du taggar en virtuell dator med hjälp av Azure CLI. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs. Du kan också tagga en virtuell dator med hjälp av Azure [PowerShell](tag-powershell.md).


Du kan visa alla egenskaper för en specifik virtuell dator, inklusive taggar, med hjälp av `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Om du vill lägga till en ny virtuell dator med hjälp av Azure CLI kan du använda `azure vm update` kommandot tillsammans med tag-parametern `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar kan du använda- `--remove` parametern i `azure vm update` kommandot.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nu när vi har använt taggar till våra resurser Azure CLI och portalen, tar vi en titt på användnings informationen för att se taggarna i fakturerings portalen.

### <a name="next-steps"></a>Nästa steg

- Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) och [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).
- Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [förstå din Azure-faktura](../cost-management-billing/understand/review-individual-bill.md).
