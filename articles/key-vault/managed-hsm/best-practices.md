---
title: Metod tips med Azure Key Vault hanterad HSM
description: I det här dokumentet beskrivs några av de rekommenderade metoderna för att använda Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90996850"
---
# <a name="best-practices-when-using-managed-hsm"></a>Metod tips när du använder hanterad HSM

## <a name="control-access-to-your-managed-hsm"></a>Kontrol lera åtkomst till hanterad HSM

Hanterad HSM är en moln tjänst som skyddar krypterings nycklar. Eftersom dessa nycklar är känsliga och affärs kritiska bör du se till att skydda åtkomsten till dina hanterade HSM: er genom att bara tillåta behöriga program och användare. Den här [artikeln](access-control.md) ger en översikt över åtkomst modellen. Den förklarar autentisering och auktorisering och rollbaserad åtkomst kontroll.
- Skapa en [Azure Active Directory säkerhets grupp](../../active-directory/fundamentals/active-directory-manage-groups.md) för HSM-administratörer (i stället för att tilldela administratörs rollen till enskilda användare). På så sätt förhindras "administrations lås" om ett enskilt konto tas bort.
- Lås åtkomst till hanterings grupper, prenumerationer, resurs grupper och hanterade HSM: er – Använd Azure RBAC för att kontrol lera åtkomsten till dina hanterings grupper, prenumerationer och resurs grupper
- Skapa roll tilldelningar per nyckel med [hanterad HSM lokal RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Använd minsta behörighets åtkomst objekt för att tilldela roller

## <a name="choose-regions-that-support-availability-zones"></a>Välj regioner som har stöd för tillgänglighets zoner

- För att säkerställa hög tillgänglighet och zon återhämtning, väljer du Azure-regioner där [Tillgänglighetszoner](../../availability-zones/az-overview.md) stöds. Dessa regioner visas som "rekommenderade regioner" i Azure Portal.

## <a name="backup"></a>Backup

- Se till att du utför regelbundna säkerhets kopieringar av HSM. Säkerhets kopieringar kan göras på HSM-nivå och för vissa nycklar. 

## <a name="turn-on-logging"></a>Aktivera loggning

- [Aktivera loggning](logging.md) för HSM. Konfigurera även aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställnings alternativ

- [Mjuk borttagning](../general/soft-delete-overview.md) är aktiverat som standard.
- Aktivera rensnings skyddet om du vill skydda mot Tvingad borttagning av HSM även efter att mjuk borttagning har Aktiver ATS.

## <a name="next-steps"></a>Nästa steg

- Se [fullständig säkerhets kopiering/återställning](backup-restore.md) för information om fullständig HSM-säkerhetskopiering/återställning.
- Se [hanterad HSM-loggning](logging.md) för att lära dig hur du använder Azure Monitor för att konfigurera loggning
- Se [Hantera](key-management.md) HANTERAde HSM-nycklar för nyckel hantering.
- Se [hanterad HSM-roll hantering](role-management.md) för att hantera roll tilldelningar.
