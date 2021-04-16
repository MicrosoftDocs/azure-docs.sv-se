---
title: Använda molngrupper för att hantera rolltilldelningar i Azure Active Directory | Microsoft Docs
description: Förhandsgranska anpassade Azure AD-roller för att delegera identitetshantering. Hantera Azure-rolltilldelningar i Azure Portal, PowerShell eller Graph API.
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
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502206"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Använda molngrupper för att hantera rolltilldelningar i Azure Active Directory (förhandsversion)

Azure Active Directory (Azure AD) introducerar en offentlig förhandsversion där du kan tilldela en molngrupp till inbyggda roller i Azure AD. Med den här funktionen kan du använda grupper för att bevilja administratörsåtkomst i Azure AD med minimal ansträngning från dina globala och privilegierade rolladministratörer.

Tänk dig följande exempel: Contoso har anlitat personer i geografiska områden för att hantera och återställa lösenord för anställda i azure AD-organisationen. I stället för att be en privilegierad rolladministratör eller global administratör att tilldela rollen Supportadministratör till varje person individuellt, kan de skapa en Contoso_Helpdesk_Administrators-grupp och tilldela den till rollen. När personer går med i gruppen tilldelas de rollen indirekt. Ditt befintliga styrningsarbetsflöde kan sedan ta hand om godkännandeprocessen och granskningen av gruppens medlemskap för att säkerställa att endast legitima användare är medlemmar i gruppen och därför tilldelas till administratörsrollen support.

## <a name="how-this-feature-works"></a>Så här fungerar den här funktionen

Skapa en ny Microsoft 365 eller säkerhetsgrupp med egenskapen "isAssignableToRole" inställd på "true". Du kan också aktivera den här egenskapen när du skapar en grupp i Azure Portal genom att aktivera **Azure AD-roller kan tilldelas till gruppen**. Oavsett hur du gör kan du sedan tilldela gruppen till en eller flera Azure AD-roller på samma sätt som du tilldelar roller till användare. Högst 250 roll tilldelningsbara grupper kan skapas i en enda Azure AD-organisation (klientorganisation).

Om du inte vill att medlemmar i gruppen ska ha stående åtkomst till rollen kan du använda Azure AD Privileged Identity Management. Tilldela en grupp som en berättigad medlem i en Azure AD-roll. Varje medlem i gruppen kan sedan få sin tilldelning aktiverad för den roll som gruppen är tilldelad till. De kan sedan aktivera sin rolltilldelning under en fast tid.

> [!Note]
> Du måste ha en uppdaterad version av Privileged Identity Management för att kunna tilldela en grupp till Azure AD-rollen via PIM. Du kan ha en äldre version av PIM eftersom din Azure AD-organisation använder Privileged Identity Management-API:et. Kontakta aliaset för att pim_preview@microsoft.com flytta din organisation och uppdatera ditt API. Läs mer i [Azure AD-roller och funktioner i PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Varför vi framtvingar skapandet av en särskild grupp för att tilldela den till en roll

Om en grupp tilldelas en roll kan alla IT-administratörer som kan hantera gruppmedlemskap också indirekt hantera rollens medlemskap. Anta till exempel att en grupp Contoso_User_Administrators tilldelas rollen Användarkontoadministratör. En Exchange-administratör som kan ändra gruppmedlemskap kan lägga till sig Contoso_User_Administrators grupp och på så sätt bli användarkontoadministratör. Som du ser kan en administratör höja sin behörighet på ett sätt som du inte vill.

Med Azure AD kan du skydda en grupp som har tilldelats en roll med hjälp av en ny egenskap med namnet isAssignableToRole för grupper. Endast molngrupper som hade egenskapen isAssignableToRole inställd på "true" när den skapades kan tilldelas till en roll. Den här egenskapen är oföränderlig. När en grupp har skapats med den här egenskapen inställd på "true" kan den inte ändras. Du kan inte ange egenskapen för en befintlig grupp.
Vi har utformat hur grupper tilldelas till roller för att förhindra att den typen av potentiell överträdelse inträffar:

- Endast globala administratörer och privilegierade rolladministratörer kan skapa en rolltilldelig grupp (med egenskapen "isAssignableToRole" aktiverad).
- Det kan inte vara en dynamisk Azure AD-grupp. Det innebär att den måste ha medlemskapstypen "Tilldelad". En automatiserad population av dynamiska grupper kan leda till att ett oönskat konto läggs till i gruppen och därmed tilldelas rollen.
- Som standard kan endast globala administratörer och privilegierade rolladministratörer hantera medlemskapet i en roll assignerbar grupp, men du kan delegera hanteringen av roll-tilldelningsbara grupper genom att lägga till gruppägare.
- För att förhindra utökade privilegier kan autentiseringsuppgifterna för medlemmar och ägare av en roll assignable grupp endast ändras av en Privilegierad Autentiseringsadministratör eller en Global administratör.
- Ingen kapsling. En grupp kan inte läggas till som medlem i en roll assignerbar grupp.

## <a name="limitations"></a>Begränsningar

Följande scenarier stöds inte just nu:  

- Tilldela lokala grupper till Azure AD-roller (inbyggt eller anpassat)

## <a name="known-issues"></a>Kända problem

- *Endast Azure AD P2-licensierade* kunder: Tilldela inte en grupp som aktiv till en roll via både Azure AD och Privileged Identity Management (PIM). Mer specifikt ska du inte tilldela en roll till en roll  tilldelningsbar grupp när den skapas och tilldela en roll till gruppen med hjälp av PIM senare. Detta leder till problem där användarna inte kan se sina aktiva rolltilldelningar i PIM samt att det inte går att ta bort den PIM-tilldelningen. Berättigade tilldelningar påverkas inte i det här scenariot. Om du försöker utföra den här tilldelningen kan det hända att du ser ett oväntat beteende, till exempel:
  - Sluttiden för rolltilldelningen kan visas felaktigt.
  - I PIM-portalen **kan Mina** roller endast visa en rolltilldelning oavsett hur många metoder som tilldelningen beviljas med (via en eller flera grupper och direkt).
- *Endast Azure AD P2-licensierade kunder* Även efter att gruppen har tagits bort visas den fortfarande som berättigad medlem i rollen i PIM-användargränssnittet. Funktionellt är det inga problem. Det är bara ett cacheproblem i Azure Portal.  
- Använd det nya [administrationscentret för Exchange](https://admin.exchange.microsoft.com/) för rolltilldelningar via gruppmedlemskap. Det gamla administrationscentret för Exchange stöder inte den här funktionen ännu. Exchange PowerShell-cmdlets fungerar som förväntat.
- Azure Information Protection portal (den klassiska portalen) känner inte igen rollmedlemskap via grupp ännu. Du kan [migrera till](/azure/information-protection/configure-policy-migrate-labels) den enhetliga plattformen för känslighetsetiketter och sedan använda Office 365 Security & Compliance Center för att använda grupptilldelningar för att hantera roller.
- [Administrationscentret för](https://config.office.com/) appar stöder inte den här funktionen ännu. Tilldela användare direkt till administratörsrollen för Office-appar.
- [M365 Compliance Center](https://compliance.microsoft.com/) stöder inte den här funktionen ännu. Tilldela användare direkt till lämpliga Azure AD-roller för att använda den här portalen.

Vi åtgärdar dessa problem.

## <a name="required-license-plan"></a>Nödvändig licensplan

Om du använder den här funktionen måste du ha en Azure AD Premium P1-licens i Azure AD-organisationen. Om du Privileged Identity Management för just-in-time-rollaktivering måste du ha en tillgänglig Azure AD Premium P2-licens. Information om hur du hittar rätt licens för dina krav finns [i Jämföra allmänt tillgängliga funktioner i planerna Kostnadsfri och Premium.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Nästa steg

- [Skapa en rolltilldelningsbar grupp](groups-create-eligible.md)
- [Tilldela en roll till en roll som kan tilldelas en grupp](groups-assign-role.md)
