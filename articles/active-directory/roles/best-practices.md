---
title: Metod tips för Azure AD-roller – Azure Active Directory
description: Metod tips för att använda Azure Active Directory roller.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112020"
---
# <a name="best-practices-for-azure-ad-roles"></a>Metod tips för Azure AD-roller

I den här artikeln beskrivs några av de bästa metoderna för att använda Azure Active Directory rollbaserad åtkomst kontroll (Azure AD RBAC). Dessa bästa metoder är härledda från vår erfarenhet av Azure AD RBAC och kundernas upplevelser som du själv. Vi rekommenderar att du också läser vår detaljerade säkerhets vägledning för att [skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. hantera till lägsta behörighet

När du planerar din strategi för åtkomst kontroll, är det en bra idé att hantera till minsta möjliga behörighet. Lägsta behörighet innebär att du ger administratörerna exakt den behörighet de behöver för att utföra sitt jobb. Det finns tre aspekter att tänka på när du tilldelar en roll till dina administratörer: en viss uppsättning behörigheter, över en viss omfattning, under en viss tids period. Undvik att tilldela bredare roller i bredare omfång, även om det i första skedet verkar vara bekvämare att göra det. Genom att begränsa roller och omfattningar begränsar du vilka resurser som är utsatta för risk om säkerhets objekts skyddet aldrig äventyras. Azure AD RBAC stöder över 65 [inbyggda roller](permissions-reference.md). Det finns Azure AD-roller för att hantera katalog objekt, t. ex. användare, grupper och program, och även för att hantera Microsoft 365 tjänster som Exchange, SharePoint och Intune. För att bättre förstå inbyggda Azure AD-roller, se [förstå roller i Azure Active Directory](concept-understand-roles.md). Om det inte finns någon inbyggd roll som uppfyller dina behov kan du skapa egna [anpassade roller](custom-create.md).  
 
### <a name="finding-the-right-roles"></a>Hitta rätt roller

Följ de här stegen för att få hjälp att hitta rätt roll.

1. I Azure Portal öppnar du [roller och administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) för att se listan över Azure AD-roller.

1. Använd **tjänst** filtret för att begränsa listan över roller.

    ![Sidan roller och administratörer i Azure AD med tjänst filtret öppen](./media/best-practices/roles-administrators.png)

1. Läs dokumentationen om [inbyggda roller i Azure AD](permissions-reference.md) . Behörigheter som är associerade med varje roll visas tillsammans för bättre läsbarhet. Information om strukturen och betydelsen av roll behörigheter finns i [så här förstår du roll behörigheter](permissions-reference.md#how-to-understand-role-permissions).

1. Läs mer i dokumentationen för den [lägsta privilegierade rollen enligt uppgift](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. Använd Privileged Identity Management för att ge just-in-Time-åtkomst

En av principerna för minsta behörighet är att åtkomsten endast ska beviljas under en viss tids period. [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) ger dig till gång till dina administratörer just-in-Time-åtkomst. Microsoft rekommenderar att du aktiverar PIM i Azure AD. Med hjälp av PIM kan en användare bli en berättigad medlem i en Azure AD-roll. Sedan kan du aktivera rollen som en begränsad tidsram varje gång som behövs för att använda den. Privilegie rad åtkomst tas bort automatiskt när tids ramen upphör att gälla. Du kan också [Konfigurera PIM-inställningar](../privileged-identity-management/pim-how-to-change-default-settings.md) så att de kräver godkännande eller får e-postmeddelanden när någon aktiverar roll tilldelningen. Meddelanden ger en avisering när nya användare läggs till i privilegierade roller. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. Aktivera Multi-Factor Authentication för alla administratörs konton

[Utifrån våra studier](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)är ditt konto 99,9% mindre sannolikt att bli komprometterade om du använder Multi-Factor Authentication (MFA). 
 
Du kan aktivera MFA på Azure AD-roller på två sätt:
- [Roll inställningar](../privileged-identity-management/pim-how-to-change-default-settings.md) i Privileged Identity Management
- [Villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Konfigurera återkommande åtkomst granskningar för att återkalla onödiga behörigheter över tid

Med åtkomst granskningar kan organisationer granska administratörs åtkomst regelbundet för att se till att endast rätt personer har fortsatt åtkomst. Regelbunden granskning av dina administratörer är avgörande på grund av följande orsaker:
- En skadlig aktör kan kompromettera ett konto.
- Personer flyttar team inom ett företag. Om det inte finns någon granskning kan de amass onödig åtkomst över tid.
 
Information om åtkomst granskningar för roller finns i [skapa en åtkomst granskning av Azure AD-roller i PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Information om åtkomst granskningar av grupper som har tilldelats roller finns i [skapa en åtkomst granskning av grupper och program i åtkomst granskningar i Azure AD](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. begränsa antalet globala administratörer till mindre än 5

Som bästa praxis rekommenderar Microsoft att du tilldelar rollen global administratör till **färre än fem** personer i din organisation. Globala administratörer har nycklar till Konungariket Storbritannien och är i bästa intresse att hålla angrepps ytan låg. Som tidigare nämnts bör alla dessa konton skyddas med Multi-Factor Authentication.

När en användare registrerar sig för en moln tjänst från Microsoft skapas en Azure AD-klient som standard och användaren blir medlem i rollen global administratör. Användare som har tilldelats rollen som global administratör kan läsa och ändra alla administrativa inställningar i din Azure AD-organisation. Med några få undantag kan globala administratörer också läsa och ändra alla konfigurations inställningar i din Microsoft 365 organisation. Globala administratörer kan också öka sin åtkomst till läsa data.

Microsoft rekommenderar att du behåller två rast glas konton som är permanent tilldelade till den globala administratörs rollen. Se till att dessa konton inte kräver samma Multi-Factor Authentication-mekanism som dina vanliga administrativa konton för att logga in, enligt beskrivningen i [Hantera åtkomst konton för nöd situationer i Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. Använd grupper för Azure AD-roll tilldelningar och delegera roll tilldelningen

Om du har ett externt styrnings system som utnyttjar grupper bör du överväga att tilldela roller till Azure AD-grupper i stället för enskilda användare. Du kan också hantera roll tilldelnings bara grupper i PIM för att säkerställa att det inte finns några stå ägare eller medlemmar i dessa privilegierade grupper. Mer information finns i [hanterings funktioner för privilegierade åtkomst till Azure AD-grupper](../privileged-identity-management/groups-features.md).

Du kan tilldela en ägare till grupper som kan tilldelas roller. Ägaren bestämmer vem som har lagts till i eller tas bort från gruppen, så indirekt bestämmer vem som ska ha roll tilldelningen. På så sätt kan en global administratör eller en privilegie rad roll administratör delegera roll hantering per roll med hjälp av grupper. Mer information finns i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. Aktivera flera roller samtidigt med hjälp av privilegierade åtkomst grupper

Det kan vara fallet att en person har fem eller sex berättigade tilldelningar till Azure AD-roller via PIM. De måste aktivera varje roll individuellt, vilket kan minska produktiviteten. Sämre än det kan de också ha flera eller flera hundra Azure-resurser som har tilldelats dem, vilket förvärrar problemet.
 
I det här fallet bör du använda [privilegierade åtkomst grupper](../privileged-identity-management/groups-features.md). Skapa en privilegie rad åtkomst grupp och ge den permanent åtkomst till flera roller (Azure AD och/eller Azure). Gör användaren till en berättigad medlem eller ägare av den här gruppen. Med bara en aktivering kommer de att ha åtkomst till alla länkade resurser.

![Diagram över privilegierade åtkomst grupper som visar aktivering av flera roller samtidigt](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. Använd molnets egna konton för Azure AD-roller

Undvik att använda lokala, synkroniserade konton för Azure AD-roll tilldelningar. Om ditt lokala konto har komprometterats kan det också kompromettera dina Azure AD-resurser.

## <a name="next-steps"></a>Nästa steg

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](security-planning.md)