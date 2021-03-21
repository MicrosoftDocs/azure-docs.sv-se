---
title: Använd Azures anpassade roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du använder Azures anpassade roller i Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88782608"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Använd Azures anpassade roller i Privileged Identity Management

Du kan behöva använda inställningar för strikt Privileged Identity Management (PIM) för vissa användare i en privilegie rad roll i din Azure Active Directory (Azure AD)-organisation, samtidigt som de ger större självständighet för andra. Överväg till exempel ett scenario där din organisation anlitar flera kontrakt för att hjälpa till med utvecklingen av ett program som kommer att köras i en Azure-prenumeration.

Som resurs administratör vill du att personalen ska vara berättigad till åtkomst utan att kräva godkännande. Dock måste alla kontrakts partner godkännas när de begär åtkomst till organisationens resurser.

Följ stegen som beskrivs i nästa avsnitt för att konfigurera riktade Privileged Identity Management inställningar för Azure-resurs roller.

## <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Om du vill skapa en anpassad roll för en resurs följer du stegen som beskrivs i [Azures anpassade roller](../../role-based-access-control/custom-roles.md).

När du skapar en anpassad roll ska du inkludera ett beskrivande namn så att du enkelt kan komma ihåg vilken inbyggd roll du avsåg att duplicera.

> [!NOTE]
> Se till att den anpassade rollen är en dubblett av den inbyggda rollen som du vill duplicera och att dess omfattning matchar den inbyggda rollen.

## <a name="apply-pim-settings"></a>Använd PIM-inställningar

När rollen har skapats i din Azure AD-organisation går du till sidan **Privileged Identity Management-Azure-resurser** i Azure Portal. Välj den resurs som rollen gäller för.

![Fönstret Privileged Identity Management-Azure-resurser](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurera Privileged Identity Management roll inställningar](pim-resource-roles-configure-role-settings.md) som ska gälla för dessa medlemmar i rollen.

Ange slutligen [roller](pim-resource-roles-assign-roles.md) för den distinkta gruppen med medlemmar som du vill ska vara mål för de här inställningarna.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Anpassade roller i Azure](../../role-based-access-control/custom-roles.md)