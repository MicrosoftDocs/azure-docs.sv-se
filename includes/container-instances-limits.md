---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384843"
---
| Resurs | Gräns |
| --- | :--- |
| Standard-SKU behållar grupper per region per prenumeration | 100<sup>1</sup> |
| Dedikerade SKU container Groups per region per prenumeration | 0<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Standard SKU-kärnor (CPU: er) per region per prenumeration | 10<sup>1, 2</sup> | 
| Standard SKU-kärnor (CPU: er) för K80 GPU per region per prenumeration | 18<sup>-1, 2</sup> |
| Standard SKU-kärnor (CPU: er) för P100 eller V100 GPU per region per prenumeration | 0<sup>1, 2</sup> |
| Portar per IP-adress | 5 |
| Logg storlek för container instans-instans som körs | 4 MB |
| Logg storlek för container instans-stoppad instans | 16 KB-eller 1 000-linjer |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |


<sup>1</sup> Skapa ett [Azure-supportbegäran][azure-support]för att begära en gräns ökning. Kostnads fria prenumerationer, inklusive [kostnads fritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/) och [Azure för studenter](https://azure.microsoft.com/offers/ms-azr-0170p/) är inte berättigade till begränsning eller kvot ökningar. Om du har en kostnads fri prenumeration kan du [Uppgradera](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) till en prenumeration där du betalar per användning.<br />
<sup>2</sup> Standard gräns för prenumeration [enligt principen betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Gränsen kan skilja sig åt för andra kategori typer.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
