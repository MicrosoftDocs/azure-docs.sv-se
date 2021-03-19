---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82562140"
---
Enheten associeras med ett lagringskonto som används som mål för dina data i Azure. Åtkomsten till lagringskontot styrs via prenumerationen och två lagringsåtkomstnycklar på 512 bitar som är kopplade till lagringskontot.

En av nycklarna används för autentisering när Azure Stack Edge-enheten ansluter till lagringskontot. Den andra nyckeln sparas i reserv så du kan rotera nycklarna med jämna mellanrum.

Många datacenter kräver nyckelrotation av säkerhetsskäl. Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din kontonyckel noga. Sprid inte lösenordet till andra användare, hårdkoda det eller spara det i klartext där andra kan se.
- Återskapa din konto nyckel via Azure Portal om du tror att den kan komprometteras. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../articles/storage/common/storage-account-keys-manage.md).
- Azure-administratören bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att komma åt lagrings kontot direkt.