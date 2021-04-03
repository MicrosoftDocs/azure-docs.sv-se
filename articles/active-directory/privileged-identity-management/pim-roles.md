---
title: Roller som du inte kan hantera i Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Beskriver de roller som du inte kan hantera i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372420"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Roller som du inte kan hantera i Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gör att du kan hantera alla [Azure AD-roller](../roles/permissions-reference.md) och [Azure-roller](../../role-based-access-control/built-in-roles.md). Azure-roller kan också innehålla dina anpassade roller som är kopplade till dina hanterings grupper, prenumerationer, resurs grupper och resurser. Det finns dock några roller som du inte kan hantera. Den här artikeln beskriver de roller som du inte kan hantera i Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Administratörsroller för klassiska prenumerationer

Du kan inte hantera följande klassiska prenumerations administratörs roller i Privileged Identity Management:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör

Mer information om administratörs rollerna för den klassiska prenumerationen finns i [klassiska prenumerations roller för prenumerationer, Azure-roller och administratörs roller för Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Vad gäller för Microsoft 365 administratörs roller?

Vi har stöd för alla Microsoft 365-roller i Azure AD-roller och-administratörer, till exempel Exchange-administratören och SharePoint-administratören, men vi stöder inte vissa roller i Exchange RBAC eller SharePoint RBAC. Mer information om dessa Microsoft 365-tjänster finns i [Microsoft 365 admin-roller](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Berättigade användare för administratörs rollen för SharePoint, enhets administratörs rollen och alla roller som försöker komma åt Microsoft Security och Compliance Center kan uppleva fördröjningar på upp till några timmar efter att ha aktiverat sin roll. Vi arbetar med dessa team för att åtgärda problemen.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)