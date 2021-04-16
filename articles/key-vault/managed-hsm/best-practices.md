---
title: Metodtips för att Azure Key Vault Managed HSM
description: Det här dokumentet beskriver några av de bästa metoderna för att använda Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482525"
---
# <a name="best-practices-when-using-managed-hsm"></a>Metodtips vid användning av Managed HSM

## <a name="control-access-to-your-managed-hsm"></a>Kontrollera åtkomsten till din hanterade HSM

Managed HSM är en molntjänst som skyddar krypteringsnycklar. Eftersom dessa nycklar är känsliga och affärskritiska bör du se till att skydda åtkomsten till dina hanterade HSM:er genom att endast tillåta auktoriserade program och användare. Den [här](access-control.md) artikeln innehåller en översikt över åtkomstmodellen. Här förklaras autentisering och auktorisering samt rollbaserad åtkomstkontroll.
- Skapa en [Azure Active Directory säkerhetsgrupp för](../../active-directory/fundamentals/active-directory-manage-groups.md) HSM-administratörerna (i stället för att tilldela rollen Administratör till enskilda användare). Detta förhindrar "utlåsning av administration" om ett enskilt konto tas bort.
- Lås åtkomst till dina hanteringsgrupper, prenumerationer, resursgrupper och hanterade HSM:er – Använd Azure RBAC för att styra åtkomsten till dina hanteringsgrupper, prenumerationer och resursgrupper
- Skapa rolltilldelningar per nyckel med hjälp [av hanterad HSM lokal RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac).
- Undvik att tilldela flera roller till samma huvudnamn för att upprätthålla uppdelningen av uppgifter. 
- Använd minsta behörighet för åtkomst till huvudnamn för att tilldela roller.
- Skapa en anpassad rolldefinition med en exakt uppsättning behörigheter.

## <a name="choose-regions-that-support-availability-zones"></a>Välja regioner som stöder tillgänglighetszoner

- För att säkerställa bästa möjliga tillgänglighet och zonåter återhämtning väljer du Azure-regioner [där Tillgänglighetszoner](../../availability-zones/az-overview.md) stöds. Dessa regioner visas som "Rekommenderade regioner" i Azure Portal.

## <a name="backup"></a>Backup

- Se till att du gör regelbundna säkerhetskopior av din HSM. Säkerhetskopieringar kan göras på HSM-nivå och för specifika nycklar. 

## <a name="turn-on-logging"></a>Aktivera loggning

- [Aktivera loggning](logging.md) för din HSM. Konfigurera även aviseringar.

## <a name="turn-on-recovery-options"></a>Aktivera återställningsalternativ

- [Mjuk borttagning](../general/soft-delete-overview.md) är på som standard.
- Aktivera rensningsskydd om du vill skydda mot tvingad borttagning av HSM även när mjuk borttagning har aktiverats.

## <a name="next-steps"></a>Nästa steg

- Se [Fullständig säkerhetskopiering/återställning](backup-restore.md) för information om fullständig säkerhetskopiering/återställning av HSM.
- Se [Hanterad HSM-loggning](logging.md) för att lära dig hur du använder Azure Monitor för att konfigurera loggning
- Se [Hantera hanterade HSM-nycklar](key-management.md) för nyckelhantering.
- Se [Hanterad HSM-rollhantering](role-management.md) för hantering av rolltilldelningar.
