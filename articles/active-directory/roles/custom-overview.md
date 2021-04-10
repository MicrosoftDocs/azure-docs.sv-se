---
title: Översikt över Azure Active Directory rollbaserad åtkomst kontroll (RBAC)
description: Lär dig hur du förstår delarna i en roll tilldelning och begränsad omfattning i Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bddb03094b73dbd6d3b8d44c76ab242caa515dc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727866"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Översikt över rollbaserad åtkomst kontroll i Azure Active Directory

Den här artikeln beskriver hur du förstår den rollbaserade åtkomst kontrollen Azure Active Directory (Azure AD). Med Azure AD-roller kan du ge dina administratörer detaljerade behörigheter genom att följa principen om minsta behörighet. Inbyggda och anpassade Azure AD-roller fungerar på begrepp som liknar de som du hittar i [det rollbaserade Access Control-systemet för Azure-resurser](../../role-based-access-control/overview.md) (Azure-roller). [Skillnaden mellan de här två rollbaserade åtkomst kontroll systemen](../../role-based-access-control/rbac-and-directory-admin-roles.md) är:

- Azure AD-roller styr åtkomsten till Azure AD-resurser, till exempel användare, grupper och program med hjälp av Graph API
- Azure-roller styr åtkomst till Azure-resurser, till exempel virtuella datorer eller lagrings enheter med Azure Resource Management

Båda systemen innehåller liknande roll definitioner och roll tilldelningar som används. Behörigheter för Azure AD-roller kan dock inte användas i anpassade Azure-roller och vice versa.

## <a name="understand-azure-ad-role-based-access-control"></a>Förstå rollbaserad åtkomst kontroll i Azure AD
Azure AD stöder två typer av roll definitioner:
* [Inbyggda roller](./permissions-reference.md)
* [Anpassade roller](./custom-create.md)

Inbyggda roller är färdiga roller som har en fast uppsättning behörigheter. Dessa roll definitioner kan inte ändras. Det finns många [inbyggda roller](./permissions-reference.md) som Azure AD stöder och listan växer. För att runda av kanterna och uppfylla dina avancerade krav stöder Azure AD även [anpassade roller](./custom-create.md). Att bevilja behörighet med anpassade Azure AD-roller är en två stegs process som inbegriper att skapa en anpassad roll definition och sedan tilldela den med en roll tilldelning. En anpassad roll definition är en samling behörigheter som du lägger till från en för inställnings lista. Dessa behörigheter är samma behörigheter som används i de inbyggda rollerna.  

När du har skapat en anpassad roll definition (eller använt en inbyggd roll) kan du tilldela den till en användare genom att skapa en roll tilldelning. En roll tilldelning ger användaren behörigheten i en roll definition i en angiven omfattning. Med den här två stegs processen kan du skapa en enda roll definition och tilldela den flera gånger i olika omfång. Ett omfång definierar uppsättningen av Azure AD-resurser som roll medlemmen har åtkomst till. Det vanligaste omfånget är hela organisationen (org-wide). En anpassad roll kan tilldelas i hela organisationen, vilket innebär att roll medlemmen har rollen behörigheter för alla resurser i organisationen. En anpassad roll kan också tilldelas i ett objekt område. Ett exempel på ett objekt omfång är ett enda program. Samma roll kan tilldelas till en användare över alla program i organisationen och sedan till en annan användare med endast en omfattning av appen Contosos utgifts rapporter.  

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hur Azure AD avgör om en användare har åtkomst till en resurs

Följande är de övergripande steg som Azure AD använder för att avgöra om du har åtkomst till en hanterings resurs. Använd den här informationen för att felsöka åtkomst problem.

1. En användare (eller tjänstens huvud namn) hämtar en token till Microsoft Graph eller Azure AD Graph-slutpunkten.
1. Användaren gör ett API-anrop till Azure Active Directory (Azure AD) via Microsoft Graph eller Azure AD Graph med Utfärdad token.
1. Beroende på omständigheterna vidtar Azure AD en av följande åtgärder:
   - Utvärderar användarens roll medlemskap baserat på [wids-anspråk](../../active-directory-b2c/access-tokens.md) i användarens åtkomsttoken.
   - Hämtar alla roll tilldelningar som gäller för användaren, antingen direkt eller via grupp medlemskap, till den resurs som åtgärden utförs på.
1. Azure AD avgör om åtgärden i API-anropet ingår i de roller som användaren har för den här resursen.
1. Om användaren inte har någon roll med åtgärden i det begärda omfånget beviljas inte åtkomst. Annars beviljas åtkomst.

## <a name="role-assignment"></a>Rolltilldelning

En roll tilldelning är en Azure AD-resurs som bifogar en *roll definition* till en *användare* i ett visst *omfång* för att bevilja åtkomst till Azure AD-resurser. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort. På grund av detta består en roll tilldelning av tre element:

- Azure AD-användare
- Rolldefinition
- Resursomfång

Du kan [skapa roll tilldelningar](custom-create.md) med hjälp av Azure Portal, Azure AD PowerShell eller Graph API. Du kan också [ange roll tilldelningarna](view-assignments.md).

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har Christer grönt tilldelats den anpassade rollen app Registration-administratör i omfånget för Contoso widget Builder-appens registrering. Tilldelningen ger Christer behörigheterna för administratörs rollen för app-registrering enbart för den här aktuella program registreringen.

![Roll tilldelningen är hur behörigheter tillämpas och har tre delar](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Säkerhetsobjekt

Ett säkerhets objekt representerar den användare som ska tilldelas åtkomst till Azure AD-resurser. En användare är en person som har en användar profil i Azure Active Directory.

### <a name="role"></a>Roll

En roll definition eller roll är en samling behörigheter. En roll definition visar de åtgärder som kan utföras på Azure AD-resurser, till exempel skapa, läsa, uppdatera och ta bort. Det finns två typer av roller i Azure AD:

- Inbyggda roller som skapats av Microsoft och som inte kan ändras.
- Anpassade roller som skapas och hanteras av din organisation.

### <a name="scope"></a>Omfång

Ett omfång är begränsningen av tillåtna åtgärder till en viss Azure AD-resurs som en del av en roll tilldelning. När du tilldelar en roll kan du ange ett omfång som begränsar administratörens åtkomst till en speciell resurs. Om du till exempel vill ge en utvecklare en anpassad roll, men bara för att hantera en specifik program registrering, kan du inkludera den specifika program registreringen som ett omfång i roll tilldelningen.

## <a name="required-license-plan"></a>Obligatorisk licens plan

Användning av inbyggda roller i Azure AD är kostnads fri, medan anpassade roller kräver en Azure AD Premium P1-licens. Hitta rätt licens för dina behov i [Jämför allmänt tillgängliga funktioner i Free, Basic och Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure AD-roller](concept-understand-roles.md)
- Skapa anpassade roll tilldelningar med hjälp av [Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa lista över rolltilldelningar](view-assignments.md)