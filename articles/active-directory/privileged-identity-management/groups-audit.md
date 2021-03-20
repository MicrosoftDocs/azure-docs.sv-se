---
title: Visa gransknings rapport för grupp tilldelningar för privilegierade användare i Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Visa aktivitet och gransknings historik för grupp tilldelningar för privilegierade åtkomst i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88141677"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Granska aktivitets historiken för grupp tilldelningar för privilegierade åtkomst (för hands version) i Privileged Identity Management

Med Privileged Identity Management (PIM) kan du Visa aktivitet, aktiveringar och gransknings historik för Azure Privileged Access Group-medlemmar och-ägare i din Azure Active Directory (Azure AD)-organisation.

> [!NOTE]
> Om din organisation har funktioner som har hanterats av en tjänst leverantör som använder [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md), visas inte roll tilldelningar som har auktoriserats av tjänste leverantören här.

Följ dessa steg om du vill visa gransknings historiken för privilegierade åtkomst grupper.

## <a name="view-resource-audit-history"></a>Visa resurs gransknings historik

**Resurs granskning** ger dig en översikt över all aktivitet som är associerad med dina privilegierade åtkomst grupper.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **privilegierade åtkomst grupper (förhands granskning)**.

1. Välj den privilegierade åtkomst grupp som du vill visa gransknings historik för.

1. Under **aktivitet** väljer du **resurs granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Resurs gransknings lista med filter](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Visa min granskning

I **min granskning** kan du Visa din personliga roll aktivitet för en privilegie rad åtkomst grupp.

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **privilegierade åtkomst grupper (förhands granskning)**.

1. Välj den privilegierade åtkomst grupp som du vill visa gransknings historik för.

1. Under **aktivitet** väljer du **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Gransknings lista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela grupp medlemskap och ägarskap i Privileged Identity Management](groups-assign-member-owner.md)
- [Godkänn eller neka begär Anden om privilegierade åtkomst grupper i PIM](groups-approval-workflow.md)
- [Visa gransknings historik för Azure AD-roller i PIM](groups-audit.md)
