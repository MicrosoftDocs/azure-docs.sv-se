---
title: Appens medgivande behörigheter för anpassade roller i Azure Active Directory | Microsoft Docs
description: Förhandsgranska appens medgivande behörigheter för anpassade Azure AD-roller i Azure Portal, PowerShell eller Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740116"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Appens medgivande behörigheter för anpassade roller i Azure Active Directory

Den här artikeln innehåller de behörigheter som för närvarande är tillgängliga för anpassade roll definitioner i Azure Active Directory (Azure AD). I den här artikeln hittar du de behörigheter som krävs för några vanliga scenarier som rör medgivande och behörigheter för appar.

## <a name="required-license-plan"></a>Obligatorisk licens plan

Om du använder den här funktionen krävs en Azure AD Premium P1-licens för din Azure AD-organisation. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Behörigheter för appmedgivande

Använd de behörigheter som anges i den här artikeln för att hantera principer för program medgivande, samt behörighet att bevilja appar.

> [!NOTE]
> Azure AD admin-portalen har ännu inte stöd för att lägga till de behörigheter som anges i den här artikeln i en anpassad katalog roll definition. Du måste [använda Azure AD PowerShell för att skapa en anpassad katalog roll](custom-create.md#create-a-role-using-powershell) med de behörigheter som anges i den här artikeln.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Bevilja delegerade behörigheter till appar för egen räkning (användar medgivande)

För att tillåta användare att bevilja medgivande till program åt sig själva (användar medgivande), beroende på en princip för program medgivande.

- Microsoft. Directory/Service princip ALS/managePermissionGrantsForSelf. identitet

Där `{id}` ersätts av ID: t för en [app medgivande-princip](../manage-apps/manage-app-consent-policies.md) som anger de villkor som måste uppfyllas för att den här behörigheten ska vara aktiv.

Om du till exempel vill tillåta användare att bevilja medgivande för deras egen räkning, kan `microsoft-user-default-low` du använda behörigheten med hjälp av den inbyggda appens medgivande princip med ID `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Bevilja behörighet till appar för alla (administrativt medgivande)

Så här delegerar du klientens administrativa medgivande till appar för både delegerade behörigheter och program behörigheter (app-roller):

- Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. identitet

Där `{id}` ersätts av ID: t för en [app medgivande-princip](../manage-apps/manage-app-consent-policies.md) som anger de villkor som måste uppfyllas för att den här behörigheten ska kunna användas.

Om du till exempel vill tillåta att rollen tilldelar klient organisationens administratörs medgivande till appar som omfattas av en anpassad [princip för program medgivande](../manage-apps/manage-app-consent-policies.md) med ID `low-risk-any-app` , använder du behörigheten `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` .

### <a name="managing-app-consent-policies"></a>Hantera principer för program medgivande

Delegera skapande, uppdatering och borttagning av principer för [program medgivande](../manage-apps/manage-app-consent-policies.md).

- Microsoft. Directory/permissionGrantPolicies/Create
- Microsoft. Directory/permissionGrantPolicies/standard/Read
- Microsoft. Directory/permissionGrantPolicies/Basic/Update
- Microsoft. Directory/permissionGrantPolicies/Delete

## <a name="full-list-of-permissions"></a>Fullständig lista över behörigheter

Behörighet | Beskrivning
---------- | -----------
Microsoft. Directory/Service princip ALS/managePermissionGrantsForSelf. identitet | Ger möjlighet att samtycka till appar för egen räkning (användar medgivande), beroende på appens medgivande princip `{id}` .
Microsoft. Directory/Service princip ALS/managePermissionGrantsForAll. identitet | Beviljar behörighet att samtycka till appar för alla (klient organisationens administratörs medgivande), beroende på appens medgivande princip `{id}` .
Microsoft. Directory/permissionGrantPolicies/standard/Read | Ger möjlighet att läsa principer för program medgivande.
Microsoft. Directory/permissionGrantPolicies/Basic/Update | Ger möjlighet att uppdatera grundläggande egenskaper för befintliga principer för program medgivande.
Microsoft. Directory/permissionGrantPolicies/Create | Ger möjlighet att skapa principer för program medgivande.
Microsoft. Directory/permissionGrantPolicies/Delete | Ger möjlighet att ta bort principer för program medgivande.

## <a name="next-steps"></a>Nästa steg

- [Skapa anpassade roller med hjälp av Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa tilldelningarna för en anpassad roll](../roles/view-assignments.md)
