---
title: Hantera behöriga Azure AD-grupper i Privileged Identity Management (PIM) | Microsoft Docs
description: Hantera medlemmar och ägare av privilegierade åtkomst grupper i Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759781fd61cd42d05f2823d390e99d128dd2fcac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512400"
---
# <a name="management-capabilities-for-privileged-access-azure-ad-groups-preview"></a>Hanterings funktioner för privilegie rad åtkomst Azure AD-grupper (för hands version)

I Privileged Identity Management (PIM) kan du nu tilldela behörighet för medlemskap eller ägarskap för privilegierade åtkomst grupper. Från och med den här för hands versionen kan du tilldela de inbyggda rollerna Azure Active Directory (Azure AD) till moln grupper och använda PIM för att hantera grupp medlemmar och ägarens berättigande och aktivering. Mer information om roll tilldelnings bara grupper i Azure AD finns i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)](../roles/groups-concept.md).

>[!Important]
> Om du vill tilldela en privilegie rad åtkomst grupp till en roll för administrativ åtkomst till Exchange, Security och Compliance Center eller SharePoint använder du Azure AD Portal- **roller och-administratörer** och inte i den privilegierade åtkomst grupps upplevelsen för att göra användaren eller gruppen tillgänglig för aktivering till gruppen.

## <a name="require-different-policies-for-each-role-assignable-group"></a>Kräv olika principer för varje grupp som tilldelas roller

Vissa organisationer använder verktyg som Azure AD Business-to-Business (B2B)-samarbete för att bjuda in sina partners som gäster till sin Azure AD-organisation. I stället för en enda just-in-Time-princip för alla tilldelningar till en privilegie rad roll kan du skapa två olika privilegierade åtkomst grupper med sina egna principer. Du kan genomdriva mindre strikta krav för dina betrodda anställda och strängare krav som godkännande arbets flöde för dina partner när de begär aktivering till den tilldelade gruppen.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>Aktivera flera roll tilldelningar i en enskild begäran

Med för hands versionen av privilegierade åtkomst grupper kan du ge arbets belastnings bara administratörer snabb åtkomst till flera roller med en enda just-in-Time-begäran. Till exempel kan din Office-administratörer ha just-in-Time-åtkomst till Exchange-administratören, administration av Office-appar, team administratör och Sök administrations roller för att noggrant undersöka incidenter dagligen. Innan idag kräver det fyra efterföljande begär Anden, som är en process som tar lite tid. I stället kan du skapa en roll tilldelnings grupp med namnet "nivå 3 Office-administratörer", tilldela den till var och en av de fyra roller som tidigare nämnts (eller någon av de inbyggda Azure AD-rollerna) och aktivera den för privilegie rad åtkomst i gruppens aktivitets avsnitt. När den har Aktiver ATS för privilegie rad åtkomst kan du konfigurera just-in-Time-inställningarna för medlemmar i gruppen och tilldela dina administratörer och ägare som giltiga. När administratörerna höjer in i gruppen blir de medlemmar i alla fyra Azure AD-roller.

## <a name="extend-and-renew-group-assignments"></a>Utöka och förnya grupp tilldelningar

När du har ställt in din tidsbegränsade ägare eller medlems tilldelningar kan den första frågan du frågar vad händer om en tilldelning upphör att gälla? I den här nya versionen tillhandahåller vi två alternativ för det här scenariot:

- Utöka – när en roll tilldelning snart upphör att gälla kan användaren använda Privileged Identity Management för att begära ett tillägg för roll tilldelningen
- Förnya – när en roll tilldelning redan har gått ut kan användaren använda Privileged Identity Management för att begära en förnyelse av roll tilldelningen

Både åtgärder som initieras av användaren kräver ett godkännande från en global administratör eller en privilegie rad roll administratör. Administratörer behöver inte längre vara i arbetet med att hantera dessa förfallo datum. De kan bara vänta på begär Anden om förlängning eller förnyelse och godkänna dem om begäran är giltig.

## <a name="next-steps"></a>Nästa steg

- [Tilldela en privilegierad åtkomst grupp ägare eller medlem](groups-assign-member-owner.md)
- [Godkänn eller neka aktiverings begär Anden för privilegierade åtkomst grupper medlemmar och ägare](groups-approval-workflow.md)
