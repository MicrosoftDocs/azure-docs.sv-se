---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 543aa50d72de5a06a9a1c7ac88ac5ecae993bc9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697918"
---
| Resurs | Gräns |
| --- | --- |
| Resurser per [resurs grupp](../articles/azure-resource-manager/management/overview.md#resource-groups) | Resurserna begränsas inte av resurs gruppen. De är i stället begränsade av resurs typen i en resurs grupp. Se nästa rad. |
| Resurser per resurs grupp, per resurs typ |800 – vissa resurs typer kan överskrida gränsen på 800. Se [resurser som inte är begränsade till 800 instanser per resurs grupp](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Distributioner per resurs grupp i distributions historiken |800<sup>1</sup> |
| Resurser per distribution |800 |
| Hanterings lås per unik [omfattning](../articles/azure-resource-manager/management/overview.md#understand-scope)  |20 |
| Antal Taggar per resurs eller resurs grupp |50 |
| Etikett nyckel längd |512 |
| Tagg värde längd |256 |

<sup>1</sup> Distributioner tas automatiskt bort från historiken när du närmar dig gränsen. Om du tar bort en post från distributions historiken påverkas inte de distribuerade resurserna. Mer information finns i [automatiska borttagningar från distributions historiken](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Mall gränser

| Värde | Gräns |
| --- | --- |
| Parametrar |256 |
| Variabler |256 |
| Resurser (inklusive antal exemplar) |800 |
| Utdata |64 |
| Mall uttryck |24 576 tecken |
| Resurser i exporterade mallar |200 |
| Mallens storlek |4 MB |
| Parameter fil storlek |64 kB |

Du kan överskrida vissa begränsningar för mallar genom att använda en kapslad mall. Mer information finns i [använda länkade mallar när du distribuerar Azure-resurser](../articles/azure-resource-manager/templates/linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden i ett objekt. Mer information finns i [objekt som parametrar](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).