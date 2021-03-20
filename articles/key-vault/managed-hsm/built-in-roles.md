---
title: Hanterad HSM lokala RBAC-inbyggda roller – Azure Key Vault | Microsoft Docs
description: En översikt över hanterade HSM-inbyggda roller som kan tilldelas till användare, tjänstens huvud namn, grupper och hanterade identiteter
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a5ecd8f13a3255a565168f62f358a6a38e3cbab4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94445225"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Hanterade HSM-lokala RBAC-inbyggda roller

Hanterad HSM lokal RBAC har flera inbyggda roller som du kan tilldela till användare, tjänstens huvud namn, grupper och hanterade identiteter. Om du vill tillåta ett huvud konto att utföra en åtgärd måste du tilldela dem en roll som ger dem behörighet att utföra åtgärden. Med alla dessa roller och åtgärder kan du bara hantera behörighet för data Plans åtgärder. Om du vill hantera kontroll Plans behörigheter för den hanterade HSM-resursen (till exempel skapa en ny hanterad HSM eller uppdatera, flytta och ta bort en befintlig) måste du använda [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="built-in-roles"></a>Inbyggda roller

|Rollnamn|Description|ID|
|---|---|---|
|Hanterad HSM-administratör| Ger fullständig åtkomst till alla data åtgärder.|a290e904-7015-4bba-90c8-60543313cdb4|
|Hanterad HSM-kryptografisk tjänsteman| Ger fullständig åtkomst till all nyckel hantering och kryptografiska krypterings åtgärder|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Hanterad HSM-kryptografi användare|Beviljar åtkomst för att skapa och använda nycklar för kryptografiska åtgärder. Det går inte att ta bort nycklar permanent.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Hanterad HSM-princip administratör| Ger behörighet att skapa och ta bort roll tilldelningar|4bd23610-CDCF-4971-bdee-bdc562cc28e4|
|Hanterad HSM-krypto|Ger Läs behörighet att läsa (men inte använda) nycklar|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Kryptering av hanterad HSM-krypto| Ger behörighet att använda en nyckel för tjänst kryptering. |33413926-3206-4cdd-b39a-83574fe37a17|
|Hanterad HSM-säkerhetskopiering| Ger behörighet att utföra en enskild nyckel eller en hel HSM-säkerhetskopiering. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Tillåtna åtgärder
> [!NOTE]  
> - Ett "X" anger att en roll tillåts utföra data åtgärden. En tom cell indikerar att rollen inte har pemission för att utföra denna data åtgärd.
> - Alla data åtgärds namn har prefixet Microsoft. managedHsm Vault/, som utelämnas i tabellerna nedan för det kortfattat.
> - Alla roll namn har prefixet "hanterad HSM", vilket utelämnas i tabellen nedan för det kortfattat.

|Data åtgärd | Administratör | Kryptografi utanordnare | Krypto-användare | Princip administratör | Kryptering av krypterings tjänst | Backup | Granskare av kryptografi|
|---|---|---|---|---|---|---|---|
|**Hantering av säkerhets domäner**|
/securitydomain/download/action|<center>Röntgen</center>||||||
/securitydomain/upload/action|<center>Röntgen</center>||||||
/securitydomain/upload/read|<center>Röntgen</center>||||||
/securitydomain/transferkey/read|<center>Röntgen</center>||||||
|**Nyckel hantering**|
|/keys/read/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||<center>Röntgen</center>||<center>Röntgen</center>|
|/keys/write/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|/keys/create|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|/keys/delete|<center>Röntgen</center>|<center>Röntgen</center>|||||
|/keys/deletedKeys/read/action|<center>Röntgen</center>|<center>Röntgen</center>|||||
|/keys/deletedKeys/recover/action|<center>Röntgen</center>|<center>Röntgen</center>|||||
|/keys/deletedKeys/delete|<center>Röntgen</center>|<center>Röntgen</center>|||||<center>Röntgen</center>|
|/keys/backup/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>|||<center>Röntgen</center>|
|/keys/restore/action|<center>Röntgen</center>|<center>Röntgen</center>|||||
|/keys/export/action|<center>Röntgen</center>|<center>Röntgen</center>|||||
|/keys/import/action|<center>Röntgen</center>|<center>Röntgen</center>|||||
|**Kryptografiska nyckel åtgärder**|
|/keys/encrypt/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|/keys/decrypt/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|/keys/wrap/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||<center>Röntgen</center>||
|/keys/unwrap/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||<center>Röntgen</center>||
|/keys/sign/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|/keys/verify/action|<center>Röntgen</center>|<center>Röntgen</center>|<center>Röntgen</center>||||
|**Roll hantering**|
|/roleAssignments/delete/action|<center>Röntgen</center>|||<center>Röntgen</center>|||
|/roleAssignments/read/action|<center>Röntgen</center>|||<center>Röntgen</center>|||
|/roleAssignments/write/action|<center>Röntgen</center>|||<center>Röntgen</center>|||
|/roleDefinitions/read/action|<center>Röntgen</center>|||<center>Röntgen</center>|||
|**Hantering av säkerhets kopiering/återställning**|
|/backup/start/action|<center>Röntgen</center>|||||<center>Röntgen</center>|
|/backup/status/action|<center>Röntgen</center>|||||<center>Röntgen</center>|
|/restore/start/action|<center>Röntgen</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Nästa steg

- Se en översikt över [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Se en självstudie om [hanterad HSM-roll hantering](role-management.md)
