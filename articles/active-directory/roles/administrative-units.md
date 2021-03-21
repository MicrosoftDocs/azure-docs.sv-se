---
title: Administrativa enheter i Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för mer detaljerad delegering av behörigheter i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558023"
---
# <a name="administrative-units-in-azure-active-directory"></a>Administrativa enheter i Azure Active Directory

I den här artikeln beskrivs administrativa enheter i Azure Active Directory (Azure AD). En administrativ enhet är en Azure AD-resurs som kan vara en behållare för andra Azure AD-resurser. En administrativ enhet kan bara innehålla användare och grupper.

Administrativa enheter begränsar behörigheter i en roll till någon del av din organisation som du definierar. Du kan till exempel använda administrativa enheter för att delegera [Administratörs](permissions-reference.md#helpdesk-administrator) rollen för supportavdelningen till regionala support specialister, så att de bara kan hantera användare i den region som de stöder.

## <a name="deployment-scenario"></a>Distributionsscenario

Det kan vara användbart att begränsa administrativ omfattning genom att använda administrativa enheter i organisationer som består av oberoende avdelningar av något slag. Överväg exemplet på en stor University som består av många autonoma skolor (skola, skola och så vidare). Varje skola har en grupp IT-administratörer som kontrollerar åtkomst, hanterar användare och ställer in principer för sin skola.

En central administratör kan:

- Skapa en roll med administratörs behörighet enbart för Azure AD-användare i den administrativa enheten för affärs skolan.
- Skapa en administrativ enhet för affärs skolan.
- Fyll i den administrativa enheten med endast affärs studenter och personal.
- Lägg till IT-teamet för affärs skolan i rollen, tillsammans med dess omfattning.

## <a name="license-requirements"></a>Licenskrav

Om du vill använda administrativa enheter behöver du en Azure Active Directory Premium-licens för varje administrativ enhets administratör och Azure Active Directory Free licenser för administrativa enhets medlemmar. Mer information finns i [komma igång med Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Hantera administrativa enheter

Du kan hantera administrativa enheter med hjälp av Azure Portal, PowerShell-cmdletar och skript, eller Microsoft Graph. Mer information finns i:

- [Skapa, ta bort, fylla i och lägga till roller i administrativa enheter](admin-units-manage.md): innehåller fullständiga instruktions procedurer.
- [Arbeta med administrativa enheter](/powershell/azure/active-directory/working-with-administrative-units): beskriver hur du arbetar med administrativa enheter med hjälp av PowerShell.
- [Stöd för diagram över administrativa enheter](/graph/api/resources/administrativeunit): innehåller detaljerad dokumentation om Microsoft Graph för administrativa enheter.

### <a name="plan-your-administrative-units"></a>Planera dina administrativa enheter

Du kan använda administrativa enheter för att logiskt gruppera Azure AD-resurser. En organisation vars IT-avdelning är spridd globalt kan skapa administrativa enheter som definierar relevanta geografiska gränser. I ett annat scenario, där en global organisation har under organisationer som är halv autonom i deras verksamhet, kan administrativa enheter representera underorganisationarna.

De kriterier som de administrativa enheterna skapas i vägleds av de unika kraven för en organisation. Administrativa enheter är ett vanligt sätt att definiera strukturen mellan Microsoft 365 tjänster. Vi rekommenderar att du förbereder dina administrativa enheter med deras användning i Microsoft 365 tjänster i åtanke. Du kan få det maximala värdet av administrativa enheter när du kan koppla vanliga resurser mellan Microsoft 365 under en administrativ enhet.

Du kan vänta dig att skapa administrativa enheter i organisationen för att gå igenom följande steg:

1. **Första införande**: din organisation kommer att börja skapa administrativa enheter baserat på inledande kriterier och antalet administrativa enheter ökar när villkoren är raffinerade.
1. **Rensning**: när villkoren har definierats tas administrativa enheter som inte längre behövs bort.
1. **Stabilisering**: organisationens struktur definieras och antalet administrativa enheter kommer inte att ändras avsevärt på kort sikt.

## <a name="currently-supported-scenarios"></a>Scenarier som stöds för närvarande

Som global administratör eller administratör för privilegierade roller kan du använda Azure AD-portalen för att:

- Skapa administrativa enheter
- Lägg till användare och grupper medlemmar i administrativa enheter
- Tilldela IT-personal till administrativa roller för enhets omfångs administratörer.

Administrativa enhets omfångs administratörer kan använda Microsoft 365 administrations Center för grundläggande hantering av användare i deras administrativa enheter. En grupp administratör med administrativ enhets omfattning kan hantera grupper med hjälp av PowerShell, Microsoft Graph och Microsoft 365 administrations Center.

>[!Note]
>Endast de funktioner som beskrivs i det här avsnittet är tillgängliga i Microsoft 365 administrations centret. Det finns inga tillgängliga funktioner på organisations nivå för en Azure AD-roll med det administrativa enhets omfånget.

I följande avsnitt beskrivs aktuella stöd för administrativa enhets scenarier.

### <a name="administrative-unit-management"></a>Hantering av administrativa enheter

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 |
| --- | --- | --- | --- |
| Skapa och ta bort administrativa enheter   |    Stöds    |   Stöds   |    Stöds inte |
| Lägga till och ta bort administrativa enhets medlemmar individuellt    |   Stöds    |   Stöds   |    Stöds inte |
| Lägga till och ta bort administrativa enhets medlemmar i bulk genom att använda CSV-filer   |    Stöds inte     |  Stöds   |    Ingen Support plan |
| Tilldela administratörer av administrativa enhets omfång  |     Stöds    |   Stöds    |   Stöds inte |
| Att lägga till och ta bort administrativa enhets medlemmar dynamiskt baserat på attribut | Stöds inte | Stöds inte | Stöds inte

### <a name="user-management"></a>Användarhantering

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 |
| --- | --- | --- | --- |
| Administrativ enhets hantering av användar egenskaper, lösen ord och licenser   |    Stöds     |  Stöds   |   Stöds |
| Administrativ enhet – definition av blockerade och avblockerade användar inloggningar    |   Stöds   |    Stöds   |    Stöds |
| Administrativ enhets begränsad hantering av autentiseringsuppgifter för användarens multifaktorautentisering   |    Stöds   |   Stöds   |   Stöds inte |

### <a name="group-management"></a>Grupphantering

| Behörigheter |   Graph/PowerShell   | Azure AD-Portal | Administrationscenter för Microsoft 365 |
| --- | --- | --- | --- |
| Administrativ enhets hantering av grupp egenskaper och medlemmar     |  Stöds   |    Stöds    |  Stöds inte |
| Administrativ enhets hantering av grupp licensiering   |    Stöds  |    Stöds   |   Stöds inte |

Administrativa enheter tillämpar endast omfång på hanterings behörigheter. De förhindrar inte att medlemmar eller administratörer använder sina [standard användar behörigheter](../fundamentals/users-default-permissions.md) för att bläddra bland andra användare, grupper eller resurser utanför den administrativa enheten. I Microsoft 365 administrations centret filtreras användare utanför en omfångs administratörs administrativa enheter. Men du kan söka efter andra användare i Azure AD-portalen, PowerShell och andra Microsoft-tjänster.

## <a name="next-steps"></a>Nästa steg

- [Hantera administrativa enheter](admin-units-manage.md)
- [Hantera användare i administrativa enheter](admin-units-add-manage-users.md)
- [Hantera grupper i administrativa enheter](admin-units-add-manage-groups.md)
- [Tilldela begränsade roller till en administrativ enhet](admin-units-assign-roles.md)
