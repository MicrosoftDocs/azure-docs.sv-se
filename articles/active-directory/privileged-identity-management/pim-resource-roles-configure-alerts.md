---
title: Konfigurera säkerhets aviseringar för Azures resurs roller i Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhets aviseringar för Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743770"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurera säkerhets aviseringar för Azures resurs roller i Privileged Identity Management

Privileged Identity Management (PIM) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din Azure Active Directory (Azure AD) organisation. När en avisering utlöses visas den på sidan aviseringar.

![Azure-resurser – aviseringar Visa avisering, risk nivå och antal](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Granska aviseringar

Välj en avisering om du vill visa en rapport som visar de användare eller roller som utlöste aviseringen, tillsammans med reparations vägledning.

![Varnings rapport om senaste genomsöknings tid, beskrivning, minsknings steg, typ, allvarlighets grad, säkerhets påverkan och hur du förhindrar nästa gång](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Aviseringar

| Varning | Allvarlighetsgrad | Utlösare | Rekommendation |
| --- | --- | --- | --- |
| **För många ägare har tilldelats till en resurs** |Medel |För många användare har ägar rollen. |Granska användarna i listan och tilldela om några till mindre privilegierade roller. |
| **För många permanenta ägare har tilldelats till en resurs** |Medel |För många användare tilldelas permanent till en roll. |Granska användarna i listan och tilldela om några för att kräva aktivering för roll användning. |
| **En duplicerad roll har skapats** |Medel |Flera roller har samma villkor. |Använd endast en av dessa roller. |

### <a name="severity"></a>Allvarlighetsgrad

- **Hög**: kräver omedelbar åtgärd på grund av en princip överträdelse. 
- **Medels Tor**: kräver ingen omedelbar åtgärd, men signalerar en eventuell princip överträdelse.
- **Låg**: kräver ingen omedelbar åtgärd, men föreslår en önskad princip ändring.

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhets aviserings inställningar

Gå till **Inställningar** på sidan aviseringar.

![Sidan aviseringar med markerade inställningar](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Anpassa inställningarna för de olika aviseringarna så att de fungerar med dina miljö-och säkerhets mål.

![Inställnings sida för en avisering för att aktivera och konfigurera inställningar](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
