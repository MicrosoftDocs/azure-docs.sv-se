---
title: Använd portalen för att distribuera Azure-Virtual Machines
description: Använda Azure PowerShell för att distribuera dekor Virtual Machines för att spara pengar.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 42f2078e9781e50712344778a33ce8735b4ce11b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677351"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Distribuera Azure-Virtual Machines med hjälp av Azure Portal

Med hjälp av [Azure-Virtual Machines](spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azure-Virtual Machines. Därför är Azures Virtual Machines bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för Azure-Virtual Machines är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du ställer in högsta pris finns i [Azure-Virtual Machines – prissättning](spot-vms.md#pricing).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en virtuell Azure-dator kan anges i kronor (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.05701` vara ett max pris på $0,05701 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

När den virtuella datorn avlägsnas har du möjlighet att antingen ta bort den virtuella datorn och den underliggande disken eller frigöra den virtuella datorn så att den kan startas om senare.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

När du distribuerar en virtuell dator kan du välja att använda en Azure-tjänstinstans.


På fliken **grundläggande** i avsnittet **instans information** är **Nej** standard för användning av en Azure-tjänstinstans.

![Skärm bild där du väljer Nej, Använd inte en Azure-tjänstinstans](./media/spot-portal/no.png)

Om du väljer **Ja** expanderas avsnittet och du kan välja en princip för [borttagning och borttagning](spot-vms.md#eviction-policy). 

![Skärm bild för att välja Ja, Använd en Azure-tjänstinstans](./media/spot-portal/yes.png)

Du kan också jämföra priser och avlägsna priser med andra liknande regioner genom att välja **Visa pris historik och jämföra priser i närliggande regioner**.

I det här exemplet är den centrala Kanada-regionen billigare och har en lägre avtagnings hastighet än regionen USA, östra.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings frekvens.":::

Du kan ändra region genom att välja det alternativ som passar dig bäst och sedan välja **OK**.

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan [simulera en avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell Azure-dator för att testa hur bra ditt program kommer att reagera på en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa Azure-Virtual Machines med [PowerShell](./windows/spot-powershell.md), [CLI](./linux/spot-cli.md)eller en [mall](./linux/spot-template.md).
