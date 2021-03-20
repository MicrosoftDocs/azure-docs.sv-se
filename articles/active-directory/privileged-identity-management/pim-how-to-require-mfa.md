---
title: MFA eller 2FA och Privileged Identity Management – Azure AD | Microsoft Docs
description: Lär dig hur Azure AD Privileged Identity Management (PIM) validerar Multi-Factor Authentication (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835007"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Multi-Factor Authentication och Privileged Identity Management

Vi rekommenderar att du kräver Multi-Factor Authentication (MFA) för alla administratörer. Detta minskar risken för angrepp på grund av ett komprometterat lösen ord.

Du kan kräva att användarna slutför en Multi-Factor Authentication-utmaning när de loggar in. Du kan också kräva att användarna slutför en Multi-Factor Authentication-utmaning när de aktiverar en roll i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). På så sätt, om användaren inte slutförde en Multi-Factor Authentication-utmaning när de loggade in, uppmanas de att göra det genom att Privileged Identity Management.

> [!IMPORTANT]
> Just nu fungerar Azure AD Multi-Factor Authentication bara med arbets-eller skol konton, inte Microsofts personliga konton (vanligt vis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox eller Outlook.com). Därför kan alla som använder ett personligt konto inte vara en berättigad administratör eftersom de inte kan använda Multi-Factor Authentication för att aktivera sina roller. Om dessa användare behöver fortsätta hantera arbets belastningar med hjälp av en Microsoft-konto kan du höja dem till permanenta administratörer för tillfället.

## <a name="how-pim-validates-mfa"></a>Hur PIM verifierar MFA

Det finns två alternativ för att verifiera Multi-Factor Authentication när en användare aktiverar en roll.

Det enklaste alternativet är att förlita dig på Azure AD Multi-Factor Authentication för användare som aktiverar en privilegie rad roll. För att göra detta måste du först kontrol lera att dessa användare är licensierade, om det behövs, och har registrerats för Azure AD Multi-Factor Authentication. Mer information om hur du distribuerar Azure AD Multi-Factor Authentication finns i [distribuera molnbaserad Azure AD-Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Vi rekommenderar, men krävs inte, att du konfigurerar Azure AD för att genomdriva multifaktorautentisering för dessa användare när de loggar in. Detta beror på att Multi-Factor Authentication-kontrollerna görs med Privileged Identity Management.

Alternativt kan du, om användarna autentiseras lokalt, ha din identitetsprovider ansvarig för Multi-Factor Authentication. Om du till exempel har konfigurerat AD Federation Services för att kräva en SmartCard-baserad autentisering innan du har åtkomst till Azure AD, [skyddar du moln resurser med Azure ad Multi-Factor Authentication och AD FS](../authentication/howto-mfa-adfs.md) innehåller instruktioner för att konfigurera AD FS för att skicka anspråk till Azure AD. När en användare försöker aktivera en roll, accepterar Privileged Identity Management att Multi-Factor Authentication redan har verifierats för användaren när den tar emot lämpliga anspråk.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Konfigurera inställningar för Azure-resurs roll i Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
