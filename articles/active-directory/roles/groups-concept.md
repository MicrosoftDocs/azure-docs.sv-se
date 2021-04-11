---
title: Använd moln grupper för att hantera roll tilldelningar i Azure Active Directory | Microsoft Docs
description: Förhandsgranska anpassade Azure AD-roller för att delegera identitets hantering. Hantera Azure Role-tilldelningar i Azure Portal, PowerShell eller Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a71a53cd2aff16102a54f5a24063615e02d11872
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169529"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Använd moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)

Azure Active Directory (Azure AD) presenterar en offentlig för hands version där du kan tilldela en moln grupp till inbyggda Azure AD-roller. Med den här funktionen kan du använda grupper för att bevilja administratörs åtkomst i Azure AD med minimal ansträngning från dina globala och privilegierade roll administratörer.

Tänk på det här exemplet: contoso har anlitat personer över geografiska områden för att hantera och återställa lösen ord för anställda i sin Azure AD-organisation. I stället för att be en privilegie rad roll administratör eller global administratör att tilldela supportavdelningen administratörs rollen till varje person individuellt, kan de skapa en Contoso_Helpdesk_Administrators grupp och tilldela den till rollen. När personer ansluter till gruppen tilldelas de rollen indirekt. Ditt befintliga styrnings arbets flöde kan sedan ta hand om godkännande processen och granskning av gruppens medlemskap för att säkerställa att endast legitima användare är medlemmar i gruppen och tilldelas därmed administrations rollen för supportavdelningen.

## <a name="how-this-feature-works"></a>Så här fungerar funktionen

Skapa en ny Microsoft 365 eller säkerhets grupp med egenskapen "isAssignableToRole" inställd på "true". Du kan också aktivera den här egenskapen när du skapar en grupp i Azure Portal genom att aktivera **Azure AD-roller kan tilldelas gruppen**. Oavsett hur du vill kan du tilldela gruppen till en eller flera Azure AD-roller på samma sätt som du tilldelar roller till användare. Högst 250 roll tilldelnings bara grupper kan skapas i en enda Azure AD-organisation (klient).

Om du inte vill att medlemmar i gruppen ska ha ständig åtkomst till rollen kan du använda Azure AD Privileged Identity Management. Tilldela en grupp som en berättigad medlem i en Azure AD-roll. Varje medlem i gruppen kan sedan ha tilldelningen aktive rad för den roll som gruppen är tilldelad till. De kan sedan aktivera sin roll tilldelning för en fast tids period.

> [!Note]
> Du måste ha en uppdaterad version av Privileged Identity Management för att kunna tilldela en grupp till Azure AD-rollen via PIM. Du kan vara på en äldre version av PIM eftersom din Azure AD-organisation använder Privileged Identity Management API. Kontakta aliaset pim_preview@microsoft.com för att flytta din organisation och uppdatera ditt API. Lär dig mer [om Azure AD-roller och-funktioner i PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Därför tillämpar vi skapandet av en särskild grupp för att tilldela den till en roll

Om en grupp har tilldelats en roll kan alla IT-administratörer som kan hantera grupp medlemskap också indirekt hantera medlemskapet för den rollen. Anta till exempel att en grupp Contoso_User_Administrators tilldelats rollen som användar konto administratör. En Exchange-administratör som kan ändra grupp medlemskap kan lägga till sig själva i Contoso_User_Administrators gruppen och på så sätt bli administratör för användar konton. Som du ser kan en administratör öka sin behörighet på ett sätt som du inte avsåg.

Med Azure AD kan du skydda en grupp som har tilldelats en roll med hjälp av en ny egenskap som heter isAssignableToRole för grupper. Endast moln grupper vars isAssignableToRole-egenskap har angetts till "true" vid skapande tillfället kan tilldelas en roll. Den här egenskapen är oföränderlig; När en grupp har skapats med den här egenskapen inställd på "true" går det inte att ändra den. Du kan inte ange egenskapen för en befintlig grupp.
Vi har utformat hur grupper tilldelas roller för att förhindra att den här typen av konflikter inträffar:

- Endast globala administratörer och privilegierade roll administratörer kan skapa en roll tilldelnings grupp (med egenskapen "isAssignableToRole" aktive rad).
- Det får inte vara en dynamisk Azure AD-grupp. det vill säga det måste ha en medlemskaps typ "tilldelad". Automatiserad ifyllning av dynamiska grupper kan leda till ett oönskat konto som läggs till i gruppen och tilldelas därmed rollen.
- Som standard kan endast globala administratörer och privilegierade roll administratörer hantera medlemskapet i en grupp som kan tilldelas av roller, men du kan delegera hanteringen av roll tilldelnings bara grupper genom att lägga till grupp ägare.
- För att förhindra rättighets ökning kan autentiseringsuppgifterna för medlemmar och ägare av en roll tilldelnings bara grupp ändras av en privilegie rad autentiserings administratör eller global administratör.
- Ingen kapsling. Det går inte att lägga till en grupp som medlem i en grupp som kan tilldelas av roller.

## <a name="limitations"></a>Begränsningar

Följande scenarier stöds inte just nu:  

- Tilldela lokala grupper till Azure AD-roller (inbyggda eller anpassade)

## <a name="known-issues"></a>Kända problem

- *Endast Azure AD P2-licensierade kunder*: Tilldela inte en grupp som aktiv till en roll via både Azure AD och PRIVILEGED Identity Management (PIM). Tilldela särskilt en roll till en roll tilldelnings grupp när den skapas *och* tilldela en roll till gruppen med hjälp av PIM senare. Detta leder till problem där användare inte kan se sina aktiva roll tilldelningar i PIM samt möjligheten att ta bort PIM-tilldelningen. Kvalificerade tilldelningar påverkas inte i det här scenariot. Om du försöker utföra den här tilldelningen kan du se oväntad funktion, till exempel:
  - Slut tiden för roll tilldelningen kan visas felaktigt.
  - I PIM-portalen kan **Mina roller** endast visa en roll tilldelning, oavsett hur många metoder som tilldelningen beviljas med (via en eller flera grupper och direkt).
- Funktionen **Aktivera mellanlagrad distribution för hanterad användar inloggning** stöder inte tilldelning via grupp.
- *Endast Azure AD P2-licensierade kunder* Även om du har tagit bort gruppen visas fortfarande en berättigad medlem av rollen i PIM-ANVÄNDARGRÄNSSNITTET. Det finns inget problem. Det är bara ett cache-problem i Azure Portal.  
- Använd det nya [administrations centret för Exchange](https://admin.exchange.microsoft.com/) för roll tilldelningar via grupp medlemskap. Det gamla administrations centret för Exchange har inte stöd för den här funktionen ännu. Exchange PowerShell-cmdletar fungerar som förväntat.
- Azure Information Protection portal (den klassiska portalen) känner inte igen roll medlemskap via gruppen än. Du kan [migrera till en enhetlig känslighets etikett plattform](/azure/information-protection/configure-policy-migrate-labels) och sedan använda Office 365 Security & Compliance Center för att använda grupp tilldelningar för att hantera roller.
- [Administrations Center för appar](https://config.office.com/) stöder inte den här funktionen ännu. Tilldela användare direkt till administratörs rollen Office-appar.

Vi åtgärdar problemen.

## <a name="required-license-plan"></a>Obligatorisk licens plan

Om du använder den här funktionen måste du ha en tillgänglig Azure AD Premium P1-licens i din Azure AD-organisation. Om du vill använda Privileged Identity Management för just-in-Time-rolltjänsten måste du ha en tillgänglig Azure AD Premium P2-licens. Information om rätt licens för dina krav finns i [jämföra allmänt tillgängliga funktioner i kostnads fria och Premium-planer](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Nästa steg

- [Skapa en rolltilldelningsbar grupp](groups-create-eligible.md)
- [Tilldela en roll till en roll tilldelnings grupp](groups-assign-role.md)
