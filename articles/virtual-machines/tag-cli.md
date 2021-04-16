---
title: Tagga en virtuell Azure-dator med hjälp av Azure CLI
description: Lär dig mer om att tagga en virtuell dator med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20bb4ab622a01646bcc61d0f691c514a25a06edc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502614"
---
# <a name="how-to-tag-a-vm-using-the-azure-cli"></a>Tagga en virtuell dator med hjälp av Azure CLI

I den här artikeln beskrivs hur du taggar en virtuell dator med hjälp av Azure CLI. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resursgrupp. Azure stöder för närvarande upp till 50 taggar per resurs och resursgrupp. Taggar kan placeras på en resurs när den skapas eller läggas till i en befintlig resurs. Du kan också tagga en virtuell dator med hjälp av Azure [PowerShell](tag-powershell.md).


Du kan visa alla egenskaper för en viss virtuell dator, inklusive taggarna, med hjälp av `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Om du vill lägga till en ny VM-tagg via Azure CLI kan du använda `azure vm update` kommandot tillsammans med taggparametern `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Om du vill ta bort taggar kan du `--remove` använda parametern i `azure vm update` kommandot .

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nu när vi har tillämpat taggar på våra resurser Azure CLI och portalen ska vi ta en titt på användningsinformationen för att se taggarna i faktureringsportalen.

### <a name="next-steps"></a>Nästa steg

- Mer information om hur du taggar Azure-resurser finns i [Azure Resource Manager Översikt](../azure-resource-manager/management/overview.md) och Använda taggar för att organisera [dina Azure-resurser.](../azure-resource-manager/management/tag-resources.md)
- Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [Förstå Din Azure-faktura.](../cost-management-billing/understand/review-individual-bill.md)
