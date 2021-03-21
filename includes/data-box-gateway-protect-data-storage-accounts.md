---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583067"
---
Enheten associeras med ett lagringskonto som används som mål för dina data i Azure. Åtkomsten till lagringskontot styrs via prenumerationen och två lagringsåtkomstnycklar på 512 bitar som är kopplade till lagringskontot.

En av nycklarna används för autentisering när Azure Stack Edge-enheten ansluter till lagringskontot. Den andra nyckeln sparas i reserv så du kan rotera nycklarna med jämna mellanrum.

Många datacenter kräver nyckelrotation av säkerhetsskäl. Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din kontonyckel noga. Distribuera inte lösen ordet till andra användare, hårdkoda det eller spara det var som helst i vanlig text som är tillgängligt för andra.
- Återskapa din konto nyckel via Azure Portal om du tror att den kan komprometteras. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../articles/storage/common/storage-account-keys-manage.md).
- Azure-administratören bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att komma åt lagrings kontot direkt.