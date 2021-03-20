---
title: Licens krav för att använda Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Beskriver licens kraven för att använda Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c9cd1c55f1b0dde173a7ffbeac92e5518db81e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005813"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licens krav för att använda Privileged Identity Management

Om du vill använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) måste en katalog ha en giltig licens. Dessutom måste licenser tilldelas till administratörer och relevanta användare. I den här artikeln beskrivs licens kraven för att använda Privileged Identity Management.

## <a name="valid-licenses"></a>Giltiga licenser

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="licenses-you-must-have"></a>Licenser du måste ha

Se till att katalogen har minst så många Azure AD Premium P2-licenser som du har anställda som ska utföra följande uppgifter:

- Användare som tilldelats som giltiga för Azure AD-eller Azure-roller som hanteras med PIM
- Användare som har tilldelats som berättigade medlemmar eller ägare av privilegierade åtkomst grupper
- Användare kan godkänna eller avvisa aktiverings begär anden i PIM
- Användare som tilldelats åtkomst granskning
- Användare som utför åtkomst granskningar

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Inga licenser krävs för användare som konfigurerar PIM, konfigurerar principer, tar emot aviseringar och ställer in åtkomst granskningar.

Mer information om licenser finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory portalen](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Exempel på licens scenarier

Här följer några exempel på licens scenarier som hjälper dig att fastställa antalet licenser som du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| Sparbanken bank har 10 administratörer för olika avdelningar och 2 globala administratörer som konfigurerar och hanterar PIM. De gör fem administratörer berättigade. | Fem licenser för administratörer som är berättigade | 5 |
| Graphic Design Institute har 25 administratörer av vilka 14 hanteras via PIM. Roll aktivering kräver godkännande och det finns tre olika användare i organisationen som kan godkänna aktiveringar. | 14 licenser för berättigade roller + tre god kännare | 17 |
| Contoso har 50 administratörer av vilka 42 hanteras via PIM. Roll aktivering kräver godkännande och det finns fem olika användare i organisationen som kan godkänna aktiveringar. Contoso visar också månads Visa de användare som har tilldelats administratörs roller och granskare, där de sex inte finns i administratörs roller som hanteras av PIM. | 42 licenser för de berättigade rollerna + fem god kännare + sex granskare | 53 |

## <a name="when-a-license-expires"></a>När en licens upphör att gälla

Om en Azure AD Premium P2, EMS E5 eller utvärderings licens upphör att gälla, kommer Privileged Identity Management funktioner inte längre att vara tillgängliga i din katalog:

- Permanenta roll tilldelningar till Azure AD-roller kommer inte att påverkas.
- Privileged Identity Management tjänst i Azure Portal, samt Graph API-cmdletar och PowerShell-gränssnitt för Privileged Identity Management, kommer inte längre att vara tillgängliga för användare att aktivera privilegierade roller, hantera privilegie rad åtkomst eller utföra åtkomst granskningar av privilegierade roller.
- Kvalificerade roll tilldelningar för Azure AD-roller tas bort eftersom användarna inte längre kan aktivera privilegierade roller.
- Pågående åtkomst granskningar av Azure AD-roller kommer att avslutas och Privileged Identity Management konfigurations inställningar tas bort.
- Privileged Identity Management kommer inte längre att skicka e-postmeddelanden om roll tilldelnings ändringar.

## <a name="next-steps"></a>Nästa steg

- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
- [Börja använda Privileged Identity Management](pim-getting-started.md)
- [Roller som du inte kan hantera i Privileged Identity Management](pim-roles.md)
