---
title: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373766"
---
Innan du tilldelar en Azure RBAC-roll till ett säkerhets objekt bestämmer du åtkomst omfånget som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget. Azure RBAC-roller som definierats i ett bredare omfång ärvs av resurserna under dem.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure blob-och Queue-resurser, från och med det snävaste omfånget:

- **En enskild behållare.** I det här omfånget gäller en roll tilldelning för alla blobar i behållaren, samt behållar egenskaper och metadata.
- **En enskild kö.** I det här omfånget gäller en roll tilldelning för meddelanden i kön, samt köegenskaper och metadata.
- **Lagrings kontot.** I det här omfånget gäller en roll tilldelning för alla behållare och deras blobbar, eller till alla köer och deras meddelanden.
- **Resursgruppen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i resurs gruppen.
- **Prenumerationen.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i prenumerationen.
- **En hanterings grupp.** I det här omfånget gäller en roll tilldelning för alla behållare eller köer i alla lagrings konton i alla resurs grupper i alla prenumerationer i hanterings gruppen.

Mer information om roll tilldelningar och omfång för Azure finns i [Vad är Azures rollbaserad åtkomst kontroll (Azure RBAC)?](../articles/role-based-access-control/overview.md).
