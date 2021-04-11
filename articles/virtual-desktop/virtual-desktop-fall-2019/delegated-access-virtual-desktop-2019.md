---
title: Delegerad åtkomst i Windows Virtual Desktop (klassisk) – Azure
description: Så här delegerar du administrativa funktioner på en Windows Virtual Desktop-distribution (klassisk), inklusive exempel.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d683548c32baa37f9657541d90d7a58561a1d723
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445031"
---
# <a name="delegated-access-in-windows-virtual-desktop-classic"></a>Delegerad åtkomst i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../delegated-access-virtual-desktop.md).

Windows Virtual Desktop har en delegerad åtkomst modell som gör att du kan definiera hur mycket åtkomst en viss användare får ha genom att tilldela dem en roll. En roll tilldelning har tre komponenter: säkerhets objekt, roll definition och omfattning. Den delegerade åtkomst modellen för Windows Virtual Desktop baseras på Azure RBAC-modellen. Mer information om de olika roll tilldelningarna och deras komponenter finns i [Översikt över Azure rollbaserad åtkomst kontroll](../../role-based-access-control/built-in-roles.md).

Windows Virtual Desktop-delegerad åtkomst stöder följande värden för varje element i roll tilldelningen:

* Säkerhetsobjekt
    * Användare
    * Tjänstens huvudnamn
* Rolldefinition
    * Inbyggda roller
* Omfång
    * Klient grupper
    * Klientorganisationer
    * Värdar för pooler
    * Appgrupper

## <a name="built-in-roles"></a>Inbyggda roller

Delegerad åtkomst i Windows Virtual Desktop har flera inbyggda roll definitioner som du kan tilldela till användare och tjänstens huvud namn.

* En RDS-ägare kan hantera allt, inklusive åtkomst till resurser.
* En RDS-deltagare kan hantera allt, men kan inte komma åt resurser.
* En RDS-läsare kan visa allt, men kan inte göra några ändringar.
* En RDS-operatör kan visa diagnostiska aktiviteter.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdletar för roll tilldelningar

Du kan köra följande cmdletar för att skapa, Visa och ta bort roll tilldelningar:

* **Get-RdsRoleAssignment** visar en lista över roll tilldelningar.
* **New-RdsRoleAssignment** skapar en ny roll tilldelning.
* **Remove-RdsRoleAssignment** tar bort roll tilldelningar.

### <a name="accepted-parameters"></a>Godkända parametrar

Du kan ändra de tre grundläggande cmdletarna med följande parametrar:

* **AadTenantId**: anger Azure Active Directory klient-ID som tjänstens huvud namn är medlem i.
* **AppGroupName**: namnet på gruppen med fjärr skrivbords appar.
* **Diagnostik**: anger diagnostisk omfattning. (Måste kombineras med antingen **infrastrukturen** eller **klient** parametrarna.)
* **HostPoolName**: namnet på poolen för fjärr skrivbords värden.
* **Infrastruktur**: anger infrastruktur omfånget.
* **RoleDefinitionName**: namnet på Fjärrskrivbordstjänster den rollbaserade rollbaserade åtkomst kontroll rollen som tilldelats användaren, gruppen eller appen. (Till exempel Fjärrskrivbordstjänster ägare, Fjärrskrivbordstjänster läsaren och så vidare.)
* **ServerPrincipleName**: namnet på det Azure Active Directory programmet.
* **SignInName**: användarens e-postadress eller User Principal Name.
* **TenantName**: namnet på fjärr skrivbords klienten.

## <a name="next-steps"></a>Nästa steg

En fullständig lista över PowerShell-cmdletar som varje roll kan använda finns i [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).

Rikt linjer för hur du konfigurerar en Windows Virtual Desktop-miljö finns i [Windows Virtual Desktop-miljö](environment-setup-2019.md).
