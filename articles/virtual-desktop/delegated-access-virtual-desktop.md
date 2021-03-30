---
title: Delegerad åtkomst i Windows Virtual Desktop – Azure
description: Så här delegerar du administrativa funktioner på en distribution av virtuella Windows-datorer, inklusive exempel.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2aa2c74704cf89c082d2837b39e82902efa0a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010063"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegerad åtkomst i Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).

Windows Virtual Desktop har en delegerad åtkomst modell som gör att du kan definiera hur mycket åtkomst en viss användare får ha genom att tilldela dem en roll. En roll tilldelning har tre komponenter: säkerhets objekt, roll definition och omfattning. Den delegerade åtkomst modellen för Windows Virtual Desktop baseras på Azure RBAC-modellen. Mer information om de olika roll tilldelningarna och deras komponenter finns i [Översikt över Azure rollbaserad åtkomst kontroll](../role-based-access-control/built-in-roles.md).

Windows Virtual Desktop-delegerad åtkomst stöder följande värden för varje element i roll tilldelningen:

* Säkerhetsobjekt
    * Användare
    * Användargrupper
    * Tjänstens huvudnamn
* Rolldefinition
    * Inbyggda roller
    * Anpassade roller
* Omfång
    * Värdar för pooler
    * Appgrupper
    * Arbetsytor

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdletar för roll tilldelningar

Innan du börjar ska du kontrol lera att du följer anvisningarna i [Konfigurera PowerShell-modulen](powershell-module.md) för att konfigurera Windows Virtual Desktop PowerShell-modulen om du inte redan har gjort det.

Windows Virtual Desktop använder Azures rollbaserad åtkomst kontroll (Azure RBAC) när du publicerar program grupper till användare eller användar grupper. Användar rollen för Skriv bords virtualisering tilldelas till användaren eller användar gruppen och omfånget är app-gruppen. Den här rollen ger användaren särskilda data åtkomst till app-gruppen.

Kör följande cmdlet för att lägga till Azure Active Directory användare i en app-grupp:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Kör följande cmdlet för att lägga till Azure Active Directory användar grupp i en app-grupp:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>Nästa steg

En fullständig lista över PowerShell-cmdletar som varje roll kan använda finns i [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).

En fullständig lista över roller som stöds i Azure RBAC finns i [inbyggda roller i Azure](../role-based-access-control/built-in-roles.md).

Rikt linjer för hur du konfigurerar en Windows Virtual Desktop-miljö finns i [Windows Virtual Desktop-miljö](environment-setup.md).
