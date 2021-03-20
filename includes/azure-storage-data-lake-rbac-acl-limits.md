---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017700"
---
| Mekanism | Omfång |Gränser | Behörighets nivå som stöds |
|---|---|---|---|
| Azure RBAC | Lagrings konton, behållare. <br>Kors resursens Azure Role-tilldelningar på prenumerations-eller resurs grupps nivå. | 2000 Azure-roll tilldelningar i en prenumeration | Azure-roller (inbyggt eller anpassat) |
| ACL| Katalog, fil |32 ACL-poster (effektivt 28 ACL-poster) per fil och per katalog. Åtkomst-och standard-ACL: er har sin egen begränsning för 32 ACL-poster. |ACL-behörighet|
