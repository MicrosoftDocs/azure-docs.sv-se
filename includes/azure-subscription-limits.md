---
title: ta med fil
description: ta med fil
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557965"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella processorer per [prenumeration](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10 000 |
| [Medadministratörer](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per prenumeration |200 |200 |
| [Lagrings konton](../articles/storage/common/storage-account-create.md) per prenumeration<sup>2</sup> |100 |100 |
| [Molntjänster](../articles/cloud-services/cloud-services-choose-me.md) per prenumeration |20 |200 |
| [Lokala nätverk](/previous-versions/azure/reference/jj157100(v=azure.100)) per prenumeration |10 |500 |
| DNS-servrar per prenumeration |9 |100 |
| Reserverade IP-adresser per prenumeration |20 |100 |
| [Tillhörighetsgrupper](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) per prenumeration |256 |256 |
| Längd på prenumerations namn (tecken) | 64 | 64 |

<sup>1</sup> Ytterligare små instanser räknas som en vCPU mot vCPU-gränsen trots att en partiell processor kärna används.

<sup>2</sup> Gränsen för lagrings kontot omfattar både standard-och Premium lagrings konton.