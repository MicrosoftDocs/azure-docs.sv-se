---
title: Tagga en virtuell dator med hjälp av en mall
description: Lär dig mer om att tagga en virtuell dator med hjälp av en mall.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897382"
---
# <a name="tagging-a-vm-using-a-template"></a>Tagga en virtuell dator med en mall

Den här artikeln beskriver hur du taggar en virtuell dator i Azure med hjälp av en Resource Manager-mall. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs.

[Den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) placerar taggar på följande resurser: Compute (virtuell dator), lagring (lagrings konto) och nätverk (offentlig IP-adress, Virtual Network och nätverks gränssnitt). Den här mallen gäller för en virtuell Windows-dator men kan anpassas för virtuella Linux-datorer.

Klicka på knappen **distribuera till Azure** från [länken mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). På så sätt navigerar du till [Azure Portal](https://portal.azure.com/) där du kan distribuera den här mallen.

![Enkel distribution med Taggar](./media/tag/deploy-to-azure-tags.png)

Den här mallen innehåller följande Taggar: *avdelning*, *program* och *skapade av*. Du kan lägga till/redigera taggarna direkt i mallen om du vill ha olika taggnamn.

![Azure-Taggar i en mall](./media/tag/azure-tags-in-a-template.png)

Som du kan se definieras taggarna som nyckel/värde-par, avgränsade med kolon (:). Taggarna måste definieras i följande format:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Spara mallfilen när du är klar med att redigera den med de taggar du väljer.

Sedan kan du fylla i värdena för taggarna i avsnittet **Redigera parametrar** .

![Redigera taggar i Azure Portal](./media/tag/edit-tags-in-azure-portal.png)

Klicka på **skapa** för att distribuera den här mallen med dina märkes värden.

### <a name="next-steps"></a>Nästa steg

- Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) och [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).
- Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [förstå din Azure-faktura](../cost-management-billing/understand/review-individual-bill.md).
