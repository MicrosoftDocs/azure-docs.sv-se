---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285217"
---
Enheten associeras med ett lagringskonto som används som mål för dina data i Azure. Åtkomsten till lagringskontot styrs via prenumerationen och två lagringsåtkomstnycklar på 512 bitar som är kopplade till lagringskontot.

En av nycklarna används för autentisering när Azure Stack Edge-enheten ansluter till lagringskontot. Den andra nyckeln sparas i reserv så du kan rotera nycklarna med jämna mellanrum.

Många datacenter kräver nyckelrotation av säkerhetsskäl. Vi rekommenderar att du följer dessa metod tips för nyckel rotation:

- Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Skydda din kontonyckel noga. Sprid inte lösenordet till andra användare, hårdkoda det eller spara det i klartext där andra kan se.
- [Återskapa din konto nyckel](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) via Azure Portal om du tror att den kan komprometteras.
- Azure-administratören bör regelbundet ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagrings avsnittet i Azure Portal för att komma åt lagrings kontot direkt.
- Du kan också använda en egen krypterings nyckel för att skydda data i ditt Azure Storage-konto. När du anger en kundhanterad nyckel används nyckeln för att skydda och kontrollera åtkomsten till nyckeln som krypterar dina data. Mer information om hur du skyddar dina data finns i [Aktivera Kundhanterade nycklar för ditt Azure Storage-konto](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
